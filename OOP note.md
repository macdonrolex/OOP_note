# Design as a Contract

> Alternative to Defensive Programming, pre & post conditions shift 
responsibility to client.

- Software viewed as a contract between designer and client

- Contracts make assumptions explicit. DbC forces you to write these assumptions 
as preconditions, post-conditions, and invariants. When assumptions are written 
down, they’re no longer silent sources of bugs.

- Correct execution achieved by 
	- Client fulfilling interface expectations  => preconditions 
	- Designer implementing software correctly => unit testing


## Class Invariants
**Definition:**  
- Conditions that must always hold true for an object after construction and 
between public method calls.

**Purpose:**  
- Ensure object consistency
- Define valid states of the class

**Examples:**
```c#
// balance >= 0
// size <= capacity
```
---
## Implementation invariants
**Definition:**  
- a condition or property that remains true throughout the execution of a 
program or the lifetime of an object

**Purpose:**  
- Ensuring data integrity , consistency, and correct state

**Examples:**
- Data Consistency: min <= max
- Encapsulated State: A Bank class enforce that balance >= 0 at the end of 
any withdrawal or deposit method.
- Data Structure Integrity: A Doubly Linked List node must always have its 
next->prev pointer pointing back to itself.


---
## Pre, Post-conditions
**Definition:**  
Pre-condition:
- State required for execution
- Must be met before call made

Post-condition:
- Guarantee of state after call processed
- Used to record potential and actual state changes

**Purpose:**  
- pre & post conditions shift responsibility to client who must
	- satisfy preconditions
	- track post conditions for potential state change(s)

**Examples:**
```c#
// Pre-conditions:
// - model must not be null or whitespace
// - initialFuel must be between 0 and MaxFuel
// Post-conditions:
// - FuelLevel == initialFuel
// - Speed == 0
public Car(string model, int initialFuel)
{
	Model = model;
	FuelLevel = initialFuel;
	Speed = 0;
}
```

---

# Coupling vs Cohesion
> Both coupling and cohesion are important factors in determining the maintainability, scalability, and reliability of a software system
## Coupling
**Definition:**  
- How tightly interrelated are two or more objects?
- Degree of dependence

**Our Goal:**
- We want lower Coupling
- We want each function only have necessary dependency relationships with other. 

## Cohesion
**Definition:**  
-  The degree to which elements within a module work together to fulfill a 
single, well-defined purpose.
- Singularity of purpose

**Our Goal:**
- We want higher Cohesion. 
- We hope each function do only one job
- We hope all members inside a class or function match their purpose.

# Object-Oriented programming
> Fast development, easy maintenance, code reusing, data security, flexible

1. Program is divided into classes and objects.
2. The emphasis on data.
3. Strong modeling to real world problems.
4. Easy to maintain the project even if it is too complex.
5. Provides strong data security.
6. Unit of program is class.
7. Provide support to new data type

## Key feature
1. Substitutability
2. Dynamic behavior (function selection determined at run-time)
3. Extensibility 
 

# OOP Relationships
> Briefly describe what "Design as a Contract" means and why it matters.
---
## Containment (Holds a)
> “holds-a” means uses but does not own.

A containment relationship is flexible because cardinality, ownership, and 
association may vary
```csharp
/*
The Car holds a pointer to a Driver.
The Driver could be created and destroyed by someone else.
The Car doesn’t own the driver.
The relationship is loose.
*/
class Driver {};
 
class Car 
{
private:
    Driver* driver;   

public:
    Car(Driver* d) : driver(d) {}
};
```
## Composition (Has a)
>“has-a” means owns and controls lifetime.

