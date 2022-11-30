# Dependencies
- Build-time dependencies are dependencies where, say, file x depends on file y
    - This can be managed via Makefiles, where file x is created given y
        - There can be many more, complex dependencies that have to be accounted for 
        - In the case of Makefile, it is necessary to appropriately match the dependencies listed on the Makefile alongside the dependencies listed in the actual source files (have the appropriate `#include` statements in the C files, as an example) - usually, tools such as Make and CMake have preprocessors to determine this beforehand
- Packaging dependencies are dependencies where packages can be installed independently
    - However, packages may depend on other packages