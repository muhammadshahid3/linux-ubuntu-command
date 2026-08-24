# Ubuntu File & Filesystem Commands (Deep Guide)

This document explains the essential Ubuntu/Linux commands for navigating, managing, and inspecting files, directories, disks, and filesystems — what each command **can do**, what it **cannot do**, and practical examples for each.

---

## 📑 Index

| # | Command | Category | Jump |
|---|---------|----------|------|
| 1 | `pwd` | Navigation | [Go](#1-pwd-) |
| 2 | `ls` | Navigation | [Go](#2-ls-) |
| 3 | `cd` | Navigation | [Go](#3-cd-) |
| 4 | `mkdir` | File/Dir Management | [Go](#4-mkdir-) |
| 5 | `touch` | File/Dir Management | [Go](#5-touch-) |
| 6 | `cp` | File/Dir Management | [Go](#6-cp-) |
| 7 | `mv` | File/Dir Management | [Go](#7-mv-) |
| 8 | `rm` | File/Dir Management | [Go](#8-rm-) |
| 9 | `rmdir` | File/Dir Management | [Go](#9-rmdir-) |
| 10 | `find` | Searching | [Go](#10-find-) |
| 11 | `locate` | Searching | [Go](#11-locate-) |
| 12 | `df` | Disk & Storage | [Go](#12-df-) |
| 13 | `du` | Disk & Storage | [Go](#13-du-) |
| 14 | `mount` | Disk & Storage | [Go](#14-mount-) |
| 15 | `umount` | Disk & Storage | [Go](#15-umount-) |
| 16 | `lsblk` | Disk & Storage | [Go](#16-lsblk-) |
| 17 | `blkid` | Disk & Storage | [Go](#17-blkid-) |
| 18 | `findmnt` | Disk & Storage | [Go](#18-findmnt-) |
| 19 | `stat` | File Inspection | [Go](#19-stat-) |
| 20 | `file` | File Inspection | [Go](#20-file-) |
| 21 | `nano` | Text Editors | [Go](#21-nano-) |
| 22 | `vim` | Text Editors | [Go](#22-vim-) |
| 23 | `cat` | Bonus | [Go](#23-cat-) |
| 24 | `less` | Bonus | [Go](#24-less-) |

Also see: [Quick Reference Table](#quick-reference-table) · [Real-World Troubleshooting Example](#real-world-troubleshooting-example)

---

## Navigation Commands

### 1. `pwd` ⭐⭐⭐⭐⭐
**P**rint **W**orking **D**irectory — shows your current location in the filesystem.

**Can do:** Show the full absolute path of where you currently are.
**Cannot do:** Doesn't show anything about files/contents — only the path itself.

```bash
pwd                    # e.g. output: /home/user/projects
pwd -P                 # show the physical path (resolves symlinks)
```

---

### 2. `ls` ⭐⭐⭐⭐⭐
Lists files and directories.

**Can do:** Show file/folder names, permissions, sizes, ownership, dates.
**Cannot do:** Doesn't show file *contents* — only listings/metadata.

```bash
ls                     # basic listing
ls -lah                # long format + hidden files + human-readable sizes
ls -la /etc            # detailed listing of a specific directory
ls -lt                 # sort by modification time (newest first)
```

---

### 3. `cd` ⭐⭐⭐⭐⭐
**C**hange **D**irectory — moves between folders.

**Can do:** Navigate to any directory you have permission to access.
**Cannot do:** Cannot list contents or create directories on its own.

```bash
cd /var/log            # go to an absolute path
cd ..                  # go up one level
cd ~                   # go to home directory
cd -                   # go back to the previous directory
```

---

## File & Directory Management

### 4. `mkdir` ⭐⭐⭐⭐
**M**a**k**e **Dir**ectory — creates new directories.

**Can do:** Create one or multiple directories, including nested ones.
**Cannot do:** Cannot create files (only directories) — use `touch` for files.

```bash
mkdir projects                    # create a single directory
mkdir -p projects/app/src         # create nested directories in one go
mkdir dir1 dir2 dir3               # create multiple directories at once
```

---

### 5. `touch` ⭐⭐⭐⭐
Creates empty files, or updates a file's timestamp if it already exists.

**Can do:** Create empty files instantly; update modification/access time.
**Cannot do:** Cannot add actual content to a file.

```bash
touch file.txt                    # create an empty file
touch file1.txt file2.txt         # create multiple files at once
touch -m file.txt                 # update only the modification time
```

---

### 6. `cp` ⭐⭐⭐⭐⭐
**C**o**p**y — copies files and directories.

**Can do:** Copy files, preserve permissions/timestamps, copy entire directory trees.
**Cannot do:** By default, doesn't copy directories unless `-r` is used (will error out).

```bash
cp file.txt backup.txt            # copy a file with a new name
cp -r project/ project_backup/    # copy an entire directory recursively
cp -v file.txt /tmp/               # verbose mode — shows what's being copied
cp -p file.txt /tmp/               # preserve permissions and timestamps
```

---

### 7. `mv` ⭐⭐⭐⭐⭐
**M**o**v**e — moves or renames files and directories.

**Can do:** Move files between locations; rename files/folders (mv is also the "rename" command in Linux).
**Cannot do:** Doesn't create a copy — the original is gone from its old location once moved.

```bash
mv file.txt /home/user/Documents/     # move a file to another directory
mv oldname.txt newname.txt            # rename a file
mv dir1/ dir2/                        # rename/move an entire directory
```

---

### 8. `rm` ⭐⭐⭐⭐⭐
**R**e**m**ove — deletes files and directories.

**Can do:** Permanently delete files/directories.
**Cannot do:** No recycle bin/undo — deleted files are **not** recoverable by default. Extreme caution needed with `-rf`.

```bash
rm file.txt                       # delete a single file
rm -r project/                    # delete a directory and its contents recursively
rm -rf project/                   # force delete, no confirmation (use with extreme care)
rm -i file.txt                    # ask for confirmation before deleting
```

> ⚠️ **Warning:** `rm -rf /` or `rm -rf *` in the wrong directory can destroy your entire system. Always double-check the path before running `rm -rf`.

---

### 9. `rmdir` ⭐⭐⭐
**R**e**m**ove **Dir**ectory — deletes **empty** directories only.

**Can do:** Safely remove empty folders.
**Cannot do:** Cannot delete a directory that has files/subfolders inside (use `rm -r` for that).

```bash
rmdir empty_folder                # remove an empty directory
rmdir -p a/b/c                    # remove nested empty directories
```

---

## Searching Files

### 10. `find` ⭐⭐⭐⭐⭐
Searches for files/directories based on name, type, size, time, permissions, etc. Searches in **real-time** across the actual filesystem.

**Can do:** Very powerful and flexible — search by name, extension, size, modified date, permissions, and even execute commands on results.
**Cannot do:** Can be slow on large filesystems since it scans live (unlike `locate`, which uses a pre-built index).

```bash
find / -name "file.txt"                    # search entire system by name
find . -type f -name "*.log"                # find all .log files in current directory
find . -type d -name "node_modules"         # find all directories named node_modules
find /var/log -mtime -1                     # files modified in the last 1 day
find . -size +100M                          # find files larger than 100MB
find . -name "*.tmp" -delete                # find and delete matching files
```

---

### 11. `locate` ⭐⭐⭐⭐
Searches for files using a pre-built database index — much faster than `find`.

**Can do:** Very fast filename searches across the whole system.
**Cannot do:** Relies on an index (`mlocate` database) that updates periodically — won't show very recently created files until the database is refreshed.

```bash
locate file.txt                   # quick search by filename
locate "*.conf"                   # search using a wildcard pattern
sudo updatedb                     # manually refresh the locate database
```

> **Note:** If `locate` isn't installed: `sudo apt install mlocate`

---

## Disk & Storage Commands

### 12. `df` ⭐⭐⭐⭐⭐
**D**isk **F**ree — shows disk space usage at the filesystem/partition level.

**Can do:** Show total, used, and available space per mounted filesystem.
**Cannot do:** Doesn't show which specific files/folders are consuming space (use `du` for that).

```bash
df -h                              # human-readable disk usage (GB/MB)
df -h /                            # usage of the root partition only
df -T                              # show filesystem type along with usage
```

---

### 13. `du` ⭐⭐⭐⭐⭐
**D**isk **U**sage — shows how much space specific files/directories are using.

**Can do:** Break down space usage folder-by-folder or file-by-file.
**Cannot do:** Doesn't show total disk capacity/free space (use `df` for that).

```bash
du -sh /var/log                    # total size of a directory (summary, human-readable)
du -h --max-depth=1 /home          # size of each subdirectory, one level deep
du -ah . | sort -rh | head -10     # top 10 largest files/folders in current directory
```

---

### 14. `mount` ⭐⭐⭐⭐
Attaches a filesystem (disk, partition, USB, network share) to a directory so it can be accessed.

**Can do:** Mount disks, ISO files, network shares, etc., to a mount point.
**Cannot do:** Cannot mount to a directory that doesn't exist yet — the mount point must exist first.

```bash
mount                              # list all currently mounted filesystems
sudo mount /dev/sdb1 /mnt          # mount a partition to /mnt
sudo mount -o loop image.iso /mnt/iso   # mount an ISO file
```

---

### 15. `umount` ⭐⭐⭐⭐
Detaches (unmounts) a mounted filesystem.

**Can do:** Safely disconnect a mounted disk/partition before removal.
**Cannot do:** Will fail with "device busy" if a file/process on that mount is still in use.

```bash
sudo umount /mnt                   # unmount by mount point
sudo umount /dev/sdb1              # unmount by device name
sudo umount -l /mnt                # lazy unmount (detach as soon as it's not busy)
```

---

### 16. `lsblk` ⭐⭐⭐⭐⭐
**L**i**s**t **Bl**ock devices — shows all disks and partitions in a tree structure.

**Can do:** Quickly see all disks, partitions, and their mount points.
**Cannot do:** Doesn't show filesystem type or UUID by default (combine with `blkid` for that).

```bash
lsblk                              # tree view of all disks/partitions
lsblk -f                           # include filesystem type and label
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT # customize which columns to show
```

---

### 17. `blkid` ⭐⭐⭐⭐
Shows block device attributes — UUID, filesystem type, label.

**Can do:** Get the UUID of a partition (essential for `/etc/fstab` entries).
**Cannot do:** Doesn't show mount points or disk usage (use `lsblk`/`df` for that).

```bash
sudo blkid                         # list UUID/type for all block devices
sudo blkid /dev/sda1                # info for a specific partition
```

---

### 18. `findmnt` ⭐⭐⭐⭐
Shows currently mounted filesystems in a readable tree format, and can verify `/etc/fstab` entries.

**Can do:** Search/filter mounted filesystems; verify if a specific mount is active.
**Cannot do:** Only shows mount-related info — not general disk usage.

```bash
findmnt                            # tree view of all mounts
findmnt /                          # check details of the root mount
findmnt --verify                   # verify /etc/fstab entries are valid
```

---

## File Inspection

### 19. `stat` ⭐⭐⭐⭐
Shows detailed metadata about a file — size, permissions, timestamps, inode.

**Can do:** Show precise creation/modification/access times, permissions in octal, inode number.
**Cannot do:** Doesn't show file content — metadata only (use `cat`/editors for content).

```bash
stat file.txt                      # full metadata of a file
stat -c "%a %n" file.txt            # show only permissions (octal) and filename
stat /                              # metadata of the root directory
```

---

### 20. `file` ⭐⭐⭐⭐
Identifies the actual type of a file by inspecting its content (not just the extension).

**Can do:** Correctly detect file type even if the extension is wrong or missing.
**Cannot do:** Doesn't show internal details like `stat` (permissions/timestamps) — only the type.

```bash
file document.pdf                  # e.g. output: PDF document, version 1.7
file script.sh                     # e.g. output: Bourne-Again shell script text
file *                             # check type of every file in current directory
```

---

## Text Editors

### 21. `nano` ⭐⭐⭐⭐⭐
A simple, beginner-friendly terminal text editor.

**Can do:** Easy to use — shortcuts are shown at the bottom of the screen at all times.
**Cannot do:** Lacks advanced features of `vim` (like macros, complex scripting, modal editing power).

```bash
nano file.txt                      # open (or create) a file for editing
```

**Common shortcuts inside nano:**
| Shortcut | Action |
|----------|--------|
| `Ctrl + O` | Save (Write Out) |
| `Ctrl + X` | Exit |
| `Ctrl + K` | Cut a line |
| `Ctrl + U` | Paste |
| `Ctrl + W` | Search |

---

### 22. `vim` ⭐⭐⭐⭐⭐
A powerful, modal text editor — steeper learning curve but extremely efficient once learned.

**Can do:** Extremely powerful editing, macros, scripting, split windows, plugins.
**Cannot do:** Not beginner-friendly — has separate modes (Normal, Insert, Visual) that confuse new users.

```bash
vim file.txt                       # open (or create) a file for editing
```

**Basic vim workflow:**
```bash
i           # enter Insert mode (start typing)
Esc         # go back to Normal mode
:w          # save
:q          # quit
:wq         # save and quit
:q!         # quit without saving (discard changes)
```

> **Tip:** If you're new to Linux, start with `nano`. Learn `vim` once you're comfortable — it's a huge productivity boost for server work since it's available on nearly every Linux system by default.

---

### 23. `cat` ⭐⭐⭐⭐⭐
Bonus — extremely common alongside file commands. Displays file content directly in the terminal.

**Can do:** Quickly view small file contents; combine (concatenate) multiple files.
**Cannot do:** Not practical for large files (floods the terminal) — use `less` for that.

```bash
cat file.txt                       # display file content
cat file1.txt file2.txt > combined.txt   # merge two files into one
cat -n file.txt                    # display with line numbers
```

---

### 24. `less` ⭐⭐⭐⭐
Bonus — views large files page-by-page without loading the whole file into memory.

**Can do:** Scroll through huge files (like logs) efficiently, search within the file.
**Cannot do:** Read-only — cannot edit the file.

```bash
less /var/log/syslog               # view a large log file page by page
```
**Inside `less`:** `/searchterm` to search, `q` to quit, arrow keys/Page Up/Down to scroll.

---

## Quick Reference Table

| Command | Purpose |
|---------|---------|
| `pwd` | Show current directory |
| `ls` | List files/directories |
| `cd` | Change directory |
| `mkdir` | Create directory |
| `touch` | Create empty file / update timestamp |
| `cp` | Copy files/directories |
| `mv` | Move or rename files/directories |
| `rm` | Delete files/directories |
| `rmdir` | Delete empty directories |
| `find` | Search files (real-time scan) |
| `locate` | Search files (indexed, fast) |
| `df` | Disk space usage (per filesystem) |
| `du` | Disk usage (per file/folder) |
| `mount` | Attach a filesystem |
| `umount` | Detach a filesystem |
| `lsblk` | List block devices/partitions |
| `blkid` | Show UUID/filesystem type |
| `findmnt` | Show/verify mounted filesystems |
| `stat` | Show detailed file metadata |
| `file` | Identify file type by content |
| `nano` | Simple terminal text editor |
| `vim` | Advanced modal text editor |
| `cat` | Display file content |
| `less` | Page through large files |

---

## Real-World Troubleshooting Example

**Scenario: "My disk is full, what's taking up space?"**

```bash
df -h                               # 1. Confirm which partition is full
du -h --max-depth=1 / | sort -rh    # 2. Find which top-level folder is the culprit
du -sh /var/log/*                   # 3. Drill down into the suspected folder
find /var/log -size +100M           # 4. Find specific large files
sudo rm /var/log/old-huge-file.log  # 5. Remove the file safely (after confirming it's safe)
df -h                               # 6. Confirm space has been freed
```

---

*Reference guide for essential Ubuntu/Linux file, directory, disk, and text-editing commands — with capabilities, limitations, and practical examples for real-world use.*# Ubuntu File & Filesystem Commands (Deep Guide)

This document explains the essential Ubuntu/Linux commands for navigating, managing, and inspecting files, directories, disks, and filesystems — what each command **can do**, what it **cannot do**, and practical examples for each.

---

## 📑 Index

| # | Command | Category | Jump |
|---|---------|----------|------|
| 1 | `pwd` | Navigation | [Go](#1-pwd-) |
| 2 | `ls` | Navigation | [Go](#2-ls-) |
| 3 | `cd` | Navigation | [Go](#3-cd-) |
| 4 | `mkdir` | File/Dir Management | [Go](#4-mkdir-) |
| 5 | `touch` | File/Dir Management | [Go](#5-touch-) |
| 6 | `cp` | File/Dir Management | [Go](#6-cp-) |
| 7 | `mv` | File/Dir Management | [Go](#7-mv-) |
| 8 | `rm` | File/Dir Management | [Go](#8-rm-) |
| 9 | `rmdir` | File/Dir Management | [Go](#9-rmdir-) |
| 10 | `find` | Searching | [Go](#10-find-) |
| 11 | `locate` | Searching | [Go](#11-locate-) |
| 12 | `df` | Disk & Storage | [Go](#12-df-) |
| 13 | `du` | Disk & Storage | [Go](#13-du-) |
| 14 | `mount` | Disk & Storage | [Go](#14-mount-) |
| 15 | `umount` | Disk & Storage | [Go](#15-umount-) |
| 16 | `lsblk` | Disk & Storage | [Go](#16-lsblk-) |
| 17 | `blkid` | Disk & Storage | [Go](#17-blkid-) |
| 18 | `findmnt` | Disk & Storage | [Go](#18-findmnt-) |
| 19 | `stat` | File Inspection | [Go](#19-stat-) |
| 20 | `file` | File Inspection | [Go](#20-file-) |
| 21 | `nano` | Text Editors | [Go](#21-nano-) |
| 22 | `vim` | Text Editors | [Go](#22-vim-) |
| 23 | `cat` | Bonus | [Go](#23-cat-) |
| 24 | `less` | Bonus | [Go](#24-less-) |

Also see: [Quick Reference Table](#quick-reference-table) · [Real-World Troubleshooting Example](#real-world-troubleshooting-example)

---

## Navigation Commands

### 1. `pwd` ⭐⭐⭐⭐⭐
**P**rint **W**orking **D**irectory — shows your current location in the filesystem.

**Can do:** Show the full absolute path of where you currently are.
**Cannot do:** Doesn't show anything about files/contents — only the path itself.

```bash
pwd                    # e.g. output: /home/user/projects
pwd -P                 # show the physical path (resolves symlinks)
```

---

### 2. `ls` ⭐⭐⭐⭐⭐
Lists files and directories.

**Can do:** Show file/folder names, permissions, sizes, ownership, dates.
**Cannot do:** Doesn't show file *contents* — only listings/metadata.

```bash
ls                     # basic listing
ls -lah                # long format + hidden files + human-readable sizes
ls -la /etc            # detailed listing of a specific directory
ls -lt                 # sort by modification time (newest first)
```

---

### 3. `cd` ⭐⭐⭐⭐⭐
**C**hange **D**irectory — moves between folders.

**Can do:** Navigate to any directory you have permission to access.
**Cannot do:** Cannot list contents or create directories on its own.

```bash
cd /var/log            # go to an absolute path
cd ..                  # go up one level
cd ~                   # go to home directory
cd -                   # go back to the previous directory
```

---

## File & Directory Management

### 4. `mkdir` ⭐⭐⭐⭐
**M**a**k**e **Dir**ectory — creates new directories.

**Can do:** Create one or multiple directories, including nested ones.
**Cannot do:** Cannot create files (only directories) — use `touch` for files.

```bash
mkdir projects                    # create a single directory
mkdir -p projects/app/src         # create nested directories in one go
mkdir dir1 dir2 dir3               # create multiple directories at once
```

---

### 5. `touch` ⭐⭐⭐⭐
Creates empty files, or updates a file's timestamp if it already exists.

**Can do:** Create empty files instantly; update modification/access time.
**Cannot do:** Cannot add actual content to a file.

```bash
touch file.txt                    # create an empty file
touch file1.txt file2.txt         # create multiple files at once
touch -m file.txt                 # update only the modification time
```

---

### 6. `cp` ⭐⭐⭐⭐⭐
**C**o**p**y — copies files and directories.

**Can do:** Copy files, preserve permissions/timestamps, copy entire directory trees.
**Cannot do:** By default, doesn't copy directories unless `-r` is used (will error out).

```bash
cp file.txt backup.txt            # copy a file with a new name
cp -r project/ project_backup/    # copy an entire directory recursively
cp -v file.txt /tmp/               # verbose mode — shows what's being copied
cp -p file.txt /tmp/               # preserve permissions and timestamps
```

---

### 7. `mv` ⭐⭐⭐⭐⭐
**M**o**v**e — moves or renames files and directories.

**Can do:** Move files between locations; rename files/folders (mv is also the "rename" command in Linux).
**Cannot do:** Doesn't create a copy — the original is gone from its old location once moved.

```bash
mv file.txt /home/user/Documents/     # move a file to another directory
mv oldname.txt newname.txt            # rename a file
mv dir1/ dir2/                        # rename/move an entire directory
```

---

### 8. `rm` ⭐⭐⭐⭐⭐
**R**e**m**ove — deletes files and directories.

**Can do:** Permanently delete files/directories.
**Cannot do:** No recycle bin/undo — deleted files are **not** recoverable by default. Extreme caution needed with `-rf`.

```bash
rm file.txt                       # delete a single file
rm -r project/                    # delete a directory and its contents recursively
rm -rf project/                   # force delete, no confirmation (use with extreme care)
rm -i file.txt                    # ask for confirmation before deleting
```

> ⚠️ **Warning:** `rm -rf /` or `rm -rf *` in the wrong directory can destroy your entire system. Always double-check the path before running `rm -rf`.

---

### 9. `rmdir` ⭐⭐⭐
**R**e**m**ove **Dir**ectory — deletes **empty** directories only.

**Can do:** Safely remove empty folders.
**Cannot do:** Cannot delete a directory that has files/subfolders inside (use `rm -r` for that).

```bash
rmdir empty_folder                # remove an empty directory
rmdir -p a/b/c                    # remove nested empty directories
```

---

## Searching Files

### 10. `find` ⭐⭐⭐⭐⭐
Searches for files/directories based on name, type, size, time, permissions, etc. Searches in **real-time** across the actual filesystem.

**Can do:** Very powerful and flexible — search by name, extension, size, modified date, permissions, and even execute commands on results.
**Cannot do:** Can be slow on large filesystems since it scans live (unlike `locate`, which uses a pre-built index).

```bash
find / -name "file.txt"                    # search entire system by name
find . -type f -name "*.log"                # find all .log files in current directory
find . -type d -name "node_modules"         # find all directories named node_modules
find /var/log -mtime -1                     # files modified in the last 1 day
find . -size +100M                          # find files larger than 100MB
find . -name "*.tmp" -delete                # find and delete matching files
```

---

### 11. `locate` ⭐⭐⭐⭐
Searches for files using a pre-built database index — much faster than `find`.

**Can do:** Very fast filename searches across the whole system.
**Cannot do:** Relies on an index (`mlocate` database) that updates periodically — won't show very recently created files until the database is refreshed.

```bash
locate file.txt                   # quick search by filename
locate "*.conf"                   # search using a wildcard pattern
sudo updatedb                     # manually refresh the locate database
```

> **Note:** If `locate` isn't installed: `sudo apt install mlocate`

---

## Disk & Storage Commands

### 12. `df` ⭐⭐⭐⭐⭐
**D**isk **F**ree — shows disk space usage at the filesystem/partition level.

**Can do:** Show total, used, and available space per mounted filesystem.
**Cannot do:** Doesn't show which specific files/folders are consuming space (use `du` for that).

```bash
df -h                              # human-readable disk usage (GB/MB)
df -h /                            # usage of the root partition only
df -T                              # show filesystem type along with usage
```

---

### 13. `du` ⭐⭐⭐⭐⭐
**D**isk **U**sage — shows how much space specific files/directories are using.

**Can do:** Break down space usage folder-by-folder or file-by-file.
**Cannot do:** Doesn't show total disk capacity/free space (use `df` for that).

```bash
du -sh /var/log                    # total size of a directory (summary, human-readable)
du -h --max-depth=1 /home          # size of each subdirectory, one level deep
du -ah . | sort -rh | head -10     # top 10 largest files/folders in current directory
```

---

### 14. `mount` ⭐⭐⭐⭐
Attaches a filesystem (disk, partition, USB, network share) to a directory so it can be accessed.

**Can do:** Mount disks, ISO files, network shares, etc., to a mount point.
**Cannot do:** Cannot mount to a directory that doesn't exist yet — the mount point must exist first.

```bash
mount                              # list all currently mounted filesystems
sudo mount /dev/sdb1 /mnt          # mount a partition to /mnt
sudo mount -o loop image.iso /mnt/iso   # mount an ISO file
```

---

### 15. `umount` ⭐⭐⭐⭐
Detaches (unmounts) a mounted filesystem.

**Can do:** Safely disconnect a mounted disk/partition before removal.
**Cannot do:** Will fail with "device busy" if a file/process on that mount is still in use.

```bash
sudo umount /mnt                   # unmount by mount point
sudo umount /dev/sdb1              # unmount by device name
sudo umount -l /mnt                # lazy unmount (detach as soon as it's not busy)
```

---

### 16. `lsblk` ⭐⭐⭐⭐⭐
**L**i**s**t **Bl**ock devices — shows all disks and partitions in a tree structure.

**Can do:** Quickly see all disks, partitions, and their mount points.
**Cannot do:** Doesn't show filesystem type or UUID by default (combine with `blkid` for that).

```bash
lsblk                              # tree view of all disks/partitions
lsblk -f                           # include filesystem type and label
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT # customize which columns to show
```

---

### 17. `blkid` ⭐⭐⭐⭐
Shows block device attributes — UUID, filesystem type, label.

**Can do:** Get the UUID of a partition (essential for `/etc/fstab` entries).
**Cannot do:** Doesn't show mount points or disk usage (use `lsblk`/`df` for that).

```bash
sudo blkid                         # list UUID/type for all block devices
sudo blkid /dev/sda1                # info for a specific partition
```

---

### 18. `findmnt` ⭐⭐⭐⭐
Shows currently mounted filesystems in a readable tree format, and can verify `/etc/fstab` entries.

**Can do:** Search/filter mounted filesystems; verify if a specific mount is active.
**Cannot do:** Only shows mount-related info — not general disk usage.

```bash
findmnt                            # tree view of all mounts
findmnt /                          # check details of the root mount
findmnt --verify                   # verify /etc/fstab entries are valid
```

---

## File Inspection

### 19. `stat` ⭐⭐⭐⭐
Shows detailed metadata about a file — size, permissions, timestamps, inode.

**Can do:** Show precise creation/modification/access times, permissions in octal, inode number.
**Cannot do:** Doesn't show file content — metadata only (use `cat`/editors for content).

```bash
stat file.txt                      # full metadata of a file
stat -c "%a %n" file.txt            # show only permissions (octal) and filename
stat /                              # metadata of the root directory
```

---

### 20. `file` ⭐⭐⭐⭐
Identifies the actual type of a file by inspecting its content (not just the extension).

**Can do:** Correctly detect file type even if the extension is wrong or missing.
**Cannot do:** Doesn't show internal details like `stat` (permissions/timestamps) — only the type.

```bash
file document.pdf                  # e.g. output: PDF document, version 1.7
file script.sh                     # e.g. output: Bourne-Again shell script text
file *                             # check type of every file in current directory
```

---

## Text Editors

### 21. `nano` ⭐⭐⭐⭐⭐
A simple, beginner-friendly terminal text editor.

**Can do:** Easy to use — shortcuts are shown at the bottom of the screen at all times.
**Cannot do:** Lacks advanced features of `vim` (like macros, complex scripting, modal editing power).

```bash
nano file.txt                      # open (or create) a file for editing
```

**Common shortcuts inside nano:**
| Shortcut | Action |
|----------|--------|
| `Ctrl + O` | Save (Write Out) |
| `Ctrl + X` | Exit |
| `Ctrl + K` | Cut a line |
| `Ctrl + U` | Paste |
| `Ctrl + W` | Search |

---

### 22. `vim` ⭐⭐⭐⭐⭐
A powerful, modal text editor — steeper learning curve but extremely efficient once learned.

**Can do:** Extremely powerful editing, macros, scripting, split windows, plugins.
**Cannot do:** Not beginner-friendly — has separate modes (Normal, Insert, Visual) that confuse new users.

```bash
vim file.txt                       # open (or create) a file for editing
```

**Basic vim workflow:**
```bash
i           # enter Insert mode (start typing)
Esc         # go back to Normal mode
:w          # save
:q          # quit
:wq         # save and quit
:q!         # quit without saving (discard changes)
```

> **Tip:** If you're new to Linux, start with `nano`. Learn `vim` once you're comfortable — it's a huge productivity boost for server work since it's available on nearly every Linux system by default.

---

### 23. `cat` ⭐⭐⭐⭐⭐
Bonus — extremely common alongside file commands. Displays file content directly in the terminal.

**Can do:** Quickly view small file contents; combine (concatenate) multiple files.
**Cannot do:** Not practical for large files (floods the terminal) — use `less` for that.

```bash
cat file.txt                       # display file content
cat file1.txt file2.txt > combined.txt   # merge two files into one
cat -n file.txt                    # display with line numbers
```

---

### 24. `less` ⭐⭐⭐⭐
Bonus — views large files page-by-page without loading the whole file into memory.

**Can do:** Scroll through huge files (like logs) efficiently, search within the file.
**Cannot do:** Read-only — cannot edit the file.

```bash
less /var/log/syslog               # view a large log file page by page
```
**Inside `less`:** `/searchterm` to search, `q` to quit, arrow keys/Page Up/Down to scroll.

---

## Quick Reference Table

| Command | Purpose |
|---------|---------|
| `pwd` | Show current directory |
| `ls` | List files/directories |
| `cd` | Change directory |
| `mkdir` | Create directory |
| `touch` | Create empty file / update timestamp |
| `cp` | Copy files/directories |
| `mv` | Move or rename files/directories |
| `rm` | Delete files/directories |
| `rmdir` | Delete empty directories |
| `find` | Search files (real-time scan) |
| `locate` | Search files (indexed, fast) |
| `df` | Disk space usage (per filesystem) |
| `du` | Disk usage (per file/folder) |
| `mount` | Attach a filesystem |
| `umount` | Detach a filesystem |
| `lsblk` | List block devices/partitions |
| `blkid` | Show UUID/filesystem type |
| `findmnt` | Show/verify mounted filesystems |
| `stat` | Show detailed file metadata |
| `file` | Identify file type by content |
| `nano` | Simple terminal text editor |
| `vim` | Advanced modal text editor |
| `cat` | Display file content |
| `less` | Page through large files |

---

## Real-World Troubleshooting Example

**Scenario: "My disk is full, what's taking up space?"**

```bash
df -h                               # 1. Confirm which partition is full
du -h --max-depth=1 / | sort -rh    # 2. Find which top-level folder is the culprit
du -sh /var/log/*                   # 3. Drill down into the suspected folder
find /var/log -size +100M           # 4. Find specific large files
sudo rm /var/log/old-huge-file.log  # 5. Remove the file safely (after confirming it's safe)
df -h                               # 6. Confirm space has been freed
```

---

*Reference guide for essential Ubuntu/Linux file, directory, disk, and text-editing commands — with capabilities, limitations, and practical examples for real-world use.*
