# Topic G — Bit manipulation and memory representation

## Q48. How do you check whether an integer is a power of two?

```cpp
bool isPowerOfTwo(unsigned int n) {
    return n != 0 && (n & (n - 1)) == 0;
}
```

Why it works: A positive power of two has exactly one set bit. Subtracting one clears that bit and sets the lower bits; ANDing the two values gives zero.

Examples:

- `8` (`1000`) → true
- `12` (`1100`) → false
- `0` → false

## Q49. How do you count set bits efficiently?

```cpp
int countBits(unsigned int n) {
    int count = 0;

    while (n) {
        n &= (n - 1);
        count++;
    }

    return count;
}
```

Each iteration removes one set bit. Complexity is $O(k)$, where $k$ is the number of set bits; the worst case is $O(w)$ for a $w$-bit integer.

## Q50. How do you check whether a machine is little-endian or big-endian?

```cpp
#include <iostream>
using namespace std;

int main() {
    unsigned int x = 1;
    unsigned char* p = reinterpret_cast<unsigned char*>(&x);

    if (p[0] == 1)
        cout << "Little-endian";
    else
        cout << "Big-endian";
}
```

On a conventional little-endian machine, this prints `Little-endian`. On a conventional big-endian machine, it prints `Big-endian`.

The test distinguishes those two common cases; unusual mixed-endian representations are outside this simple classification.

## Q51. What is structure padding and alignment?

```cpp
struct A {
    char c;
    int x;
    char d;
};
```

The size of `A` is not necessarily the sum of its member sizes. The compiler may insert padding between members and at the end to satisfy alignment requirements.

For example, on a common ABI with 1-byte `char`, 4-byte `int`, and 4-byte `int` alignment, `sizeof(A)` is often `12`, not `6`.

Important: The exact size depends on the target ABI, compiler options, and type alignment. Do not memorize `12` as a universal answer.

## Q52. What is the difference between a function pointer and a pointer to a member function?

A function pointer refers to a free or static function:

```cpp
int add(int a, int b) {
    return a + b;
}

int (*fp)(int, int) = add;
cout << fp(2, 3);
```

Output: `5`.

A pointer to a non-static member function has a different type and is invoked on an object:

```cpp
struct A {
    void show() {}
};

void (A::*mp)() = &A::show;

A obj;
(obj.*mp)();
```
