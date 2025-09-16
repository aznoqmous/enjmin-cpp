# Streams

## Print a value
```cpp
#include <iostream>
using namespace std;

cout << "Hello" << endl
cout << "My favorite number is :" << 31221 << endl
```

## Prompt a value
```cpp
#include <iostream>
using namespace std;

int value;
cin >> value;
```

## Write a file
```cpp
#include <iostream>
#include <fstream>
using namespace std;


ofstream outputFile;
outputFile.open("my_text_file.txt");
outputFile << "Hello !!" << endl;
```

## Read a file
```cpp
#include <string>
#include <iostream>
#include <fstream>
using namespace std;

ifstream input;
input.open("my_text_file.txt");

while(true){
    string value;

    // Read a word - or
    input >> value;

    // Read a line
    getline(input, value);

    if(input.fail()) break;
    cout << value << endl; // print
}

input.close();
```

## String Stream : **string** to **int**
```cpp
#include <iostream>
#include <sstream> // notice the dumb name
using namespace std;

stringstream inputStringStream;
int intRepresentation;
inputStringStream << kevinsFavoriteNumberString;
inputStringStream >> intRepresentation;
``` 

## String Stream : **string** to **int**
```cpp
#include <iostream>
#include <string>
#include <sstream> // notice the dumb name
using namespace std;

stringstream outputStringStream;
outputStringStream << kevinsFavoriteNumber;
string stringRepresentation = outputStringStream.str();
```

## Stream value conversion

```cpp
bool z = cout; // Converted to a bool implicitly
bool isGood = !cout.fail(); // Equivalent to the above line

// in other words : 
stream.fail() == !stream
(stream >> value) == stream

// so we could simplify reading data to :
while(stream >> value){
    // use value
}
```

## Mixing >> and getline
```cpp
#include <iostream>
using namespace std;

int age;
string name;

cin >> age; // cin allows casts to int
cin.ignore(numeric_limits<streamsize>::max(), '\n'); // skip until newline

getline(cin >> ws, name); // get first word from line

cout << name << " - " << age << endl;
```

## Stream Manipulators

| function | cout | cin
|-|-|-
| `endl` | insert a newline and flush the stream | -
| `ws`   | - | skips all currently available whitespaces
| `boolalpha` | print `true` or `false` for booleans | -
| `hex` | print numbers in hexadecimal ||
| `dec` | print numbers in decimal ||
| `oct` | print numbers in octal ||
| `setprecision(x)` | adjust the precision numbers print with ||
| `setw(x)` | output `x` times fill characters (see `setfill`, whitespace by default) | set the max input size (works well with `string`, `char[]`) |
| `setfill(x)` | set the fill character used by `setw` |