In a has-a relation, the subObject provides key functionality and affects the state of the composing object.
With **fixed cardinality**, the number of sub-objects may **be defined by design or set upon object construction but it does not vary within an object’s lifetime**.
```csharp
/*
Car has an Engine. 
The engine is constructed when the car is constructed and destroyed when the car is destroyed. 
Lifetime is tightly coupled.
*/
class Engine 
{
public:
    void start() {}
};

class Car 
{
private:
    Engine engine;

public:
    void start() 
    {
        engine.start();
    }
};
```
## Inheritance (Is a)
```csharp
/*
Dog inherits from Animal.
A Dog is an Animal because it can do everything an Animal does.
Any function expecting an Animal can accept a Dog (polymorphism).
*/
class Animal 
{
public:
    void eat() 
    {
        std::cout << "Animal eating...\n";
    }
};
 
// Can call eat from a Dog object.
class Dog : public Animal 
{    
public:
    void bark() 
    {
        std::cout << "Dog barking...\n";
    }
};
```
## Dependency injection
> An object doesn't create what it need. Instead, it depends on external source.

``` cpp
// No DI
class Car
{
    Engine engine;   // 自己決定用哪一種 Engine
public:
    void start()
    {
        engine.start();
    }
};
// Car 直接依賴具體的 Engine (tight coupling)
// Car：
//	決定「用什麼引擎」
//	負責「建立與管理引擎」

// DI
class Car
{
    Engine* engine;
public:
    Car(Engine* e) : engine(e) {}
};
// Car 只使用 Engine，不知道：
// Engine 怎麼來
// 是哪一種 Engine
```

# Compile time vs Run time
## Compile time
> The compiler must know exact types, layouts, and stack sizes.
## Run time
> The program is executing, reacting to input, configuration, files, user actions, etc.

Anything that depends on:
- user input
- file contents
- network data
- configuration files
- environment variables

is typically a run-time decision.

# Big 5 (For c++)
```cpp
#include <iostream>
using namespace std;

class IntBox
{
private;
	int* data;
public;
	// Constructor
	IntBox(int value)
	{
		data = new int(value);
	}
	// Destructor
	~IntBox()
	{
		delete data;
	}
	// Copy Constructor
	IntBox(const IntBox& other)
	{
		data = new int(*other.data);
	}
	// Copy Assignment
	IntBox& operator=(const IntBox& other)
	{
		// No self-assignment
		if (this != other)
		{
			delete data;
			data = new int(*other.data);
		}
		return *this;
	}
	// Move Constructor
	IntBox(IntBox&& other) noexcept
		: data(other.data)
	{
		other.data = nullptr;
	}
	// Move Assignment
	IntBox& operator=(IntBox&& other) noexcept
	{
		if (this != other)
		{
			delete data;
			data = other.data;

			other.data = nullptr;
		}
		return *this;
	}
};
```
## Copy assignment operator
```cpp
// copy-and-swap idiom
class Buffer
{
public:
    Buffer(const Buffer& other);   // copy ctor
    ~Buffer();

    Buffer& operator=(Buffer other)   // 注意：by value
	// a = b時會先呼叫copy constructor來建立一個暫時的other
    {
        swap(other); //
        return *this;
    }

    void swap(Buffer& other) noexcept
    {
        std::swap(data, other.data);
        std::swap(size, other.size);
    }
};
// 1. other 是 copy constructed，如果 copy 失敗 → assignment 根本不開始
// 2. swap(other), *this 與 other 交換資源
// 3. other 離開 scope, destructor 自動清掉舊的資源
```
```cpp
// 另一種做法
Buffer& operator=(const Buffer& other)
{
    if (this == &other)
        return *this;

    // 先配置新資源
    char* newData = new char[other.size];

    // 成功後再釋放舊資源
    delete[] data;

    data = newData;
    size = other.size;

    return *this;
}
```
## Move constructor
> Move constructors are used to transfer resources(like memory or file handles)
from one object to another without making a copy, which make program faster and 
more efficient. They're especially useful when working with temporary objects or large data.
- Transfer without copying.
- Fast and efficient.
- Useful on temporary objects or large data.
```cpp
#include <cstring>

class Buffer
{
private:
    char* data;
    size_t size;

public:
    Buffer(size_t s)
        : size(s)
    {
        data = new char[size];
    }

    // copy constructor
    Buffer(const Buffer& other)
        : size(other.size)
    {
        data = new char[size];
        std::memcpy(data, other.data, size);
    }
    // ⭐ move constructor
    //Buffer&& == rvalue reference, other是快消失的物件
    Buffer(Buffer&& other) noexcept
        : data(other.data), size(other.size)
    {
        other.data = nullptr;   // 關鍵！
        other.size = 0;
    }

    ~Buffer()
    {
        delete[] data;
    }

    // temp 是 local object（lvalue）
    // 回傳時會建立一個「回傳用的暫時物件」
    //
    // 1. 建立 temp（stack object）
    //    temp.data -> heap A
    //
    // 2. 建立 __return_value（stack / hidden object）
    //    __return_value.data -> heap B
    //    （copy constructor, Buffer __return_value = temp;）
    //
    // 3. 離開 makeBuffer scope
    //    呼叫 temp.~Buffer()
    //    heap A 被釋放
    //
    // 4. caller 用 __return_value 初始化 b
    //    （可能 move 或直接消除, Buffer b = makeBuffer();）
    // 如果有 move constructor，建立 __return_value 時會使用 move
    // __return_value 會接手 temp 所擁有的 heap 資源（data 指向的記憶體）
    // temp 會被留在一個可安全析構的空狀態
    // 因此不會發生 heap 資源的複製，也不會造成資源浪費
    Buffer makeBuffer()
    {
        Buffer temp(1024);
        return temp;   
    }// call destructor
};
```

