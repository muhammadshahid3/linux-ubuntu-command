Ubuntu Root Filesystem

This document shows the main directories and filesystem structure under the Linux root directory "/".

Root Filesystem

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

Main Root Directories

Directory| Category
"/"| Root filesystem
"/bin"| Essential user commands
"/boot"| Bootloader and kernel files
"/dev"| Device files
"/etc"| System configuration
"/home"| Normal users' home directories
"/lib"| Essential shared libraries
"/lib64"| 64-bit libraries/linking support where applicable
"/media"| Removable media mount points
"/mnt"| Temporary mount point
"/opt"| Optional/third-party software
"/proc"| Virtual process/kernel filesystem
"/root"| Root user's home directory
"/run"| Runtime system data
"/sbin"| System administration commands
"/srv"| Data served by system services
"/sys"| Virtual kernel/device filesystem
"/tmp"| Temporary files
"/usr"| User-space programs, libraries and data
"/var"| Variable data such as logs and caches

Important Note

Modern Ubuntu uses usr-merge, so "/bin", "/sbin", and "/lib" may be symbolic links or otherwise integrated with "/usr" locations.

For example:

ls -ld /bin /sbin /lib

Explore the Root Filesystem

List directories:

ls /

Detailed listing:

ls -lah /

Show directory tree if "tree" is installed:

tree -L 1 /

Check filesystem mounts:

findmnt

Check disk usage:

df -h
