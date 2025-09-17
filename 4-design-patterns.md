# Design Patterns
- [Design Patterns](#design-patterns)
  - [Singleton](#singleton)
    - [How it works](#how-it-works)
    - [Classic usages](#classic-usages)
    - [Example](#example)
    - [Advantages](#advantages)
    - [Disadvantages](#disadvantages)
  - [State Machine](#state-machine)
    - [How it works](#how-it-works-1)
    - [Classic usages](#classic-usages-1)
    - [Example](#example-1)
    - [Advantages](#advantages-1)
    - [Disadvantages](#disadvantages-1)
  - [Factory](#factory)
    - [How it works](#how-it-works-2)
    - [Classic usages](#classic-usages-2)
    - [Example](#example-2)
    - [Advantages](#advantages-2)
    - [Disadvantages](#disadvantages-2)
  - [Observer](#observer)
    - [How it works](#how-it-works-3)
    - [Classic usages](#classic-usages-3)
    - [Example](#example-3)
    - [Advantages](#advantages-3)
    - [Disadvantages](#disadvantages-3)

## Singleton

### How it works
Only one object in memory referenced by other objects

### Classic usages
- Managers
- Pools  
...
### Example
```cpp
class Singleton {
private:
    static Singleton* instan // Private constructor
    Singleton()  // Delete copy constructor and assignment
    Singleton(const Singleton&) = delete // C++11
    Singleton& operator=(const Singleton&) = del // C++11

public:
    static Singleton& getInstance() {
        if (instance == nullptr)
            instance = new Singleton();
        return *instance;
    }
    void method( // do something
    }
};

Singleton* Singleton::instance = nullptr // must be initialized once in a cpp file!

int main() {
    Singleton& s1 = Singleton::getInstance() // The instance is created during this first call to getInstance
    s1.method();

    Singleton& s2 = Singleton::getInstance() // This time the instance has already been created and is just returned
    if(&s1 == &s2) return 1; // Will return 1 (true)
    return 0;
}
```

### Advantages
**Single Instance** – Ensures only one object exists.  
**Global Access** – Easily accessible from anywhere.  
**Lazy Initialization** – Created only when needed.  
**Efficient Resource Use** – Saves memory/resources.  
**Avoids Global Variables** – Cleaner and safer.  
### Disadvantages
**Global State** – Can lead to hidden dependencies.  
**Tight Coupling** – Harder to change or extend.  
**Testing Issues** – Difficult to mock in unit tests.  
**Thread Safety** – Must handle carefully in multi-threaded apps.  
**Overuse** – Misuse can clutter your design.

## State Machine

### How it works
A set of methods in an object produces different behaviours corresponding to the state of this object

### Classic usages
- NPC's AI
- Complex managers

### Example 

```cpp
class Context;

class State {
 protected:
  Context *_Context;
 public:
  virtual ~State() { }
  void setContext(Context *context) { // allow setting the context
    _Context = context;
  }
  virtual void Method1() = 0;  // virtual null method used to 
  virtual void Method2() = 0;
};

class Context {
  /**
   * @var State A reference to the current state of the Context.
   */
 private:
  State *_State;
 public:
  Context(State *state) : _State(nullptr) {
    this->TransitionTo(state);
  }
  ~Context() {
    delete _State;
  }
  /**
   * The Context allows changing the State object at runtime.
   */
  void TransitionTo(State *state) {
    if (this->_State != nullptr)
      delete this->_State;
    this->_State = state;
    this->_State->setContext(this);
  }
  /**
   * The Context delegates part of its behaviour to the current State object.
   */
  void Request1() {
    this->_State->Method1();
  }
  void Request2() {
    this->_State->Method2();
  }
};

/**
 * Concrete States implement various behaviours, associated with a state of the
 * Context.
 */
class ConcreteStateA : public State {
 public:
  void Method1() overrid // ConcreteStateA handles request1
  }
  void Method2() overrid // ConcreteStateA handles re // ConcreteStateA wants to change the state of the context
    this->_Context->TransitionTo(new ConcreteStateB);
  }
};

class ConcreteStateB : public State {
 public:
  void Method1() overrid // ConcreteStateB handles request1
  }
  void Method2() overrid // ConcreteStateB handles re // ConcreteStateB wants to change the state of the context
    this->_Context->TransitionTo(new ConcreteStateA);
  }
};

/**
 * The client code.
 */
void ClientCode() {
  Context *context = new Context(new ConcreteStateA);
  context->Request1();
  context->Request2();
  delete context;
}

int main() {
  ClientCode();
  return 0;
}
```
### Advantages
**Single Responsibility Principle** – Organize the code related to particular states into separate classes.  
**Open/Closed Principle** – Introduce new states without changing existing state classes or the context.  
**Simplification** – Simplify the code of the context by eliminating bulky state machine conditionals.

### Disadvantages
**Overkill** – If to few states, or to few changes.  
**Memory management** – This implementation does a lot of allocations / desallocations, could you find a way to avoid those?

## Factory

### How it works
An abstract class is used to instantiate specific objects through specialized factories.

### Classic usages
- Loggers  
- Database connectors  
- UI components factory  
- Game object factory  

### Example

```cpp
#include <iostream>

// Abstract object, all created objects must inherit from this class
class Shape {
public:
    virtual void draw() = 0; // Pure virtual function
    virtual ~Shape() {}
};

// A specific object that inherit from Shape
class Circle : public Shape {
public:
    void draw() override {
        std::cout << "Drawing Circle" << std::endl;
    }
};

// Another specific object
class Square : public Shape {
public:
    void draw() override {
        std::cout << "Drawing Square" << std::endl;
    }
};

// The abstract shape factory, used as interface of all factories
class ShapeFactory {
public:
    virtual Shape* createShape() = 0; // Factory method
    virtual ~ShapeFactory() {}
};

// A specific shape factory, used to create specific object (circle)
class CircleFactory : public ShapeFactory {
public:
    Shape* createShape() override {
        return new Circle();
    }
};

// A specific shape factory, used to create specific object (square)
class SquareFactory : public ShapeFactory {
public:
    Shape* createShape() override {
        return new Square();
    }
};

// An example of usage
int main() {
    ShapeFactory* factory;

    factory = new CircleFactory();
    Shape* shape1 = factory->createShape();
    shape1->draw(); // Output: Drawing Circle
    delete shape1;
    delete factory;

    factory = new SquareFactory();
    Shape* shape2 = factory->createShape();
    shape2->draw(); // Output: Drawing Square
    delete shape2;
    delete factory;

    return 0;
}
```

### Advantages
**Loose Coupling** – Client doesn’t depend on concrete classes  
**Easy to Extend** – Add new types by creating new factories  
**Cleaner Code** – Object creation logic is in one place  
**Supports Polymorphism** – You can treat all shapes uniformly

### Disadvantages
**More Classes** – One factory per product type increases class count  
**More Complexity** – Not worth using for very simple object creation  
**Subclassing Needed** – Every new type needs a new factory subclass.

## Observer
### How it works
A way to notify objects when something occurs in another object
### Classic usages
Managing events

### Example

```cpp

// Abstract class for the object that needs to receive the notification
class Observer {
public:
    virtual void update(void *observedData) = 0;
    virtual ~Observer() {}
};

// Class to include to be observed
#include <vector>
class Observed {
private:
    std::vector<Observer*> observers;
public:
    void registerObserver(Observer* observer) override {
        observers.push_back(observer);
    }
    void removeObserver(Observer* observer) override {
        observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
    }
    void notifyObservers(void *observedData) override {
        for (Observer* observer : observers) {
            observer->update(observedData);
        }
    }
    virtual ~Subject() {}
};

// Example of a concrete observed object, a weather station
class WeatherStation : public Observed {
public:
    struct DATA {
      float temperature;
      float humidity;
      float pressure;
    } _Data;
    void changeData(float temp, float hum, float pres) {
        _Data.temperature = temp;
        _Data.humidity = hum;
        _Data.pressure = pres;
        notifyObservers(&_Data); // Notify all observers of the new data
    }
};

// Example of observer as displays to display the data of the observed
#include <iostream>
#include <algorithm>
class Display : public Observer {
public:
    void update(void* observedData) override {
      ConcreteObserved::DATA *data = (ConcreteObserved::DATA*)observedData;
      std::cout << "Display: Temperature = " << data.temperature
                << "°C, Humidity = " << data.humidity
                << "%, Pressure = " << data.pressure << " hPa" << std::endl;
    }
};

// Example of usage
int main() {
    WeatherStation station;
    Display display1;
    Display display2;
    station.registerObserver(&display1);
    station.registerObserver(&displa // First update
    station.ChangeData(25.5, 60.0, 1013. // Second update
    station.ChangeData(24.8, 58.0, 1014.5);
    return 0;
}
```

### Advantages
**Loose Coupling** – Subject doesn’t care who the observers are.  
**Flexible** – You can add or remove observers anytime.  
**Reusable** – Observers can be used in different programs.  
**Event-Driven** – Great for live data or GUI events.  
### Disadvantages
**Memory & Performance** – Too many observers can slow things down.  
**Notification Order** – Not guaranteed which observer gets updated first.  
**Changes** – Changes can lead to potentially unnecessary processing.  
**Unwanted Updates** – All observers are notified, even if not all care about every change.