# Dependency Injection
> Dependency Injection is a design technique where an object receives the objects it depends on from the outside, instead of creating them itself.

Dependency Injection is a design technique in which a class receives its dependencies from external sources rather than creating them internally. This promotes loose coupling, flexibility, and testability. Dependencies injected through a constructor are typically marked readonly to ensure they are not changed after object creation and to preserve class invariants.
```c#
public interface IEmailService
{
    void Send(string msg);
}

public class EmailService : IEmailService
{
    public void Send(string msg) { }
}

public class OrderProcessor
{
	//_email should only be assigned once.
    private readonly IEmailService _email;

	// Constructor injection
    public OrderProcessor(IEmailService email)
    {
        _email = email;
    }

    public void Process()
    {
        _email.Send("Order placed");
    }
}
```

# Interface (For c#, java)
> An interface specifies what operations a class must provide, but not how they are implemented.

Example:
```c#
// IVehicle.cs
public interface IVehicle
{
    void Start();
    void Stop();
}
```
```c#
// Vehicles.cs
public class Car : IVehicle
{
    public void Start()
    {
        Console.WriteLine("Car engine started.");
    }

    public void Stop()
    {
        Console.WriteLine("Car engine stopped.");
    }
}

public class Motorcycle : IVehicle
{
    public void Start()
    {
        Console.WriteLine("Motorcycle engine started.");
    }

    public void Stop()
    {
        Console.WriteLine("Motorcycle engine stopped.");
    }
}
```
```c# 
// Garage.cs
public class Garage
{
    public void TestVehicle(IVehicle vehicle)
    {
        vehicle.Start();
        vehicle.Stop();
    }
}
```
```c#
// Program.cs
class Program
{
    static void Main()
    {
        Garage garage = new Garage();

        IVehicle car = new Car();
        IVehicle motorcycle = new Motorcycle();

        garage.TestVehicle(car);
        garage.TestVehicle(motorcycle);
    }
}
```

C++ does not have a separate interface keyword, but it supports the interface concept using virtual functions
```cpp
class Vehicle 
{
public:
    virtual void Start() = 0; // pure virtual function
    virtual ~Vehicle() = default; // virtual destructor
};

class Car : public Vehicle
{
public:
	void Start() override
	{
		// Car-specific logic
	}
	~Car() override
	{
		// clean up
	}
}
```


