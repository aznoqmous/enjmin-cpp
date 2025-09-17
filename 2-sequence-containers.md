# Sequence containers

## std::stack
```cpp
stack<int> myStack;

int size = myStack.size(); // get stack size

if(myStack.empty()) // is the stack empty

myStack.push(10); // add an element to the top

int top = myStack.top(); // get top element

myStack.pop(); // remove top element
```

## std::vector
```cpp
vector<int> v; // create an empty vector
vector<int> v(n); // create an empty vector with n 0
vector<int> v(n, k); // create an empty vector with n k

v.push_back(k); // add k at the end of the vector
v.pop_back(); // remove the last element of the vector 

int size = v.size(); // get the vector size
v.clear(); // clear the vector

if(v.empty()) // is the vector empty

int k = v.at(i); // get element at index i
v.at(i) = k; // replace element at index i

int a = v[i]; // get at index i without bound checking
v[i] = a; // replace element at index i without bound checking 
```

## std::deque
```cpp
deque<int> d;
d.push_front(k); // add an element to the front
d.pop_front(k); // remove the front element

```