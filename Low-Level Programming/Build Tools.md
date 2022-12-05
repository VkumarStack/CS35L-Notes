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

# Makefile Syntax

     # variables can be set during the make command
     # i.e. make target variable=VALUE2
     variable=VALUE
     folder=dir

     target: dependency1 dependency2 ...
        ...script to build target...

     # Dereference variable as with shell
     target2: 
        $variable
    
     # Targets can be be in directories
     $(dir)/target3: 
        script

     # $@ expands to the targetname
     # $< expands to the first dependency
     # $^ expands to a list of dependencies
     # the % wildcard applies a rule to multiple files correspondingly named
     %.o: %.cpp %.h
        script

     # .PHONY for rules that do not correspond to a file - like build or clean
     .PHONY: build clean