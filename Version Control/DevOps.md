# DevOps
- An even simpler type of version control involves using **backups**
- Backups are necessary to mitigate system failures (i.e. hard drive failure, database table drops, outside attacks, cosmic ray bit flips)
    - It can be important to know how commonly system failures occur - as this can be useful knowledge when considering how frequently to backup. For hard drives, this is known as the  **Annualized Failure Rate (AFR)** - which is the estimated percentage of drives that fail in a year 
- A naive backup system involves periodically making a copy of the whole state (the necessary parts of the system). To recover from a failure, the most recent backup (usually) is copied to the current state
    - It is important to test one's recovery strategy (if it does not work when there is time to backup)
    - This does not scale very well
- Ideally, one should backup both RAM + registers and secondary storage
    - Usually, though, it is difficult to back up RAM (since RAM so frequently changes), so backups tend to involve secondary storage
    - What is usually saved is the contents of the files, the metadata concerning the files (owners, permissions, directory structure, etc.), the partitions (file system layout)
        - An alternate school of thinking involves saving all of the blocks of data (which contain the aforementioned information)
- When performing backups, if you choose to idle the system when backing up, then the entire backup process is simplified since the state of the system is not being changed during the backup process
    - If the system is not chosen to idle during backups, then backups are much more complicated to perform - as it could not match if the state of the system is changed during the process
        - i.e. `tar -cf /backup/file *` would backup everything in the current directory, but this would be different if the files were being modified as the command runs 
- Another question to ask if that of how many backups should be retained (at least two, to account for the case where the system crashes during the backup process)
- To perform backups more cheaply:
    - Do them less often
    - Perform backups to a cheaper device (hdd instead of ssd)
    - Backup to a remote service 
    - Perform incremenetal backups where, instead of creating an entire copy of the state of the system, just the changes between backups are stored - this can be done by keeping track of the timestamps when the data is changed 
        - This results in a more difficult recovery strategy, as you must first go to the first full backup, and then apply the changes
            - It is more common, then, to occasionally do full backups alongside incremenetal backups 
- Backup Optimizations:
    - One way to optimize backups is through the usage of deduplication (copy-on-write)
        - Effectively, this involves not making a copy but instead storing a reference to the original file and, when the original file is changed, updating the copy to retain the original version
            - This approach is faster and takes less space, but is not as good for backups (usually) because it depends on the original file not corrupting (better for possible software failures rather than hardware failures)
    - Another way to optimize backups is to compress the files (usually at the block level or file level)
    - Another optimization strategy is multiplexing, where a single backup device is used to backup many different systems
    - Another optimization strategy involves staging - first backup to flash, and then disk, and then optical tape
    - Another strategy involves data grooming, where old, junk data is removed before backups
- Backups can be checksummed (checksums are backed up somewhere else) to ensure integrity and also encrypted to ensure it is not misused 
- It would be nice to have file systems with backups built-in (Git without having to do any commands)
    - One way to do this is via the versioning approach, where each "version" of the file is stored - you can view each old version 
        - An issue with this is to choose *when* to automatically create a new version for a file (it may be overkill to do it for every single change to the file)
            - One way may to do it every time the file is closed and a change has been made
    - Another way is to take a consistent snapshot of the entire file system periodically, using the copy-on-write strategy 