# 4 Pillars
## Encapsulation
> Encapsulation is the bundling of data and the methods that operate on that data into a single unit, while also restricting direct access to some of the object's internal details.

**Purpose:**
- **protecting internal data** Encapsulation prevents external code from directly modifying an object’s internal state in unsafe or inconsistent ways. Wikipedia notes that it limits direct access to an object's components
- **hiding implementation details**  Clients interact with a stable interface without needing to know how things work inside. This allows developers to change internals later without breaking other code

**How to achieve it?**
- **Declare data members as private:** This restricts direct access to the data from outside the class.
- **Provide public methods (getters and setters):** These methods act as intermediaries, allowing controlled access to the data. Getters retrieve data values, while setters modify them.
- **Implement data validation:** Within the setter methods, you can validate data before assigning it to the object’s properties, ensuring data integrity.
## Inheritance
> Inheritance is the mechanism by which one class (the subclass) acquires the properties and behaviors of another class (the superclass), allowing code reuse and the expression of “is-a” relationships.

**Purpose:**
- **code reuse** Inheritance allows common data and behavior to be defined once in a base class and reused by derived classes, reducing duplication.
- **modeling hierarchical relationships** It represents natural “is-a” relationships in the problem domain, such as a `Car` being a type of `Vehicle`.
- **supporting polymorphism** Inheritance enables objects of different subclasses to be treated uniformly through a common base type.

**How to achieve it?**
- **Define a base (super) class:** Put shared data and behavior in the base class.
- **Create derived (sub) classes:** Use inheritance to extend or specialize the base class.
- **Override virtual methods:** Subclasses can provide specialized behavior while keeping the same interface.
- **Use access control (`protected`, `public`):** Control which members are accessible to subclasses.

## Polymorphism
> Polymorphism is the ability to treat objects of different concrete types uniformly through a common interface, while allowing each object to exhibit its own behavior.  
This principle is about objects taking on different forms or behaviors depending on the context.

**Purpose:**
- **flexibility and extensibility** New types can be added without modifying existing client code.
- **reducing conditional logic** Polymorphism replaces complex `if`/`switch` statements with dynamic method dispatch.
- **supporting heterogeneous collections** Different object types can be stored and processed together through a common base type.

**How to achieve it?**
- **Define a common interface or base class:** Declare shared operations.
- **Use method overriding:** Subclasses provide their own implementations of virtual methods.
- **Enable dynamic binding:** Ensure methods are resolved at run time based on the object’s actual type.
- **Access objects through base types:** Use base class references or pointers in client code.

## Abstraction
> Abstraction is the process of focusing on the essential features of an object while hiding unnecessary implementation details, exposing only what the client needs to use.

**Purpose:**
- **reducing complexity** Abstraction simplifies interaction by hiding low-level details.
- **improving maintainability** Changes to implementation details do not affect client code as long as the abstraction remains stable.
- **separating what from how** Clients depend on *what* an object does, not *how* it does it.

**How to achieve it?**
- **Use interfaces or abstract classes:** Define required behavior without providing full implementations.
- **Expose minimal public APIs:** Publish only the operations needed by clients.
- **Hide concrete implementations:** Keep implementation classes and details private or internal.
- **Program to abstractions:** Depend on interfaces rather than concrete classes.

# Operator Overloading
>Redefine how your operators work for your own classes.  
Syntactic sugar  
A way to express **behavior**, **abstraction**, and **relationships** in a way that feels natural and aligns with the principles of OOP


**The Rules:**
- Define new meaning for existing operator
- Cannot change compiler: Compiler must be able to process syntax as before
- CANNOT change parity of operation: "Parity" refers to the number of operand an
operator takes. For instance, "+" needs 2 operands, and "!" needs 1.
- CANNOT define functions for new symbols

**When to use it?**
- When it improves readability and expressiveness
	- For instance `c = a.add(b)` versus  `c = a + b`
