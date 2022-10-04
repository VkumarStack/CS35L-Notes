# COMP SCI 35L NOTES
## Linux Shell and Shell Scripting
### Linux and Files

Linux is an operating system (OS), like Windows or MacOS, that acts as an interface between hardware and user applications.
  - Linux is written in C and makes use of various C libraries.
  - Linux itself is based on the Unix operating system. There are various distributions ("distros") of Linux that add commonly used packages and applications on top of the Linux kernel. 

Operating systems like Linux are useful because they allow for the management of **persistent** data - that is, data that remains even after a device loses power. This data is represented through files, and these files as a whole are managed through a file system. Linux utilizes a tree-based file system. Directories are effectively containers for files, and these directories may contain directories within themselves (subdirectories), thereby creating a tree structure. The root directory on a Linux system is **/**, and from this root directory, all other files can be found. 

Linux users should be familiar with some common directories and their usages:
  - **/bin** contains executable programs (this includes commands) that are part of the Linux operating system, such as cp, cat, ls, etc.
  - **/dev** contains *representations* of external devices such as flashdrives, keyboards, etc. and are typically used in programs that need to interact with such devices
  - **/lib** contains library files essential to the system
  - **/etc** contains system configuration files and initialization scripts
  - **/home** contains the home for the system's root user (administrator)
  - **/usr** contains files for programs that are intended for the user and are not vital to the core system
    - **/usr/bin** contains executable programas that are not essential to Linux
