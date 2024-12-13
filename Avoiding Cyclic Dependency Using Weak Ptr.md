# Avoiding Cyclic Dependency Using `std::weak_ptr` in C++

Cyclic dependencies can be a common problem in systems that use `std::shared_ptr`, especially in complex graph-like structures where two or more objects hold shared references to each other. This can prevent objects from being properly deleted, leading to memory leaks. `std::weak_ptr` provides a way to avoid this problem by allowing non-owning references that do not contribute to the reference count.



## Table of Contents
1. [Introduction to Cyclic Dependency](#introduction-to-cyclic-dependency)
2. [How `std::shared_ptr` Leads to Cyclic Dependency](#how-stdsharedptr-leads-to-cyclic-dependency)
3. [Using `std::weak_ptr` to Avoid Cyclic Dependencies](#using-stdweakptr-to-avoid-cyclic-dependencies)
4. [Code Example: Cyclic Dependency with `std::shared_ptr`](#code-example-cyclic-dependency-with-stdsharedptr)
5. [Code Example: Avoiding Cyclic Dependency with `std::weak_ptr`](#code-example-avoiding-cyclic-dependency-with-stdweakptr)
6. [Best Practices](#best-practices)
7. [Conclusion](#conclusion)


## Introduction to Cyclic Dependency

A **cyclic dependency** occurs when two or more objects refer to each other, forming a cycle. In the context of smart pointers, this means that objects owned by `std::shared_ptr` instances can indirectly reference each other, which leads to increased reference counts that are never decremented, even when no one else is using the objects.

This prevents the objects from being deallocated, causing memory leaks.

### Example of Cyclic Dependency:

```cpp
#include <iostream>
#include <memory>

class A;
class B;

class A {
public:
    std::shared_ptr<B> b_ptr;
    ~A() { std::cout << "A destroyed\n"; }
};

class B {
public:
    std::shared_ptr<A> a_ptr;
    ~B() { std::cout << "B destroyed\n"; }
};

int main() {
    std::shared_ptr<A> a = std::make_shared<A>();
    std::shared_ptr<B> b = std::make_shared<B>();
    
    a->b_ptr = b;
    b->a_ptr = a; // Cyclic dependency: A points to B, B points to A
    
    return 0;
}
```

In this example, both `A` and `B` point to each other. When the `shared_ptr`s go out of scope, the reference count for both `A` and `B` will never reach zero, because each one is keeping the other alive. As a result, neither object will be destroyed, leading to a memory leak.



## How `std::shared_ptr` Leads to Cyclic Dependency

`std::shared_ptr` uses **reference counting** to manage the lifetime of an object. Each `std::shared_ptr` that points to an object increments a counter. When the last `std::shared_ptr` is destroyed, the reference count drops to zero, and the object is deallocated.

However, when two objects owned by `std::shared_ptr` refer to each other, the reference count never drops to zero, even if no other references exist. This is because:

- Object A holds a `std::shared_ptr` to object B.
- Object B holds a `std::shared_ptr` to object A.

As long as the `std::shared_ptr` instances exist, their reference counts will not go to zero, and the objects will not be destroyed.



## Using `std::weak_ptr` to Avoid Cyclic Dependencies

`std::weak_ptr` is a smart pointer that does **not** affect the reference count of the object it points to. This means it can be used to break the cycle in a graph structure, where one object can hold a non-owning reference to another object, thereby not preventing its deletion.

### Key Points about `std::weak_ptr`:
- It does not increase the reference count of the object.
- It is used to observe the object without affecting its lifetime.
- It can be converted to a `std::shared_ptr` using the `.lock()` method, which provides a way to check if the object is still alive.
- It is commonly used to avoid cyclic dependencies in graph-like data structures.



## Code Example: Cyclic Dependency with `std::shared_ptr`

Let's illustrate how cyclic dependencies occur with `std::shared_ptr` in a graph-like structure.

```cpp
#include <iostream>
#include <memory>

class Node {
public:
    std::shared_ptr<Node> next;
    ~Node() { std::cout << "Node destroyed\n"; }
};

int main() {
    std::shared_ptr<Node> node1 = std::make_shared<Node>();
    std::shared_ptr<Node> node2 = std::make_shared<Node>();
    
    node1->next = node2;
    node2->next = node1;  // Cyclic dependency: node1 -> node2 -> node1
    
    return 0;
}
```

In this example, `node1` and `node2` point to each other. As a result, neither object is deleted because the reference count never reaches zero, causing a memory leak.



## Code Example: Avoiding Cyclic Dependency with `std::weak_ptr`

Now, let's modify the code to use `std::weak_ptr` to avoid the cyclic dependency. We will make the `next` pointer in the `Node` class a `std::weak_ptr`.

```cpp
#include <iostream>
#include <memory>

class Node {
public:
    std::weak_ptr<Node> next;  // Use weak_ptr to avoid cyclic reference
    ~Node() { std::cout << "Node destroyed\n"; }
};

int main() {
    std::shared_ptr<Node> node1 = std::make_shared<Node>();
    std::shared_ptr<Node> node2 = std::make_shared<Node>();
    
    node1->next = node2;
    node2->next = node1;  // No cyclic dependency with weak_ptr
    
    return 0;
}
```

### Explanation:
- We changed `next` from a `std::shared_ptr` to a `std::weak_ptr` in the `Node` class.
- Now, even though `node1` points to `node2` and `node2` points back to `node1`, there is no cyclic reference because `std::weak_ptr` does not increase the reference count.
- When `node1` and `node2` go out of scope, their reference count will drop to zero, and they will be properly destroyed.



## Best Practices

- **Avoid using `std::shared_ptr` for bi-directional relationships**: In cases where two objects reference each other, one of the references should be a `std::weak_ptr` to break the cycle.
- **Use `std::weak_ptr` for non-owning references**: When an object only needs to observe another object without keeping it alive, use `std::weak_ptr` to prevent cyclic dependencies.
- **Manage object lifetimes carefully**: In complex systems with shared ownership, always ensure that cyclic references are avoided using `std::weak_ptr` or redesigning the ownership model.



## Conclusion

Cyclic dependencies can be a major issue in C++ programs that use `std::shared_ptr` for shared ownership. By using `std::weak_ptr`, you can avoid these issues by allowing one object to observe another without preventing it from being deleted. This ensures proper memory management and prevents memory leaks in systems with complex object relationships.

Using `std::weak_ptr` is a crucial technique for managing resources safely and efficiently in C++ programs with shared ownership patterns.