- When your class behaves like a built-in-type
	- Smart Pointers
		- Overloading `->` and `*` just like raw pointer
	- Strings 
		- `string fullName = firstName.Append(" ").Append(lastName)`;
	- Big Integers
		- We might need our own BigInt class to store a HUGE integer.
		- `BigInt total = score1.Multiply(score2);` -> `BigInt total = score1 * score2;`
- When it supports OOP
	- Encapsulation & Abstraction
		- Hides messy logic behind a clean symbol(`+`, `-`...).
	- Polymorphism
		- Allows functions or operators to act differently based on the specific data type of their arguments
		```cpp
		// 3 different types of +
		Point operator+(const Point& other);
		Matrix operator+(const Matrix& other);
		String operator+(const String& other);
		```
	- LSP
		- Subclasses should preserve expected behavior
		- EX: Superclass' `a + b` returns a + b + 100
		- All subclasses should preserve this `+` overload

**Binary Operations and the `this` Pointer**
- binary operation means an operator that needs 2 operands(+, -, *, /...)
- C++ (Class method)
	- A + B -> A.operator+(B)
	- The left side(A) invokes the method 
	- The method only needs B as parameter because A was passed as the hidden
	`this` pointer.
```cpp
class Point {
public:
    int x, y;

    Point(int x, int y) : x(x), y(y) {}

    // Binary operator as a member method.
    // The left operand is 'this', so we only pass the right operand ('other').
    Point operator+(const Point& other) {
        // We can access our own x and y, plus the other's x and y
        return Point(this->x + other.x, this->y + other.y); 
    }
};

// Usage:
// Point p1(1, 2);
// Point p2(3, 4);
// Point p3 = p1 + p2; // Translates to: p1.operator+(p2)
```
- C++ (Global/Friend method)
	- If you define the overload outside the class, it doesn't belong to any specific object instance.
	- Because there is no calling object, there is NO `this` pointer.
	- `A + B` translates to a standalone global function call: `operator+(A, B)`
```cpp
class Point {
public:
    int x, y;

    Point(int x, int y) : x(x), y(y) {}

    // Declare the global function as a friend if it needs private access
    // (Though our x and y are public here, it's good practice to know)
    friend Point operator+(const Point& left, const Point& right);
};

// Binary operator as a global method.
// No 'this' pointer, so we must pass BOTH operands.
Point operator+(const Point& left, const Point& right) {
    return Point(left.x + right.x, left.y + right.y);
}

// Usage:
// Point p1(1, 2);
// Point p2(3, 4);
// Point p3 = p1 + p2; // Translates to: operator+(p1, p2)
```
- C# (static method)
	- C# enforces a much stricter rule for operator overloading to keep things predictable.
	- In C#, all operator overloads must be declared as `public static`.
	- Just like a C++ global function, a binary operator in C# always requires you to explicitly pass both operands as parameters (e.g., `public static Point operator +(Point a, Point b)`).
```c#
public class Point 
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) 
    {
        X = x;
        Y = y;
    }

    // Binary operator as a static method.
    // Must be public and static. Must take BOTH operands.
    public static Point operator +(Point left, Point right) 
    {
        return new Point(left.X + right.X, left.Y + right.Y);
    }
}

// Usage:
// Point p1 = new Point(1, 2);
// Point p2 = new Point(3, 4);
// Point p3 = p1 + p2; // Translates to: Point.op_Addition(p1, p2) under the hood
```

# Quiz
## Week1 Quiz
### 1. Look at the code below. Pick the better selection:
``` cpp
class Engine {
public:
    void start() {}
};

class Car {
private:
    Engine engine;   

public:
    void start() {
        engine.start();
    }
};
```
**Ans:**
- Car has an Engine.
- The engine is constructed when the car is constructed and destroyed when the 
car is destroyed.
- Lifetime is tightly coupled.

