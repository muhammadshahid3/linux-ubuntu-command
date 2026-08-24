# Ubuntu Root Filesystem

A quick reference guide to the main directories and structure under the Linux root directory (`/`) on Ubuntu.

## Root Filesystem Tree

```
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── lib64
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

## Main Root Directories

| Directory | Category                                              |
|-----------|--------------------------------------------------------|
| `/`       | Root filesystem                                        |
| `/bin`    | Essential user commands                                |
| `/boot`   | Bootloader and kernel files                             |
| `/dev`    | Device files                                            |
| `/etc`    | System configuration                                    |
| `/home`   | Normal users' home directories                          |
| `/lib`    | Essential shared libraries                               |
| `/lib64`  | 64-bit libraries/linking support where applicable         |
| `/media`  | Removable media mount points                              |
| `/mnt`    | Temporary mount point                                    |
| `/opt`    | Optional/third-party software                            |
| `/proc`   | Virtual process/kernel filesystem                         |
| `/root`   | Root user's home directory                                |
| `/run`    | Runtime system data                                       |
| `/sbin`   | System administration commands                            |
| `/srv`    | Data served by system services                            |
| `/sys`    | Virtual kernel/device filesystem                           |
| `/tmp`    | Temporary files                                           |
| `/usr`    | User-space programs, libraries and data                    |
| `/var`    | Variable data such as logs and caches                       |

## Important Note

Modern Ubuntu uses **usr-merge**, so `/bin`, `/sbin`, and `/lib` may be symbolic links or otherwise integrated with `/usr` locations.

For example:

```bash
ls -ld /bin /sbin /lib
```

## Exploring the Root Filesystem

**List directories:**

```bash
ls /
```

**Detailed listing:**

```bash
ls -lah /
```

**Show directory tree (if `tree` is installed):**

```bash
tree -L 1 /
```

**Check filesystem mounts:**

```bash
findmnt
```

**Check disk usage:**

```bash
df -h
```

---

*Reference document for the standard Ubuntu/Linux root filesystem hierarchy (FHS).*
