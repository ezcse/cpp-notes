# `new` vs `malloc` in C++

This README explains the key differences between the `new` operator and `malloc` function in C++ for dynamic memory allocation, with detailed insights into their syntax, usage, and behavior.

## Table of Contents

1. [Introduction](#introduction)
2. [Syntax](#syntax)
3. [Initialization](#initialization)
4. [Deallocation](#deallocation)
5. [Type Safety](#type-safety)
6. [Error Handling](#error-handling)
7. [Customization](#customization)
8. [Alignment](#alignment)
9. [Array Allocations](#array-allocations)
10. [Summary](#summary)

## 1. Introduction

In C++, both `new` and `malloc` are used for dynamic memory allocation, but they serve different purposes and behave differently. 

- **`new`**: A C++ keyword that allocates memory and also initializes objects by calling constructors. It is primarily used in object-oriented programming.
- **`malloc`**: A function from C that allocates raw memory without initialization. It requires manual typecasting in C++ and does not call constructors.

## 2. Syntax

### `new`:
```cpp
Type* ptr = new Type;           // Allocates memory for one object of type `Type`.
Type* arr = new Type[size];     // Allocates memory for an array of `size` elements.
```

### `malloc`:
```cpp
Type* ptr = (Type*)malloc(sizeof(Type));            // Allocates memory for one object of `Type`.
Type* arr = (Type*)malloc(size * sizeof(Type));     // Allocates memory for an array of `size` elements.
```

Key Difference:
- `new` does not require typecasting, while `malloc` does.

## 3. Initialization

### `new`:
- Allocates memory **and** calls constructors to initialize objects.
- For primitive types:
    - `new Type()` initializes the value (e.g., `0` for integers).
    - `new Type` does not initialize the value.

Example:
```cpp
int* ptr = new int(5);  // Allocates and initializes an int to 5.
int* arr = new int[10]();  // Allocates an array of 10 ints, initialized to 0.
```

### `malloc`:
- Allocates **uninitialized** raw memory, which may contain **garbage values**.

Example:
```cpp
int* ptr = (int*)malloc(sizeof(int));  // Uninitialized memory (garbage values).
```

## 4. Deallocation

### `new`:
- Use `delete` for single objects and `delete[]` for arrays.
- Calls destructors to clean up properly.

Example:
```cpp
delete ptr;        // Deletes single object and calls destructor.
delete[] arr;      // Deletes array and calls destructor for each element.
```

### `malloc`:
- Use `free()` to deallocate memory.
- Does **not** call any destructors.

Example:
```cpp
free(ptr);  // Deallocates memory but does not call destructors.
```

## 5. Type Safety

### `new`:
- Automatically returns a pointer of the correct type (no casting needed).

### `malloc`:
- Returns a `void*` (generic pointer), which must be explicitly **typecast** in C++.

Example:
```cpp
MyClass* obj = new MyClass();            // No casting required.
MyClass* obj = (MyClass*)malloc(sizeof(MyClass));  // Explicit cast required.
```

## 6. Error Handling

### `new`:
- Throws a `std::bad_alloc` exception if allocation fails.

Example:
```cpp
try {
    int* ptr = new int[1000000000];  // May throw bad_alloc on failure.
} catch (std::bad_alloc& e) {
    std::cerr << "Memory allocation failed: " << e.what() << std::endl;
}
```

Alternatively, you can use `nothrow`:
```cpp
int* ptr = new (std::nothrow) int[1000000000];  // Returns nullptr on failure.
```

### `malloc`:
- Returns **`NULL`** if allocation fails. You must check for `NULL` manually.

Example:
```cpp
int* ptr = (int*)malloc(1000000000 * sizeof(int));
if (ptr == NULL) {
    std::cerr << "Memory allocation failed" << std::endl;
}
```

## 7. Customization

### `new`:
- Can be **overloaded** to customize memory allocation for specific classes.

Example:
```cpp
void* operator new(size_t size) {
    std::cout << "Allocating " << size << " bytes\n";
    return malloc(size);
}
```

### `malloc`:
- `malloc` is a standard C library function and cannot be overloaded in C++.

## 8. Alignment

### `new`:
- Automatically ensures proper memory **alignment** for the type being allocated.

### `malloc`:
- Does not guarantee alignment for complex data types; special functions like `posix_memalign` may be required.

## 9. Array Allocations

### `new[]`:
- Allocates memory for arrays and calls constructors for each element.

Example:
```cpp
int* arr = new int[10];  // Allocates an array of 10 integers.
```

### `malloc`:
- Allocates memory for arrays, but does not call constructors.

Example:
```cpp
int* arr = (int*)malloc(10 * sizeof(int));  // Allocates an array of 10 integers.
```

## 10. Summary

| Feature          | `new`                                  | `malloc`                            |
|------------------|----------------------------------------|-------------------------------------|
| Language         | C++                                    | C, C++                              |
| Allocates        | Memory + Calls constructor             | Memory only                         |
| Initialization   | Yes (calls constructor)                | No (garbage values)                 |
| Deallocation     | `delete` / `delete[]`                  | `free()`                            |
| Type Safety      | Yes                                    | No (requires casting)               |
| Error Handling   | Throws `std::bad_alloc` on failure     | Returns `NULL` on failure           |
| Customization    | Can be overloaded                      | Cannot be overloaded                |
| Alignment        | Automatically aligned                  | May not guarantee alignment         |
| Array Allocation | Supports constructor calls for arrays  | Does not call constructors          |

---
