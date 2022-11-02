# Git Overview
- Git maintains three things:
    - Git maintains the working files (source code) of the project that Git will keep track of
    - Git maintains an object database that records the history of the development of the project (a tree of files)
        - This is contained in the `.git` directory
    - Git maintains a staging area (index) that records possible future changes 
        - `git commit` appends whatever is in the index and appends it to the history and clean out the index (since it is already recorded)

# Starting a Git Project
- `git init` sets up an empty repository and index in the current directory
    - This isn't common, you will more often work with other projects
- `git clone [URL]` clones the entire database of an existing project into your own device and creates the corresponding working files 

# Making Changes
- After editing the working files (add code, remove code, etc.), use `git add FILE1 FILE2 ...` adds the files FILE1, FILE2, etc. to the index 
    - After adding a file to an index, if you make an edit, you must do `git add` again with those files as arguments since index only tracks the state of the file as it was added (not as it changes)
- After a satisfactory amount of files have been added `git commit` can be used to actually save those changes to the repository's history
    - `git commit` actually changes the commit that the HEAD of the Git repository points to 
    - `git commit -m 'MESSAGE' FILE1 FILE2...` will add the files FILE1, FILE2 to the index and then commit them with MESSAGE, all in one command
# Commits
- Each commit in Git has a unique ID, representing a checksum of the contents of the commit, which verifies that the content has not been corrupted as well as provides a unique identifier to a given commit
- Commit messages are important - they indicate **why** a commit was made and **why** it is important
    - Reading through all the code associated with a change can take too much effort, a simple commit message can be more meaningfully understood
    - Commit messages also allow for the history of a project to be better comprehended - as you go back, you can see the motivation for each commit 
    - Comments are meant to explain the workings of code; commit messages are meant to explain the workings behind a change of code 
- A common pattern is a committer and author being different; an individual creates code but another individual can commit it for them
    - Under `git log`, this results in differences in author date and commit date
- Common Commit Message Styling:
    - **First Line**: Subject line containing the most important information (at most 50 characters)
    - **Second Line**: Blank
    - **Body**: Details about certain changes (up to 50 lines, with at most 72 characters per line)

# Tools
- To inspect an existing project, you can:
    - Look at the existing working files - this lets you look at the current state of the project (rather than the history)
- To inspect the history of a project (all of the commits up to the latest revision in reverse order), use `git log`
    - Using `git log` allows you to determine why changes have been made and who made certain changes
    - `git log A..B` shows commits starting from, but not including A and up to and including B (where A and B are the checksum identifiers or the tags for those specific commits)
        - `HEAD` is an identifier for the latest version
        - `V^`, where V is an identifier (tag or checksum), gives the version just before V 
    - `git log B` starts from the very beginning of the project all the way up to B
- `git diff` by default displays the change between the index and working files
    - `git diff --cached` displays the changes between the latest commit and the index
    - `git diff HEAD` displays the changes between the last commit and the working files 
    - `git diff VERSION1..VERSION2` displays the changes between    VERSION1 and VERSION2, which can be either commit IDs or tags (or head, etc.)
- `git ls-files` lists all of the files that are under version control (this will not list files that have been added to version control)
- `git checkout -f` restores files in the working tree to match the version in the index or in the specified tree
- `git grep [PATTERN]` will perform a grep search on all files tracked by Git
- `git status` gives a brief summary of the status of the project (such as changes that are staged, changes that are not staged, files that are not being tracked)
- `git clean` removes all untracked files
    - `git clean -n` will list all files that would be removed, but does not actually remove them
- A `.gitignore` file is used to list all files (or filename patterns) that Git should not track
    - `.gitignore` files themselves can be tracked by Git 
    - It is typical to put executable files, temporary files (those beginning with #), machine code files (.o files, since they are machine dependent and therefore is not portable), imported packages (i.e. node_modules), and passwords/security keys (store them in an environment variable) in your `.gitignore` file
- `git config` is used as a tool for configurations of Git on the device
    - `git config -l` lists all configurations
    - `.git/config` is a file created when a repository is created/cloned, and this configuration can be edited accordingly (though if there is any errors, then Git stop works)
        - `.git/config` is not under version control, so be careful when working with it (any file under the `.git/` is not versioned)
    - There is a `/.gitconfig` file located usually at the home directory that contains global configurations 
- `git show [COMMIT]` indicates the changes that were made for a specific commit, or the most recent commit by default
    - `git show pretty=fuller [COMMIT]` presents more information about the commit (i.e. CommitDate and AuthorDate) 

# Remotes
- When a repository is cloned, it is entirely possible for the upstream source to be changed, causing the current (local) repository to be outdated
- `git fetch origin` allows the repository to be aware of the latest upstream version, but does not actually make any changes to the repository
- `git pull` does the same as `git fetch`, but actually merges the upstream changes into the current repository 
    - If there is a complicated merge, the pull may fail

# Revisions
- If you make a mistake before comitting, you should just remove that mistake from the staging area (if it is not already removed)
- If there has already been a commit, you can:
    - Commit a new, fixed version (old -> broken -> fixed <-- HEAD)
    - Use `git commit --amend` which branches off of the old version with the fixes onto the broken version (old -> broken, old -> fixed <-- HEAD)
        - This presents an issue if the repository is remote, since this changes the structure of the repository tree
    - Use `git reset [COMMIT]` which changes the state of the repository to what it was previously (i.e. `git reset --hard HEAD^`)

# Branching
- Each commit in Git has a parent (except for the first commit), and the most recent commit has a pointer HEAD to it
- Branching involves diverging from a given a commit and making changes independent of different branches
    - One may find themselves making using of branching for creating new features, for test development, and so forth
- A branch can be thought of as a lightweight pointer to the tip of that branch in the tree
- `git checkout -b newbranch HEAD^^` creates a new branch named newbranch based off the third-to-last commit of the current head pointer
- `git branch -d maint` deletes the name of the branch, but all of that branch's commits are still present in the tree 
- When the HEAD pointer is not pointing to any branch, Git is in the 'detached HEAD state', where commits cannot be retained unless a branch is derived from that specific commit 
- Cherry-picking fixes is a pattern where, if a bug is introduced relatively early in the tree but there have already been numerous commits and branches following, proper changes are applied to each branch 
    - While it can be awkward to individually go to each branch and then apply the commit, the process can be automated via shell scripting
    -     git add F
          git commit -m 'Emegency fix!'
          git diff main^! > t.diff
          git checkout maint // switches to maint branch
          patch < t.diff // patch will modify old files to look like the old files, according to the diff of the files (the files being changed are located in the meta portion of the diff output)
          git add F
          git commit -m 'Emergency fix!'
        - This can fail if files that were patched were previously being modified in any of the branches

# Merging
- Merging involves "combining" two branches of development. A merge commit has multiple parents (one from each branch parent)
- `git merge BRANCHNAME` attempts to merge the current branch (where HEAD is) with BRANCHNAME 
- When a merge involves a clash between commits X and Y, Git will try to find the common ancestor of X and Y, A, and "reconcile" them
    - Git will sort of find the changes between A and X (diff A X) and the changes between A and Y (diff A Y)
    - If these differences are independent, then a simple merge can occur where each differences is applied - compute a 3-way merge and replace the working files accordingly
- When the prior approach does not work, and both X and Y modify the same lines of code, then Git will store the collisions in the corresponding files with Git's collision notation and leaves it up to the user to determine which versions of the code to  favor
    -     <<<<<<<
          A 
          =======
          B
          >>>>>>>
          