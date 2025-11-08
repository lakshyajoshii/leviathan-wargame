# Leviathan Wargame — Walkthrough (Levels 0 → 2)

**Author:** Lakshya  
**Target:** OverTheWire — Leviathan  
**Tested on:** Leviathan SSH server (leviathan.labs.overthewire.org) — port 2223

---

## Overview
This repository contains a concise, step-by-step walkthrough of the first few levels (leviathan0 → leviathan2) of the OverTheWire *Leviathan* wargame. The goal is to document the commands, techniques, and reasoning used to progress through the game while keeping the write-up educational.

> ⚠️ Note: This walkthrough includes hints & commands used **during the challenge**. It is intentionally *high-level* enough to teach techniques without spoiling advanced puzzles. Use it for learning and not for malicious activity.

---

## Prerequisites
- A terminal on your machine. On Windows, use **WSL (Windows Subsystem for Linux)** or a proper Linux terminal.
- `ssh` client installed (built into most OSes).
- Basic familiarity with Linux commands (`ls`, `cat`, `strings`, `grep`, etc.).
- Network access allowing outbound SSH on port `2223`.

---

## How to connect to Leviathan
From your **local machine** (not while logged into a Leviathan level), connect as follows:

```bash
ssh leviathan0@leviathan.labs.overthewire.org -p 2223
# password: leviathan0
```

**Important:** When you are inside a level (e.g. logged in as `leviathan0`), you **must `exit`** back to your local shell before connecting to the next level. The game blocks SSH connections originating from `localhost` on the server.

---

## Walkthrough — Level 0 → Level 1

### 1. Access the Server
```bash
sshpass -p leviathan0 ssh leviathan0@leviathan.labs.overthewire.org -p2223
```

### 2. List all files
```bash
ls -la
```

### 3. Go into the backup directory
```bash
cd .backup/
```

### 4. Check the contents
```bash
ls
# Output: bookmarks.html
```

### 5. Search for passwords inside the file
```bash
grep password bookmarks.html
```

### 6. Copy the discovered password
```
3QJ3TgzHDq
```

### 7. Use it to connect to the next level
```bash
ssh leviathan1@leviathan.labs.overthewire.org -p 2223
# password: 3QJ3TgzHDq
```

---

## Walkthrough — Level 1 → Level 2

### 1. List all files
```bash
ls -la
```

### 2. Find and run the `check` binary
```bash
./check
```

It prompts for a password.

### 3. Input the correct password
```
sex
```

When entered correctly, the program spawns a shell with `$` prompt (a shell as `leviathan2`).

### 4. Retrieve the next password
```bash
cat /etc/leviathan_pass/leviathan2
# Output: NsN1HwFoyN
```

### 5. Connect as `leviathan2`
```bash
exit
ssh leviathan2@leviathan.labs.overthewire.org -p 2223
# password: NsN1HwFoyN
```

---

## Notes & Learnings
- Always explore hidden folders using `ls -la`.
- Look for interesting file permissions (`r-s` indicates SUID programs).
- Use tools like `grep` and `strings` to discover embedded credentials or keywords.
- Be cautious: do not run random binaries without understanding their permissions.
- Each level teaches basic security and privilege escalation principles.

---

## Security & Ethics
- Only use these techniques on machines you own or on intentionally vulnerable targets (like OverTheWire). Do not attempt to exploit remote systems without permission.
- Do not store or publish any **sensitive** or **private** credentials you may encounter.

---


## Acknowledgements
- OverTheWire — for the Leviathan wargame and learning platform.

---


