# Smart Pointers in C++

Smart pointers in C++ provide automatic memory management by ensuring that dynamically allocated objects are automatically destroyed when they are no longer in use. This helps prevent memory leaks and dangling pointers. In C++, smart pointers are part of the Standard Template Library (STL) and are implemented in the `<memory>` header.

This README covers the three types of smart pointers in C++:
- `std::unique_ptr`
- `std::shared_ptr`
- `std::weak_ptr`

We will also explore their internal representation, especially the control block shared between `std::shared_ptr` and `std::weak_ptr`.

---

## Table of Contents
1. [Smart Pointer Overview](#smart-pointer-overview)
2. [std::unique_ptr](#stduniqueptr)
3. [std::shared_ptr](#stdsharedptr)
4. [std::weak_ptr](#stdweakptr)
5. [Internal Representation of Smart Pointers](#internal-representation-of-smart-pointers)
6. [Functions Supported by Smart Pointers](#functions-supported-by-smart-pointers)
7. [Use Cases](#use-cases)
8. [Conclusion](#conclusion)

---

## Smart Pointer Overview

A **smart pointer** is a wrapper class around a raw pointer. It automatically manages the lifetime of the object it points to, ensuring that the object is destroyed when it's no longer needed. Smart pointers eliminate the need for manual memory management and help avoid memory management issues like:
- Memory leaks
- Dangling pointers
- Double free errors

There are three types of smart pointers in C++:
- **`std::unique_ptr`**: Exclusive ownership of an object.
- **`std::shared_ptr`**: Shared ownership of an object with reference counting.
- **`std::weak_ptr`**: A non-owning reference to an object managed by a `std::shared_ptr`.

---

## `std::unique_ptr`

`std::unique_ptr` represents exclusive ownership of an object. No two `unique_ptr`s can point to the same object simultaneously. When the `unique_ptr` is destroyed, the object it owns is also destroyed. `std::unique_ptr` cannot be copied but can be moved to transfer ownership.

### Example:

```cpp
std::unique_ptr<int> ptr1 = std::make_unique<int>(42);  // Exclusive ownership
std::unique_ptr<int> ptr2 = std::move(ptr1);            // Ownership transferred
```

### Key Characteristics:
- **Exclusive ownership**: Only one `unique_ptr` can own the object at a time.
- **No reference counting**: Since it's exclusive, no need to count references.
- **Move semantics**: Can be transferred using `std::move()` but not copied.

---

## `std::shared_ptr`

`std::shared_ptr` provides **shared ownership** of an object. Multiple `shared_ptr` instances can own the same object, and the object is destroyed only when the last `shared_ptr` releases ownership. This is managed via a reference count.

### Example:

```cpp
std::shared_ptr<int> ptr1 = std::make_shared<int>(100);  // Shared ownership
std::shared_ptr<int> ptr2 = ptr1;                        // Shares ownership
```

### Key Characteristics:
- **Shared ownership**: Multiple `shared_ptr`s can share ownership of the same object.
- **Reference counting**: A reference count is maintained to track how many `shared_ptr`s own the object.
- **Automatic destruction**: When the last `shared_ptr` is destroyed, the object is automatically deleted.

---

## `std::weak_ptr`

`std::weak_ptr` is a **non-owning** smart pointer that works in conjunction with `std::shared_ptr`. It does not affect the reference count of the object but provides a safe way to access an object without extending its lifetime. `weak_ptr` is typically used to prevent **cyclic dependencies** that can lead to memory leaks in `shared_ptr`-based systems.

### Example:

```cpp
std::shared_ptr<int> sharedPtr = std::make_shared<int>(10);
std::weak_ptr<int> weakPtr = sharedPtr;  // Weak reference

if (auto lockedPtr = weakPtr.lock()) {   // Try to get a shared_ptr and increase reference count by 1
    std::cout << *lockedPtr << std::endl;
} else {
    std::cout << "Object no longer exists" << std::endl;
}
```

### Key Characteristics:
- **Non-owning**: Does not own the object, so it doesn’t affect the reference count.
- **Safe access**: Allows safe access to the managed object using `lock()`.
- **Prevents cyclic references**: Often used to prevent cyclic dependencies between `shared_ptr`s.

---

## Internal Representation of Smart Pointers

### `std::shared_ptr` and `std::weak_ptr` Control Block

Both `std::shared_ptr` and `std::weak_ptr` share a common **control block** to manage the object's lifetime and reference counting. The control block contains:
- **Strong reference count**: Tracks the number of `std::shared_ptr`s pointing to the object.
- **Weak reference count**: Tracks the number of `std::weak_ptr`s referring to the control block (but not owning the object).
- **Pointer to the object**: The actual object being managed.

### Control Block Structure:

```text
Control Block
+-------------------------------+
| strong_ref_count (shared_ptrs) |
| weak_ref_count   (weak_ptrs)   |
| Pointer to managed object      |
+-------------------------------+
```

- **Strong reference count**: Incremented by `std::shared_ptr` instances. When this count reaches zero, the object is destroyed.
- **Weak reference count**: Incremented by `std::weak_ptr` instances. When this count reaches zero, the control block itself is destroyed.
  
`std::weak_ptr` holds a weak reference to the control block and can access the object if it is still alive (i.e., strong reference count > 0). However, `weak_ptr` does not contribute to the strong reference count, meaning it doesn't extend the object's lifetime.

---

## Functions Supported by Smart Pointers

### Common Functions for Smart Pointers:

- **`get()`**: Returns the raw pointer managed by the smart pointer.
- **`operator*()`**: Dereferences the stored pointer to access the object.
- **`operator->()`**: Provides pointer-like access to the object’s members.
- **`reset()`**: Releases the current object and optionally takes ownership of a new one.
- **`swap()`**: Swaps the managed object with another smart pointer.
- **`operator bool()`**: Checks if the smart pointer owns an object.

### Additional Functions for `std::shared_ptr`:

- **`use_count()`**: Returns the number of `shared_ptr`s managing the object.
- **`unique()`**: Returns `true` if the `shared_ptr` is the only one managing the object.
- **`lock()`**: (from `std::weak_ptr`) Attempts to get a `shared_ptr` from the `weak_ptr`.

### Additional Function for `std::weak_ptr`:

- **`lock()`**: Returns a `std::shared_ptr` if the object is still alive. Returns an empty `shared_ptr` if the object has been destroyed.
- **`expired()`**: Returns `true` if the managed object has been destroyed (i.e., the strong reference count is zero).

---

## Use Cases

### `std::unique_ptr`
- **Ownership transfer**: When you want one and only one owner of an object and want to ensure it gets deleted automatically.
- **Resource management**: Often used for managing resources like file handles, sockets, and dynamic memory where exclusive ownership is required.

### `std::shared_ptr`
- **Shared ownership**: Useful in situations where multiple entities need to share access to a single resource.
- **Dynamic lifetime management**: Common in complex systems with multiple owners of the same object.

### `std::weak_ptr`
- **Breaking cyclic dependencies**: Used when objects form cyclic references and you want to avoid memory leaks.
- **Temporary access**: When you need temporary access to an object managed by `std::shared_ptr` without extending its lifetime.

---

## Conclusion

Smart pointers in C++ (`std::unique_ptr`, `std::shared_ptr`, and `std::weak_ptr`) provide a powerful, flexible, and safe way to manage dynamic memory. They reduce the risk of memory-related errors, such as leaks, dangling pointers, and double deletion, by automating object lifetime management.

Understanding the internal representation of `std::shared_ptr` and `std::weak_ptr` — especially their shared control block — is essential for avoiding common pitfalls like cyclic references and over-retention of objects. By leveraging the right smart pointer for your use case, you can write safer and more efficient C++ code.