1. df — Disk Free
Shows how much disk space is used and available.
df -h


Filesystem  → the disk or partition name
Size        → total disk size
Used        → how much is used
Avail       → how much is free
Use%        → percentage used
Mounted on  → where it is attached (/ = root)


2. free — Free Memory (RAM)
Shows how much RAM and swap is used and available.

free -h             (-h humain redable)


total      → total RAM installed
used       → RAM currently in use
free       → completely unused RAM
buff/cache → RAM used by system cache (can be freed)
available  → RAM actually available for new programs


3. nproc — Number of Processors
Shows how many CPU cores your system has. Very simple command.

nproc


4. top — Live System Monitor
Shows a live real-time view of everything happening on your system — CPU, RAM, and all running processes.
top


Reading the process columns:
PID     → process ID number
USER    → who is running it
%CPU    → how much CPU it is using
%MEM    → how much RAM it is using
COMMAND → the program name

Keyboard shortcuts inside top:
bashq           → quit top
k           → kill a process (type PID then Enter)
M           → sort by memory usage
P           → sort by CPU usage
u           → filter by username
1           → show individual CPU cores
h           → help