### 2. Look at the code below. Pick the better selection:
```cpp
class Driver {};
 

class Car {
private:
    Driver* driver;   
 

public:
    Car(Driver* d) : driver(d) {}
};
```
**Ans:**
- The Car holds a pointer to a Driver.
- The driver could be created and destroyed by someone else.
- The car doesn’t own the driver.
- The relationship is loose.

### 3. Look at the code below. Pick the better selection:
```cpp
class Animal {
public:
    void eat() {
        std::cout << "Animal eating...\n";
    }
};
 

class Dog : public Animal {    
public:
    void bark() {
        std::cout << "Dog barking...\n";
    }
};
```
**Ans:**
- Dog inherits from Animal.
- A Dog is an Animal because it can do everything an Animal does.
- Any function expecting an Animal can accept a Dog (polymorphism).

### 4. A class invariant is a guarantee about object state that: (Pick the one that does not belong)
**Ans:**
-  *is always true whenever a client interacts with an object through its public methods*
	- A class invariant is not required to hold during the execution of a public method.
- is true after construction
- is preserved by all public operations 
- keeps the object logically consistent.

### 5. Would a client of a class know about an implementation invariant? Explain
**Ans:**
- Probably not, as client does not know the implementation details.

### 6. Give an example of a Composition Employee class that would be considered a Has a. Please produce the data members only
**Ans:**
```c#
public class Employee
{
    private string _name;
    private int _id;
 

    // Composition: Employee HAS An Address
    private Address _address;
 

    // Composition: Employee HAS A Department
    private Department _department;
 

    // Composition: Employee HAS A ContactInfo
    private ContactInfo _contactInfo;
}
```

### 7. What is Cardinality?
**Ans:**
- Cardinality describes how many instances of one class can relate to another (1, 0..1, *, many, etc.)
- Cardinality is the numerical relationship between instances of different classes

### 8. Describe and give an example of a subclass
**Ans:**
- A subclass is a class that inherits variables and methods from another class. An example could be Dog in which it might inherit from Animal. 
```cpp
class Animal {
public:
    void eat() {
        std::cout << "Animal eating...\n";
    }
};
 

class Dog : public Animal {    
public:
    void bark() {
        std::cout << "Dog barking...\n";
    }
};
```

### 9. A method is a  _______  describing how to perform one of an objects operations
**Ans:**
- function

## Week4 Quiz
### 1. When is heap memory used (from Chapter 3)
**Ans:**
- Heap memory is used when the concrete type of an object cannot be determined at compile time and must be selected dynamically at runtime, typically through polymorphism.
- When objects must live out of the scope that created them. If it is created on the stack, it will not exit after the function is over.
- When objects are too big for the stack.
- Use the heap when your object's lifetime, size or type(polymorphism) must be decided at runtime. Use the stack when the object is simple, local, and predictable.
	- Stack allocation requires the compiler to know the exact type, size, and layout.
	```cpp
	// 1. This is fine
	Car c;          // type known at compile time
	Vehicle v;     // type known at compile time

	// 2. This confuse the compiler:“How much stack space should I reserve?”
	// decided at runtime
	if (userChoosesCar)
		??? = Car();
	else
		??? = Bus();

	// 3. Pointers and heap allocation solve this
	Vehicle* v; // fixed size know at compile time
	if (userChoosesCar)
    	v = new Car();
	else
    	v = new Bus();
	// The stack only holds Vehicle*
	// The heap holds the actual Car or Bus
	```

### 2. What is the primary difference between shadow and deep copying?
**Ans:**
- A shallow copy copies the top-level object only, not the data it points to.
	- Pointers are copy as pointers, not as the data they reference.
	- Both objects end up point to the sam underlying memory
	- Fast, but dangerous if the object owns heap memory

### 3. Why move semantics is useful in class design
**Ans:**
- It improves performance by reducing unnecessary deep copies of resources

### Describe rule of 5, and show some example.
**Ans:**
- TODO

## Week5 Quiz

