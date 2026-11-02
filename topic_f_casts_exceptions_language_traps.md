# Topic F — Type casting, exceptions and language traps

## Q41. What is the difference between the four C++ casts?

| Cast | Purpose |
| --- | --- |
| `static_cast` | Well-defined compile-time conversions and certain related type conversions |
| `dynamic_cast` | Runtime-checked casts within polymorphic class hierarchies |
| `const_cast` | Adds or removes `const`/`volatile` qualifiers |
| `reinterpret_cast` | Low-level reinterpretation between certain types; use cautiously |

Example:

```cpp
Base* p = new Derived();

Derived* d = dynamic_cast<Derived*>(p);
```

For a pointer cast that fails at runtime, `dynamic_cast` returns `nullptr`. For a failed reference cast, it throws `std::bad_cast`.

The base class must be polymorphic for these runtime-checked downcasts.

## Q42. What is exception safety in C++?

Exception safety describes what guarantees a function provides when an exception is thrown.

- No-throw guarantee: the operation will not throw.
- Strong guarantee: failure leaves the program state unchanged.
- Basic guarantee: invariants remain valid and resources are not leaked, but state may change.

RAII and standard-library resource-management types are key tools for exception-safe programming.

## Q43. What happens if a destructor throws an exception during stack unwinding?

If another exception is already being propagated and a destructor throws a second exception that escapes, the program calls `std::terminate()`.

Interview answer: Destructors should generally not let exceptions escape. Resource cleanup should be designed to be non-throwing.

## Q44. What is the difference between `volatile` and `atomic`?

- `volatile` affects how accesses are treated by the implementation; it does not make an operation atomic or provide inter-thread synchronization.
- `std::atomic<T>` provides atomic operations and can provide memory-ordering guarantees for concurrent code.

Use atomics, mutexes, or other synchronization mechanisms for thread-safety—not `volatile` alone.

## Q45. What is undefined behavior in C++?

Undefined behavior means the C++ standard imposes no requirements on the program's behavior.

Examples include:

- Signed integer overflow.
- Dereferencing an invalid or dangling pointer.
- Accessing an array out of bounds.
- Reading an uninitialized automatic variable in a way that uses an indeterminate value.
- Unsequenced conflicting modifications such as `i++ + i++`.

OA rule: If the code invokes undefined behavior, do not assume it has one predictable output.

## Q46. What is the difference between a data race and a race condition?

- A data race occurs when threads access the same memory location concurrently, at least one access is a write, and the accesses are not properly synchronized. In C++, this causes undefined behavior.
- A race condition is a broader situation in which correctness depends on the timing or ordering of operations.

A mutex or appropriate atomic operations can eliminate specific data races, although race conditions may still exist at the logical level.

## Q47. What are `static`, `extern`, and `inline` used for?

- `static` has different meanings depending on context: static storage duration for a local variable, internal linkage for a namespace-scope variable/function, and shared class-level members for a class.
- `extern` commonly declares an entity defined elsewhere or specifies external linkage.
- `inline` permits compatible definitions to appear in multiple translation units under the One Definition Rule; it is not simply a command forcing the compiler to inline a function call.

These keywords are common sources of output and compilation questions.
