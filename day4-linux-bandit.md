# Day 4 — Linux Fundamentals + OverTheWire Bandit + TryHackMe Linux Part 1

**Date:** April 6, 2026
**Time spent:** Approximately 2 hours
**What I covered:** linuxjourney.com Grasshopper course, Linux commands in Kali terminal, OverTheWire Bandit levels 0 through 3, TryHackMe Linux Fundamentals Part 1

---

## How the day went

Linux finally started feeling natural today. The first three days were all about understanding networks from the outside — ports, protocols, packets. Today I went inside the machine. Working through linuxjourney.com and immediately typing every command in Kali made the commands stick in a way that just reading never would.

OverTheWire Bandit was the highlight. It is technically a game — you SSH into a remote server and use Linux commands to find hidden passwords. But it is also exactly how real penetration testing works. Finding a file with unusual permissions, reading a file with a name that breaks normal syntax, and discovering hidden files — these are real CTF and real-world techniques. I did not expect a learning exercise to feel that genuine.

---

## Why Linux matters so much in cybersecurity

Before I get into what I learned, I want to write down why I am spending time on this.

90% of servers on the internet run Linux. Every major cloud provider — AWS, Google Cloud, Azure — runs Linux underneath. Most cybersecurity tools are built for Linux. Kali Linux itself is built on Debian Linux. When you get a shell on a compromised system during a penetration test, it is almost always a Linux shell.

You cannot be effective in cybersecurity without knowing Linux. This is not optional background knowledge. It is the environment where the work happens.

---

## linuxjourney.com — Grasshopper Course

I completed 8 lessons today from linuxjourney.com. The site is free, has no login required, and each lesson is short and focused. Most importantly, I ran every single command in my Kali terminal immediately after reading it. The lesson teaches it, the terminal makes it real.

---

## Linux Commands Learned Today — with explanations

### Navigation commands

```
pwd
```
Print Working Directory. Shows your current location in the file system. When you first open a terminal, you are in /home/kali — that is your home directory. Think of it like typing your current address.

```
ls
```
List. Shows files and folders in your current directory. Basic but used constantly.

```
ls -la
```
List with -l (long format showing permissions, owner, size, date) and -a (all files, including hidden ones). Hidden files start with a dot. This is critical in security — attackers hide malware in hidden files and directories.

```
cd /home
cd ..
cd ~
```
Change Directory. /home goes to the home directory. .. goes up one level. ~ goes to your personal home directory no matter where you are. I use cd constantly — every terminal session involves navigating with cd.

### File operations

```
touch myfile.txt
```
Creates an empty file. Also updates the timestamp of an existing file. Used in scripts to create placeholder files and in security research to test file system permissions.

```
mkdir myfolder
mkdir -p folder1/folder2/folder3
```
Make a directory. -p creates the full path, including parent directories that do not exist yet. The -p flag is used in automation scripts constantly.

```
cat myfile.txt
```
Concatenate — outputs the content of a file to the terminal. One of the most used commands. In security: reading configuration files, log files, and password files.

```
echo "some text" > myfile.txt
```
Writes text into a file. The > overwrites any existing content. >> appends to the end without overwriting. This distinction matters in scripts.

```
cp myfile.txt copy.txt
mv copy.txt renamed.txt
rm renamed.txt
```
Copy, move/rename, remove. Standard file operations. rm is permanent — Linux has no recycle bin by default. In security, attackers use rm to delete evidence of their activity.

### Searching

```
find / -name "myfile.txt" 2>/dev/null
```
Searches the entire file system (/) for a file named myfile.txt. The 2>/dev/null hides permission error messages, so the output is clean. This is a fundamental reconnaissance technique — inside a compromised system, finding is how you locate sensitive files like passwords, keys, and configuration files.

```
grep "password" /etc/passwd
```
Searches for the word "password" inside a specific file. grep is probably the most used command in cybersecurity after ls. It searches for patterns inside files. Used for: searching log files for failed logins, finding credentials in configuration files, and parsing tool output.

### Getting help

```
man ls
```
Manual pages. Shows full documentation for any command. Press Q to quit. In a real engagement, when you need to know every flag a tool supports, man is where you go.

---

## File Permissions — why they matter for security

When I ran ls -la in Kali today, I saw output like this:

```
-rwxr-xr-- 1 kali kali 1234 Apr 6 16:00 myscript.sh
drwxr-xr-x 2 kali kali 4096 Apr 6 15:00 myfolder
```

That string of letters at the start is the permission set. Breaking it down:

```
- rwx r-x r--
│  │   │   │
│  │   │   └── Others: read only
│  │   └────── Group: read + execute
│  └────────── Owner: read + write + execute
└───────────── File type: - = file, d = directory
```

**r = read** — can view the file contents
**w = write** — can modify the file
**x = execute** — can run the file as a program

**Why this matters for hacking:** The most common Linux privilege escalation technique is finding a file with SUID permissions set — meaning the file runs with the owner's permissions rather than the caller's. If a SUID file is owned by root and has a vulnerability, a low-privilege attacker can exploit it to gain root access. Finding SUID files is one of the first things an attacker does after getting initial access to a Linux system.

```
find / -perm -4000 2>/dev/null
```
This command finds every SUID file on the system. I ran it in Kali today and found several — including some that are common privilege escalation targets on real systems.

---

## OverTheWire Bandit — Levels 0 through 3

OverTheWire Bandit is a free online hacking game at overthewire.org/wargames/bandit. You SSH into a remote Linux server, and each level gives you a challenge. Solve it using Linux commands to find the password for the next level.

This is not a simulation of real Linux work. It IS real Linux work on a real remote server.

