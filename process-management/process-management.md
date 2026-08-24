# Ubuntu Process Management Commands (Deep Guide)

This document explains the essential Ubuntu/Linux commands for viewing, controlling, and managing processes — what each command **can do**, what it **cannot do**, and practical examples for each.

---

## 📑 Index

| # | Command | Category | Jump |
|---|---------|----------|------|
| 1 | `ps` | Viewing Processes | [Go](#1-ps-) |
| 2 | `top` | Viewing Processes | [Go](#2-top-) |
| 3 | `htop` | Viewing Processes | [Go](#3-htop-) |
| 4 | `pstree` | Viewing Processes | [Go](#4-pstree-) |
| 5 | `pgrep` | Viewing Processes | [Go](#5-pgrep-) |
| 6 | `kill` | Controlling Processes | [Go](#6-kill-) |
| 7 | `killall` | Controlling Processes | [Go](#7-killall-) |
| 8 | `pkill` | Controlling Processes | [Go](#8-pkill-) |
| 9 | `nice` | Priority Management | [Go](#9-nice-) |
| 10 | `renice` | Priority Management | [Go](#10-renice-) |
| 11 | `nohup` | Background/Session Management | [Go](#11-nohup-) |
| 12 | `jobs` | Background/Session Management | [Go](#12-jobs-) |
| 13 | `fg` | Background/Session Management | [Go](#13-fg-) |
| 14 | `bg` | Background/Session Management | [Go](#14-bg-) |
| 15 | `disown` | Background/Session Management | [Go](#15-disown-) |
| 16 | `systemctl` | Service Management | [Go](#16-systemctl-) |
| 17 | `service` | Service Management | [Go](#17-service-) |
| 18 | `uptime` | System Load | [Go](#18-uptime-) |
| 19 | `free` | System Load | [Go](#19-free-) |
| 20 | `vmstat` | System Load | [Go](#20-vmstat-) |
| 21 | `lsof` | Process/File Inspection | [Go](#21-lsof-) |
| 22 | `strace` | Process/File Inspection | [Go](#22-strace-) |
| 23 | `watch` | Utilities | [Go](#23-watch-) |

Also see: [Quick Reference Table](#quick-reference-table) · [Real-World Troubleshooting Example](#real-world-troubleshooting-example)

---

## Viewing Processes

### 1. `ps` ⭐⭐⭐⭐⭐
**P**rocess **S**tatus — takes a snapshot of currently running processes.

**Can do:** Show PID, CPU/memory usage, user, command, and process state at the moment it's run.
**Cannot do:** It's a one-time snapshot, not live/real-time — for continuous monitoring use `top`/`htop`.

```bash
ps aux                          # show all running processes (BSD style, very common)
ps -ef                          # show all processes (System V style, with full command)
ps aux | grep nginx             # find a specific process by name
ps -o pid,ppid,cmd -p 1234      # show specific columns for a given PID
```

---

### 2. `top` ⭐⭐⭐⭐⭐
Real-time, continuously updating view of running processes and system resource usage.

**Can do:** Live view of CPU/memory usage per process, sortable, interactive (can kill processes from within).
**Cannot do:** Interface is a bit dated/hard to read compared to `htop` — no mouse support, no color by default on all systems.

```bash
top                              # launch live process monitor
top -u username                  # show processes for a specific user only
```

**Useful keys inside `top`:**
| Key | Action |
|-----|--------|
| `P` | Sort by CPU usage |
| `M` | Sort by memory usage |
| `k` | Kill a process (prompts for PID) |
| `q` | Quit |

---

### 3. `htop` ⭐⭐⭐⭐⭐
An improved, more user-friendly, colorized version of `top`.

**Can do:** Mouse support, color-coded bars for CPU/memory, easy scrolling, tree view, search, kill via function keys.
**Cannot do:** Not installed by default on all Ubuntu systems — needs manual installation.

```bash
sudo apt install htop            # install if not already present
htop                              # launch interactive process viewer
```

**Useful keys inside `htop`:** `F3` search, `F5` tree view, `F9` kill, `F10` quit.

---

### 4. `pstree` ⭐⭐⭐⭐
Displays running processes as a tree, showing parent-child relationships.

**Can do:** Quickly visualize which process spawned which (helpful for understanding process hierarchy).
**Cannot do:** Doesn't show CPU/memory usage — purely structural, use `ps`/`top` for resource stats.

```bash
pstree                           # tree view of all processes
pstree -p                        # include PIDs in the tree
pstree username                  # tree view for a specific user's processes
```

---

### 5. `pgrep` ⭐⭐⭐⭐
Searches for processes by name and returns matching PIDs.

**Can do:** Quickly find the PID(s) of a running process by name, without piping `ps` into `grep`.
**Cannot do:** Only returns PIDs/names — doesn't show CPU/memory details (combine with `ps -p` for that).

```bash
pgrep nginx                      # get PID(s) of processes named "nginx"
pgrep -l ssh                     # show PID + process name together
pgrep -u username                # find processes owned by a specific user
```

---

## Controlling Processes

### 6. `kill` ⭐⭐⭐⭐⭐
Sends a signal to a process (commonly used to terminate it), targeting a specific **PID**.

**Can do:** Gracefully or forcefully stop a process, or send other signals (pause, resume, reload config).
**Cannot do:** Requires the PID — you can't target a process by name directly (use `killall`/`pkill` for that).

```bash
kill 1234                        # send default signal (SIGTERM - graceful stop) to PID 1234
kill -9 1234                     # force kill (SIGKILL - cannot be ignored by the process)
kill -l                          # list all available signals
```

---

### 7. `killall` ⭐⭐⭐⭐
Kills all processes matching a given **name** (not PID).

**Can do:** Stop every instance of a program at once (useful when multiple copies are running).
**Cannot do:** Matches by exact process name — a slightly wrong name won't match anything, and it affects ALL matching processes, which can be risky.

```bash
killall firefox                  # kill all firefox processes
killall -9 chrome                # force kill all chrome processes
killall -u username node         # kill all "node" processes owned by a specific user
```

---

### 8. `pkill` ⭐⭐⭐⭐
Similar to `killall`, but more flexible — can match by partial name, user, or other attributes.

**Can do:** Pattern-based matching (regex-like), filter by user, terminal, or parent process.
**Cannot do:** Because it supports partial matches, it's easy to accidentally match and kill unintended processes — use carefully.

```bash
pkill nginx                      # kill processes matching "nginx"
pkill -9 -u username             # force kill all processes owned by a user
pkill -f "python script.py"      # match against the full command line, not just process name
```

---

## Priority Management

### 9. `nice` ⭐⭐⭐
Starts a new process with a specified priority (niceness value: -20 highest priority, 19 lowest).

**Can do:** Control how much CPU priority a new process gets relative to others.
**Cannot do:** Only works when **starting** a process — cannot change the priority of an already-running one (use `renice` for that). Regular users can only lower priority (increase niceness), not raise it, without `sudo`.

```bash
nice -n 10 ./backup_script.sh    # start a script with lower priority (nicer to other processes)
nice -n -5 ./important_task.sh   # start with higher priority (requires sudo for negative values)
```

---

### 10. `renice` ⭐⭐⭐
Changes the priority of an **already running** process.

**Can do:** Adjust CPU priority of a live process without restarting it.
**Cannot do:** Cannot change I/O priority (that's a separate tool, `ionice`) — only CPU scheduling priority.

```bash
renice 10 -p 1234                # change priority of PID 1234 to 10
sudo renice -5 -p 1234           # raise priority (needs sudo for negative values)
renice 5 -u username             # change priority for all processes of a user
```

---

## Background & Session Management

### 11. `nohup` ⭐⭐⭐⭐
Runs a command so it keeps running even after you log out or close the terminal (ignores the "hangup" signal).

**Can do:** Keep long-running scripts/processes alive after disconnecting from SSH/terminal.
**Cannot do:** Doesn't run the process in the background by itself — you still need to add `&` for that.

```bash
nohup ./long_script.sh &          # run in background, survives terminal close
nohup python app.py > output.log 2>&1 &   # redirect output to a log file too
```

---

### 12. `jobs` ⭐⭐⭐
Lists background/suspended jobs running in the **current terminal session**.

**Can do:** Show which jobs are running or stopped in the current shell.
**Cannot do:** Only shows jobs from the current terminal session — not system-wide processes (use `ps`/`top` for that).

```bash
jobs                              # list background jobs in this session
jobs -l                           # include PIDs in the listing
```

---

### 13. `fg` ⭐⭐⭐
**F**ore**g**round — brings a background/suspended job back to the foreground.

**Can do:** Resume interacting with a job that was running in the background or paused.
**Cannot do:** Only works on jobs from the current shell session (job numbers from `jobs`).

```bash
fg                                 # bring the most recent background job to foreground
fg %2                              # bring job number 2 (from `jobs` list) to foreground
```

---

### 14. `bg` ⭐⭐⭐
**B**ac**k**ground — resumes a paused job, but keeps it running in the background.

**Can do:** Continue a job that was stopped (e.g., via `Ctrl+Z`) without bringing it to the foreground.
**Cannot do:** Can't background a job that hasn't been started/suspended in the current shell.

```bash
bg                                  # resume the most recent stopped job in the background
bg %1                               # resume job number 1 in the background
```

---

### 15. `disown` ⭐⭐⭐
Removes a job from the shell's job table, so it survives even if the terminal is closed (without needing `nohup`).

**Can do:** Detach an already-running background job from the current shell session.
**Cannot do:** Doesn't redirect output like `nohup` does — output may still cause issues if the terminal closes mid-write.

```bash
./long_task.sh &                    # start a job in background
disown                              # detach the most recent background job from the shell
disown -a                           # detach all background jobs
```

---

## Service Management

### 16. `systemctl` ⭐⭐⭐⭐⭐
The modern tool to manage `systemd` services (start, stop, enable, check status).

**Can do:** Manage system services, view logs integration, enable/disable services at boot, check detailed status.
**Cannot do:** Only works on systemd-based systems (which is virtually all modern Ubuntu, but not universal across all Linux distros).

```bash
sudo systemctl status nginx         # check status of a service
sudo systemctl restart nginx        # restart a service
sudo systemctl enable nginx         # enable a service to start on boot
sudo systemctl stop nginx           # stop a service
```

---

### 17. `service` ⭐⭐⭐
Older, simpler interface for managing services — still works on modern Ubuntu as a wrapper around `systemctl`.

**Can do:** Quick start/stop/restart of services with simpler syntax.
**Cannot do:** Doesn't offer the deeper control/status detail that `systemctl` provides (like boot-enable status, dependency info).

```bash
sudo service nginx status           # check service status (older syntax)
sudo service nginx restart          # restart a service (older syntax)
```

---

## System Load & Resources

### 18. `uptime` ⭐⭐⭐⭐
Shows how long the system has been running, plus load averages.

**Can do:** Quick glance at system load (1, 5, 15-minute averages) and number of logged-in users.
**Cannot do:** Doesn't break down load by individual process (use `top`/`htop` for that detail).

```bash
uptime                              # e.g. output: up 5 days, load average: 0.15, 0.20, 0.18
```

---

### 19. `free` ⭐⭐⭐⭐⭐
Shows memory (RAM) and swap usage.

**Can do:** Quickly check total, used, free, and available memory/swap.
**Cannot do:** Doesn't show which specific process is using how much memory (use `top`/`ps` for that).

```bash
free -h                             # human-readable memory usage (GB/MB)
free -h -s 2                        # refresh every 2 seconds (continuous monitoring)
```

---

### 20. `vmstat` ⭐⭐⭐
**V**irtual **M**emory **Stat**istics — reports on processes, memory, paging, block I/O, and CPU activity.

**Can do:** Get a combined snapshot of system performance (useful for diagnosing bottlenecks).
**Cannot do:** Output is dense/less human-friendly at first glance compared to `htop` or `free`.

```bash
vmstat                              # single snapshot
vmstat 2 5                          # update every 2 seconds, 5 times total
```

---

## Process & File Inspection

### 21. `lsof` ⭐⭐⭐⭐
**L**i**s**t **O**pen **F**iles — shows which files (including network sockets) are opened by which processes.

**Can do:** Find out which process is using a specific file or port (great for "port already in use" errors).
**Cannot do:** Can be slow on systems with a huge number of open files; requires `sudo` for full system-wide visibility.

```bash
sudo lsof -i :8080                  # find which process is using port 8080
lsof -p 1234                        # list all files opened by PID 1234
lsof /var/log/syslog                # find which process(es) have this file open
```

---

### 22. `strace` ⭐⭐⭐
Traces system calls made by a process — useful for deep debugging.

**Can do:** See exactly what a program is doing at the system-call level (file access, network calls, errors).
**Cannot do:** Produces very verbose output; can slow down the traced process; requires some familiarity with system calls to interpret.

```bash
strace ls                           # trace system calls made by the `ls` command
strace -p 1234                      # attach to and trace an already-running process
strace -c ls                        # show a summary/count of system calls instead of full trace
```

---

## Utilities

### 23. `watch` ⭐⭐⭐⭐
Runs a command repeatedly at a set interval, showing live updating output.

**Can do:** Turn any static command into a "live dashboard" (e.g., watch disk space change, watch process count).
**Cannot do:** Only re-runs the command — doesn't provide interactivity like `top`/`htop`.

```bash
watch -n 2 df -h                    # refresh disk usage every 2 seconds
watch -n 1 "ps aux | grep nginx"    # continuously monitor a specific process
```

---

## Quick Reference Table

| Command | Purpose |
|---------|---------|
| `ps` | Snapshot of running processes |
| `top` | Live process/resource monitor |
| `htop` | Improved, colorized live monitor |
| `pstree` | Process tree (parent-child view) |
| `pgrep` | Find PID(s) by process name |
| `kill` | Send signal to a process (by PID) |
| `killall` | Kill processes by exact name |
| `pkill` | Kill processes by pattern/user |
| `nice` | Start a process with set priority |
| `renice` | Change priority of a running process |
| `nohup` | Keep process alive after logout |
| `jobs` | List background jobs in current shell |
| `fg` | Bring a background job to foreground |
| `bg` | Resume a stopped job in background |
| `disown` | Detach a job from the shell session |
| `systemctl` | Manage systemd services |
| `service` | Manage services (legacy syntax) |
| `uptime` | System uptime and load averages |
| `free` | Memory/swap usage |
| `vmstat` | System performance snapshot |
| `lsof` | Show open files/ports per process |
| `strace` | Trace system calls of a process |
| `watch` | Repeat a command at intervals |

---

## Real-World Troubleshooting Example

**Scenario: "A process is stuck, using too much CPU, and I don't know why."**

```bash
top                                  # 1. See which process is consuming high CPU (note the PID)
ps -p 1234 -o pid,ppid,cmd,%cpu,%mem # 2. Get detailed info on that specific PID
pstree -p 1234                       # 3. See what spawned it / its child processes
strace -p 1234                       # 4. See what system calls it's stuck on (optional deep-dive)
lsof -p 1234                         # 5. Check what files/ports it currently has open
kill 1234                            # 6. Try graceful termination first
kill -9 1234                         # 7. Force kill only if it doesn't respond to step 6
```

**Scenario: "Port 8080 is already in use, I can't start my app."**

```bash
sudo lsof -i :8080                   # find which process is holding the port
kill -9 <PID>                        # kill that process
```

---

*Reference guide for essential Ubuntu/Linux process management commands — with capabilities, limitations, and practical examples for real-world troubleshooting.*
