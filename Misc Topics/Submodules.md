# Submodules
- Consider when your project needs some other project's source code
    - 1) This needs to be buildable even on platforms where the other project's source code is absent and cannot be installed
    - 2) Source-code only packages are used for portability (emulate missing code)
- `git submodule add URL` will create an empty subdirectory and a `.gitmodules` file 
    - `git submodule init` 
    - `git submodule update` updates the submodule
    - This approach allows for the project to evolve alongside the submodule (if it is continually updating)
        - One can perform controlled updates to the submodule (specify which submodule commit to update)