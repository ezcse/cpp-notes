
# C++ Interview Preparation Questions and Answers (Set 1)

### 1. What is the difference between malloc and calloc?

**Answer**: 
- `malloc(size_t size)` allocates a block of memory of the specified size and returns a pointer to it, without initializing the memory. 
- `calloc(size_t num, size_t size)` allocates memory for an array of `num` elements of `size` bytes each and initializes all bytes to zero. 

Example:
```cpp
int *arr1 = (int*)malloc(10 * sizeof(int)); // Allocates uninitialized memory
int *arr2 = (int*)calloc(10, sizeof(int));  // Allocates zero-initialized memory
```

### 2. Explain deep copy and shallow copy.

**Answer**: 
- **Deep Copy**: When a class defines its own copy constructor, it performs a deep copy by allocating new memory for its members (especially pointer members), ensuring that modifications to one object do not affect another.
- **Shallow Copy**: If a class does not define a copy constructor, the compiler provides a default copy constructor that performs a shallow copy. In this case, pointer members point to the same memory location, which can lead to issues when one object modifies the data.

```cpp
class Test {
public:
    int *ptr;
    Test() { ptr = new int(0); } // Constructor
    // Deep copy constructor
    Test(const Test &obj) {
        ptr = new int();
        *ptr = *obj.ptr;
    }
};
```

### 3. What is realloc() and how does it work?

**Answer**: 
`realloc()` changes the size of the memory block previously allocated with `malloc()` or `calloc()`. It allocates a new block of memory of the specified size, copies the existing values to this new block, and frees the old block.

Pitfalls include failure to allocate the requested size, which can lead to memory leaks if not handled properly.

Example:
```cpp
int *p = (int *)malloc(sizeof(int) * 10);
p = (int *)realloc(p, sizeof(int) * 5); // Resize the memory block
```

### 4. What is virtual inheritance?

**Answer**: 
Virtual inheritance is a technique used in C++ to solve the diamond problem in multiple inheritance scenarios. When a derived class inherits from two base classes that share a common base class, virtual inheritance ensures that only one instance of the common base class is created, preventing ambiguity.

```cpp
class A {
public:
    void fun() {}
};

class B : virtual public A {};
class C : virtual public A {};

class D : public B, public C {}; // D inherits A virtually
```

### 5. What is a virtual table (vtable)?

**Answer**: 
A virtual table (vtable) is a mechanism used in C++ to support dynamic (or runtime) polymorphism through virtual functions. Each class that contains or inherits virtual functions has its own vtable, which stores pointers to the virtual functions defined for that class.

- **vptr**: Each object of a class with virtual functions contains a pointer to its class's vtable, allowing the appropriate function to be called based on the object's actual type.

### 6. Explain RAII (Resource Acquisition Is Initialization).

**Answer**: 
RAII is a programming idiom in C++ where resource allocation is tied to object lifetime. When an object is created, it acquires resources (like memory, file handles, etc.), and when the object goes out of scope, its destructor is called, automatically releasing the resources. This approach helps prevent memory leaks and resource mismanagement.

Example:
```cpp
#include <memory>

void example() {
    std::unique_ptr<int> ptr(new int(10)); // Automatically releases memory when out of scope
}
```

### 7. What is the difference between new and malloc?

**Answer**: 
- `new` is an operator that allocates memory for an object, calls its constructor, and returns a pointer to the object. It throws an exception if memory allocation fails.
- `malloc()` is a standard library function that allocates a specified amount of memory and returns a pointer to it without calling constructors. It returns `NULL` on failure.

Example:
```cpp
int *arr1 = new int[5]; // Allocates memory and calls constructor (if any)
int *arr2 = (int *)malloc(sizeof(int) * 5); // Just allocates memory
```

### 8. What are move semantics?

**Answer**: 
Move semantics allow the resources of an rvalue (temporary object) to be moved rather than copied. This is achieved through move constructors and move assignment operators. This reduces overhead and improves performance, especially when dealing with large objects.

Example:
```cpp
class MyClass {
public:
    MyClass(MyClass&& other) { /* Move resources from other */ }
    MyClass& operator=(MyClass&& other) { /* Move assignment */ }
};
```

### 9. What is the Rule of Three?

**Answer**: 
The Rule of Three states that if a class defines one of the following special member functions, it should probably explicitly define all three:
1. Copy Constructor
2. Copy Assignment Operator
3. Destructor

This ensures proper management of resources, especially when dealing with dynamic memory.

### 10. What is the Rule of Five?

**Answer**: 
The Rule of Five extends the Rule of Three. In addition to the three mentioned functions, if a class defines either a move constructor or a move assignment operator, it should also explicitly define:
1. Move Constructor
2. Move Assignment Operator


The Rule of Five ensures that your class is optimized for both copying and moving:

