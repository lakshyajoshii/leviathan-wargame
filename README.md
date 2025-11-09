# Leviathan Wargame — Walkthrough (Levels 0 → 7)

**Author:** Lakshya
**Target:** OverTheWire — Leviathan
**Tested on:** Leviathan SSH server (leviathan.labs.overthewire.org) — port 2223

---

## Table of Contents

* [Overview](#overview)
* [Prerequisites](#prerequisites)
* [How to connect to Leviathan](#how-to-connect-to-leviathan)
* [Walkthrough — Level 0 → Level 1](#walkthrough--level-0-→-level-1)
* [Walkthrough — Level 1 → Level 2](#walkthrough--level-1-→-level-2)
* [Walkthrough — Level 2 → Level 3](#walkthrough--level-2-→-level-3)
* [Walkthrough — Level 3 → Level 4](#walkthrough--level-3-→-level-4)
* [Walkthrough — Level 4 → Level 5](#walkthrough--level-4-→-level-5)
* [Walkthrough — Level 5 → Level 6](#walkthrough--level-5-→-level-6)
* [Walkthrough — Level 6 → Level 7](#walkthrough--level-6-→-level-7)
* [Notes & Learnings](#notes--learnings)
* [Security & Ethics](#security--ethics)
* [Contribution Guidelines](#contribution-guidelines)
* [Acknowledgements](#acknowledgements)

---

## Overview

This repository contains a concise, step-by-step walkthrough of the first several levels (leviathan0 → leviathan7) of the OverTheWire *Leviathan* wargame. The goal is to document the commands, techniques, and reasoning used to progress through the game while keeping the write-up educational.

> ⚠️ Note: This walkthrough includes hints & commands used **during the challenge**. It is intentionally *high-level* enough to teach techniques without spoiling advanced puzzles. Use it for learning and not for malicious activity.

---

## Prerequisites

* A terminal on your machine. On Windows, use **WSL (Windows Subsystem for Linux)** or a proper Linux terminal.
* `ssh` client installed (built into most OSes).
* Basic familiarity with Linux commands (`ls`, `cat`, `strings`, `grep`, etc.).
* Network access allowing outbound SSH on port `2223`.

---

## How to connect to Leviathan

From your **local machine** (not while logged into a Leviathan level), connect as follows:

```bash
ssh leviathan0@leviathan.labs.overthewire.org -p 2223
# initial password: leviathan0
```

**Important:** When logged into a level (e.g. as `leviathan0`), **exit** back to your local shell before connecting to the next level. The OverTheWire server blocks SSH connections originating from the server itself.

---

## Walkthrough — Level 0 → Level 1

### Goals

* Learn to find hidden files and search file contents for credentials.

### Commands & Steps

```bash
# connect (example uses sshpass for automation; prefer interactive ssh)
sshpass -p leviathan0 ssh leviathan0@leviathan.labs.overthewire.org -p2223

# list everything, including hidden files
ls -la

# change into the backup folder found in the home directory
cd .backup/

# list files in the backup folder
ls
# Output: bookmarks.html

# search for the word 'password' inside the bookmarks.html file
grep password bookmarks.html
```

### Solution (password for level 1)

```
3QJ3TgzHDq
```

### Next step

```bash
# exit current shell, then connect as leviathan1 (from your local machine)
exit
ssh leviathan1@leviathan.labs.overthewire.org -p 2223
# password: 3QJ3TgzHDq
```

---

## Walkthrough — Level 1 → Level 2

### Goals

* Run and inspect binaries provided in the home directory; learn to use basic input to trigger behaviour.

### Commands & Steps

```bash
# once connected as leviathan1
ls -la

# run the provided binary (named 'check' in this walkthrough)
./check
```

`./check` prompts for input (a password). When the correct password is supplied, the binary spawns a shell as `leviathan2`.

### Hint

* Try simple inputs first. Some CTF binaries expect a small, easy-to-guess string (or may check against an embedded string).

### Solution (password to input to spawn the shell)

```
sex
```

### Retrieve next-level password

```bash
# after the spawned shell (you are now in lev2's shell), read the password file
cat /etc/leviathan_pass/leviathan2
# Output: NsN1HwFoyN
```

### Next step

```bash
exit
ssh leviathan2@leviathan.labs.overthewire.org -p 2223
# password: NsN1HwFoyN
```

---

## Walkthrough — Level 2 → Level 3

### Goals

* Inspect files and binaries; discover how the level binary behaves.

### Commands & Steps

```bash
# connect as lev2 and list files
ls -la

# find and run the level binary (name may be 'leviathan2' or similar)
./leviathan2

# sometimes use tools like strings, ltrace or file to inspect behaviour
strings ./leviathan2 | head -n 50
file ./leviathan2
ltrace ./leviathan2
```

### Hint

* Look for suspicious outputs or strings within binaries that might hint at next-step files or passwords.

### Expected action

* Use the string or trace hints to locate `/etc/leviathan_pass/leviathan3` and `cat` it to retrieve the next password.

---

## Walkthrough — Level 3 → Level 4

### Goals

* Use `ltrace` to inspect a binary's runtime library calls and identify how it handles input.

### Commands & Steps

```bash
ssh leviathan3@leviathan.labs.overthewire.org -p 2223
ls

# trace the binary to observe library calls (shows calls like snprintf/printf)
ltrace ./level3

# run the binary directly
./level3

# check for embedded strings
strings ./level3 | grep -i "printf\|snprintf\|password\|leviathan"

# finally read the password file
cat /etc/leviathan_pass/leviathan4
```

### Solution (password for level 4)

```
WG1egElCvO
```

---

## Walkthrough — Level 4 → Level 5

### Goals

* Discover hidden directories and decode simple encodings (e.g., binary ASCII sequences).

### Commands & Steps

```bash
ssh leviathan4@leviathan.labs.overthewire.org -p 2223

# list including hidden files
ls -la

# move into discovered hidden folder
cd .trash/

# view the file containing binary-like data (example sequence provided)
cat <file-in-.trash>
# observed content:
# 00110000 01100100 01111001 01111000 01010100 00110111 01000110 00110100 01010001 01000100 00001010

# decode the space-separated binary octets into ASCII using python
python3 - <<'PY'
bs = "00110000 01100100 01111001 01111000 01010100 00110111 01000110 00110100 01010001 01000100"
print(''.join([chr(int(b,2)) for b in bs.split()]))
PY

# read the next-level password file if present
cat /etc/leviathan_pass/leviathan5
```

### Solution (password for level 5)

```
0dyxT7F4QD
```

---

## Walkthrough — Level 5 → Level 6

### Goals

* Trace file operations, observe `/tmp` usage, and exploit symlink behavior when applicable.

### Commands & Steps

```bash
ssh leviathan5@leviathan.labs.overthewire.org -p 2223
ls

# inspect runtime behaviour; ltrace shows file opens/reads
ltrace ./leviathan5

# create a temporary file and write sample content
touch /tmp/file.log ; echo "hello" > /tmp/file.log

# re-run ltrace or the binary to see if it reads /tmp/file.log
ltrace ./leviathan5
cat /tmp/file.log

# if the binary reads from /tmp/file.log, replace it with a symlink to the next password
ln -sf /etc/leviathan_pass/leviathan6 /tmp/file.log

# run the binary again; it should read the symlinked file
./leviathan5
```

### Solution (password for level 6)

```
szo7HDB88w
```

---

## Walkthrough — Level 6 → Level 7

### Goals

* Understand brute-forcing small keyspaces vs. reasoning about algorithmic checks.

### Commands & Steps

```bash
ssh leviathan6@leviathan.labs.overthewire.org -p 2223
ls

# run the level binary to observe its input expectations
./leviathan6

# if it expects a 4-digit code and there is no easy analytic way, a brute-force loop can be used locally
# be cautious and respectful to the server: avoid spamming the remote host in production systems
for i in {0000..9999} ; do ./leviathan6 $i; done

# when correct code is reached, read the next-level password file
cat /etc/leviathan_pass/leviathan7
```

### Solution (password for level 7)

```
qEs5Io5yM8
```

---

## Notes & Learnings

* Always use `ls -la` to reveal hidden files and dot-directories.
* `strings`, `file`, `ltrace`, and `strace` are essential for inspecting unknown binaries; use them to discover file paths and runtime behaviour.
* `ln -s` (symlink) can be used to direct a program to read a different file — a common CTF technique demonstrating race/symlink vulnerabilities.
* Brute force is acceptable for very small keyspaces but learn to prefer analysis and tracing to avoid unnecessary load.
* Use collapsible sections in your public README to place hints before answers so readers can try themselves.

---

## Security & Ethics

* Only use these techniques on machines you own or on intentionally vulnerable targets (like OverTheWire). Do not attempt to exploit remote systems without permission.
* Do not store or publish any **sensitive** or **private** credentials you may encounter.
* Respect the OverTheWire terms of service and do not cause unnecessary load on their infrastructure.

---

## Contribution Guidelines

* Add each new level as its own markdown file under `levels/` and link it from the main README's Table of Contents.
* Keep Hints before Solutions (use `<details><summary>` blocks to hide solutions by default).
* When adding command output, redact sensitive-looking strings if unsure, or annotate that the output is from the wargame and safe to publish.
* Mention tools used and why (e.g., `ltrace` shows library calls, `strace` shows syscalls, `strings` extracts printable sequences).

---

## Acknowledgements

* OverTheWire — for the Leviathan wargame and learning platform.

---

*The passwords for every level can be different for each user as they change over time but the proess of finding them will be the same.*
