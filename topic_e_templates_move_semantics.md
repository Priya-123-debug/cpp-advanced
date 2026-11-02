# Topic E — Templates, move semantics and advanced language features

## Q33. What is a template?

A template lets you write generic functions or classes that work with multiple types.

```cpp
template <typename T>
T maximum(T a, T b) {
    return a > b ? a : b;
}

cout << maximum(10, 20);
cout << maximum(2.5, 1.5);
```

Output:

```cpp
201.5
```

The first call returns an `int`; the second returns a `double`. Template argument deduction determines `T` from the arguments.

## Q34. What is the difference between a function template and function overloading?

```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

int add(int a, int b) {
    return a + b + 100;
}

cout << add(2, 3);
```

Output: `105`

The non-template overload is preferred over the function-template specialization when both are otherwise equally good matches.

## Q35. What is an lvalue and an rvalue?

At a practical level for C++ interviews:

- An lvalue identifies an object with identity, such as a named variable.
- An rvalue commonly refers to a temporary value or object that can be used to initialize or move into another object.

```cpp
int x = 10;
int& a = x;    // Lvalue reference
int&& b = 20;  // Rvalue reference
```

A non-const lvalue reference generally cannot bind directly to an rvalue. An rvalue reference can bind to an appropriate temporary.

## Q36. What is move semantics?

Move semantics allow a type to transfer ownership of a resource instead of making an expensive deep copy.

```cpp
#include <iostream>
#include <string>
#include <utility>
using namespace std;

int main() {
    string a = "NVIDIA";
    string b = move(a);

    cout << b;
}
```

Guaranteed output: `NVIDIA`

After moving, `a` remains valid but its value is unspecified. Do not assume it must be empty.

`std::move` itself does not move anything; it casts its argument to an rvalue reference so that a suitable move operation can be selected.

## Q37. What is the difference between `unique_ptr`, `shared_ptr`, and `weak_ptr`?

- `unique_ptr`: exclusive ownership; cannot be copied, but can be moved.
- `shared_ptr`: shared ownership managed through a reference count.
- `weak_ptr`: non-owning reference to an object managed by `shared_ptr`; does not keep the object alive.

Use `unique_ptr` by default for exclusive dynamic ownership. Use `shared_ptr` only when shared lifetime is actually required.

## Q38. What is `constexpr`?

`constexpr` enables a variable or function to participate in compile-time evaluation when the relevant conditions are met.

```cpp
constexpr int square(int x) {
    return x * x;
}

constexpr int n = square(5);
```

`n` is `25` and is computed at compile time. A `constexpr` function can also be called at runtime with non-constant arguments.

## Q39. What is the difference between `struct` and `class` in C++?

Their main language-level differences are defaults:

- `struct`: members and base-class inheritance are public by default.
- `class`: members and base-class inheritance are private by default.

Both support constructors, destructors, inheritance, polymorphism, and member functions.

## Q40. What is a friend function?

A `friend` function is not a member of the class, but it can access that class's private and protected members.

```cpp
class A {
    int x = 10;

    friend void show(const A& a);
};

void show(const A& a) {
    cout << a.x;
}
```

Output: `10`.

Friendship is not inherited, transitive, or automatically reciprocal.
