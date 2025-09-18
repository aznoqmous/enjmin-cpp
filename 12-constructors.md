# Constructors
- [Constructors](#constructors)
  - [Initialization vs Assignment](#initialization-vs-assignment)
  - [Types of constructors](#types-of-constructors)
  - [Default constructors](#default-constructors)
  - [Initialization lists](#initialization-lists)
  - [Copy Constructors](#copy-constructors)
  - [Copy Assignment](#copy-assignment)
  - [Removing constructors](#removing-constructors)
  - [Preprocessor](#preprocessor)
    - [`#include`](#include)
    - [`#ifndef` / `#define` / `#endif`](#ifndef--define--endif)
    - [`#pragma once`](#pragma-once)
  - [Bonus](#bonus)
    - [Vector Constructors](#vector-constructors)
    - [Default parameters](#default-parameters)
    - [Most vexing parse](#most-vexing-parse)

## Initialization vs Assignment

**Initialization** transforms an object’s initial junk data into valid data.  
**Assignment** replaces existing valid data with other valid data.

**Initialization** is defined by the constructor for a type.  
**Assignment** is defined by the assignment operator `=` for a type.

## Types of constructors
```cpp
// Default constructors
Widget x;

// Copy constructors
Widget y(x);
Widget z = x;

// Copy assignment
z = x;
```
- Constructors have **no return value** (not event void)
- Constructors have the **same name as the type** in question
- Constructors can have an **initialization list**
  
## Default constructors
```cpp
class Vector {
    Vector() {
        logicalSize = 0;
        allocatedSize = 8;
        elems = new int[allocatedSize];
    }
};

// Both of these lines call the constructor
Vector x;
Vector *y = new Vector();

```

## Initialization lists
Initialization lists allow us to **initialize** (not assign) data members when we initialize our type.

```cpp
// Assignment
struct Widget {
    const int value;
    Widget();
};
Widget::Widget() {
    value = 42; //ERROR
}

// Initialization
struct Widget {
    const int value;
    Widget();
};
Widget::Widget(): value(42) {
}
```

Initialization lists can have **multiple parts**.
```cpp
struct Person {
    int age;
    string name;
    Person();
};
Person::Person() : age(36), name("Kanye") {
    //Empty constructor since nothing to assign
}
```

Initialization can also rely on a **constructor argument**.
```cpp
struct ConstMember {
    const int value;
    ConstMember(int v) : value(v) {}
};
int main() {
    ConstMember b(42); // value is 42
}
```

## Copy Constructors
A copy constructor is called when an instance of a type is constructed from
another instance.  

```cpp
class Vector {
    public:
        Vector(const Vector& other);
};

Vector::Vector(const Vector& other){
    // define copy mecanic
}
```
```cpp
// directly call the copy constructor
int x(4);

// implicitly call the copy constructor
int y = 2;

//-------------------------------------

Vector<string> a(10, "hi");

// directly call the copy constructor
Vector<string> b(a);

// implicitly call the copy constructor
Vector<string> c = a; 
```

**Caution when copying over pointers !**
```cpp
Vector<int> a;
a.push_back(8);
a.push_back(6);
a.push_back(7);
Vector<int> b = a;
b[0] = 9;
cout << a[0] << endl; // print 9
```

## Copy Assignment
**Assignment** takes an already initialized object and gives it new values
```cpp
class Widget {
    public:
        Widget& operator=(const Widget& other);
        //Other member vars and functions
};
Widget& Widget::operator=(const Widget& other) {
    // Code to copy data from other
}
```
Implementing the `copy assignment operator` is tricky for a couple of reasons :
- Catching memory leaks
- Handling self assignment
- Understanding the return value

## Removing constructors
```cpp
class Vector {
    public:
        // copy constructor
        Vector(const Vector& other) = delete;
        
        // assignment constructor
        Widget& operator=(const Widget& other) = delete;
};
```

## Preprocessor
**Preprocessor** is the first stage of the compiler and does **text manipulation**.  
Preprocessor **directives** are prefaced by `#`.

### `#include`
Simply copy/paste the file content in place.

### `#ifndef` / `#define` / `#endif`
```cpp
#ifndef NAME
#define NAME // define NAME so code will be paste the first time the file is included

/**
 * this code will not be paste if NAME is defined
 */

#endif
```

### `#pragma once`
Does the samething as the `#ifndef` / `#define` combo

## Bonus
### Vector Constructors
```cpp
// Initialize an empty vector:
vector<int> a;
// 42 elements: all zero
vector<int> b(42);
// 42 elements: all set to 11
vector<int> c(42, 11);
```

### Default parameters
In C++, a function can take default parameters, as long as they are defined in the **declaration** and not in the **definition**.
```cpp
// Declare our default arguments
void f(int a, int b = 5, int c = 42);
// Define our function
void f(int a, int b, int c) {
    cout << a << " ";
    cout << b << " ";
    cout << c << endl;
}
f(1); // 1 5 42
f(1, 2); // 1 2 42
f(1, 2, 3); // 1 2 3
```

### Most vexing parse
```cpp
int x(); //meant to do int x; or int x(2); 
```