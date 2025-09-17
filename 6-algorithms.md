# Algorithms

- [Algorithms](#algorithms)
  - [Abstraction](#abstraction)
  - [Iterator Adapters](#iterator-adapters)
    - [Stream iterator](#stream-iterator)
    - [Inserter iterator](#inserter-iterator)
      - [std::copy](#stdcopy)
      - [std::back\_inserter](#stdback_inserter)
      - [std::front\_inserter](#stdfront_inserter)
      - [std::inserter](#stdinserter)

## Abstraction

Abstraction allows to express the general structure of a problem instead of a particular implementation.

Different types correspond to a different level of abstractions.

- **Basic types** are the lowest level of abstraction as they hold a **single value**
- **Containers** features a single implementation that handles multiple basic types (`int`, `bool`, `double`...).
- **Iterators** allows us to abstract which container was used
- **Algorithms** are operations such as sorting, partitioning, filtering eg. can be written to handle any container type (ie, any iterators)

**Algorithms > Iterators > Containers > Basic Types**

Following sections feature `<algorithm>` functionnalities

## Iterator Adapters
### Stream iterator
Allows to transform a stream into an iterator
```cpp
int main() {
  ifstream input("numbers.txt");
  
  istream_iterator<int> fileItr(input);

  istream_iterator<int> endItr; // empty istream_iterator == end of file

  int sum = accumulate(fileItr, endItr, 0);
  
  cout << "Sum: " << sum << endl;
  
  return 0;
}
```

### Inserter iterator
#### std::copy
`copy` allow you to copy a container elements into another elements using an iterator.  
```cpp
vector<int> v;
v.push_back(1);
v.push_back(650);
v.push_back(867);
v.push_back(5309);
vector<int> vcopy(4);
copy(v.begin(), v.end(), vcopy.begin()); // will break if vcopy.size() < v.size() !
```
**Caution** : if the target element size is less than the source size, it will break
![Coyote](/assets/coyote.jpg)
Reason why you should combine `copy` with inserter functions

#### std::back_inserter
Allows you to avoid cases where the target element would not have enough space
```cpp
vector<int> v {
    1, 650, 867, 5309
};
vector<int> vcopy(3);
copy(v.begin(), v.end(), back_inserter(vcopy));
```

Dereferencing a `back_inserter` calls `push_back`

```cpp
vector<int> v;
front_insert_iterator<vector<int>> insertIterator = back_inserter(d);
*insertIterator = 10; // v = [10]
*insertIterator = 20; // v = [10, 20]
*insertIterator = 30; // v = [10, 20, 30]
```

#### std::front_inserter
Allows you to mimic a `push_front` copy on `deque`s
```cpp
deque<int> d {
    1, 650, 867, 5309
};
deque<int> dcopy;
copy(d.begin(), d.end(), back_inserter(dcopy));
// or
copy(d.begin(), d.end(), front_inserter(dcopy));
```

Dereferencing a `front_inserter` calls `push_front`

```cpp
deque<int> d;
front_insert_iterator<deque<int>> insertIterator = front_inserter(d);
*insertIterator = 10; // d = [10]
*insertIterator = 20; // d = [20, 10]
*insertIterator = 30; // d = [30, 20, 10]
```

#### std::inserter
To use when working with `set` and `map`