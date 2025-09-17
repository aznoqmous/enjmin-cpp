# Templates
- [Templates](#templates)
  - [Function overloading](#function-overloading)
  - [Templates](#templates-1)
    - [Type inference](#type-inference)
    - [Templates + iterators](#templates--iterators)
  - [Iterator Types](#iterator-types)
    - [Common iterators traits](#common-iterators-traits)
    - [Input iterators](#input-iterators)
    - [Output iterators](#output-iterators)
    - [Random access iterators](#random-access-iterators)
    - [Example : Copy elements from a collection to another](#example--copy-elements-from-a-collection-to-another)

## Function overloading

Allows to write different function implementation according to its parameters types.
```cpp
int min(int a, int b) {
    return (a < b) ? a : b;
}
double min(double a, double b) {
    return (a < b) ? a : b;
}
```

## Templates
Allows to use the same function for different types.  
Under the hood, the compiler will generate a new function per type used inside your code.
```cpp
template <typename T> 
T min(T a, T b){
    return a < b ? a : b;
}

int a = 4;
int b = 9;
int c = min<int>(a, b); // c = 4

double a = 4.5;
double b = 9.6;
double c = min<double>(a, b); // c = 4.5
```

### Type inference
If every arguments of type `T` has the same type, the compiler will guess the right type to use.
```cpp
int a = 4;
int b = 9;
int c = min(a, b); // not a problem ! (y)
```

### Templates + iterators
Templates work well with iterators, regardless of the type of collection.

```cpp
template<typename IteratorType, typename ElementType>
IteratorType MyFind(IteratorType first, IteratorType last, ElemType target)
{
  while (first!=last) {
    if (*first == target) return first;
    ++first;
  }
  return last;
}


vector<int> myContainer = {
    0, 1, 3, 4, 5, 6, 7
}

vector<int>::iterator myIterator = MyFind(myContainer.begin(), myContainer.end(), 4);

if(myIterator != myContainer.end()){
    // Found !
}
else {
    // Not found :(
}
```

## Iterator Types
### Common iterators traits
- Can be created from an existing iterator
- Can be advanced using `++`
- Can be compared with `==` and `!=`

```cpp
vector<double> v = ...;
vector<double>::iterator itr = v.begin();
for (; itr != v.end(); itr++) {
    // do container stuff
}
```

### Input iterators
Can be dereferenced on the **right hand side** of an expression
```cpp
vector<double> v = ...;
vector<double>::iterator itr = v.begin();
double val = *itr;
```
### Output iterators
Can be dereferenced on the **left hand side** of an expression
```cpp
vector<double> v = ...;
vector<double>::iterator itr = v.begin();
*itr = 2.5;
```

### Random access iterators
Can be incremented or decremented by
arbitrary amounts using `+`, `-`, and related operations
```cpp
vector<int> v = ...;
vector<int>::iterator itr = v.begin();
for (; itr != v.end(); itr += 3) {
 //...
}
```

### Example : Copy elements from a collection to another
```cpp
template<typename InputIterator, typename OutputIterator>
OutputIterator MyCopy(InputIterator first, InputIterator last, OutputIterator destination) {
  // As long as we have more elements to process:
  while (first!=last) {
    // copy and advance destination:
    *destination = *first;
    ++destination;
    // advance the input range:
    ++first;
  }
  return destination;
}
```