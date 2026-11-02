# Understanding `const` Pointers

In a pointer declaration, `const` applies to the type immediately before it. This tells you whether the pointed-to value, the pointer itself, or both are constant.

```cpp
const int* p;
int* const p2 = nullptr;
const int* const p3 = nullptr;
```

## `const int* p`

`p` is a changeable pointer to a `const int` (also written `int const*`). You can make `p` point somewhere else, but you cannot change an integer through `p`.

```cpp
int first = 10;
int second = 20;
const int* p = &first;

p = &second; // OK: the pointer can be reassigned
// *p = 30;   // Error: cannot modify the int through p
```

As originally written, `const int* p;` does not initialize `p`. If it is a local variable, its value is indeterminate; initialize it before reading or dereferencing it.

## `int* const p2 = nullptr`

`p2` is a constant pointer to a changeable `int`. The pointer cannot be reassigned after initialization. The declaration initializes it to `nullptr`, however, so it points to no integer and must not be dereferenced.

```cpp
int number = 10;
int* const fixed_pointer = &number;

*fixed_pointer = 20; // OK: the pointed-to int is changeable
// fixed_pointer = nullptr; // Error: the pointer itself is const
```

## `const int* const p3 = nullptr`

`p3` is a constant pointer to a `const int`: neither the pointer nor the pointed-to value can be changed through it. Here it is initialized to `nullptr`, so it points to no object and cannot be dereferenced.

```cpp
int number = 10;
const int* const fixed_observer = &number;

// *fixed_observer = 20; // Error: cannot modify the int through this pointer
// fixed_observer = &number; // Error: the pointer itself is const
```

## Quick way to read them

- `const int*`: pointer can change; pointed-to `int` cannot be changed through this pointer.
- `int* const`: pointer cannot change; pointed-to `int` can be changed.
- `const int* const`: neither pointer nor pointed-to `int` can be changed through this pointer.

These rules describe what operations the pointer allows. They do not make an object immutable if another non-const pointer can still modify it.