# Templates in C++

Templates are a powerful feature in C++ that allow **generic programming**. They enable us to define functions or classes with **generic types**, which can then work with any data type, without rewriting the same logic for each type.

There are two types of templates in C++:
1. **Function Templates**
2. **Class Templates**

---

### **1. Function Templates**
Function templates are used to write a **single function** that can work with any data type. The **data type is passed as a parameter to the function** at compile time. This allows the function to work with different types without requiring function overloading or rewriting the same function for each type.

#### **Syntax**:
```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}
```

- Here, `T` is a placeholder for a data type. When the function is called, `T` will be replaced by the appropriate data type.
- `typename T` or `class T` can be used to define a generic type.

#### **Example**:
```cpp
#include <iostream>
using namespace std;

// Function template to add two values
template <typename T>
T add(T a, T b) {
    return a + b;
}

int main() {
    cout << "Addition of integers: " << add(3, 4) << endl;     // Works with integers
    cout << "Addition of doubles: " << add(3.5, 4.5) << endl;  // Works with doubles
    cout << "Addition of chars (ASCII values): " << add('A', 'B') << endl;  // Works with chars
    
    return 0;
}
```

- **Explanation**: 
  - In the above example, the `add` function works with `int`, `double`, and even `char`. The compiler will generate the appropriate function code based on the arguments passed.
  - For `add(3, 4)`, `T` becomes `int`. 
  - For `add(3.5, 4.5)`, `T` becomes `double`.
  - For `add('A', 'B')`, `T` becomes `char` (added based on their ASCII values).

---

### **2. Class Templates**
Class templates allow us to create a class that can work with any data type. This is useful when we want a class to operate on multiple types, but with the same structure.

#### **Syntax**:
```cpp
template <typename T>
class MyClass {
private:
    T data;
public:
    MyClass(T d) : data(d) {}
    void display() {
        cout << "Data: " << data << endl;
    }
};
```

- `T` is the placeholder for the data type, similar to function templates.

#### **Example**:
```cpp
#include <iostream>
using namespace std;

// Class template for a simple class
template <typename T>
class MyClass {
private:
    T data;
public:
    MyClass(T d) : data(d) {}  // Constructor to initialize data
    void display() {
        cout << "Data: " << data << endl;
    }
};

int main() {
    MyClass<int> obj1(10);   // Works with int
    obj1.display();
    
    MyClass<double> * obj2 = new MyCalss<double>(5.75);  // Works with double
    obj2->display();
    
    MyClass<string> obj3("Hello");  // Works with string
    obj3.display();
    
    return 0;
}
```

- **Explanation**: 
  - The `MyClass` template is instantiated with `int`, `double`, and `string` types. Each instantiation creates a different version of the class.
  - `obj1` is a class instance with an `int` type.
  - `obj2` is a class instance with a `double` type.
  - `obj3` is a class instance with a `string` type.

---

### **Template Specialization**

There may be cases where you want different behavior for specific types, even though the template generally works for all types. In such cases, **template specialization** is used.

#### **Syntax for Specialization**:
```cpp
template <>
class MyClass<char> {
private:
    char data;
public:
    MyClass(char d) : data(d) {}
    void display() {
        cout << "Specialized for char: " << data << endl;
    }
};
```

#### **Example**:
```cpp
#include <iostream>
using namespace std;

// General template class
template <typename T>
class MyClass {
private:
    T data;
public:
    MyClass(T d) : data(d) {}
    void display() {
        cout << "Data: " << data << endl;
    }
};

// Template specialization for char type
template <>
class MyClass<char> {
private:
    char data;
public:
    MyClass(char d) : data(d) {}
    void display() {
        cout << "Specialized for char: " << data << endl;
    }
};

int main() {
    MyClass<int> obj1(10);  // Uses general template
    obj1.display();
    
    MyClass<char> obj2('A');  // Uses specialized template for char
    obj2.display();
    
    return 0;
}
```

- **Explanation**:
  - For `MyClass<int>`, the general template is used.
  - For `MyClass<char>`, the specialized template is used, which displays a custom message.

---

### **Template with Multiple Parameters**

Templates can also take multiple parameters. You can define a template with more than one type.

#### **Example**:
```cpp
#include <iostream>
using namespace std;

template <typename T1, typename T2>
class Pair {
private:
    T1 first;
    T2 second;
public:
    Pair(T1 f, T2 s) : first(f), second(s) {}
    void display() {
        cout << "First: " << first << ", Second: " << second << endl;
    }
};

int main() {
    Pair<int, double> obj(5, 3.14);
    obj.display();  // Output: First: 5, Second: 3.14
    
    Pair<string, char> obj2("Hello", 'A');
    obj2.display();  // Output: First: Hello, Second: A
    
    return 0;
}
```

- **Explanation**:
  - Here, `Pair<int, double>` and `Pair<string, char>` are examples of class templates with multiple parameters. This can be useful when dealing with complex data combinations.

---

### **Advantages of Templates**:
1. **Code Reusability**: Templates enable writing generic code that works with any data type, reducing duplication and effort.
2. **Type Safety**: With templates, the compiler ensures that operations are only performed on compatible types.
3. **Performance**: Templates allow for compile-time polymorphism, which is more efficient than runtime polymorphism (e.g., inheritance and virtual functions).

---


### **Key Points Recap**:
- **Function Templates** allow writing a single function for different types.
- **Class Templates** allow writing generic classes that work with any type.
- Templates increase code reusability, type safety, and performance.
- **Template Specialization** allows customizing templates for specific types.
- **Template Metaprogramming** allows computations at compile time for performance optimization.

