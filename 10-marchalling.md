# Marchalling

## Definition
Converting data in memory (like a C++ object) into a format that can be stored or sent somewhere else, and then turning it back later.

### Mangling
**Name mangling** or **name decoration** is used to distinguish methods that could have the same name but differ from their parameters or class, it is a way to add more information in the exported name of a method.

## C++ with C#
- C#
    - Interpreted language
    - No strong control of structure sizes
    - No strong control of memory management
- C++
    - Compiled language
    - Strong control (sometimes not so strong) of structure sizes
    - Strong control of memory management
- Use cases:
    - Using a dynamic library from C#
    - Using a C++ class in C#

## Using dll from C#
### Call function
```cpp
// lib.cpp

extern "C" __declspec(dllexport)
int Addition (int a, int b) {
    return a+b;
}
```
- `extern "C"` is used to fix the call type
- `__declspec(dllexport)` is used to export the function (make it accessible from the
dll)

```cs
// main.cs

[DllImport("lib.dll")]
public static extern int Addition (int a, int b);
```
- `DllImport` is used to specify in which file to look for the function
- The dll file has to be near the executable
- dll functions are `public static` and `extern`
- Because function has been declared `extern "C"` no mangling is applied

### Use class
```cpp
// lib.cpp

CPerson::CPerson(string name, string firstname){
    Name = name;
    FirstName = firstname;
}

CPerson::~CPerson(void) {}

void CPerson::Print(){
    cout << "My name is " << FirstName << " " << Name << endl;
}

// Factory
extern "C" __declspec(dllexport)
CPerson* CPersonNew ( {
    return new CPerson("Buendia", "Axel");
}

// Recyle
extern "C" __declspec(dllexport)
void CPersonDelete (CPerson* cp) {
    delete cp;
} 

```

- **Factories** are needed to allow C++ side creation of the instance
- **Recycles** are needed to free the C++ side created instances
- The factory uses **constant parameters**, we will see later how to pass parameters

```cpp
// lib.h

#pragma once
#include <iostream>
#include <string>
using namespace std;

class __declspec(dllexport) CPerson {
    private:
        string Name;
        string FirstName;
    public:
        CPerson(string name, string firstname);
        ~CPerson(void);
        void Print();
}

extern "C" __declspec(dllexport) void CPersonDelete (CPerson* cp);

extern "C" __declspec(dllexport) CPerson* CPersonNew ();
```
- The class is exported with `__declspec(dllexport)`


```cs
// main.cs

[DllImport("lib.dll", EntryPoint = "CPersonNew")]
public static extern IntPtr NewCPerson();

[DllImport("lib.dll", EntryPoint = "CPersonDelete")]
public static extern void DeleteCPerson(IntPtr cp);
```

- During link time, the `Print` method cannot be found
- This is due to C++ **name mangling**
- **Name mangling** or **name decoration** is used to distinguish methods that could have the same name but differ from their parameters or class, it is a way to add more information in the exported name of a method
- To get decorated names, use `dumpbin.exe` (in visual C++):
```cpp
// dumpbin.exe /exports lib.dll
5 4 000110EB ?Print@CPerson@@QAEXXZ = @ILT+230(?Print@CPerson@@QAEXXZ)
6 5 00011203 CPersonDelete = @ILT+510(_CPersonDelete)
7 6 0001112C CPersonNew = @ILT+295(_CPersonNew)
```
- `CPersonDelete` and `CPersonNew` are exported without any decoration
- `Print` is exported with its decoration, just use the decorated name in the EntryPoint

```cs
// main.cs

[DllImport(
    "lib.dll", 
    EntryPoint = "?Print@CPerson@@QAEXXZ", 
    CharSet = CharSet.Unicode, 
    CallingConvention = CallingConvention.ThisCall
)]
public static extern void PrintCPerson(IntPtr this);

[DllImport(
    "lib.dll", 
    EntryPoint = "?Print@CPerson@@QAEXXZ", 
    CharSet = CharSet.Unicode, 
    CallingConvention = CallingConvention.ThisCall
)]
public static extern void PrintCPerson(IntPtr this);
```
- `CharSet` is used to define the encodage of string, not used yet
- `CallingConvention` is used to define the calling convention, here we use the `ThisCall` which includes the implicit pointer `this`


#### Passing string parameters
```cpp
// lib.h

#pragma once
#include <wchar.h>

class __declspec(dllexport) CPerson {
    private:
        wchar_t Name;
        wchar_t FirstName;
    public:
        CPerson(wchar_t name, wchar_t firstname);
        ~CPerson(void);
        void Print();
}

extern "C" __declspec(dllexport) void CPersonDelete (CPerson* cp);

extern "C" __declspec(dllexport) CPerson* CPersonNew (wchar_t* name, wchar_t* firstname);
```
- `std::string` is replaced par `wchar_t` (a more basic type)
- Some types are automatically marshalled, see **blittable** types
- Try to stay on **basic types**, `arrays` can be tricky to pass

```cpp
// lib.cpp

#include "lib.h"

CPerson::CPerson(wchar_t name, wchar_t firstname){
    wcscpy_s(Name, name);
    wcscpy_s(FirstName, firstname);
}

CPerson::~CPerson(void){}
    void CPerson::Print(){
    wprintf(L"My name is %s %s\n", FirstName, Name);
}

extern "C" __declspec(dllexport) void CPersonDelete (CPerson* cp){
    delete cp;
}

extern "C" __declspec(dllexport) CPerson* CPersonNew (wchar_t* name, wchar_t* firstname){
    return new CPerson(name, firstname);
}

```

- `wcscpy_s` is used to copy the `wchar`
- `wprintf` is used to output the `wchar` 

```cs
// main.cs

[DllImport(
    "lib.dll", 
    EntryPoint = "CPersonNew", 
    CharSet = CharSet.Unicode
)]
public static extern IntPtr NewCPerson();

[DllImport(
    "lib.dll",
    EntryPoint = "CPersonDelete", 
    CharSet = CharSet.Unicode
)]
public static extern void DeleteCPerson(IntPtr cp);

[DllImport(
    "lib.dll", 
    EntryPoint = "?Print@CPerson@@QAEXXZ",
    CharSet = CharSet.Unicode,
    CallingConvention = CallingConvention.ThisCall
)]
public static extern void PrintCPerson(IntPtr this);

static void Main(string[] args){
    IntPtr cp = NewCPerson("Buendia", "Axel");
    PrintCPerson(cp);
    DeleteCPerson(cp);
}
```

- Be careful about the **mangling** of `Print` which could have changed (should not)
- The `wchar_t` is automatically marshalled to C# string