### 1. Why is the relationship of vehicle to car known as a "IS A " relationship as opposed to a "HAS A" . Explain in at least one paragraph.
**Ans:**
- The Car -> Vehicle relationship is considered as "IS-A" relationship because **Car is a specialized type of Vehicle**, not something that a Vehicle owns or contains. A Car instance can do what a Vehicle can do.

### 2. Vehicle would be considered the base class and Car would be the superclass?
**Ans:**
- No, Vehicle would be a base class, but Car would be a "subclass".Car extents Vehicle.

### 3. Create the C++ class for Car. You only have to do the header. The vehicle class is given for you, along with an UML diagram showing the relationship.
Car

fields: 
- numberOfDoors_: int

functions:
- Car(make: string, model: string, year: int, numberOfDoors: int)
- GetNumberOfDoors(): int
- Start(): void
```cpp
#include <string>

class Vehicle {
public:
    Vehicle(const std::string& make, const std::string& model, int year);
    virtual ~Vehicle() = default;

    // Getters
    std::string GetMake() const;
    std::string GetModel() const;
    int GetYear() const;

    // Virtual behavior
    virtual void Start() const;
    virtual void Stop() const;

protected:
    std::string make_;
    std::string model_;
    int year_;
};
```
**Ans:**
```cpp
#include <string>
#include "Vehicle"

using namespace std;
class Car : public Vehicle
{
public:
	// make suer user "const string&" instead of "string".
	Car(const string& make, const string& model, int year, int numberOfDoors);
	int GetNumberOfDoors();
	void Start() const override; // Remember to override

private:
	int numberOfDoors_;
}
```

### 4. What is a "bloated" class> Please give a good answer. How do you address a bloated class?
**Ans:**
- A bloated class is a class that has too many responsibilities, fields, and methods, often handling unrelated concerns. Such a class is considered low cohesion. It becomes difficult to understand and maintain. <br>
We should refactor a bloated class. Decompose it into smaller, more cohesive classes.

### 5. Polymorphism only works at compile time
**Ans:**
- No, it only works at run time.

### 6. Polymorphism allows a child class to inherit fields and methods from a parent class.
**Ans:**
- No, that's inheritance. Polymorphism allows objects of different types to be treated as instances of a common super class.

### 7. Explain Overloading with respect to Polymorphism. Use example if you are able
**Ans:**
- Function overloading is a form of compile-time polymorphism in which multiple functions share the same name and have different signatures, where a signature is defined by the function name and the number, types, and order of its parameters.
``` cpp
#include <iostream>
using namespace std;

class Math {
public:
    int Add(int a, int b) {
        return a + b;
    }

    double Add(double a, double b) {
        return a + b;
    }

    int Add(int a, int b, int c) {
        return a + b + c;
    }
};

int main() {
    Math m;

    cout << m.Add(2, 3) << endl;        // calls Add(int, int)
    cout << m.Add(2.5, 3.1) << endl;    // calls Add(double, double)
    cout << m.Add(1, 2, 3) << endl;     // calls Add(int, int, int)
}
```

## Week7 Quiz

### 1. 
>Suppose you just created a special class for employees. You had functions such as AddEmployee, DeleteEmployee. Now suppose you wanted to add some logging functionality. You have a another class called Logger that would enable you to print diagnostic messages to a file or console.  
Explain in a paragraph or two, whether you would use inheritance between the Employee and Logger, or Composition.  

**Ans:**


### 2.
>Lets suppose you have a class called GUIButton that had some core functionality. Lets say you were asked to create a specialized version of a GUIButton called ImageButton. Would you use Inheritance or composition? Why


**Ans:**

### 3.
>Tell me what will happen on the console when this is called:

```cpp
int main() {
    std::vector<std::unique_ptr<Animal>> animals;

    animals.push_back(std::make_unique<Dog>());
    animals.push_back(std::make_unique<Duck>());

    for (const auto& a : animals) {
        a->Speak();   // Polymorphic call
    }

    return 0;
}
```
**Ans:**

## Week8 Quiz

