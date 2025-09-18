# RAII
- [RAII](#raii)
  - [Definition](#definition)
  - [Resources](#resources)
  - [RAII Approach](#raii-approach)
  - [Copying RAII objects](#copying-raii-objects)
  - [Smart Pointers](#smart-pointers)
    - [unique\_ptr](#unique_ptr)
    - [shared\_ptr](#shared_ptr)
    - [weak\_ptr](#weak_ptr)
    - [make](#make)

## Definition
**R**esource **A**cquisition **I**s **I**nitialization
- When the object is created (initialized) → it acquires the resource.
- When the object is destroyed (goes out of scope) → it automatically releases the resource.

Skipping the destroy/release phase leads to bugs and memory leaks.

## Resources
Some resource management:

| | Acquire | Release
|-|-|-
|Files|fopen|fclose
|Memory|new, new[]|delete, delete[]
|Locks|lock, try_lock|unlock
|Sockets|socket|close

Classic resource management example :
```cpp
// Here's C file I/O with resources marked
void printFile(const char *name) {
    // Acquire the file resource
    FILE *f = fopen(name, "r"); // Forget to open and s** happens

    // Print the contents of the file

    // Release the file resource
    fclose(f); // Forget to close and all hell breaks lose
}
```
## RAII Approach
```cpp
struct FileObj {
    FILE *ptr;

    // Acquire the file resource
    FileObj(char *name) : ptr(fopen(name, "r")) {}

    // Release the file resource
    ~FileObj() {
        fclose(ptr);
    }
};
void printFile(const char *name) {
    // Initialize the object
    // Implicitly acquire the resource
    FileObj o(name);

    // Print the contents of the file

    // Destructor the object
    // Implicitly release the resource
}
```

## Copying RAII objects
Caution when copying RAII objects, one object destroying a shared pointer can cause some issues !  
See [smart pointers](#smart-pointers)

## Smart Pointers

### unique_ptr
- Will delete the attached resource when the pointer is destroyed
- Cannot be copied
```cpp
{
std::unique_ptr<int> p = new int;
    // Use p
} 
// Freed !
```

### shared_ptr
- The resource can be stored by any number of `shared_ptrs`
- It will be deleted once none of them point to it anymore
- Only works if new `shared_ptrs` are made through copying
- Implemented with a reference counter, free the resource when the counter hit 0
- If there are **circular_reference**, it will never be freed
```cpp
{
    std::shared_ptr<int> p1 = new int;
    // Use p1
    {
        std::shared_ptr<int> p2 = p1;
        // Use p1 and p2
    }
    // Use p1
}
// Freed!
```

### weak_ptr
- Meant to solve the circular reference problem
- Can be created from a shared_ptr, but doesn’t increase reference count
- Can’t be directly used, but you can get a shared_ptr from it if the resource still exists
```cpp
{
    std::shared_ptr<int> p1 = new int;
    {
        // Doesn’t increment count
        std::weak_ptr<int> p2 = p1;
        // Returns an empty pointer if the resource is freed already
        std::shared_ptr<int> p3 = p2.lock();
    }
}
// Freed!
```

### make
`make` functions can call `new` after everything has completed successfully and avoid memory leaks due to exceptions.

```cpp
std::unique_ptr<int> p1 = std::make_unique<int>();
std::shared_ptr<string> p1 = std::make_shared<string>("hi");
std::shared_ptr<string> p1 = std::make_shared<string>(5, '!');
```