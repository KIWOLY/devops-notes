# Disk, Storage & Package Management

---

## Disk Usage: df & du

`df` reports space per mounted filesystem (see [`df` in 3-bash.md](3-bash.md)); `du` reports space used by specific files/folders.

```bash
du -h                  # disk usage for files/folders
du -sh /var/log         # summary size of a folder
du -sh * | sort -rh     # size of everything in the current folder, largest first
```

> `df -h` shows overall disk pressure; `du -sh /*` shows where the space actually went — use both together when a server runs low on disk.

---

## Partitions & Mounting

```bash
lsblk                              # list disks/partitions as a tree
fdisk -l                            # detailed partition info (needs sudo)
mount                               # show currently mounted filesystems
sudo mount /dev/sdb1 /mnt/data      # manually mount a partition
sudo umount /mnt/data                # unmount it
```

`/etc/fstab` defines which partitions mount automatically at boot:

```bash
cat /etc/fstab
```

Format: `device  mount_point  filesystem_type  options  dump  pass`

> Relevant for cloud servers: when attaching extra block storage (e.g. on Oracle Cloud), add an fstab entry so it remounts automatically after a reboot.

---

## Archiving & Compression

```bash
tar -cvf archive.tar folder/        # create an archive (no compression)
tar -xvf archive.tar                 # extract it
tar -czvf archive.tar.gz folder/     # create + gzip compress (most common)
tar -xzvf archive.tar.gz             # extract a gzipped archive
gzip file.txt                         # compress a single file
gunzip file.txt.gz                    # decompress it
zip -r archive.zip folder/            # zip format
unzip archive.zip
```

Flag reference: `c`=create, `x`=extract, `v`=verbose, `z`=gzip, `f`=file (always last, before the filename).

---

## Package Management (apt)

```bash
sudo apt update                      # refresh available package list (no installs)
sudo apt upgrade                     # upgrade installed packages
sudo apt install nginx                # install a package
sudo apt remove nginx                 # remove package, keep config
sudo apt purge nginx                  # remove package and its config
sudo apt autoremove                    # clean up unused dependencies
apt list --installed | grep nginx     # check if a package is installed
apt-cache search postgresql            # search packages by keyword
```

> Always run `apt update` before `apt install` on a fresh session to avoid stale package list errors.
