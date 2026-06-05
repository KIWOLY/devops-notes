What is Linux?
   Linux is an open source operating system — just like Windows or macOS, but free and highly customizable.


Most servers in the world run Linux because it is:

1. Free
2. Secure
3. Fast and lightweight
4. Highly configurable

Linux Distributions (Distros)
Linux comes in many flavors:

   Distro                       Used For
   Ubuntu                       Most popular for servers & learning
   Amazon Linux                 Default on AWS EC2
   CentOS / RHEL                Enterprise environments
   Debian                       Stable, minimal servers
   Alpine                       Inside Docker containers


What is Shell?
   The shell is a program that takes your commands and tells the OS what to do. It is the language you speak to Linux.

You type a command → Shell interprets it → Linux executes it

Think of it like this:

     Linux is the engine
     Shell is the steering wheel

Types of Shells

Shell              Notes
bash               Most common, default on Ubuntu/Amazon Linux
zsh                Popular on Mac, more features than bash
sh                 Basic, minimal shell
fish               Beginner friendly, colorful


What is Bash?

Bash is both a shell (command interpreter) and a scripting language. It was created in 1989 and is the default shell on almost every Linux server.


Essential Linux Commands
Navigation:

bashpwd           # where am I?
ls            # list files
ls -la        # list with details + hidden files
cd /var/log   # change directory
cd ..         # go up one level
cd ~          # go to home directory


Files & Folders:

bashtouch file.txt      # create empty file
mkdir myfolder          # create directory
cp file.txt backup.txt  # copy file
mv file.txt /tmp/       # move file
rm file.txt             # delete file
rm -rf myfolder/        # delete folder (careful!)
cat file.txt            # view file content
nano file.txt           # edit file (beginner friendly)
vim file.txt            # edit file (powerful, used in servers)


Permissions:

bashls -la              # see permissions
chmod 400 key.pem       # set permissions (like we did!)
chmod 755 script.sh     # owner=rwx, group=rx, others=rx
chown ubuntu:ubuntu file.txt  # change owner



System Info:
bashtop                 # live process monitor
htop                    # better version of top
df -h                   # disk space
free -h                 # RAM usage
uname -a                # OS info
whoami                  # current user

Networking:

bashping google.com         # test connection
curl ifconfig.me        # get your public IP
netstat -tulpn          # see open ports
ss -tulpn               # modern version of netstat


Search:

bashfind / -name "file.txt"         # find a file
grep "error" /var/log/syslog    # search inside file
grep -r "keyword" /var/log/     # search recursively