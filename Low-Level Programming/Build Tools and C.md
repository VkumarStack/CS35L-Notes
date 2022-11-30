# Build Tools
- Audiences for build tools:
    - Coders: Write source code
    - Builders: Run 'make' to piece coders' outputs together
    - Distributors: Use output of builders to incorporate into a syste,
    - Installers: Install the output of distributors
    - Users/Testers: Use output of installer
- There are different build tools for different languages - There may be even cases where a novel language does not have a well-developed build tool yet 
    - You can write your own build tool using simple commands (i.e. create a shell script that does `gcc -c` for every single file ending with `.c` and then linking each object file together into an executable via `gcc x.o y.o ... -o foo`)
- Build tools are generally for compiled languages - interpreted languages more frequently have package managers that can act as build tools (i.e. Node Package Manager)
- `make` is a common build tool built in to the Linux terminal
    - `make` involves running a script from a `Makefile` which specifies dependencies and rules for building and linking
    - i.e. Makefile
        -     x.o: x.c
                   gcc -c x.c
              y.o: y.c
                   gcc -c y.c
              z.o: z.c
                   gcc -c z.c
              foo: x.o y.o z.o
                   gcc x.o y.o z.o -o foo
        - `make foo` can be used to get the executable foo
    - `make` is smart enough to only redo dependencies when necessary (if x.c is updated only, as an example, it will only recompile `x.o` and `foo` since those are the only dependencies)
    - `make` has some of its own syntax for listing dependencies, but most of its work is done via shell commands
- Dependencies:
    - Dependencies are between object files or executable file and source code files 
    - As an example, if both `y.c` and `z.c` use the header file `w.h`, then `y.o` and `z.o` have dependencies on `w.h` (not `y.c` and `z.c` having dependencies on `w.h`)
- For large projects with multiple subdirectories, one strategy is to have the root have a `Makefile` and each subdirectory have its own `Makefile` that is delegated work by the parent `Makefile`
    - This allows for the build process to be modularized, however it has a drawback of not being able to be run in parallel 
- Another, more common strategy involves a single, large Makefile that can be run in parallel
    - This single, large Makefile is often created automatically using external tools
    - However, having dependencies loses opportunities for parallelism (as you have to wait for the dependency to be created before running in parallel)
