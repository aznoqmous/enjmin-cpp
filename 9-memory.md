# Memory

- [Memory](#memory)
  - [Intro : Different memories](#intro--different-memories)
  - [Stack](#stack)
    - [Definition](#definition)
    - [Stack allocator](#stack-allocator)
      - [Next free space ?](#next-free-space-)
      - [Delete](#delete)
      - [Pros](#pros)
      - [Cons](#cons)
  - [Heap](#heap)
    - [Definition](#definition-1)
    - [Heap allocator](#heap-allocator)
      - [Next free space?](#next-free-space)
      - [Delete](#delete-1)
      - [Pros](#pros-1)
      - [Cons](#cons-1)
  - [Registers](#registers)
    - [Definition](#definition-2)
    - [How to use them ?](#how-to-use-them-)
  - [Memory alignment](#memory-alignment)
    - [Example](#example)
    - [Bit field](#bit-field)
      - [Definition](#definition-3)
    - [Alignment and Bit fields](#alignment-and-bit-fields)


## Intro : Different memories
- **stack** used for local variables with limited scope, very fast
- **heap** used for persistent variables (created with new), much slower
- **registers** used for very local and basic typed variables, fastest

## Stack
### Definition
```cpp
void Function () {
    Vector x;
}
```
- `x` is allocated on **the stack**
- **memory allocation** is **very fast** (because of the allocator)
- **size of the stack is limited** (stack overflow error)
- **memory is freed** at the end of the **variable scope**
- stack is **unique** to each thread (not shared)

### Stack allocator
Process as a stack

#### Next free space ?
  - Address at the **top of the stack**
  - Shift the top of the stack according to the needed size

#### Delete
  - Free the memory attached to the pointer address
  - Do not shift the top of the stack

#### Pros
- Very fast

#### Cons
- Limited size
- Does not manage fragmentation in memory

## Heap
### Definition
```cpp
void Function () {
    Vector* x = new Vector();
}
```

- `x` is allocated on **the heap**
- **memory allocation** is **much slower** (because of the
allocator)
- **size of the heap** is **almost unlimited**
- **desallocation** must be **made manually**
- heap is **shared** among the threads

### Heap allocator
Complex allocator

#### Next free space?
- Complex management of holes in memory
- Bookkeeping of free spaces
  
#### Delete
- Free the memory attached to the pointer address
- Register the new hole in the book keeping
  
#### Pros
- Unlimited size
- Manage defragmention of the memory
  
#### Cons
- Much slower

## Registers
### Definition
```cpp
void Function () {
    register int i = 0;
}
```
- ``i`` **may be** stored in a **register** (compiler choice) _(lol)_
- **fastest** read and write (faster than in RAM)
- very limited (depend on the processor), only **basic types**
- Registers have **no memory address**
- NB: ``register`` keyword is deprecated _(soooo ???)_
  
### How to use them ?
- Only basic types (int then float)
- Variable usage **in the scope**
- No access to variable address
- To check, look at the **assembly**
- Use inline assembly `asm{}`

## Memory alignment
**RAM ↔ BUS ↔ CPU**

When data is stored in `RAM`, the `CPU` will read/write it faster if the data starts at certain memory addresses.  
This "lining up" of data at specific addresses is called **memory alignment**.

CPUs are optimized to read data at addresses that are **multiples of the data size**.  
If data is not aligned, the CPU may need extra work (multiple memory accesses), which makes the program slower.

### Example

- `char` → 1 byte
- `int` → usually 4 bytes
- `double` → usually 8 bytes

The compiler **arranges them in memory** so each type starts at an address that matches its size.

```cpp
struct Example {
    char a;   // 1 byte
    int b;    // 4 bytes
    char c;   // 1 byte
};
```
Without alignment (not real), **6 bytes**:
```
a | b b b b | c
```

With alignment (real), **12 bytes** (on a 4-byte alignment system):
```
a | padding padding padding | b b b b | c | padding padding padding
```
The **padding bytes** are added automatically by the compiler to keep things aligned.

### Bit field
#### Definition
```cpp
struct Date {
    unsigned int weekDay : 3; //0..7 (3 bits)
    unsigned int monthDay : 6; //0..31 (6 bits)
    unsigned int month : 5; //0..12 (5 bits)
    unsigned int year : 8; //0..100 (8 bits)
}

```

- **Bit field** regroup several members which sizes are less than an `int`.  
- Each bit field member is declared with its own **size** in **bits**.  
- Limited to `char`, `short`, `int`, `long`, `long long` and `enum`.  
- Size of a bit field **cannot exceed his type size** (remaining bits are used for padding).
- Bit field members have **no address** (no `&` on bit fields)
- /!\ Padding depends on the compiler /!\
  
### Alignment and Bit fields
```cpp
struct Date {
    unsigned int weekDay : 3; //0..7 (3 bits)
    unsigned int monthDay : 6; //0..31 (6 bits)
    unsigned int month : 5; //0..12 (5 bits)
    unsigned int year : 8; //0..100 (8 bits)
    // total : 22 bits
    unsigned int padding : 10; //(10 bits to match 32 bits)
}

```
- To force memory alignment, padding is added to match the next CPU
meaningful size (here 32 bits)
- An unnamed 0 sized bit field member can be used to **force padding**