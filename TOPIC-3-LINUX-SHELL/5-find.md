find is one of the most powerful Linux commands.find is one of the most powerful Linux commands. It searches for files and directories based on any criteria you give it.

Basic syntax
bashfind  WHERE  OPTIONS  WHAT
find  /home  -name    "file.txt"

1. Find by name
bash# Find exact filename
find / -name "first-shell.sh"

# Find anywhere in home folder
find ~ -name "first-shell.sh"

# Find in current directory
find . -name "first-shell.sh"

# Case insensitive search
find / -iname "First-Shell.SH"

# Find by extension
find / -name "*.sh"       # all shell scripts
find / -name "*.log"      # all log files
find / -name "*.pem"      # all AWS keys
find ~ -name "*.py"       # all python files

2. Find by type
bash-type f    # files only
-type d    # directories only
-type l    # symbolic links only

# Find all directories named logs
find / -type d -name "logs"

# Find all files ending in .sh
find ~ -type f -name "*.sh"

# Find all symbolic links
find /etc -type l



find . -name "file.txt"          → find by name
find . -type f                   → find files only
find . -type d                   → find directories only
find . -size +100M               → find large files
find . -mtime -7                 → modified last 7 days
find . -user kiwoly              → owned by user
find . -perm 777                 → find by permission
find . -name "*.log" -exec rm {} + → find and delete
find / -name "*.sh" 2>/dev/null  → suppress errors



They do the same thing BUT:
find . -name "*.sh"              # shows ALL output including errors
find . -name "*.sh" 2>/dev/null  # hides error messages

What is 2>/dev/null?
First understand file descriptors — Linux has 3 streams:
0 = stdin   → input  (keyboard)
1 = stdout  → output (normal results)
2 = stderr  → errors (error messages)
So:
bash2>          → redirect error stream
/dev/null   → a black hole (deletes everything sent to it)

2>/dev/null → throw all errors into the black hole


command > file        # redirect stdout (1) to file
command 2> file       # redirect stderr (2) to file
command > file 2>&1   # redirect both stdout and stderr to file
command 2>/dev/null   # throw errors away
command &>/dev/null   # throw everything away (stdout + stderr)
