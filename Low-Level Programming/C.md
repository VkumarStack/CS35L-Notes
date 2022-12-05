# C
- Memory allocation:
    - *Static allocation* involves the amount of memory being specified before runtime - this memory is allocated at the beginning of the program and then free them at the end of the program
        - i.e. `int x`
        - i.e. `static int a[1000]`
    - *Stack allocation* involves calling a function or bracket context (`{}`) - this memory is allocated at the beginning of the function call and then freed at the end of the function call
        - i.e:
        -     int f(int n)
              {
                    char buf[512]; // subq $512,%rsp
                    buf[0] = n;
              } // addq $512, %rsp
        - The stack is allocated and freed first-in-first-out
    - *Heap allocation* involves using `malloc` to obtain a pointer to storage with a specified number of bytes - this data must be freed using `free`
        - i.e. `p = malloc(sizeof(int))` ... `free(p)`
        - A `void*` is a generic pointer - a pointer of any type can be converted to `void*`
            - i.e. 
                -     char c;
                      int i;
                      void *p;
                      p = &c;
                      p = &i;
                      int *pi = p;
                      return pi;
        - Attempting to use a dangling pointer will result in undefined behaviour (some systems will result in the program crashing, whereas others may involve dereferencing some random memory location)
        - Allocated data that is not freed results in memory leaks
        - The heap can be freed in any order  

- `static` indicates that the variable `a` should be only visible in the source file that it belongs to
    - This allows for private namespacing, which allows for code to be modular
    - A `static` variable will be treated as static even if it is a local variable (it will exist for the lifetime of the program, even if the function is exited)
- Variables without the `static` keyword can be visible from any included file
    - `extern` (i.e. `extern int x`) indicates that a variable is defined somewhere else and will be connected with the linker 