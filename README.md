# CPP Notes

## Types of Segmentation Faults in C++

A segmentation fault  occurs when a program tries to access memory it doesn't have permission to access.
1. **Modifying a String Literal**:
   - String literals are stored in read-only memory. Attempting to modify them results in a segfault.
   - Example:
     ```cpp
     char* str = "GFG";
     str[2] = 'K'; // Problem: trying to modify read-only memory. Causes a segfault
     ```

2. **Accessing an Address That Is Freed**:
   - Dereferencing a pointer after freeing the memory block leads to a segfault.
   - Example:
     ```cpp
     int* p = (int*)malloc(sizeof(int));
     *p = 100;
     free(p);
     *p = 110; // Segmentation fault
     ```

3. **Accessing Out-of-Bounds Array Index**:
   - Trying to access an array element beyond its bounds results in a segfault.
   - Example:
     ```cpp
     int arr[5];
     arr[10] = 42; // Segmentation fault
     ```

4. **Improper Use of `scanf()`**:
   - Incorrect usage of `scanf()` can lead to memory corruption and segfaults.
   - Example:
     ```cpp
     int num;
     scanf("%d", num); // Missing '&' before 'num'
     ```

5. **Stack Overflow**:
   - Recursive functions or excessive function calls can cause the stack to overflow, resulting in a segfault.
     ```
     int stupidFunction(int n)
     {
       return stupidFunction(n);
     }
     ```

6. **Dereferencing an Uninitialized or NULL Pointer**:
   - Accessing memory through an uninitialized or NULL pointer leads to segfaults.
   - Example:
     ```cpp
     int* ptr; // Uninitialized pointer
     *ptr = 42; // Segmentation fault
     ```


## Size of a Class in C++

Size of an empty class is 1. The compiler allocates 1 byte to an object of an empty class for its unique address identification. 
```cpp
   //sizeof(Test) = 1 Byte
   class Test{
   };
```
Size of int is 4B, float is 4B, char is 1B, short is 2B, long is 2B and double is 8B. 

```cpp
   //sizeof(Test) = 4B
   class Test{
      int i;
   };

   //sizeof(Test1) = 12B. char c = 1B + 3B padding = 4B
   class Test1{
     int i;
     int j;
     char c;
   };

   //sizeof(Test2) = 12B. Because the size of the char is c, and in Test1, padding was added to the size of extra 3 bytes, hence in test2 the size is still 12B, because
   char c,d,e; 3B + 1B padding = 4B//
   class Test2{
     int i;
     int j;
     char c,d,e;
   };

   //sizeof(Test3) = 16B. char c,d,e,f,g; 5B + 3B padding = 8B//
   class Test3{
     int i,j;
     char c,d,e,f,g;
   };

   //sizeof(Test4) = 12B. char c=1B, short h=2B, padding = 1B//
   class Test4{
     int i,j;
     char c;
     short h;
   };

   //sizeof(Test4) = 12B. char c=1B, short h=2B, padding = 1B//
   class Test4{
     int i,j;
     char c;
     short h;
   };

   //sizeof(Test5) = 16B. int i,j = 8B, char c=1B, short h,a=4B, padding = 3B//
   class Test5{
     int i,j;
     char c,d;
     short h,a;
   };

   //sizeof(Test6) = 16B. double d = 8B, int i=4B + padding = 4B//
   class Test6{
     int i;
     double d;
   };

   //sizeof(Test7) = 16B. double d = 8B, int i=4B, char c=1B, padding = 4B//
   class Test7{
     int i;
     char c;
     double d;
   };

   //sizeof(Test8) = 24B. double d = 8B, int i,j=8B, char c=1B, padding = 7B//
   class Test8{
     int i,j;
     char c;
     double d;
   };

   //sizeof(Test9) = 1B. Nonvirtual functions do not contribute to the object size at all.
   //There is nothing you need to store in an object to be able to call its nonvirtual member function.
   class Test9{
      void fun1(){}
      int fun2(){}
      void fun3(){
         int i = 5;
         int j = 6;
      }
   };

   //sizeof(Test10) = 8B. When a class has at least one virtual function, the compiler typically adds a hidden pointer, often called the `vptr` (virtual table pointer),
   //to each instance of the class. This pointer points to the vtable (virtual table), which contains the addresses of the virtual functions.
   class Test10{
      virtual void fun1()=0;
      virtual int fun2(){}
      virtual void fun3(){
         int i = 5;
         int j = 6;
      }
   };

   //sizeof(Test12) = 8B.
   class Test11{

   };
   class Test12: virtual Test11{
    
   };

   //sizeof(Test14) = 12B. int i,j=8B, char c=1B, char d=1B, 2B padding.
   class Test13{
      int i,j;
      char c;
   };
   class Test14: Test13{
       char d;
   };

   //sizeof(Test14) = 24B. virtual intehritance=8B , int i,j=8B, char c=1B, char d=1B, 6B padding.
   class Test15{
      int i,j;
      char c;
   };
   class Test16: virtual Test13{
       char d;
   };

```
















