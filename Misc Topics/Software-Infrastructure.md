# Software Infrastructure
- Interpreters:
    - The source code is first checked for syntax, and then translated into an intermediate form that can be more easily executed (i.e. tree structure with symbol table)
    - The interpreter is a program (written in a low level language) that walks through the intermediate form and executes the code
        - Compared to bytecode compilation, this approach is faster for translating the code into the intermediate form
        - This approach is also easier to debug
    - An alternative approach involves translating to bytecode - which is a small subset of instructions that is NOT machine code 
        - This is more efficient than working through the intermediate form (i.e. recursively going through the tree) but is not as efficient as compiling to machine code because bytecode is not platform specific (and cannot leverage all the hardware specific instructions)

- Compilers:
    - Compilers convert source code to machine code (dependent on the platform, i.e. x86-64)
        - Compilation takes longer to translate, but they allow for much better performance 
        - Compiled programs are more difficult to debug (though it is still obviously possible through tools such as GDB)
        - Everything is compiled, even if that code won't be run in the actual program
    - When designing a compiler, there is a question of portability - what platform (architecture) can the compiler run on and generate code to
        - The *host* is the platform that the compiler is *run on*
        - The *target* is the platform that the compiler *builds code for*
            - GCC assumes that the platform has a flat address space (each pointer is the same width) and can address any part of memory using pointers 
            - GCC assumes support for 8-bit bytes and 16-bit words (higher sizes are optional)
        - The *build* is the platform that the *compiler is actually compiled on*
            - Usually, GCC is compiled by itself
        - Compiler design (i.e. GCC) is modularized - split between code that is machine independent and machine dependent 
            - It is also modularized between languages - split between code that is language independent (algorithms for all languages) and language dependent (i.e. only for C or only for Java)
        - GCC makes use of a separate programming language for reading machine description files, which can be used to convert to C code that can be used for compiling 
            - This language is similar to Lisp

- Just-in-time Compilers: 
    - Convert to bytecode and have subroutines that compile bytecodes to machine code 
    - Typically, the functions that are run a lot are just-in-time compiled so that the program is more efficient during the next run