### 1. Give me a C++ example of operator overloading in c++ and explain its value
**Ans:**
```cpp
// professor
#include <iostream>

class Counter {
private:
    int value;

public:
    Counter(int v) : value(v) {}

    // Overload the + operator
    Counter operator+(const Counter& other) const {
        return Counter(value + other.value);
    }

    int GetValue() const { return value; }
};

int main() {
    Counter a(5);
    Counter b(3);

    Counter c = a + b;   // uses overloaded +

    std::cout << c.GetValue() << "\n";  // prints 8
    return 0;
}
```
```cpp
//mine
class Triangle{

private:
	int base;
	int height;   

public:
	Triangle(int b, int h): base(b), height(h) {}
	Triangle operator+(const Triangle& other) const {
		return Triangle (height + other.height, base + other.base);
	}
}
/*
Triangle operator+(const Triangle& other) const override the basic + operator. Now if we perform Triangle1 + Triangle2, it will return a new Triangle with sums of heights and bases in the two Triangles.
*/
```

## Week 9 Quiz
### 1. What is the primary purpose of an interface in C#?
- To define a contract that classes must implement 
- To Automatically generate method bodies for subclasses 
- To store shared fields for derived classes 
- To provide multiple Inheritance of implementation 

**Ans:**
The correct answer is: To define a contract that classes must implement.

In C#, an interface acts as a blueprint or a "contract." When a class implements an interface, it is essentially making a promise that it will provide an implementation for all the methods, properties, or events defined within that interface.

Why the other options are incorrect:

- To automatically generate method bodies: Interfaces (traditionally) do not provide the implementation; they only define the signature. The class itself is responsible for writing the code inside the method.

- To store shared fields: Interfaces cannot contain instance fields (variables). If you need to share fields between classes, you would typically use an Abstract Class instead.

- To provide multiple inheritance of implementation: C# does not support multiple inheritance of classes (inheriting code from more than one parent). However, a class can implement multiple interfaces, allowing it to follow multiple "contracts" at once.

### 2. Look at the code below. It uses a design pattern we talked about last week. What is it and what does this pattern do? What does this code snippet do?
```c#
using System;

public class Notifier
{
    // xxx  defines the signature of the behavior being injected
    public delegate void SendMethod(string message);

    private readonly SendMethod _send;

    public Notifier(SendMethod sendMethod)
    {
        _send = sendMethod;
    }

    public void Notify(string message)
    {
        Console.WriteLine("Preparing notification...");
        _send(message); // Delegation happens here
        Console.WriteLine("Notification complete.");
    }
}

// Concrete behaviors
public class NotificationMethods
{
    public static void SendEmail(string msg)
    {
        Console.WriteLine($"Sending EMAIL: {msg}");
    }

    public static void SendSms(string msg)
    {
        Console.WriteLine($"Sending SMS: {msg}");
    }
}

public class Program
{
    public static void Main()
    {
        // Inject email behavior
        var emailNotifier = new Notifier(NotificationMethods.SendEmail);
        emailNotifier.Notify("Project update available.");

        // Inject SMS behavior
        var smsNotifier = new Notifier(NotificationMethods.SendSms);
        smsNotifier.Notify("Server is down!");
    }
}
```
**Ans:**
It is a delegate pattern, as it is delegate the task to another object.  
It essentially is passing in a function pointer to which it will go to that function. 


### 3. Try to explain what these two assertions mean:
- Inheritance is like building with a tall Tree
- Composition is like building with Lego’s
**Ans:**
It is a metaphor:

Tall Tree - 

A class extends another class, which extends another, forming a
single upward path
.
Each level depends on the one above it.
Lego - 

You build objects by combining smaller objects.

>my

Inheritance is like building with a tall Tree because there is a hierarchical relationship between each classes. The higher level classes provide basic functions, and lower level classes provide additional.


Composition is like building with Lego’s because the relationship between each classes is not limited. It is not necessary for a class to provide some fields or functions that other classes have.