### Connecting to the server

```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

This is an SSH connection on port 2220 (non-standard port — another real-world concept). The first time you connect it asks you to verify the server fingerprint — you type yes. The password is bandit0.

---

### Level 0 → 1: Password stored in a file called readme

**Challenge:** Find the password for the next level.

**Commands used:**
```
ls
cat readme
```

**What I found:** A long string of characters — the password for Level 1.

**What this teaches:** Basic file navigation and reading. ls to see what is there. cat to read it. Fundamental, but you would be surprised how many people do not instinctively reach for ls first.

---

### Level 1 → 2: Password in a file named "-"

**Challenge:** The file is named with a single dash character.

**The problem:** In Linux, a single dash (-) means standard input, not a filename. Running cat - just waits for keyboard input. The file exists, but the name breaks normal syntax.

**Solution:**
```
cat ./-
```

The ./ tells Linux "this is a file in the current directory" — forcing it to treat it as a filename rather than a flag. This exact technique appears in real CTF competitions and real penetration tests when attackers encounter files with unusual names.

**What this teaches:** Linux parses special characters before reading filenames. Understanding this is the difference between getting stuck and solving the problem.

---

### Level 2 → 3: Password in a file with spaces in the name

**Challenge:** The file is named "spaces in this filename" — spaces break normal syntax because the shell treats each word as a separate argument.

**Solution:**
```
cat "spaces in this filename."
```

Or alternatively:
```
cat spaces\ in\ this\ filename
```

The quotes tell the shell to treat everything inside as one argument. Backslash escaping does the same thing by telling the shell to treat the next character literally.

**What this teaches:** Shell metacharacter handling. Spaces, special characters, and quotes — these all have meaning in bash. Understanding shell parsing is essential for writing scripts and for exploiting command injection vulnerabilities where an attacker injects shell characters into input fields.

---

### Level 3 → 4: Password in a hidden file inside a directory

**Challenge:** The file is hidden inside a directory called inhere.

**Solution:**
```
cd inhere
ls -la
cat .hidden
```

Regular ls shows nothing. ls -la shows the hidden file .hidden (starts with a dot). cat reads it.

**What this teaches:** Hidden files in Linux. Attackers commonly hide malware, backdoors, and persistence scripts in hidden directories and files — often with names like .bash_history, .ssh, or inside hidden directories with misleading names. ls -la should become automatic any time you are investigating a system.

---

## Bandit Solutions Summary

| Level | Key Linux concept | Command used |
|-------|-------------------|--------------|
| 0 → 1 | Reading files | cat readme |
| 1 → 2 | Special character filenames | cat ./- |
| 2 → 3 | Spaces in filenames | cat "spaces in this filename" |
| 3 → 4 | Hidden files | ls -la then cat .hidden |

---

## TryHackMe — Linux Fundamentals Part 1 Completed

**Room:** tryhackme.com/room/linuxfundamentalspart1
**Status:** 100% complete

This room has a built-in Linux terminal — no Kali needed. It covers basic navigation and file operations with guided exercises and questions.

Key thing I confirmed in this room: the difference between absolute paths and relative paths.

**Absolute path:** Starts from the root /. Example: /home/kali/documents/notes.txt
Always works no matter where you currently are in the file system.

**Relative path:** Based on where you currently are. Example: documents/notes.txt
Only works if you are already in /home/kali.

In security scripts, absolute paths are safer because the script works correctly regardless of where it is called from.

---

## Commands Reference — Day 4 Complete List

```
# Navigation
pwd                          # show current directory
ls                           # list files
ls -la                       # list all files including hidden with details
cd /path                     # change to absolute path
cd ..                        # go up one level
cd ~                         # go to home directory

# Files
touch filename.txt           # create empty file
mkdir foldername             # create directory
mkdir -p path/to/folder      # create full path
cat filename.txt             # read file content
echo "text" > file.txt       # write to file (overwrites)
echo "text" >> file.txt      # append to file
cp source.txt dest.txt       # copy file
mv old.txt new.txt           # move or rename file
rm filename.txt              # delete file permanently

# Searching
find / -name "file.txt" 2>/dev/null    # find file by name
find / -perm -4000 2>/dev/null         # find SUID files
grep "word" filename.txt               # search for text in file

# Help
man command                  # full manual for any command

# OverTheWire Bandit connection
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

---

## What surprised me today

The OverTheWire Bandit challenges feel like puzzles, but they are not really puzzles — they are practical Linux skills. Level 1 is not a trick; it teaches you that filenames can contain characters that break normal syntax, and you need to handle them differently. Level 3 is not a trick, it teaches you that ls alone is incomplete — you always need ls -la.

At every level, I thought about why this would come up in real security work. The answer for every level so far: constantly.

---

## Questions I am carrying forward

- What is the difference between /bin, /sbin, /usr/bin, and /usr/local/bin?
- What does sudo actually do, and why is it a privilege escalation target?
- What are cron jobs, and how do attackers abuse them for persistence?
- What is the PATH environment variable, and how does PATH hijacking work?

---

## Status Update

**TryHackMe rooms completed:** 4 (Networking, OSI Model, DNS in Detail, Linux Fundamentals Part 1)
**OverTheWire Bandit:** Level 4 — continuing tomorrow
**GitHub commits:** 4 consecutive days
**Linux commands memorized:** 18

---

## Tomorrow — Day 5

- Professor Messer DNS Record Types video
- TryHackMe: HTTP in Detail room
- TryHackMe: DNS in Detail room
- Python: Automate the Boring Stuff, chapters 1 and 2
- Write first Python script and upload to GitHub
