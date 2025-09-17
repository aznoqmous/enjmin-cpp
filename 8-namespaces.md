# Objects and Namespaces
- [Objects and Namespaces](#objects-and-namespaces)
  - [Basic namespace](#basic-namespace)
  - [Splitting namespaces](#splitting-namespaces)
  - [Nested namespaces](#nested-namespaces)
  - [using namespace](#using-namespace)
  - [Scope resolution](#scope-resolution)
  - [Static members and functions](#static-members-and-functions)
  - [Nested Classes](#nested-classes)

## Basic namespace
```cpp
namespace stanford
{
    void foo();
}

int main()
{
    stanford::foo();
}

```

## Splitting namespaces
Many files can contribute to the same namespace
```cpp
namespace stanford
{
    void foo();
}
namespace stanford
{
    void bar();
}
```

## Nested namespaces
```cpp
namespace A
{
    namespace B
    {
        void foo();
    }
}

// usage
A::B::foo();
```

**C++ 11**
```cpp
namespace A::B
{
    void foo();
}
```


## using namespace
```cpp
// without using namespace
std::cout << "Hello" << std::endl;

-------------------------------

// with using namespace
using namespace std;
cout << "Hello" << endl;
```

```cpp
using std::cout;
cout << "Hello" << std::endl;
```

## Scope resolution

Use `::` when accessing something that belongs to all instances of a class.
- Implementations for functions
- `static` members
- Nested classes

Use `.` when accessing something that belongs to an object.

## Static members and functions
`static` members are shared by all instances of a class.
Only `const` members can be set inside the prototype
```cpp
class GlobalCounter
{
    private:
        // const member - set inside prototype
        const static int count = 0;
}
```

```cpp
class GlobalCounter
{
    private:
        // non const member - must be set outside of prototype
        static int count;
}
int GlobalCounter::count = 0;
```
Accessing static member and functions is done using the scope resolution operator.
```cpp
class GlobalCounter
{
    public:
        const static int count = 0;
        static in numHours(){ return 24; }
}

cout << GlobalCounter::count << endl; // prints "0"
cout << GlobalCounter::numHours() << endl; // prints "24"
```

## Nested Classes
You can nest classes, and it works pretty much as expected
```cpp
class A {
    public:
        class B {
        // Implementation
        };
};

int main() {
    A::B b;
}
```