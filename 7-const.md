# Const

`const` allows us to reason about whether a variable will be changed.

## Function parameters
Informs the final user that a given parameter won't be changed, without having to double check the function implementation
```cpp
void f(const int& x)
{
    // some obscure dark magic
}

void g()
{
    int x = 2;

    f(x);

    // x will always be equal to 2
}
```

## Member functions
A const member function assure that its usage will let the caller object properties unchanged.  
It allows to use said methods with const objects.
```cpp
struct Planet
{
    int countPopulation() const;
    void explode();
}

Planet::countPopulation() const
{
    return 42;
}

void checkPlanet(const Planet &p)
{
    cout << p.countPopulation() << endl; // ok: countPopulation is a const member function

    p.explode(); // not ok : explode is not a const member function
    
}
```

## Removing const
`const_cast` allows to remove const
```cpp
const int &constI = 12345; // some value
int &i = const_cast<int&>(constI);
```

```cpp
const int x = 42;
int y = x;
y = 100; // isokkk
```

## Const and non const function implementations
Best way to have 2 identical implementation of const and non const versions of a function is to have the non-const function call the const version.

```cpp
const string& at(const Tree &tree, size_t index)
{
// implementation
}

string& at(Tree &tree, size_t index)
{
    const Tree &constTree = tree;
    return const_cast<string&>(at(constThis, index));
}
```

## Const pointers

```cpp
//constant pointer to a non-constant Widget
// -> prevent changing pointer target, allows changing its value
Widget* const p;

//non-constant pointer to a constant Widget
// -> allows changing pointer target, prevent changing its value 
const Widget* p; 
Widget const* p;

//constant pointer to a constant Widget
// -> prevent changing the pointer target and its value
const Widget* const p;
Widget const* const p;
```

## Const iterators
You can make iterator read only by using a `const_iterator`.  
```cpp
const vector<int>::iterator itr = v.begin();
*itr = 5; //OK! changing what itr points to
++itr; //BAD! can’t modify itr

vector<int>::const_iterator itr = v.begin();
*itr = 5; //BAD! can’t change value of itr
++itr; //OK! changing v
int value = *itr; //OK! reading from itr
```

## nullptr
Same as `NULL` but can be implicitly converted to `int` 