- Copying: Requires deep copying to avoid issues with shared resources (e.g., shallow copies leading to double deletion).
- Moving: Avoids the performance hit of copying by "stealing" resources from temporary objects, leaving them in a safe but empty state.
  
##### Example of Rule Of Five
```cpp
class MyClass {
private:
    int* data;
public:
    // Constructor
    MyClass(int value) {
        data = new int(value);
    }

    // Copy Constructor
    MyClass(const MyClass& other) {
        data = new int(*other.data);
    }

    // Copy Assignment Operator
    MyClass& operator=(const MyClass& other) {
        if (this != &other) {
            delete data;
            data = new int(*other.data);
        }
        return *this;
    }

    // Move Constructor (Move Semantics)
    MyClass(MyClass&& other) noexcept {
        data = other.data;  // Take ownership of other's data
        other.data = nullptr;  // Leave the old object in a valid state
    }

    // Move Assignment Operator
    MyClass& operator=(MyClass&& other) noexcept {
        if (this != &other) {
            delete data;  // Clean up old data
            data = other.data;  // Take ownership of other's data
            other.data = nullptr;  // Leave the old object in a valid state
        }
        return *this;
    }

    // Destructor
    ~MyClass() {
        delete data;
    }
};
```

### 11. What is placement new?

**Answer**: 
Placement new allows an object to be constructed at a specific memory location without allocating new memory. It is used when you want to control the memory allocation process.

Example:
```cpp
#include <new>
#include <cstdlib>

void* p = std::malloc(sizeof(int));
int* obj = new (p) int(5); // Construct an int at the allocated memory
std::free(p); // Free the memory
```

### 12. What is a functor?

**Answer**: 
A functor is an object that can be called as if it were a function. This is achieved by overloading the `()` operator in a class. Functors can hold state and provide flexibility in algorithms.

Example:
```cpp
class MyClass {
public:
    int operator()(int x) {
        return x * 2; // Example functionality
    }
};

// Usage
MyClass myFunc;
int result = myFunc(5); // Calls the functor
```

### 13. Explain the `const` keyword in C++. How does it apply to pointers, member functions, and class instances?

1. **`const` with Variables**:
   - **Example**: `const int a = 10;`
     - This means `a` is a constant integer, and its value cannot be modified after initialization.
   - **Explanation**: `const` can be applied to any variable to make it immutable, meaning its value cannot be changed after being set.

2. **`const` with Pointers**:
   The way `const` is applied to pointers can be tricky as there are two possible scenarios:
   
   - **Pointer to `const` Data**: 
     - Example: `const int *p = new int(5);`
     - **Explanation**: Here, `p` is a pointer to a constant integer. This means the value that `p` points to (`*p`) cannot be modified. However, the pointer itself can be changed to point to a different memory location.
     - `*p = 7;` // Invalid (can't change the value pointed to)
     - `p = new int(10);` // Valid (can change the pointer itself)
     
   - **`const` Pointer to Non-`const` Data**:
     - Example: `int *const p = new int(5);`
     - **Explanation**: In this case, `p` is a constant pointer to an integer. This means the pointer cannot point to a different memory location, but you can modify the value of the integer it points to.
     - `*p = 7;` // Valid (can modify the value)
     - `p = new int(10);` // Invalid (can't change the pointer)

   - **`const` Pointer to `const` Data**:
     - Example: `const int *const p = new int(5);`
     - **Explanation**: Here, both the pointer and the data it points to are constant. You can neither change the value being pointed to nor change the pointer itself.
     - `*p = 7;` // Invalid (can't modify the value)
     - `p = new int(10);` // Invalid (can't change the pointer)

3. **`const` with Member Functions**:
   - **Example**:
     ```cpp
     class MyClass {
     public:
         int x;
         void myFunc() const {
             // x = 5; // Invalid, because this function is marked as const
         }
     };
     ```
   - **Explanation**: When a member function is marked as `const`, it promises not to modify any member variables of the class (unless those variables are marked as `mutable`). It also ensures that the function can be called on `const` objects of the class.

4. **`const` with Class Instances**:
   - **Example**:
     ```cpp
     const MyClass obj;
     obj.myFunc(); // Can only call const member functions
     ```
   - **Explanation**: When an object of a class is declared as `const`, only member functions that are marked `const` can be called on that object. This prevents accidental modification of the object's state.

#### Additional Examples:

1. **`const` with Function Parameters**:
   - **Example**:
     ```cpp
     void myFunc(const int a) {
         // a cannot be modified inside this function
     }
     ```
   - **Explanation**: Marking a parameter `const` means that its value cannot be changed within the function body.

2. **`const` Return Types**:
   - **Example**:
     ```cpp
     const int& getX() const {
         return x;
     }
     ```
   - **Explanation**: The return type is `const int&`, meaning the caller cannot modify the value of the returned reference. Additionally, the `const` after the function name ensures the function doesn't modify any member variables.

