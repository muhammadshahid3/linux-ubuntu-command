Ubuntu Filesystem — Directory Purpose

This document explains what each major directory in the Ubuntu Linux filesystem is used for and highlights important files and subdirectories.

---

"/" — Root

The root directory is the top level of the entire Linux filesystem.

Everything in Linux is located somewhere under "/".

/
├── boot
├── etc
├── home
├── usr
├── var
└── ...

---

"/bin" — Essential Commands

Contains essential user commands.

Examples may include:

cat
cp
mv
ls
rm
mkdir

On modern Ubuntu systems, "/bin" is commonly merged with "/usr/bin".

Check:

ls -ld /bin

---

"/boot" — Boot Files

Contains files required to boot the operating system.

Common contents:

/boot/
├── grub/
├── vmlinuz-*
├── initrd.img-*
└── config-*

Important concepts:

- Linux kernel
- Initial RAM filesystem
- GRUB bootloader

---

"/dev" — Device Files

Linux represents hardware and certain kernel interfaces through files under "/dev".

Examples:

/dev/sda
/dev/nvme0n1
/dev/null
/dev/zero
/dev/random
/dev/tty

Useful command:

ls -lah /dev

---

"/etc" — System Configuration

One of the most important directories for Linux administration.

Contains system-wide configuration files.

Important files and directories:

/etc/
├── hostname
├── hosts
├── fstab
├── passwd
├── shadow
├── group
├── sudoers
├── resolv.conf
├── ssh/
├── systemd/
└── apt/

Important Files

"/etc/hostname"

Stores the system hostname.

cat /etc/hostname

"/etc/hosts"

Local hostname-to-IP mappings.

cat /etc/hosts

"/etc/fstab"

Defines filesystems that should be mounted.

cat /etc/fstab

"/etc/passwd"

Contains user account information.

cat /etc/passwd

"/etc/shadow"

Contains password-related authentication information.

sudo cat /etc/shadow

"/etc/ssh/"

Contains SSH configuration.

Common file:

/etc/ssh/sshd_config

---

"/home" — User Home Directories

Contains the home directories of normal users.

Example:

/home/
├── user1/
├── user2/
└── user3/

A user's personal files, projects and configuration are commonly stored here.

---

"/lib" — Essential Libraries

Contains essential shared libraries required by programs and the system.

On modern Ubuntu systems, this is commonly integrated with "/usr/lib".

Check:

ls -ld /lib

---

"/lib64" — 64-bit Library Support

On systems where it exists separately, "/lib64" contains 64-bit libraries and/or dynamic linker-related files.

Its exact structure depends on the Ubuntu architecture and usr-merge setup.

Check:

ls -lah /lib64

---

"/media" — Removable Media

Used for mounting removable storage devices.

Examples:

USB drives
External drives
Other removable media

---

"/mnt" — Temporary Mount Point

Traditionally used as a temporary location for mounting filesystems.

Example:

sudo mount /dev/sdb1 /mnt

---

"/opt" — Optional Software

Used for optional or third-party software packages.

Example:

/opt/
└── application/

---

"/proc" — Process and Kernel Information

"/proc" is a virtual filesystem.

It exposes information about:

- Running processes
- CPU
- Memory
- Kernel
- System state

Important entries:

/proc/cpuinfo
/proc/meminfo
/proc/uptime
/proc/loadavg
/proc/mounts
/proc/net/

Process information can be found under:

/proc/<PID>/

Useful commands:

cat /proc/cpuinfo
cat /proc/meminfo
cat /proc/uptime

---

"/root" — Root User's Home

This is the home directory of the "root" user.

Important distinction:

/       → Root of the entire filesystem

/root   → Home directory of the root user

---

"/run" — Runtime Data

Contains runtime information created since boot.

It may contain:

- PID files
- Unix sockets
- Service runtime information
- Other temporary runtime state

Example:

ls -lah /run

Much of "/run" is recreated during boot.

---

"/sbin" — System Administration Commands

Contains system administration utilities.

Modern Ubuntu commonly integrates "/sbin" with "/usr/sbin".

Check:

ls -ld /sbin

---

"/srv" — Service Data

Intended for data served by system services.

Example:

/srv/
└── application-data/

---

"/sys" — Kernel and Hardware Information

"/sys" is a virtual filesystem exposing information about:

- Hardware
- Devices
- Drivers
- Kernel subsystems

Important directories include:

/sys/
├── block/
├── bus/
├── class/
├── devices/
├── firmware/
├── fs/
└── kernel/

---

"/tmp" — Temporary Files

Used for temporary files created by applications and users.

Example:

ls -lah /tmp

Applications may use "/tmp" for temporary data.

---

"/usr" — User-Space Programs and Data

Contains a large portion of the operating system's user-space software, libraries and documentation.

Main directories:

/usr/
├── bin/
├── sbin/
├── lib/
├── share/
└── local/

"/usr/bin"

Contains many executable programs.

ls /usr/bin

"/usr/sbin"

Contains many system administration utilities.

"/usr/lib"

Contains libraries used by programs.

"/usr/share"

Contains architecture-independent data such as documentation, locale data and other shared resources.

"/usr/local"

Used for software installed locally by the administrator.

---

"/var" — Variable Data

Contains data that changes frequently while the system is running.

Main directories:

/var/
├── cache/
├── lib/
├── log/
├── spool/
└── tmp/

"/var/log"

Contains system and application logs.

Examples may include:

/var/log/syslog
/var/log/auth.log
/var/log/kern.log

Useful commands:

ls -lah /var/log

tail -f /var/log/syslog

"/var/cache"

Contains cached application/package data.

"/var/lib"

Contains persistent state/data maintained by applications and services.

"/var/spool"

Contains queued data such as mail or print jobs.

---

Quick Reference

Directory| Main Purpose
"/"| Root of filesystem
"/bin"| Essential commands
"/boot"| Boot files
"/dev"| Device files
"/etc"| Configuration
"/home"| User home directories
"/lib"| Essential libraries
"/media"| Removable media
"/mnt"| Temporary mounts
"/opt"| Optional software
"/proc"| Process/kernel information
"/root"| Root user's home
"/run"| Runtime data
"/sbin"| System administration commands
"/srv"| Service data
"/sys"| Kernel/device information
"/tmp"| Temporary files
"/usr"| User-space programs/data
"/var"| Variable data/logs

---

Troubleshooting Connection

Understanding these directories helps with real Linux troubleshooting.

Problem
   ↓
Identify the subsystem
   ↓
Find the relevant directory/file
   ↓
Inspect the information
   ↓
Identify the root cause
   ↓
Apply the fix

Examples:

Disk Full
   → /var/log
   → df -h
   → du -sh

SSH Problem
   → /etc/ssh/
   → /var/log/
   → systemctl
   → journalctl

Mount Problem
   → /etc/fstab
   → /dev
   → /mnt
   → lsblk
   → findmnt

Memory Problem
   → /proc/meminfo
   → free
   → top
