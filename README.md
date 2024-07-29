# CPP Notes

## Tpes of Segmentation Faults in C++

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

