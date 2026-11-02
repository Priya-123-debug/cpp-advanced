# Topic C — Advanced C++ output questions

These are practice questions designed around the output-prediction patterns reported in NVIDIA assessments. Predict the answer before reading the explanation.

## Q16. What is the output of this increment expression?

```cpp
int i = 5;
int x = i++ + 2;

cout << i << " " << x;
```

Answer:

```cpp
6 7
```

`i++` produces the old value, `5`, and then increments `i`.

Contrast:

```cpp
int i = 5;
int x = ++i + 2;
```

Output: `6 8`.

Important: Do not apply the same reasoning blindly to expressions such as `i++ + ++i`. In C++, that expression has undefined behavior because the modifications are unsequenced relative to each other.

## Q17. What is the output?

```cpp
int x = 10;
cout << x++ << " " << ++x;
```

Answer: `10 12`

The first expression outputs `10` and increments `x` to `11`; the second increments it to `12` and outputs `12`. The operands of `<<` are evaluated in the required left-to-right sequencing order in modern C++.

## Q18. Is this code valid?

```cpp
int x = 10;
cout << x++ + x++;
```

Answer: No defined output should be predicted. This is undefined behavior: the two modifications of `x` are unsequenced relative to each other.

An OA may present undefined-behavior questions as if every program must have one numerical output. The correct answer may instead be that the behavior is undefined.

## Q19. What is the output?

```cpp
int x = 5;

if (x > 2)
    if (x < 4)
        cout << "A";
    else
        cout << "B";
```

Answer: No output.

The `else` belongs to the nearest unmatched `if`, namely `if (x < 4)`. Since `x < 4` is false, the `else` executes—but note that this code actually prints `B`.

Correct output:

```cpp
B
```

The lesson: indentation does not determine which `if` owns an `else`.

## Q20. What is the output?

```cpp
#include <iostream>
using namespace std;

int f() {
    static int x = 0;
    return ++x;
}

int main() {
    cout << f() << " ";
    cout << f() << " ";
    cout << f();
}
```

Answer:

```cpp
1 2 3
```

A local static variable is initialized once and retains its value between function calls.

## Q21. What is the output?

```cpp
int x = 10;

void fun() {
    int x = 20;
    cout << x << " ";
    cout << ::x;
}

int main() {
    fun();
}
```

Answer:

```cpp
20 10
```

`x` refers to the local variable. `::x` refers to the global variable.

## Q22. What is the output?

```cpp
class A {
public:
    A() { cout << "A"; }
};

class B {
public:
    B() { cout << "B"; }
};

class C : public A, public B {
public:
    C() { cout << "C"; }
};

int main() {
    C obj;
}
```

Answer:

```cpp
ABC
```

Base classes are initialized in the order in which they are declared in the inheritance list—not the order in which they appear in the constructor's member-initializer list.

## Q23. What is the difference between `sizeof` and `strlen`?

```cpp
char s[] = "NVIDIA";

cout << sizeof(s) << " " << strlen(s);
```

Answer:

```cpp
7 6
```

- `sizeof(s)` returns the size of the array in bytes, including the terminating null character.
- `strlen(s)` counts characters before the first null character.

Include `<cstring>` when using `strlen`.

## Q24. What is the output?

```cpp
int a[] = {10, 20, 30, 40};
int* p = a;

cout << *(p + 2) << " " << p[3];
```

Answer:

```cpp
30 40
```

Array indexing is equivalent to pointer arithmetic: `p[i]` is defined as `*(p + i)`.

## Q25. Why is this code dangerous?

```cpp
int arr[3] = {10, 20, 30};
cout << arr[3];
```

Answer: Undefined behavior. Valid indexes are `0`, `1`, and `2`.

An out-of-bounds access may appear to print some value on one system, crash on another, or behave differently under optimization. None of those outcomes is guaranteed.
