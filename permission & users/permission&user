# Linux Permissions & User Management

> A complete, beginner-to-DevOps-friendly reference for understanding and managing Linux file permissions, users, and groups — from first principles to a real-world multi-environment production setup.

## Table of Contents

1. [What Are Linux File Permissions?](#1-what-are-linux-file-permissions)
2. [Understanding `ls -l`](#2-understanding-ls--l)
3. [Permission Categories](#3-permission-categories)
4. [Read, Write and Execute Permissions](#4-read-write-and-execute-permissions)
5. [Numeric Permissions](#5-numeric-permissions)
6. [`chmod` — Changing Permissions](#6-chmod--changing-permissions)
7. [`chown` — Changing Ownership](#7-chown--changing-ownership)
8. [`chgrp` — Changing Group Ownership](#8-chgrp--changing-group-ownership)
9. [Practical Permission Examples](#9-practical-permission-examples)
10. [Linux Users](#10-linux-users)
11. [Creating and Managing Users](#11-creating-and-managing-users)
12. [Groups](#12-groups)
13. [Group-Based Permissions in Practice](#13-group-based-permissions-in-practice)
14. [`sudo` and Privilege Escalation](#14-sudo-and-privilege-escalation)
15. [DevOps Practical Project: Dev, Staging, Production](#15-devops-practical-project-dev-staging-production)
16. [Permission Matrix](#16-permission-matrix)
17. [Troubleshooting Scenarios](#17-troubleshooting-scenarios)
18. [Best Practices](#18-best-practices)
19. [Cheat Sheet](#19-cheat-sheet)

---

## 1. What Are Linux File Permissions?

### Why Linux has file permissions

Linux is a **multi-user operating system**. That means many different users — and many different programs running as those users — can be active on the same machine at the same time. Without a control mechanism, any user or process could read, modify, or delete any file on the system, including files belonging to other people or to the operating system itself.

File permissions are that control mechanism. They define **who can do what** to a given file or directory. Every single file and directory on a Linux system — configuration files, scripts, logs, binaries, even device files — has an owner and a set of permission rules attached to it.

### Why permissions matter for security

Permissions are one of the most fundamental security layers in Linux:

- They prevent one user from tampering with another user's files.
- They stop regular users from modifying critical system files (like the kernel or `/etc/passwd`).
- They limit the damage a compromised application can do, because the application only has the permissions of the user running it.
- They allow controlled collaboration: teams can share files with each other without exposing them to everyone on the system.

**Real-world analogy:** Think of a shared office building. You have a key to your own office (owner), a keycard that opens shared team rooms (group), and the lobby is open to everyone (others). A security guard (the kernel) checks your credentials every time you try to open a door.

### How Linux controls access to files and directories

Every time a process (a running program, acting on behalf of a user) tries to access a file, the Linux kernel checks:

1. **Who is asking?** (the user ID, and the group IDs, of the process)
2. **What are they asking to do?** (read, write, or execute)
3. **What does the file's permission table allow for that user?**

If the requested action is allowed, the kernel grants access. If not, the process receives a `Permission denied` error.

### Authentication vs. Authorization

These two words are often confused, but they mean very different things:

| Concept | Question it answers | Example |
|---|---|---|
| **Authentication** | "Who are you?" | Logging in with a username and password, or an SSH key |
| **Authorization** | "What are you allowed to do?" | Once logged in as `shahid`, can you read `/etc/shadow`? Can you write to `/var/www/app`? |

Authentication happens once, at login. Authorization (via file permissions, `sudo` rules, etc.) is checked **every single time** a resource is accessed.

### What happens when a user tries to access a file

Step by step, when a user runs a command like `cat /etc/shadow`:

1. The `cat` process runs with the identity (UID/GID) of the user who launched it.
2. The kernel looks up the permission bits on `/etc/shadow`.
3. The kernel compares the process's identity against the file's owner, group, and "others" permissions.
4. Based on that comparison, the kernel either allows the read or returns `Permission denied`.

This check happens transparently, in microseconds, on every file operation on the system.

---

## 2. Understanding `ls -l`

The `ls -l` command is the primary way to inspect permissions from the command line.

```bash
ls -l app.sh
```

Example output:

```
-rwxr-xr-- 1 shahid developers 4096 Aug 24 app.sh
```

### Breaking down the output

```
-rwxr-xr--  1  shahid  developers  4096  Aug 24  app.sh
│└┬┘└┬┘└┬┘  │    │         │         │      │       │
│ │  │  │   │    │         │         │      │       └─ File name
│ │  │  │   │    │         │         │      └───────── Last modified date/time
│ │  │  │   │    │         │         └──────────────── File size (bytes)
│ │  │  │   │    │         └────────────────────────── Group owner
│ │  │  │   │    └──────────────────────────────────── User owner
│ │  │  │   └───────────────────────────────────────── Number of hard links
│ │  │  └───────────────────────────────────────────── Permissions for Others
│ │  └──────────────────────────────────────────────── Permissions for Group
│ └─────────────────────────────────────────────────── Permissions for Owner/User
└───────────────────────────────────────────────────── File type
```

### Field-by-field explanation

**File type (1st character)**

| Symbol | Meaning |
|---|---|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device |
| `b` | Block device |
| `s` | Socket |
| `p` | Named pipe (FIFO) |

**Owner/User (`rwx`)** — permissions that apply to the user who owns the file.

**Group (`r-x`)** — permissions that apply to members of the file's group.

**Others (`r--`)** — permissions that apply to everyone else on the system.

**Permission bits (`r`, `w`, `x`, `-`)** — `r` = read, `w` = write, `x` = execute, `-` = permission not granted. These are covered in depth in [Part 4](#4-read-write-and-execute-permissions).

So `-rwxr-xr--` means: this is a regular file; the owner (`shahid`) can read, write, and execute it; members of the `developers` group can read and execute it but not modify it; everyone else can only read it.

---

## 3. Permission Categories

Every file has exactly three permission categories, each with its own independent set of `r`, `w`, `x` bits.

### User / Owner

The **owner** is normally the user who created the file. Ownership can be transferred using `chown` (see [Part 7](#7-chown--changing-ownership)). The owner is granted the level of access defined by the first `rwx` triplet, regardless of what group they belong to.

**Example:** If `shahid` creates `app.sh`, `shahid` is the owner and gets whatever permissions are set in the "owner" slot — independent of group or "others" settings.

### Group

Every file also belongs to a **group**. Any user who is a member of that group receives the permissions defined in the second `rwx` triplet, *even if they are not the file's owner*.

Groups exist so that permissions can be managed for **teams of people at once**, instead of having to configure access user-by-user. If ten developers need access to the same project directory, you put all ten in a `developers` group and set the group permission once — you don't touch ten separate user accounts.

**Example:** A `developers` group with read/write access to `/opt/company/dev/app` lets every developer collaborate without the admin managing ten individual permission entries.

### Others

**"Others"** refers to every user on the system who is neither the file's owner nor a member of the file's group. This is the broadest and least trusted category, so it should almost always be granted the fewest permissions — often none at all.

**Practical example:**

```
-rwxr-x--- 1 shahid developers 4096 Aug 24 deploy.sh
```

- `shahid` (owner): full control — read, write, execute.
- Anyone in `developers` (group): can read and run it, but not modify it.
- Everyone else (others): no access at all — not even read.

---

## 4. Read, Write and Execute Permissions

```
r = read
w = write
x = execute
```

These three bits mean **different things** depending on whether they apply to a file or a directory.

### On Files

| Bit | Meaning on a file |
|---|---|
| `r` (read) | Allows viewing the file's contents (e.g., with `cat`, `less`, opening it in an editor) |
| `w` (write) | Allows modifying, overwriting, or truncating the file's contents |
| `x` (execute) | Allows the file to be **run** as a program or script |

**Important:** A file needs the `x` bit to be executed directly (e.g., `./app.sh`). Without it, even the owner will get `Permission denied`, even though they might be able to read or edit the script's text.

### On Directories

Directory permissions behave differently because a directory isn't "content" in the same sense — it's a list of file names.

| Bit | Meaning on a directory |
|---|---|
| `r` (read) | Allows **listing** the contents of the directory (e.g., `ls` will work) |
| `w` (write) | Allows **creating, deleting, or renaming** entries inside the directory (this permission belongs to the directory, not the individual files!) |
| `x` (execute) | Allows **entering/traversing** the directory (e.g., `cd` into it, or accessing a file inside it by path) — sometimes called the "search" bit |

**Practical examples:**

- A directory with `r--` (read only, no execute): you can list file names with `ls`, but you cannot `cd` into it or access any file inside it by path. This is a common source of confusion.
- A directory with `--x` (execute only, no read): you can `cd` into it and access files **if you know their exact names**, but you cannot list its contents with `ls`. This is often used for directories where you want controlled access without exposing a directory listing.
- A directory with `rwx`: full access — list, enter, and create/delete/rename files inside it.

**Key insight:** Deleting a file depends on the **write permission of the directory it lives in**, not on the permissions of the file itself. This surprises many beginners — you can delete a read-only file if you have write access to its parent directory.

---

## 5. Numeric Permissions

Linux permissions can be expressed as **numbers** instead of letters — this is called octal notation, and it's the most common way permissions are set in scripts and documentation.

### The base values

```
Read    = 4
Write   = 2
Execute = 1
```

Each permission triplet (owner, group, others) is a **sum** of the values that apply.

### How they combine

| Combination | Sum | Meaning |
|---|---|---|
| Read + Write + Execute | 4 + 2 + 1 = **7** | `rwx` |
| Read + Write | 4 + 2 = **6** | `rw-` |
| Read + Execute | 4 + 1 = **5** | `r-x` |
| Write + Execute | 2 + 1 = **3** | `-wx` |
| Read only | 4 = **4** | `r--` |
| Write only | 2 = **2** | `-w-` |
| Execute only | 1 = **1** | `--x` |
| Nothing | 0 = **0** | `---` |

### Quick reference table

| Number | Symbolic | Meaning |
|---|---|---|
| 7 | `rwx` | Read, write, and execute |
| 6 | `rw-` | Read and write |
| 5 | `r-x` | Read and execute |
| 4 | `r--` | Read only |
| 3 | `-wx` | Write and execute |
| 2 | `-w-` | Write only |
| 1 | `--x` | Execute only |
| 0 | `---` | No permissions |

A full permission value has **three digits**: owner, group, others — in that order.

### Common permission sets explained

| Mode | Owner | Group | Others | Real-world use case |
|---|---|---|---|---|
| **755** | rwx | r-x | r-x | Scripts/binaries and directories anyone can use, but only the owner can modify. Common default for executables. |
| **644** | rw- | r-- | r-- | Standard file permission — owner edits, everyone else can only read. Common for configs and documents that aren't secret. |
| **700** | rwx | --- | --- | Fully private to the owner. Personal scripts, SSH key directories (`~/.ssh`). |
| **750** | rwx | r-x | --- | Owner full control, group can use/read, others get nothing. Good for team project directories. |
| **770** | rwx | rwx | --- | Owner and group both have full control; outsiders have none. Ideal for shared collaborative directories. |
| **775** | rwx | rwx | r-x | Like 770, but others can also read/traverse. Useful for semi-public shared folders. |
| **600** | rw- | --- | --- | Fully private file, no execute. Used for private keys, credentials, secrets. |
| **640** | rw- | r-- | --- | Owner can edit, group can read, others get nothing. Common for config files containing sensitive-but-shared data. |

---

## 6. `chmod` — Changing Permissions

`chmod` ("change mode") sets the permission bits on a file or directory. It can be used in **numeric** or **symbolic** form.

### Numeric form

```bash
chmod 755 file        # owner: rwx, group: r-x, others: r-x
chmod 644 file        # owner: rw-, group: r--, others: r--
chmod 700 file        # owner: rwx, group/others: none
chmod 750 directory   # owner: rwx, group: r-x, others: none
```

Numeric mode is fast and precise — you set the **entire** permission set in one command. It's the preferred style for scripts and automation because it's unambiguous.

### Symbolic form

Symbolic mode lets you **add, remove, or set** specific bits without touching the rest.

```
u = user/owner
g = group
o = others
a = all (u + g + o)
```

Examples:

```bash
chmod u+x script.sh              # add execute permission for the owner
chmod g+w file.txt                # add write permission for the group
chmod o-r file.txt                # remove read permission for others
chmod u=rwx,g=rx,o= file.sh       # explicitly set: owner=rwx, group=r-x, others=none
```

Symbolic mode is especially useful when you only want to **tweak one bit** without recalculating the whole numeric value — for example, making one script executable without changing its other permissions.

### Recursive permissions

```bash
chmod -R 755 directory/
```

The `-R` flag applies the change to the directory **and every file and subdirectory inside it**, recursively.

### ⚠️ The risks of `chmod -R`

Using `chmod -R` carelessly is one of the most common Linux mistakes:

- It applies **the same** permission to files and directories alike — but files and directories often need different execute-bit treatment (a config file usually shouldn't be executable, but a directory usually needs `x` to be traversable).
- Running `chmod -R 777` recursively on a directory (especially something like `/` or `/etc`) can make the entire system insecure or even unbootable, by exposing sensitive files to every user.
- It can silently overwrite carefully tuned individual permissions that were previously set for a reason.
- It provides no undo — always double-check the target path before running it, and consider `chmod -R` with `find` for more surgical control, e.g.:

```bash
# Set directories to 755 and files to 644 separately (safer than one blanket chmod -R)
find /path/to/project -type d -exec chmod 755 {} \;
find /path/to/project -type f -exec chmod 644 {} \;
```

---

## 7. `chown` — Changing Ownership

`chown` ("change owner") changes **who owns** a file — both the user owner and, optionally, the group owner.

```bash
chown user file                 # change the user owner only
chown user:group file           # change both user and group owner
chown -R user:group directory/  # change ownership recursively
```

Examples:

```bash
chown deploy app.sh
chown deploy:developers app.sh
chown -R ops01:operations /opt/company/prod/
```

### `chmod` vs. `chown` — the key difference

| | `chmod` | `chown` |
|---|---|---|
| **What it changes** | *What* the owner/group/others are allowed to do | *Who* the owner and group actually are |
| **Analogy** | Deciding what a keyholder is allowed to unlock | Deciding who gets handed the keys |
| **Typical use** | Locking down or opening access levels | Transferring a file to a new user or team |

In short: `chown` decides **who** the file belongs to; `chmod` decides **what** that person/group/everyone else can do with it. They're almost always used together when setting up a new project directory.

---

## 8. `chgrp` — Changing Group Ownership

`chgrp` ("change group") changes only the **group** that owns a file, without touching the user owner.

```bash
chgrp developers project/
```

### When is `chgrp` useful?

- When you want to reassign a file or directory to a different team **without changing who the individual owner is**.
- When automating hand-offs — e.g., a CI/CD pipeline creates a build artifact as its own service account, then `chgrp`s it to the team that should be able to read it.

### `chgrp` vs. `chown`

`chown user:group file` can do everything `chgrp` does (and more, since it also sets the user). `chgrp group file` is simply a more explicit, single-purpose tool when you only need to touch the group — useful for clarity and for restricting what a script is allowed to modify.

---

## 9. Practical Permission Examples

### Example 1 — Private file

```bash
chmod 600 secrets.env
```

Only the owner can read or write; no one else — not even the group — has any access. Appropriate for credentials, private keys, and `.env` files containing secrets.

### Example 2 — Executable script

```bash
chmod 755 deploy.sh
```

The owner can edit and run it; everyone else can only run it (read + execute), not modify it. Appropriate for shared utility scripts and CLI tools installed system-wide.

### Example 3 — Shared development directory

```bash
chmod 770 /opt/company/dev/app
```

Owner and group both get full read/write/execute access, so any team member in the group can collaborate freely; outsiders get nothing. Appropriate for a team workspace where trusted teammates need to add, edit, and remove files.

---

## 10. Linux Users

### What is a Linux user?

A **user** is an identity the system uses to determine ownership and permissions. Every process on Linux runs *as* some user, and that identity is what the kernel checks against file permissions.

### Types of users

| Type | Description |
|---|---|
| **Root user** | The superuser (UID `0`). Bypasses virtually all permission checks. Has unrestricted access to the entire system. |
| **Normal user** | A regular human account, created for people to log in and work. Subject to normal permission checks. |
| **System user** | A non-interactive account created for services/daemons (e.g., `www-data`, `postgres`). Typically has no login shell and isn't meant for humans. |

### UID and GID

- **UID (User ID):** a unique number identifying a user. Root is always `0`. Normal users typically start from `1000` upward (distribution-dependent); system users usually occupy a lower reserved range.
- **GID (Group ID):** a unique number identifying a group, used the same way but for group membership.

### Home directory

Each user typically has a home directory (e.g., `/home/shahid`) — their personal space for files, configuration, and settings.

### Shell

The **shell** is the program launched when the user logs in interactively (e.g., `/bin/bash`, `/bin/zsh`). System/service accounts are usually given `/usr/sbin/nologin` or `/bin/false` to explicitly prevent interactive login.

### Important user/group files

| File | Purpose |
|---|---|
| `/etc/passwd` | Lists every user account: username, UID, GID, home directory, shell, etc. (world-readable, contains no passwords despite the name) |
| `/etc/shadow` | Stores the actual **hashed** passwords and password aging policy. Highly sensitive. |
| `/etc/group` | Lists every group and its members. |
| `/etc/gshadow` | Stores group passwords (rare) and group administrators — the group equivalent of `/etc/shadow`. |

### Why `/etc/shadow` must not be readable by normal users

`/etc/shadow` contains password **hashes**. If any user could read it, they could copy those hashes off the system and run offline brute-force or dictionary attacks against them, with no rate limiting and no logging. That's why `/etc/shadow` is owned by `root` and typically set to `640` or stricter, readable only by root (and sometimes a `shadow` group used by specific system utilities) — never by regular users.

---

## 11. Creating and Managing Users

### `adduser` vs. `useradd`

| | `useradd` | `adduser` |
|---|---|---|
| **Nature** | Low-level binary, present on virtually all Linux distros | Higher-level, interactive Perl/shell script (Debian/Ubuntu-based systems) that wraps `useradd` |
| **Behavior** | Minimal by default — often does **not** create a home directory or set a password unless you pass extra flags | Interactive — prompts for password, full name, and other details; creates the home directory automatically |
| **Best for** | Scripting/automation, where you want full explicit control | Manual, interactive user creation by an admin |

```bash
sudo adduser username     # interactive, beginner-friendly, sensible defaults
sudo useradd username     # scriptable, minimal, requires explicit flags for home dir/shell
```

### Creating a user step by step

```bash
# Create the user with a home directory and default shell
sudo adduser deploy

# Or, using useradd with explicit options
sudo useradd -m -s /bin/bash deploy
#            │    └─ set login shell
#            └────── create home directory
```

### Setting a password

```bash
sudo passwd deploy
```

You'll be prompted to enter and confirm the new password.

### Checking a user

```bash
id username                  # shows UID, GID, and group memberships
getent passwd username       # shows the user's /etc/passwd entry
cat /etc/passwd | grep username   # manual lookup
```

### Deleting a user

```bash
sudo userdel username        # deletes the user account, but leaves the home directory behind
sudo userdel -r username     # deletes the user account AND their home directory/mail spool
```

Use `-r` deliberately — it permanently removes the user's files. Back up anything important first.

---

## 12. Groups

### What is a Linux group?

A group is a named collection of users that permissions can be assigned to collectively. Instead of granting five people access one-by-one, you add them to a group and grant the group access once.

### Why groups matter

- **Scalability:** managing permissions for a team of 50 is impractical user-by-user.
- **Consistency:** everyone in the group gets the same access, reducing configuration drift.
- **Auditability:** it's easier to review "who is in the `production-admins` group" than to review individual file ACLs across a whole filesystem.

### Primary group vs. supplementary (secondary) groups

- **Primary group:** the group a user's newly created files belong to by default. Each user has exactly one primary group, recorded in `/etc/passwd`.
- **Supplementary groups:** additional groups a user belongs to, granting extra access without changing their default file-creation group. A user can belong to many supplementary groups.

### Key group commands

```bash
groupadd groupname          # create a new group
groupdel groupname          # delete a group
groups username             # list groups a user belongs to
id username                 # show UID, primary GID, and all group memberships
getent group groupname      # show a group's entry (members, GID)
usermod                     # modify an existing user (including group memberships)
gpasswd                     # administer group membership/passwords
```

### Creating a group and adding a user

```bash
sudo groupadd developers
sudo usermod -aG developers username
```

### Why `-aG` matters

- `-a` = **append**. Adds the user to the specified group(s) **without removing them from any existing group**.
- `-G` = specifies the list of supplementary groups to add.

### ⚠️ The danger of `usermod -G` without `-a`

```bash
sudo usermod -G developers username   # DANGEROUS if used alone
```

Without `-a`, `usermod -G` **replaces** the user's entire supplementary group list with only the group(s) listed. This can silently strip a user out of every other group they belonged to — including groups they need for other systems (e.g., `docker`, `sudo`, `wheel`) — leading to confusing, hard-to-diagnose access loss. **Always use `-aG`, never `-G` alone**, unless you specifically intend to overwrite the entire group list.

---

## 13. Group-Based Permissions in Practice

Consider a project with this layout:

```
project/
├── frontend/
├── backend/
└── shared/
```

### Step 1 — Create the group

```bash
sudo groupadd developers
```

### Step 2 — Add team members

```bash
sudo usermod -aG developers alice
sudo usermod -aG developers bob
```

### Step 3 — Assign group ownership

```bash
sudo chown -R :developers project/
```

This changes only the **group** owner (the leading `:` means "leave the user owner unchanged"), recursively, for the whole project tree.

### Step 4 — Set appropriate permissions

```bash
sudo chmod -R 770 project/
```

This gives the owner and every member of `developers` full read/write/execute access, while anyone outside the group has none.

### Result

- `alice` and `bob`, both members of `developers`, can read, edit, add, and remove files anywhere under `project/`.
- A user **not** in `developers` gets `Permission denied` the moment they try to `cd` into `project/`, because the `x` bit for "others" is unset.
- New files created inside `project/` by `alice` or `bob` will typically inherit the parent directory's group if the **setgid bit** is also applied (`chmod g+s project/`) — a common addition in real collaborative setups, ensuring every new file automatically belongs to `developers` regardless of who creates it.

---

## 14. `sudo` and Privilege Escalation

### What is `sudo`?

`sudo` ("superuser do") lets an authorized user run a specific command as another user — typically root — without logging in as root directly. It's the standard mechanism for controlled privilege escalation on modern Linux systems.

### Why root access should be limited

- Every action taken as root has **no permission checks** — a single mistaken command can damage or destroy the entire system.
- Sharing the root password (or letting everyone log in directly as root) removes accountability — you lose the ability to know *who* did *what*.
- `sudo` provides an audit trail: actions are logged (commonly to `/var/log/auth.log` or `/var/log/secure`) against the real user's identity, not just "root."
- Limiting root access follows the **principle of least privilege** — users should only be able to do what their role actually requires.

### `/etc/sudoers`

This file defines **who** is allowed to run **what**, as **which user**, and whether a password is required. It uses a specific, strict syntax.

### `visudo`

```bash
sudo visudo
```

`visudo` is the required way to edit `/etc/sudoers`. It locks the file during editing and, critically, **validates the syntax before saving**. A broken `/etc/sudoers` file (from a typo made in a plain text editor) can lock every user — including root via `sudo` — out of administrative access.

**Do not** open and edit `/etc/sudoers` directly with `nano`, `vim`, or any other editor outside of `visudo`. If you must use a specific editor, set it safely:

```bash
sudo EDITOR=nano visudo
```

### Granting administrative access safely — example

Adding a user to an administrative group (distribution-dependent, e.g., `sudo` on Debian/Ubuntu or `wheel` on RHEL/CentOS) is generally safer and more maintainable than editing `/etc/sudoers` line-by-line for every user:

```bash
sudo usermod -aG sudo deploy   # Debian/Ubuntu-style
```

For fine-grained, auditable rules (e.g., "this user may only restart the `nginx` service"), add a dedicated file under `/etc/sudoers.d/` rather than editing the main file directly — each rule stays isolated and easy to review or remove.

---

## 15. DevOps Practical Project: Dev, Staging, Production

This section walks through a realistic permission and user-management design for a company running three environments.

### Target directory structure

```
/opt/company/
├── dev/
│   ├── app/
│   ├── logs/
│   └── config/
│
├── staging/
│   ├── app/
│   ├── logs/
│   └── config/
│
└── prod/
    ├── app/
    ├── logs/
    └── config/
```

### Step 1 — Create the directory structure

```bash
sudo mkdir -p /opt/company/{dev,staging,prod}/{app,logs,config}
```

### Step 2 — Create the groups

```bash
sudo groupadd developers
sudo groupadd qa
sudo groupadd operations
sudo groupadd deployers
```

### Step 3 — Create the users

```bash
sudo useradd -m -s /bin/bash dev01
sudo useradd -m -s /bin/bash dev02
sudo useradd -m -s /bin/bash qa01
sudo useradd -m -s /bin/bash ops01
sudo useradd -m -s /bin/bash deploy
```

Set each user's password interactively (never hardcode passwords in scripts or commit them anywhere):

```bash
sudo passwd dev01
sudo passwd dev02
sudo passwd qa01
sudo passwd ops01
sudo passwd deploy
```

### Step 4 — Assign users to appropriate groups

```bash
sudo usermod -aG developers dev01
sudo usermod -aG developers dev02
sudo usermod -aG qa qa01
sudo usermod -aG operations ops01
sudo usermod -aG deployers deploy

# Operations often needs visibility across all environments:
sudo usermod -aG developers,qa ops01
```

### Step 5 — Design the permission model

**Development environment** — the most open, since it's meant for active building and testing:

```bash
sudo chown -R root:developers /opt/company/dev/app
sudo chmod -R 770 /opt/company/dev/app          # developers: full access

sudo chown -R root:developers /opt/company/dev/logs
sudo chmod -R 770 /opt/company/dev/logs

sudo chown -R root:developers /opt/company/dev/config
sudo chmod -R 770 /opt/company/dev/config

# Give QA read/execute access to app and logs so they can test and inspect
sudo setfacl -R -m g:qa:rx /opt/company/dev/app
sudo setfacl -R -m g:qa:rx /opt/company/dev/logs
```

**Staging environment** — a controlled pre-production environment where developers get limited access but QA is more active:

```bash
sudo chown -R deploy:qa /opt/company/staging/app
sudo chmod -R 750 /opt/company/staging/app       # qa: rx, deployers own it

sudo chown -R deploy:qa /opt/company/staging/logs
sudo chmod -R 750 /opt/company/staging/logs

sudo chown -R ops01:operations /opt/company/staging/config
sudo chmod -R 750 /opt/company/staging/config

# Developers get read-only visibility via ACL, not full group ownership
sudo setfacl -R -m g:developers:rx /opt/company/staging/app
```

**Production environment** — the most restricted. Developers should not have direct, unrestricted access; deployment is handled through a dedicated account.

```bash
sudo chown -R deploy:operations /opt/company/prod/app
sudo chmod -R 750 /opt/company/prod/app          # operations: rx, deploy owns it, no developer access

sudo chown -R ops01:operations /opt/company/prod/logs
sudo chmod -R 750 /opt/company/prod/logs          # operations can read/write logs

sudo chown -R root:operations /opt/company/prod/config
sudo chmod -R 640 /opt/company/prod/config        # sensitive: owner rw, group r, others none

# QA gets read-only access to app for verification, nothing else
sudo setfacl -R -m g:qa:rx /opt/company/prod/app
```

### Handling secrets in configuration directories

For any file containing credentials, API keys, or tokens (e.g., `/opt/company/prod/config/.env`):

```bash
sudo chown root:operations /opt/company/prod/config/.env
sudo chmod 640 /opt/company/prod/config/.env
```

- Only `root` and members of `operations` should be able to read it.
- Never set secret files to `644` or broader — anyone on the system could read them.
- Never commit real secret values into documentation, Git repositories, or shared chat — reference them only by path and permission mode, as done throughout this document.

### Why this model works

- **Developers** can experiment freely in `dev`, get read-only visibility in `staging`, and have **no direct access** to `prod` — enforcing separation between "build" and "run" environments.
- **QA** can read across environments to test and verify, but cannot modify production data.
- **Operations** holds administrative control over staging and production, since they're responsible for uptime and incident response.
- **`deploy`** is a dedicated service-style account used specifically for deployment automation (e.g., CI/CD pipelines), so deployments aren't tied to any individual human's personal account.

---

## 16. Permission Matrix

| Environment | User/Group | App | Logs | Config |
|---|---|---|---|---|
| Dev | `developers` | RWX | RW | RW |
| Dev | `qa` | R/X | R | R |
| Staging | `developers` | R/X | R | R |
| Staging | `qa` | RWX | RW | R |
| Production | `developers` | No access | R | No access |
| Production | `qa` | R/X | — | — |
| Production | `operations` | RWX | RW | RW |
| Production | `deployers` | Deployment access (owner) | R | Controlled (root:operations, 640) |

**Notes on the matrix:**

- In **Production**, `developers` intentionally have **no access** to `app` or `config` — this is a deliberate best-practice choice, not an oversight, and reflects separation of duties between building software and running it.
- **`deployers`** own the production `app` directory so automated deployment pipelines can update it without granting broad human access.
- **Config** directories at every stage should use the tightest permissions that still let the responsible group function, especially in staging and production where real (or real-like) secrets may be present.

---

## 17. Troubleshooting Scenarios

### 1. Permission denied

**Symptoms:** Command returns `Permission denied` when reading, writing, or executing a file.

**Investigation:**
```bash
ls -l file
id
```

**Commands:** `ls -l`, `id`, `whoami`

**Root Cause:** The user is neither the owner nor a member of the file's group, and "others" permission doesn't grant the needed access.

**Fix:**
```bash
sudo chmod g+rw file      # or add the user to the appropriate group
```

**Verification:**
```bash
ls -l file
sudo -u username cat file   # test as that specific user
```

---

### 2. User cannot access project directory

**Symptoms:** `cd project/` fails with `Permission denied`, even though `ls -l` shows reasonable-looking permissions.

**Investigation:**
```bash
ls -ld project/
namei -l /opt/company/dev/app
```

**Commands:** `ls -ld`, `namei -l`, `stat`

**Root Cause:** The directory itself, or one of its **parent** directories, is missing the execute (`x`) bit for the user — directory traversal requires `x` at every level of the path, not just the final directory.

**Fix:**
```bash
sudo chmod o+x /opt/company        # ensure every parent directory is traversable
sudo chmod 750 /opt/company/dev/app
```

**Verification:**
```bash
namei -l /opt/company/dev/app
sudo -u dev01 ls /opt/company/dev/app
```

---

### 3. User is in the group but still cannot access files

**Symptoms:** `groups username` shows the correct group, but access still fails.

**Investigation:**
```bash
groups username
id username
```

**Root Cause:** The user was added to the group **after their current login session started**. Group membership changes don't apply to already-running shells/sessions.

**Fix:** Have the user log out and log back in, or start a new shell with the updated group applied:
```bash
newgrp developers
```

**Verification:**
```bash
id username   # confirm the group now appears in the active session
```

---

### 4. Wrong owner

**Symptoms:** A deployment script or application can't write to a file it should own.

**Investigation:**
```bash
ls -l file
stat file
```

**Root Cause:** The file was created by the wrong user (e.g., created manually by an admin instead of by the `deploy` service account), so ownership doesn't match what the application expects.

**Fix:**
```bash
sudo chown deploy:deployers file
```

**Verification:**
```bash
ls -l file
```

---

### 5. Wrong group

**Symptoms:** Team members can't collaborate on a shared file even though individual ownership looks fine.

**Investigation:**
```bash
ls -l file
getent group developers
```

**Root Cause:** The file's group owner doesn't match the team's group (e.g., it's still set to a personal default group instead of `developers`).

**Fix:**
```bash
sudo chgrp developers file
```

**Verification:**
```bash
ls -l file
```

---

### 6. Incorrect chmod value

**Symptoms:** A script that used to run now fails with `Permission denied`, after someone recently changed permissions.

**Investigation:**
```bash
ls -l script.sh
```

**Root Cause:** A `chmod` command accidentally removed the execute bit (e.g., `chmod 644` was applied to something that needed `755`).

**Fix:**
```bash
sudo chmod 755 script.sh
```

**Verification:**
```bash
ls -l script.sh
./script.sh
```

---

### 7. User accidentally removed from supplementary groups

**Symptoms:** A user who previously had access to multiple resources (e.g., `docker`, `developers`) suddenly loses access to all of them at once after an admin ran a `usermod` command.

**Investigation:**
```bash
id username
getent passwd username
```

**Root Cause:** `usermod -G group` was used **without `-a`**, which replaced the user's entire supplementary group list with just the single group specified.

**Fix:**
```bash
sudo usermod -aG developers,docker,qa username   # restore all needed groups explicitly
```

**Verification:**
```bash
groups username
id username
```

---

### 8. Application cannot read configuration file

**Symptoms:** Application logs show it cannot open its config file at startup.

**Investigation:**
```bash
ls -l /opt/company/prod/config/app.conf
ps -ef | grep app_process    # confirm which user the app actually runs as
```

**Root Cause:** The config file's owner/group doesn't include the account the application runs as, or permissions are too restrictive (e.g., `600` owned by `root` while the app runs as `appuser`).

**Fix:**
```bash
sudo chown root:operations /opt/company/prod/config/app.conf
sudo chmod 640 /opt/company/prod/config/app.conf
sudo usermod -aG operations appuser
```

**Verification:**
```bash
sudo -u appuser cat /opt/company/prod/config/app.conf
```

---

### 9. Application cannot write logs

**Symptoms:** Application errors with "cannot open log file for writing" or silently fails to log.

**Investigation:**
```bash
ls -ld /opt/company/prod/logs
ls -l /opt/company/prod/logs/app.log
```

**Root Cause:** The `logs/` directory (or the log file itself) lacks write permission for the group/user the application runs as.

**Fix:**
```bash
sudo chown -R appuser:operations /opt/company/prod/logs
sudo chmod -R 770 /opt/company/prod/logs
```

**Verification:**
```bash
sudo -u appuser touch /opt/company/prod/logs/test.log
```

---

### 10. Developer can modify production files when they should not

**Symptoms:** A security review finds that a developer account has write access to production application files.

**Investigation:**
```bash
ls -l /opt/company/prod/app
id dev01
getent group operations
```

**Root Cause:** The developer was mistakenly added to the `operations` or `deployers` group, or production directories were set with overly broad permissions (e.g., `775` or `777`) instead of restricting to the appropriate group.

**Fix:**
```bash
sudo gpasswd -d dev01 operations     # remove the developer from the privileged group
sudo chmod 750 /opt/company/prod/app # tighten permissions
```

**Verification:**
```bash
id dev01
sudo -u dev01 touch /opt/company/prod/app/test 2>&1   # should return Permission denied
```

---

## 18. Best Practices

- **Follow the principle of least privilege** — grant only the access a user or process actually needs to do its job, nothing more.
- **Avoid unnecessary root access** — use `sudo` for specific commands rather than logging in as root or handing out root passwords.
- **Use groups instead of individually assigning permissions** — it scales, stays consistent, and is easier to audit.
- **Avoid `chmod 777`** — this grants full read/write/execute to literally everyone on the system; it's almost never the right fix and usually masks a deeper ownership/group problem.
- **Be careful with recursive `chmod`** — always verify the target path first, and consider separating file vs. directory permission logic (see [Part 6](#6-chmod--changing-permissions)).
- **Protect `/etc/shadow`** — never loosen its permissions; it should remain root-only.
- **Use `sudo` responsibly** — prefer `sudoers.d/` drop-in files with narrowly scoped rules over broad blanket access, and always edit with `visudo`.
- **Separate development and production access** — developers building software should not, by default, have the same access as operators running it in production.
- **Restrict production access** — production should have the smallest, most carefully managed set of privileged accounts on the system.
- **Use dedicated deployment users where appropriate** — automation and CI/CD pipelines should run as their own service account (e.g., `deploy`), not as a personal or shared human account.
- **Never store plaintext passwords in Git** — passwords and secrets belong in a secrets manager or encrypted vault, never in version control.
- **Never commit secrets** — this includes API keys, tokens, private keys, and `.env` files; use `.gitignore` and secret scanning tools to help enforce this.
- **Verify permissions after making changes** — always run `ls -l`/`ls -ld`/`stat` (and test as the actual affected user, e.g., with `sudo -u username`) after any permission or ownership change, rather than assuming it worked.

---

## 19. Cheat Sheet

### Permission values

| Octal | Symbolic | Meaning |
|---|---|---|
| 7 | rwx | Read + write + execute |
| 6 | rw- | Read + write |
| 5 | r-x | Read + execute |
| 4 | r-- | Read only |
| 3 | -wx | Write + execute |
| 2 | -w- | Write only |
| 1 | --x | Execute only |
| 0 | --- | No access |

### Common `chmod` examples

```bash
chmod 755 file            # rwxr-xr-x — standard executable
chmod 644 file            # rw-r--r-- — standard readable file
chmod 700 file            # rwx------ — fully private
chmod 750 dir             # rwxr-x--- — owner full, group read/execute
chmod 770 dir             # rwxrwx--- — owner + group full access
chmod 600 secret.env      # rw------- — private secret file
chmod 640 config.conf     # rw-r----- — owner edits, group reads
chmod -R 755 dir/         # recursive (use with caution)
chmod u+x script.sh       # add execute for owner
chmod g+w file.txt        # add write for group
chmod o-r file.txt        # remove read for others
chmod u=rwx,g=rx,o= file  # explicit symbolic assignment
```

### Common `chown` examples

```bash
chown user file                  # change owner only
chown user:group file            # change owner and group
chown :group file                # change group only (via chown)
chown -R user:group directory/   # recursive ownership change
```

### Common `chgrp` examples

```bash
chgrp group file
chgrp -R group directory/
```

### User commands

```bash
sudo adduser username         # interactive user creation (Debian/Ubuntu)
sudo useradd -m -s /bin/bash username   # scriptable user creation
sudo passwd username          # set/change password
id username                   # show UID, GID, groups
getent passwd username        # show /etc/passwd entry
sudo userdel username         # delete user, keep home directory
sudo userdel -r username      # delete user and home directory
```

### Group commands

```bash
sudo groupadd groupname             # create group
sudo groupdel groupname             # delete group
groups username                     # list a user's groups
getent group groupname              # show group entry/members
sudo usermod -aG groupname username # add user to group (safe, append)
sudo gpasswd -d username groupname  # remove user from group
newgrp groupname                    # apply new group to current session
```

### `sudo` commands

```bash
sudo command              # run a single command as root
sudo -u username command  # run a command as a specific user
sudo -i                   # start an interactive root shell
sudo visudo               # safely edit /etc/sudoers
```

### Important configuration files

| File | Purpose |
|---|---|
| `/etc/passwd` | User account records |
| `/etc/shadow` | Hashed passwords, password aging (root-only) |
| `/etc/group` | Group definitions and members |
| `/etc/gshadow` | Group passwords/administrators |
| `/etc/sudoers` | `sudo` privilege rules (edit only via `visudo`) |
| `/etc/sudoers.d/` | Drop-in directory for modular `sudo` rules |

### Troubleshooting commands

```bash
ls -l file            # view file permissions/ownership
ls -ld directory       # view directory's own permissions (not its contents)
id username            # view UID/GID/group memberships
groups username        # view group memberships
getent passwd username # look up a user account
getent group groupname # look up a group
stat file               # detailed metadata, including permission bits and timestamps
namei -l /path/to/file  # show permissions for every component of a path
```

---

**End of documentation.** This reference is intended to grow alongside a real DevOps environment — as new environments, teams, or compliance requirements are introduced, extend the group model and permission matrix in [Part 15](#15-devops-practical-project-dev-staging-production) and [Part 16](#16-permission-matrix) accordingly, rather than reaching for broad, easy-but-insecure permissions like `777`.
