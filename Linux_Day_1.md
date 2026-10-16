# Global AIIT — Linux Day 1 (Ubuntu 22 on AWS EC2)

## Session Goal (1.5 Hours)

By the end of this session, students should be able to:

- Understand what Linux is.
- Understand EC2 and SSH basics.
- Launch and connect to an Ubuntu server.
- Navigate the Linux filesystem confidently.
- Create, edit, copy, and delete files and folders.
- Understand basic permissions and users.
- Run basic system monitoring commands.
- Feel comfortable using the terminal.

---

# Part 1 — Introduction

## What is Linux?

- Open-source operating system.
- Most servers run Linux.
- Cloud, DevOps, AI, and Kubernetes depend heavily on Linux.

## What is AWS EC2?

- A virtual machine in the cloud.
- Allows you to rent and use a server on demand.

## What is SSH?

- SSH stands for Secure Shell.
- It provides secure remote login to a Linux server.

---

# Part 2 — Launch Ubuntu EC2

1. Open the AWS Management Console and navigate to EC2.
2. Click **Launch Instance**.
3. Set the instance name to `linux-practice`.
4. Select **Ubuntu Server 22.04 LTS**.
5. Choose instance type `t2.micro`, if available and suitable.
6. Create or select a key pair.
7. Allow SSH traffic on port `22` from your trusted IP address.
8. Launch the instance.

---

# Part 3 — SSH Connection

## Windows (PowerShell)

Connect to the EC2 instance using your private key:

```bash
ssh -i key.pem ubuntu@PUBLIC-IP
```

Replace `key.pem` with your downloaded key file and `PUBLIC-IP` with the instance's public IP address.

## Mac/Linux

Set secure permissions on the private key:

```bash
chmod 400 key.pem
```

Connect to the EC2 instance:

```bash
ssh -i key.pem ubuntu@PUBLIC-IP
```

---

# Part 4 — First Linux Commands

### Check the current user

```bash
whoami
```

Displays the currently logged-in username.

### Check the current directory

```bash
pwd
```

Prints the current working directory.

### List files and directories

```bash
ls
```

Lists files and directories.

```bash
ls -l
```

Displays permissions, ownership, file size, and modification time.

```bash
ls -la
```

Displays hidden files and directories as well.

---

# Part 5 — Linux Filesystem

Navigate to the root directory:

```bash
cd /
ls
```

## Important Directories

| Directory | Purpose |
|---|---|
| `/home` | User home directories |
| `/etc` | System configuration files |
| `/var` | Logs and variable application data |
| `/tmp` | Temporary files |
| `/bin` | Essential user commands |
| `/root` | Root user's home directory |
| `/opt` | Optional application software |
| `/dev` | Device files |
| `/proc` | Process and kernel information |

---

# Part 6 — Directory Navigation

```bash
cd /home
```

Changes the current directory to `/home`.

```bash
cd ..
```

Moves one level up.

```bash
cd ~
```

Moves to the current user's home directory.

```bash
clear
```

Clears the terminal screen.

---

# Part 7 — Create Files and Folders

Create a directory:

```bash
mkdir linux-class
```

Move into it:

```bash
cd linux-class
```

Create multiple directories:

```bash
mkdir notes scripts backups
```

Create a file:

```bash
touch file1.txt
```

Create multiple files:

```bash
touch a.txt b.txt c.txt
```

List the contents:

```bash
ls
```

---

# Part 8 — View File Content

Write content into a file:

```bash
echo "Welcome to Linux" > file1.txt
```

Display the file content:

```bash
cat file1.txt
```

Append additional content:

```bash
echo "Linux is powerful" >> file1.txt
```

Display the updated content:

```bash
cat file1.txt
```

**Remember:**

- `>` overwrites the file's existing content.
- `>>` appends content to the end of the file.

---

# Part 9 — Copy, Move, and Delete

Copy a file:

```bash
cp file1.txt backup.txt
```

Rename a file:

```bash
mv backup.txt renamed.txt
```

Move a file into the `notes` directory:

```bash
mv renamed.txt notes/
```

Delete a file:

```bash
rm a.txt
```

Delete a directory and its contents:

```bash
rm -r scripts
```

**Caution:** `rm` permanently removes files from the command line without moving them to a recycle bin.

---

# Part 10 — Searching

Find a file named `file1.txt` under `/home`:

