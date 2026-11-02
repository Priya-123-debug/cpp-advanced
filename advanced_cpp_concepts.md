# Advanced C++ Concepts

## Q8. What is RAII in C++?

RAII means **Resource Acquisition Is Initialization**. A resource is acquired as an object is initialized and released automatically when the owning object's lifetime ends.

```cpp
#include <memory>

auto p = std::make_unique<int>(42);
```

When `p` goes out of scope, its managed integer is automatically deleted. RAII applies to memory, file handles, mutex locks, and other resources. Because cleanup happens as objects leave scope, including during exception handling, RAII helps make code exception-safe.

## Q0. What is the difference between compile-time and runtime polymorphism?

- **Compile-time polymorphism** is resolved by the compiler. Examples include function overloading, operator overloading, and templates.
- **Runtime polymorphism** is resolved while the program runs, using virtual function dispatch through a base-class interface.

```cpp
struct Shape {
    virtual double area() const = 0;
    virtual ~Shape() = default;
};

struct Square : Shape {
    double side;

    double area() const override {
        return side * side;
    }
};

double get_area(const Shape& shape) {
    return shape.area(); // Dispatches to the actual object's implementation
}
```

Here, `get_area` works through a `Shape` reference, and the call to `area()` selects the override for the object's actual type at runtime.

## Q12. Why should a polymorphic base class usually have a virtual destructor?

When deleting a derived object through a base pointer, the base destructor must be virtual. Otherwise, `delete` through that pointer has undefined behavior.

```cpp
Base* pointer = new Derived();
delete pointer; // Undefined behavior if Base's destructor is non-virtual
```

Correct design:

```cpp
class Base {
public:
    virtual ~Base() = default;
};
```

With a virtual destructor, deleting through `Base*` invokes the derived destructor first, followed by the base destructor.

## Q13. What is object slicing?

Object slicing happens when a derived object is copied into a base object by value. The derived-only part is discarded, leaving a separate object that contains only the base-class portion.

```cpp
#include <iostream>

struct Base {
    int x = 1;
};

struct Derived : Base {
    int y = 2;
};

int main() {
    Derived d;
    Base b = d;
    std::cout << b.x << '\n'; // Prints 1; b has no y member
}
```

To preserve the derived object's dynamic type, use a suitable base-class reference or pointer instead of copying it into a base object by value. Slicing can also occur when passing or returning base objects by value.

## Q14. What is the difference between a copy constructor and copy assignment?

A copy constructor creates a new object from an existing object. A copy-assignment operator assigns the contents of an existing object into another object that already exists.

```cpp
class A {
public:
    A() = default;
    A(const A& other);
    A& operator=(const A& other);
};

A a;
A b = a; // Copy construction: b is created from a
A c;
c = a;   // Copy assignment: c already exists
```

Common signatures are `A(const A& other)` and `A& operator=(const A& other)`. For resource-owning classes, copy assignment must safely manage the destination's existing resource and handle self-assignment.

## Q15. What is the difference between an overload, an override, and a hidden function?

- **Overloading:** functions share a name but have different parameter lists in the same scope.
- **Overriding:** a derived class provides a matching implementation of a virtual base-class function. Use `override` to have the compiler check this.
- **Hiding:** a declaration in a derived class hides base-class functions with the same name during ordinary name lookup. A same-named non-virtual function does not override the base function.

```cpp
struct Base {
    void f(int) {}
    virtual void g() {}
};

struct Derived : Base {
    using Base::f; // Exposes Base::f(int) alongside Derived::f(double)
    void f(double) {}
    void g() override {}
};
```

Without `using Base::f;`, declaring `Derived::f(double)` hides the base-class overload `Base::f(int)` when looking up `f` on a `Derived` object. The `using` declaration brings the base overload into the derived class's overload set.