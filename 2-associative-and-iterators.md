# Associative Container and Iterators

## std::set
```cpp
#include <iostream>
#include <string>
#include <set>
using namespace std;

set<string> groceryList;
groceryList.insert("a"); // add an element to the set
groceryList.erase("a"); // remove an element from the set

// Note: set doesnt allow duplicates
groceryList.insert("a");
groceryList.insert("a");
groceryList.insert("a");
cout << groceryList.count("a") << endl; // prints "1"
groceryList.erase("a");
cout << groceryList.count("a") << endl; // prints "0"
```

## std::map
```cpp
#include <iostream>
#include <string>
#include <map>
using namespace std;

map<string, int> myMap;
myMap["foo"]++;
myMap["bar"]++;
myMap["foo"]++;

cout << myMap["foor"] << endl; // prints "2"
```

## std::pair
A pair is like a single collection element
```cpp
pair<string, int> p;
p.first = "Cell phone";
p.second = 33612310321;
```

## std::multiset
Multisets allow storing of multiple copies of an element
```cpp
#include <iostream>
#include <multiset>
using namespace std;

multiset<int> myMultiset;
myMultiset.insert(3);
myMultiset.insert(3);
cout << myMultiset.count(3) << endl; // prints "2"
```

## std::multimap
Multimap doesnt have a [] operator. Kind of a pairs-multiset.
```cpp
#include <iostream>
#include <string>
#include <map>
using namespace std;

multimap<int, int> myMultimap;
myMultimap.insert(make_pair(3, 3));
myMultimap.insert(make_pair(3, 12));
cout << myMultimap.count(3) << endl; // prints "2"
```

## std::unordered_map
Same as map but unsorted. Faster but uses more memory.
```cpp
unordered_map<int, int> m;
unordered_map<int, int>::iterator i = m.begin();
unordered_map<int, int>::iterator end = m.end();
while (i != end) {
    cout << (*i).first << (*i).second << endl;
    ++i;
}

```

## Iterators
An iterator point to values in any container.
```cpp
// Initialization example
set<int>::iterator itr = mySet.begin();

// Getting the current iterator value
cout << *itr << endl;

// Advance the iterator to the next value (convention to put ++ before the iterator)
++itr;

// Check if the iterator as reached the last value
if(itr == mySet.end()) return;
```

### Example : iterating on std::map
```cpp
/** C++03 with iterators */
map<string, int> myMap;
map<string, int>::iterator i;
map<string, int>::iterator end = map.end();
for(i = myMap.begin(); i != end; i++){
    cout << (*i).first << " " << (*i).second << endl;
}

/** C++11 without iterators + auto (hidden) */
map<string, int> map;
for (auto& a : map) {
 cout << a.first << " " << a.second << endl;
}
```

### Other iterators usages
#### Easy boundaries
```cpp
vector<int> v(10, 0);
v.erase(v.begin() + 5, v.end()); // removes 6th to last entries 
```
#### Customized boundaries
```cpp
set<int>::iterator i = mySet.lower_bound(7); // iterates through elements greater or equal to 7
set<int>::iterator end = mySet.lower_bound(26); // and less than 26
while (i != end) {
    cout << *i << endl;
    ++i;
}
```

## Range based for loops
```cpp

set<string> mySet;
for(string str : mySet) {
    cout << str << endl;
}

map<string, int> myMap;
for (std::pair<string, int> p : myMap) {
    cout << a.first << " " << a.second << endl;
}

```