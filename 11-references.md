# References

## Reference Style
```cpp
int *x; // good
int* x; // bad
int * x; // bad

// because...
int* x, y, z; // only x is a pointer !

int *x, *y, *z; // all is good all is clear

```

**`&` and `*` bind to the name, not the type**

## Basic reference
```cpp
// reference variable
void main(){
    int x = 1;
    int& y = x;
    y = 2;
    cout << x << endl; // prints 2
}

// reference as function argument
void f(int& a){
    a += 1;
}

void main(){
    int x = 1;
    f(x);
    cout << x << endl; // prints 2
}
```

### Function return referenced value
```cpp
struct Person {
    string nameVar;
    string& name() { return nameVar; }
};

void main() {
    Person p = { "Joe" };
    p.name() = "Bob";
    cout << p.name() << endl; // prints "Bob"
}
```