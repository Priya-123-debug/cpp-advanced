# Topic H — Templates, object size and advanced traps

## Q53. Does a virtual function increase the size of an object?

Not necessarily by a fixed amount guaranteed by the C++ standard.

Many common implementations store a hidden virtual-table pointer in each object of a polymorphic class. That often increases object size, but the language does not require a particular vtable implementation or exact byte overhead.

## Q54. Can a constructor be virtual?

No. A constructor cannot be virtual because an object must be constructed before its dynamic dispatch behavior can be used in the usual way.

A destructor can be virtual, and virtual factory functions or factory methods are common patterns for creating derived objects through a base-class interface.

## Q55. What is the diamond problem in multiple inheritance?

```cpp
struct A {
    int x = 10;
};

struct B : A {};
struct C : A {};
struct D : B, C {};
```

`D` contains two separate `A` subobjects, so `d.x` is ambiguous.

Use virtual inheritance when a single shared `A` subobject is desired:

```cpp
struct B : virtual A {};
struct C : virtual A {};
```

Then `D` has one shared virtual `A` base subobject.

## Q56. What is the difference between a pure virtual function and an abstract class?

```cpp
class Shape {
public:
    virtual double area() const = 0;
    virtual ~Shape() = default;
};
```

A class with at least one pure virtual function for which the class has no final overrider is abstract and cannot be instantiated directly.

Derived concrete classes must provide the required overrides.

## Q57. What is SFINAE?

SFINAE means Substitution Failure Is Not An Error. In relevant template-substitution contexts, if substituting template arguments makes a candidate declaration invalid, that candidate can be removed from overload resolution rather than causing an immediate compilation error.

It is used for conditional template overloads. Modern C++ also provides concepts and `requires` clauses, which often express these constraints more clearly.

## Q58. What is the difference between `std::array` and `std::vector`?

- `std::array<T, N>` has a fixed size determined at compile time.
- `std::vector<T>` is dynamically resizable.

Both support contiguous storage and constant-time random access. `vector` may reallocate when its capacity is exceeded; `array` has no dynamic capacity growth.

## Q59. What is copy elision?

Copy elision is when the compiler omits certain copy or move operations, constructing an object directly in its destination.

```cpp
struct A {};

A create() {
    return A{};
}

int main() {
    A a = create();
}
```

In modern C++ (C++17 and later), this example benefits from guaranteed copy elision for the prvalue result. Do not assume every return-by-value operation guarantees elision; named return value optimization (NRVO) is a separate optimization.

## Q60. What is the difference between `std::thread` and `std::async`?

- `std::thread` explicitly starts a thread of execution and requires correct lifetime management, including joining or detaching.
- `std::async` expresses asynchronous execution and returns a `std::future` for retrieving a result or exception. Depending on the launch policy, execution may be asynchronous or deferred.

Neither automatically solves data races; shared mutable state still requires proper synchronization.
