# Pointers and Memory Safety in Advanced C++

Pointers store the address of an object. The important question is not only whether a pointer has an address, but whether the object at that address is still alive and who is responsible for its lifetime.

## Wild pointers

A wild pointer is an uninitialized pointer. Its value is indeterminate, so dereferencing it or using it as though it referred to an object causes undefined behavior.

```cpp
int* value;       // Wild: not initialized
// *value = 42;   // Undefined behavior

int* empty = nullptr; // Initialized, but points to no object
if (empty != nullptr) {
	*empty = 42;
}
```

Initialize pointers when they are declared. Use `nullptr` to represent “points to nothing”; do not use an uninitialized pointer as a substitute.

## Dangling pointers

A dangling pointer once referred to a valid object, but that object's lifetime has ended. The pointer value may still look plausible, but accessing the former object is undefined behavior.

```cpp
int* make_dangling() {
	int local = 7;
	return &local; // The local object is destroyed when the function returns
}

// int* pointer = make_dangling();
// int value = *pointer; // Undefined behavior: pointer is dangling
```

Dangling pointers can also result from deleting an object without clearing aliases, or from invalidation by a container operation such as `std::vector` reallocation.

```cpp
int* pointer = new int{7};
delete pointer;
pointer = nullptr; // This clears this pointer, not any other aliases
```

Setting one pointer to `nullptr` does not repair other pointers to the deleted object. Prefer automatic storage for local values and standard-library containers for collections, so lifetime management is handled by scope and library abstractions.

## Memory leaks

A memory leak occurs when dynamically allocated memory is no longer reachable or cannot be released. In the example below, returning before `delete` leaks the allocation:

```cpp
int* create_value() {
	int* result = new int{42};
	return result; // Caller now owns the allocation and must delete it
}
```

Manual ownership is easy to get wrong: every successful `new` must be paired with exactly one `delete`, including when exceptions or early returns occur. Prefer RAII (Resource Acquisition Is Initialization): tie a resource's lifetime to an object whose destructor releases it.

## Smart pointers

The smart-pointer types in `<memory>` express ownership and release managed objects automatically when their owners are destroyed.

### `std::unique_ptr`: exclusive ownership

Use `std::unique_ptr` when one owner controls an object. It cannot be copied, but ownership can be transferred with `std::move`.

```cpp
#include <memory>

auto value = std::make_unique<int>(42);
// The int is deleted automatically when value leaves scope.

auto next_owner = std::move(value);
// value is now empty; next_owner owns the int.
```

Prefer `std::make_unique<T>(...)` over writing `new T(...)` directly. Use a raw pointer or reference to observe the object without taking ownership, and do not `delete` an object owned by a `unique_ptr`.

### `std::shared_ptr`: shared ownership

Use `std::shared_ptr` only when multiple parts of a program genuinely need to share ownership. The object is destroyed when the last owning `shared_ptr` is destroyed or reset.

```cpp
auto first_owner = std::make_shared<int>(42);
auto second_owner = first_owner; // Both share ownership
```

Shared ownership has bookkeeping costs and can hide unclear lifetime relationships. Do not choose it just to make a pointer easy to copy.

### `std::weak_ptr`: non-owning observation of shared objects

Use `std::weak_ptr` to observe an object managed by `shared_ptr` without extending its lifetime. Lock it to obtain a temporary `shared_ptr` and check whether the object is still alive.

```cpp
std::weak_ptr<int> observer = first_owner;

if (auto owner = observer.lock()) {
	// The object is alive while owner exists.
	int value = *owner;
}
```

`weak_ptr` is also useful for breaking ownership cycles. If two objects hold `shared_ptr`s to each other, their reference counts may never reach zero, so neither object is destroyed. Make at least one link non-owning with `weak_ptr` when that matches the relationship.

## Practical rules

- Prefer values, `std::vector`, and other standard-library types over manual `new` and `delete`.
- Use `std::unique_ptr` for single ownership and `std::shared_ptr` only for real shared ownership.
- Use raw pointers and references as non-owning observers; document nullable pointers when that matters.
- Never dereference a wild, null, or dangling pointer.
- Remember that a pointer or reference to an element of a container may be invalidated when the container changes.
- Follow the Rule of Zero: let standard-library members manage resources so your class usually needs no custom destructor, copy constructor, or assignment operator.
- Enable compiler warnings and use memory sanitizers or leak-detection tools during testing.
