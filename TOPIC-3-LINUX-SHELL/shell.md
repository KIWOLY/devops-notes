What is Shell?
   The shell is a program that takes your commands and tells the OS what to do. It is the language you speak to Linux.

You type a command → Shell interprets it → Linux executes i


What is shell scripiting 
     Shell scripting is essentially writing a series of commands in a file to automate tasks — instead of typing commands one by one every time.

Simple Analogy

Imagine every morning you do this manually:
    #!/bin/bash
    sudo apt update
    sudo apt upgrade -y
    sudo systemctl restart nginx
    echo "Done!"

Instead of typing 4 commands every time, you put them in a file and run it once:
bash ./morning-setup.sh    ( we start with bash cause we did not give permision)
That file = a shell script.


what is chmod 
    chmod = change + mode (permissions mode)

    It is a Linux command that controls who can read, write, or execute a file.

    Three types of people in Linux
Every file has 3 groups of people:
Owner   → the person who created the file (YOU)
Group   → a team of users on the system
Others  → everyone else

Three types of permission
Each group can have 3 types of access:
r = read     → can open and view the file
w = write    → can edit and modify the file
x = execute  → can run the file (scripts, programs)


r = 4  (read)
w = 2  (write)
x = 1  (execute)
- = 0  (nothing)

rwx = 4+2+1 = 7   (full access)
rw- = 4+2+0 = 6   (read + write)
r-x = 4+0+1 = 5   (read + execute)
r-- = 4+0+0 = 4   (read only)
--- = 0+0+0 = 0   (no access)

chmod 755
       │││
       ││└── others = 5 = r-x
       │└─── group  = 5 = r-x
       └──── owner  = 7 = rwx

/first-shell.sh    → looked in ROOT / folder — wrong place
./first-shell.sh   → looked in current folder — correct! ✅
first-shell.sh     → searched PATH folders — not there!


What is #!/bin/bash?
It is called a Shebang (also written as sha-bang or hashbang). It is always the very first line of every shell script.

#!          /bin/bash
│           │
│           └── path to the interpreter (bash)
│
└── shebang characters (hash + exclamation)


What Does it Actually Do?
When you run a script, Linux reads the first line to know which program should execute this file.


Different Shebangs for Different Languages
The shebang changes depending on what language your script is written in:

#!/bin/bash          # bash script
#!/bin/sh            # basic sh shell
#!/usr/bin/python3   # python script
#!/usr/bin/env node  # nodejs script
#!/usr/bin/env ruby  # ruby script
#!/usr/bin/perl      # perl script

What Shell Scripting Can Do
    1. Automate Server Setup
    2. Automate Backups
    3. Monitor a Service
    4. Deploy an Application


Common Shell Scripting Tools Used Together
These commands are used heavily inside scripts:

Command          What it does
echo             Print text
grep             Search text
awk              Process & extract columns from text
sed               Find and replace text
cut                Cut sections of text
sort             Sort output
wc               Count lines/words
tee               Print AND save to file
xargs             Pass output as arguments