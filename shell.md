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
bash./morning-setup.sh 
That file = a shell script.





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