- Building Makefiles:
    - It may be the case where you are writing a program that will run differently if it has access to a feature (i.e. if including a library works)
        - This is difficult to do in C/C++, so what is usually done is an external shell script (`./configure`) probes for the features (i.e. check if gcc exists) and then return whether it does (say in a header file)
            - The return message can then be checked in the actual program
        - A build tool, then, would need to run `./configure` before running make (as `./configure` would create a Makefile)
        - This process is automated by `Autoconf`, which is a program that generates `./configure` scripts 
            - `Autoconf` creates an `./configure` script, which then makes `Makefile`, which then builds the program

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
- Debugging Tools:
    - `gcc -fstack-protector` attempts to catch stack overflow issues - declaring a buffer inside of a function puts that buffer on the stack, which, if exceeded, can directly modify the stack register %rsp's return address to return to elsewhere in the program
        - This option results in extra code being generated to create a randomized "flag" that is checked before a stack is exited to see if it has been tampered with
    - `gcc -O` optimizes the code - this is not the default as it may require the compilation process to take longer 
        - `gcc -O0` does not optimize
        - `gcc -O2`, `gcc -O3`, `gcc -O4` optimize for runtime performance (but may generate hard to debug code)
        - `gcc -Os` optimizes for executable size 
        - `gcc -Og` optimizes for debugging
    - `gcc -flto` will perform a link-time optimization 
        - Normally, gcc only optimizes one module at a time - meaning that a function called from another file will not be optimized 
        - In the linking process, gcc will use the source code in each object file to create an optimized linked version
    - The warnings that are turned on can vary depending on project
        - Don't take every warning to heart - as it can result in lots of time wasted to appease compiler warnings
    - `gcc -Wall`, turns on all warnings that are useful (it doesn't show ALL warnings because some warnings may be resolved during runtime)
        - `-Wcomment` warns for bad comments (i.e. `/* /* */`)
        - `Wparentheses` warns for bad parentheses (or lack of) (i.e. `i << j + k` or `i < j || k < l && m < n`)
        - `Waddress` warns for obviously bad address operations (i.e. doing pointer comparison when string comparison is meant to be done `if (pointer == "b") // you were supposed to dereference the pointer here`) 
        - `Wstrict=aliasing` warns for pointer aliasing: i.e.  
            -     int i;
                  long* p = (long*)&i;
                  *p = 19;
            - Doing can result in memory being "stepped on" since a long is 8 bytes but an int is 4 bytes, so altering the contents of the memory will result in some other 4 byte quantity being trashed
        - `Wmaybe-uninitialized` warns for a path that may use an uninitialized variable
    - `gcc -Wextra` turns on extra warnings (compared to `Wall`)
        - `Wtype-limits` is used to warn for weird typing cases (i.e. `unsigned u; if (u < 0)`)
    - `gcc -fanalyzer` checks for interprocedural warnings - normally, gcc will only analyze a single function and not other paths (i.e. via function calls), but this option allows for analysis interprocedural warnings to be found  
        - This is expensive at compile-time (similar to using `-flto`)
    - Static checking involves debugging a program before it even runs - performing checks at compile time rather than runtime 
        - `static_assert` allows for an assertion to be made only during compile time - it does not exist during runtime
            - The expression being asserted must be computable at compile time (as otherwise there would be no way to make a proper assertion using it) - think of the Halting Problem
        -      size_t size_sum(size_t a, size_t b) {
                if (a + b <= SIZE_MAX) // This condition is always true
                    return a + b;
                fatal_error("size overflow");
               }
        -      size_t size_sum(size_t a, size_t b) {
                if (a <= SIZE_MAX - b) // This works but now there is a compiler warning for no return path
                    return a + b;
                fatal_error("size overflow");
               }
        -      -Noreturn void fatal_error(char const*);
                size_t size_sum(size_t a, size_t b) {
                if (a <= SIZE_MAX - b)
                    return a + b;
                fatal_error("size overflow");
               }
            - The `-Noreturn void` keyword "helps" the compiler know that the fatal_error function does not return to the caller
        - `int hash(char *buf, size_t bufsize) __attribute__((pure));`
            - `__attribute__((pure))` tells the compiler that the function does not change the state of the machine
                - This allows for the compiler to make optimizations, such as code reordering:
                    -      char buff[1000];
                           char c = buf[0];
                           i = hash(buf, sizeof buf);
                           if (c == buf[0]) // must be true
        - `int square(int) __attribute__((const));`
            - `__attribute__((const))` tells the compiler that the function will always return the same result given the same parameters (it will not read from global memory, which could possibly result in different return values for the same parameters)
        - `int my_printf(char const *fmt, ...) __attribute__((nonnull(1), format(printf, 1, 2)));`
            - The `...` parameter allows for a variable number of arguments (of an unspecified type)
            - The `nonnull` attribute indicates that the first argument (pointer) cannot be null
            - The `format(printf, 1, 2)` indicates that the function "acts" like printf in terms of parameter ordering
    - Dynamic checking involves debugging a program during runtime
        - This can involve your own checks
        - This can also involve system checks:
            -     #include <stdckint.h> // C23
                  if (ckd_add(r, a, b)) *r = a + b
                    return EOVERFLOW;
                - A downside to this is that the code may become harder to read 
        - This can also involve checks inserted by the compiler
            - `gcc -fsanitize=undefined` checks for undefined behaviour at runtime and will crash it - this results in extra code being generated
                - This will miss catching undefined behaviour for addressing problems
            - `gcc -fsanitize=address` will check for most addressing errors and crash, similar to `-fansitize=undefined` (though you cannot use both)
            - `gcc -fsanitize=leak` will look for memory leaks (this can be combined with the other options)
            - `gcc -fsanitize=thread` will attempt to look for race conditions (when making use of multiple threaded programming)
        - Valgrind allows for programs to be run in a simulated environment which allows for more runtime checking (such as leaks)
            - i.e. `valgrind diff x y`
            - Valgrind can be used for any executable and requires no special flags, but it is slower (since it is effectively emulating the program)
                - The checking done by Valgrind is also less extensive compared to the aforementioned gcc flags because it can only check the machine code (rather than the source code)
        - Dynamic checking is not perfect - it does not catch every single issue, and even when it does, it may catch issues only for that run
    - Portability checking involves checking if code can run on different platforms
        - Machine Platforms (for C):
            - 64-bit platform: `gcc`
            - 32-bit platform: `gcc -m32`
        - Operating Systems:
            - Linux vs. MacOS vs. Android
        - Browser (for Javascript):
            - Chromium vs. Firefox vs. ...
            - This can be somewhat done with version checks (check what version the browser is on and then return an appropriate template)
            - What is more commonly done is to use portability modules which are used by the main code 
    - Code Optimizations:
        - `__builtin_unreachable()` is a function that is meant to never be called
            - This is meant to be used in performance cases where it is necessary to "let the compiler know" certain information:
                -     int sqrt(int n) {
                        if (n < 0)
                            __builtin_unreachable();
                            // This lets the compiler know that 
                            // n is nonnegative, so it can make optimizations that 
                            // would otherwise fail on negatives
                      } 
        - `__attribute__((aligned(NUM)))` allocates a variable on an address that is a multiple of NUM (NUM must be a power of 2)
            - This is good for cache optimizations
            - `long x[2]__attribute__((aligned(16)))`
        - `int f(int)__attribute__((cold))` indicates that f is on a branching path that is likely not meant to be taken
            - This makes branching prediction easier for compilers since commonly used instructions can be cached
        - `int f(int)__attribute((hot))` indicates that f is on a branching path that is likely to be taken
        - Compilers can perform profiling, which means they insert extra instructions into the code to count each time each instruction is executed, run the program, and then recompile the program and use the collected statistics to figure out which functions are "hot" and "cold"
            - This will work fine as long as the profiling runs are representatives of how the program will actually work 
            - `gcc --coverage`
            - `gprof`
- Debugging Tips:
    - You're better off not debugging - if there is bad code, then it is better to refactor cleaner, reliable code instead of delving into debugging hell
        - Be proactive when creating code - so that bugs are less likely (use static checking, etc)
    - Create good test cases (that can allow for bugs to be more easily exposed)
        - Test-Driven-Development is a principle that prioritizes test cases over code - test cases should be written first, and then the code later 
    - Use a better platform - i.e. switch from C++ to Java if your program crashes frequently
    - Practice defensive programming - assume that other modules that your source code is using are broken
        - i.e. Use logs (print statements) to keep track of what is going on
        - i.e. Use a checkpoint that will save the state of the program at a given time and a restart that will restore the state from the most recently saved state
        - i.e. Use assertions to reliably crash the program when necessary
        - i.e Use exception handling (try and catch blocks)
        - i.e. Use barricades, which are modules that check for external data and ensure that only clean data is passed in to your working code
        - i.e. Use interpreters - execute programmers step-by-step
        - i.e. Use virtual machines so that a program cannot corrupt the rest of the system
- Debugging Tools:
    - Valgrind is one debugging tool
    - Another is strace:
        - `strace COMMAND` will run COMMAND (or program) and output every system call made
    - Devops tools:
        - `ps` - Lists all processes 
        - `top` - Lists the most resource-intensive processes
    - GDB is another popular debugger - GDB runs the program and makes use of special system calls to manipulate the process (stop/start the process, view the memory in the process, or modify some of the memory in the process)
        - This is still secure because GDB can only control processes with the same user ID
        - By default, compiling with GCC leaves out debugging information
            - `gcc -g` compiles with debugging information (`g1`, `g2`, `g3`) will increase the amount of debugging information included
                - The `-g` flag will bloat the program with debugging information (tables)
                - It is a bad idea to combine the `-g` and `-O` flags because optimizations will often make the code unreadable - which means you will not be debugging the same source code. It is a good idea to use the `-O0` flag to not optimize at all
        - `(gdb) set cwd DIR` sets the current working directory of the program being debugged to be DIR
        - `(gdb) set env VAR VALUE` sets the environment variable VAR to have a value of VALUE
        - `(gdb) set disable-randomization off` turns off the randomization of addresses (done to prevent stack overflow attacks)
        - `(gdb) run ARGS` will run the program with ARGS as the command line arguments
        - `(gdb) attach PID` will take control of a process that is running the program being debugged
            - This is typically used to debug an already running program without having to kill it off
        - `(gdb) detach`
        - `(gdb) Ctrl-C` stops the program and lets gdb take control
            - This happens automatically if the program crashes (but does not exit)
        - `(gdb) continue` continues the program (if it is stopped)
        - `(gdb) step` single-step through the source program
            - This can be tricky, as source code does not translate one-to-one with machine code
        - `(gdb) stepi` single-step through the machine code
        - `(gdb) next` single-step through the source, but skip over function calls
        - `(gdb) fin` finish the current function and then stop
        - `(gdb) b LOCATION` sets a breakpoint at LOCATION, which can be a function name, file name and line number, etc. 
            - gdb is able to perform breakpoints (and similar functionality) by modifying the machine code with a special instruction to cause the program to trap (and untrap when continued)
        - `(gdb) watch VAR` sets a watchpoint for VAR, resulting in the program stopping when VAR changes 
            - gdb is able to perform watchpoints by leveraging hardware support for watchpoints
        - `(gdb) p EXPR` will print EXPR (using the same rules as C)
            - This allows you to call functions in the program or even modify existing variables
                - i.e. `p (c -= 15) // decrements c by 15`
        - `(gdb) checkpoint` saves the state of the process and allows for it to be restarted from that checkpoint later 
        - `(gdb) rc` performs a reverse continue until the most recent breakpoint is found (this creates checkpoints)
        - `(gdb) target` is used for cross-debugging - on an x84, ARM, or MIPS device via a virtual computer 
