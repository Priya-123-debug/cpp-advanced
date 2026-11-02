# Topic D — STL, iterators and containers

## Q26. What is the difference between `vector` and `list`?

| Property | `std::vector` | `std::list` |
| --- | --- | --- |
| Storage | Contiguous | Doubly linked nodes |
| Random access | O(1) | O(n) |
| Insert at end | Amortized O(1) | O(1) with the relevant position/iterator |
| Insert in middle | O(n) shifting | O(1) once the position is known |
| Cache locality | Generally excellent | Generally poorer |
| Memory overhead | Usually lower | Higher due to node links and allocation overhead |

For most sequence workloads, `vector` is the default choice unless the workload specifically benefits from `list`'s iterator stability or insertion/deletion characteristics.

## Q27. What is iterator invalidation?

An iterator becomes invalid when an operation makes it no longer valid to use.

Example:

```cpp
vector<int> v = {1, 2, 3};

auto it = v.begin();
v.push_back(4);

// Do not assume `it` remains valid.
```

If `push_back` causes reallocation, all iterators and references to vector elements are invalidated.

OA rule: Know how `insert`, `erase`, `push_back`, and reallocation affect iterators for `vector`, `deque`, `list`, and `unordered_map`.

## Q28. What is the difference between `map` and `unordered_map`?

| Property | `map` | `unordered_map` |
| --- | --- | --- |
| Underlying structure | Ordered tree | Hash table |
| Key order | Sorted | No guaranteed order |
| Typical lookup | O(log n) | Average O(1) |
| Worst-case lookup | O(log n) | O(n) |
| Useful when | Sorted traversal or range queries matter | Fast average key lookup matters |

## Q29. What is the difference between `push_back()` and `emplace_back()`?

```cpp
vector<pair<int, int>> v;

v.push_back(make_pair(1, 2));
v.emplace_back(1, 2);
```

Both add a pair representing `(1, 2)`.

- `push_back` inserts an existing object by copying or moving it.
- `emplace_back` constructs an object in place from the supplied arguments.

`emplace_back` is not automatically faster in every situation; the benefit depends on the type and arguments.

## Q30. What is the difference between `priority_queue` and `queue`?

- `queue`: FIFO order.
- `priority_queue`: removes the highest-priority element first according to its comparator.

```cpp
priority_queue<int> pq;
pq.push(10);
pq.push(30);
pq.push(20);

cout << pq.top();
```

Output: `30`

For a min-heap:

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

The smallest element is at the top.

## Q31. What is the difference between `sort()` and `stable_sort()`?

- `sort()` sorts elements but does not guarantee that equal elements retain their original relative order.
- `stable_sort()` preserves the relative order of equivalent elements.

Both are comparison-based sorting algorithms; typical implementations have $O(n\log n)$ complexity for `sort`, while `stable_sort` has $O(n\log n)$ complexity when sufficient extra memory is available.

## Q32. What is the difference between `lower_bound()` and `upper_bound()`?

For a sorted array:

```cpp
vector<int> a = {1, 2, 2, 2, 4};

cout << (lower_bound(a.begin(), a.end(), 2) - a.begin()) << " ";
cout << (upper_bound(a.begin(), a.end(), 2) - a.begin());
```

Output:

```cpp
1 4
```

- `lower_bound`: first position whose value is greater than or equal to `2`.
- `upper_bound`: first position whose value is strictly greater than `2`.

Both require the range to be appropriately partitioned for the given comparison; for the usual ascending-order use, sort the vector first.
