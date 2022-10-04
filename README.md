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

A link is a way to access a file. Most links that you will see on Linux are **hard links**, meaning that they link the specified name of the file and the *actual data of the file itself*. On the other hand, a **soft link**, also known as a **symbolic link**, stores the path to a file but does link to the actual file itself - you can think of them soft links as pointing to hard links.
  - Soft links can link to a file or a directory. Hard links cannot link to directories (as this could create a loop in the tree stucture of the file system which then messes with any navigation commands like `find`).
  - Deleting a soft link does not delete the original file. To delete the original file, you must delete all hard links to that file.

Linux is a multiuser operating system. The SEAS Linux server, as an example, contains the files and programs for all students in the class, each of whom have their personal directory. Linux has many mechanisms to prevent users from messing with other users' data. For example, only the **super user** has unrestricted access to the files of all users (to execute a command as a super user, you can do `sudo COMMAND` but this entering the password for the system). Another mechanism involves file permissions. In Linux, a file can be read, written, and/or executed, and the permission to do each of these things can be managed with commands such as `chmod`. These permissions consider groups such as the **owner** of the file, members of a **group** that are associated to a file by the superuser, and all **other** users that don't fit the prior two definitions.