```bash
find /home -name file1.txt
```

Search for the word `Linux` inside `file1.txt`:

```bash
grep "Linux" file1.txt
```

---

# Part 11 — File Permissions

View file permissions:

```bash
ls -l
```

Example permission:

```text
-rw-r--r--
```

## Permission Symbols

| Symbol | Meaning |
|---|---|
| `r` | Read |
| `w` | Write |
| `x` | Execute |

## Change Permissions

Give everyone read, write, and execute permissions:

```bash
chmod 777 file1.txt
```

Set standard read/write permissions for the owner and read-only permissions for others:

```bash
chmod 644 file1.txt
```

Create a shell script:

```bash
touch test.sh
```

Make it executable:

```bash
chmod +x test.sh
```

**Security note:** Avoid `chmod 777` unless there is a specific reason. It grants permissions more broadly than most files require.

---

# Part 12 — Users and Root

Check the current user:

```bash
whoami
```

Switch to the root user:

```bash
sudo su
```

Exit the root shell:

```bash
exit
```

Root is the administrative user with extensive control over the system. Use administrative privileges carefully.

---

# Part 13 — System Information

Display kernel and system information:

```bash
uname -a
```

Display Linux distribution information:

```bash
cat /etc/os-release
```

Display CPU information:

```bash
lscpu
```

Check memory usage:

```bash
free -h
```

Check disk space:

```bash
df -h
```

Display running processes:

```bash
ps -ef
```

Monitor processes interactively:

```bash
top
```

Press `q` to exit `top`.

---

# Part 14 — Networking Basics

Display network interfaces and IP addresses:

```bash
ip a
```

Test connectivity to Google:

```bash
ping google.com
```

Press `Ctrl + C` to stop the ping command.

---

# Part 15 — Package Management

Update the package repository:

```bash
sudo apt update
```

Install the `tree` utility:

```bash
sudo apt install tree -y
```

Display directories in a tree structure:

```bash
tree
```

---

# Part 16 — Simple Shell Script

Create a shell script:

```bash
vi hello.sh
```

Add the following content:

```bash
#!/bin/bash

echo "Hello Students"
echo "Welcome to Linux"
```

Save and exit Vim:

1. Press `Esc`.
2. Type `:wq`.
3. Press `Enter`.

Make the script executable:

```bash
chmod +x hello.sh
```

Execute the script:

```bash
./hello.sh
```

Expected output:

```text
Hello Students
Welcome to Linux
```

---

# Part 17 — Mini Challenge

Complete the following tasks independently.

1. Create a directory named `globalai`.
2. Inside it, create three directories:
   - `docs`
   - `scripts`
   - `backups`
3. Create a file named `student.txt`.
4. Add your name to the file.
5. Copy the file into the `backups` directory.
6. Display the file content.
7. Check the system's memory usage.
8. Check the available disk space.

### Useful Commands

```bash
mkdir -p globalai/{docs,scripts,backups}
cd globalai
touch student.txt
echo "Your Name" > student.txt
cp student.txt backups/
cat student.txt
free -h
df -h
```

Replace `Your Name` with your actual name.

---

# Important Concepts

## 1. Linux is Case Sensitive

```text
file.txt != FILE.txt
```

Linux treats these as different filenames.

## 2. Everything is a File

Linux represents many system resources through a file-like interface, including:

- Regular files
- Directories
- Device interfaces
- Process and kernel information

## 3. Root User

- The root account is the main administrative account.
- It has extensive privileges over the system.
- Administrative commands should be used carefully.

## 4. Why Linux Matters

Linux powers many technologies and platforms, including:

- AWS
- Kubernetes
- Docker
- AI servers
- Hadoop
- Databricks infrastructure
- Telecom infrastructure
- Cloud platforms

---

# Bonus Commands

Display command history:

```bash
history
```

Display the current date and time:

```bash
date
```

Display a calendar:

```bash
cal
```

If `cal` is unavailable, install the package that provides it:

```bash
sudo apt install ncal -y
```

Check the total size of the current directory:

```bash
du -sh .
```

---

# Next Session — Day 2

Topics planned for the next session:

- Vim and Nano
- Users and groups
- Advanced file permissions
- Linux services
- System logs
- Crontab and task scheduling
- Process management
- SCP file transfers
- SSH keys
- Bash scripting basics

---

**Global AIIT — Linux Administration Training**

