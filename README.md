# COMP SCI 35L NOTES
## Linux Shell and Shell Scripting
### Linux and Files

Linux is an operating system (OS), like Windows or MacOS, that acts as an interface between hardware and user applications.
  - Linux is written in C and makes use of various C libraries.
  - Linux itself is based on the Unix operating system. There are various distributions ("distros") of Linux that add commonly used packages and applications on top of the Linux kernel. 

Operating systems like Linux are useful because they allow for the management of **persistent** data - that is, data that remains even after a device loses power. This data is represented through files, and these files as a whole are managed through a file system. Each file is uniquely identifed by an **inode** Linux utilizes a tree-based file system. Directories are effectively containers for files, and these directories may contain directories within themselves (subdirectories), thereby creating a tree structure. The root directory on a Linux system is **/**, and from this root directory, all other files can be found. 

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

Linux is a multiuser operating system. The SEAS Linux server, as an example, contains the files and programs for all students in the class, each of whom have their personal directory. Linux has many mechanisms to prevent users from messing with other users' data. For example, only the **super user** has unrestricted access to the files of all users (to execute a command as a super user, you can do `sudo COMMAND` but this requires entering the password for the system). Another mechanism involves file permissions. In Linux, a file can be read, written, and/or executed, and the permission to do each of these things can be managed with commands such as `chmod`. These permissions consider groups such as the **owner** of the file, members of a **group** that are associated to a file by the superuser, and all **other** users that don't fit the prior two definitions.

Linux is also a multiprocess operating system. Many processes can run in the background. You can run a command in the background by including a `&` after the command. You can put a background task in the foreground using the `fg PID` command, where PID is the process ID of the task (more on that later). Similarly, you can use `bg PID` to put a task running in the foreground to the background.

### Commonly Used Commands
Commands are effectively just executable programs that use the shell as an interface. To run an executable file or command that is in the directory you are currently in, you must prepend that command with `./`. Commonly used commands are found through the system PATH, which is an environment variable that lists all locations that Linux should look for when a command is entered (i.e. if you use `cp` then the system will probably look for it in /bin or /usr/bin, both of which are already on the system's PATH). When developing commands or executable programs meant to use system-wide, it is often useful to append that command to a directory that is already listed in the PATH or to append a new directory containing that command to the system's PATH.

#### Navigation Commands
- `pwd` prints the full path of the current directory
- `cd [DIRECTORY]` changes the current directory to DIRECTORY. 
  - Omitting the argument changes the current directory to the default home directory for the user, which can also be accessed using **~**. So, `cd` is the same as `cd ~`.
  - The DIRECTORY argument can be an absolute path (a directory starting from the root directory all the way to the current directory) or a relative path (a directory relative to the current directory)
  - Using **.** as an option changes the current directory to the current directory (so nothing at all happens basically). Using **..** as an option changes the current directory to its parent directory.
- `ls` lists the contents of the current directory
  - `-a` flag lists any hidden files in the directory (such as . or ..)
  - `-l` flag lists all files in the directory in long format (displays **permissions, number of links, owner, owner group, file size, modification date, and file name**)
  - `-r` flag recursively lists the directory tree (so it will list all the contents of subdirectories and their subdirectories) 
  - `-t` flag lists files by modification time
  - `-S` flag lists files by file size
- `which COMMAND` specifies the file location of COMMAND, as well as any aliases for it
- `command --version` usually lists the version that the command (or executable program) is running on
- `find [PATH] [OPTION] [EXPRESSION]` starting from PATH, find files that meet the criteria specified in OPTION, and perform EXPRESSION those found files
  - Common Options (for any numberic arguments NUM, +NUM is used for arguments greater than NUM, NUM is used for arguments equal to NUM, and -NUM is used for arguments less than NUM):
    - `-type T` where T is a character representing the type (d for directory, f for regular file, l for symbolic link, etc.) will only find files that match the type T
    - `-name NAME` will only find files that match NAME (this is the name of the file not the name of the entire directory path to the file)
    - `-iname NAME` case insensitive -name
    - `-inum NUM` will find file that has inode number NUM
    - `-executable` will find files that are executable
    - `-regex PATTERN` will find the **WHOLE PATH** that matches the Regex PATTERN 
    - `-mtime NUM` will find files that were modified NUM*24 hours ago
    - `-mmin NUM` will find files that were modified NUM minutes ago
    - `-links NUM` will find files that have NUM links
  - Common Expressions:
    - `-delete` will delete each file found
    - -`-exec COMMAND {} \;` will execute the command COMMAND with each file found as an argument
#### File Management Commands
- `touch FILENAME` updates the modification of FILENAME if it exists and creates an empty file named FILENAME if it otherwise does not already exist
  - To actually edit a file, use a text editor such as Emacs, Vim, or Nano
- `mkdir DIRNAME` creates a directory originating in the current directory
- `rm NAME` removes the file NAME
  - `-r` will recursively remove the directory NAME, meaning that it will also remove everything under the directory **BE CAREFUL WHEN USING THIS** 
- `rmdir DIRNAME` removes the empty directory DIRNAME; if DIRNAME is not empty, then the command fails
- `mv SOURCE DESTINATION` moves the file SOURCE to DESTINATION. If DESTINATION is a directory, the file will now a subdirectory of DESTINATION. IF DESTINATION is a file, then SOURCE will be renamed to SOURCE (if SOURCE already exists, then it will be overriden).
- `cp SOURCE DESTINATION` copies the file SOURCE to DESTINATION. If DESTINATION is a directory, the copied file will be a subdirectory of DESTINATION. Otherwise, DESTINATION will be copied to the current directory.
- 
