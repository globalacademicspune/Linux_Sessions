# Global AIIT — Linux Day 2 (Ubuntu 22)

## Topic: Users, Permissions, Processes, Services, Logs & SSH

### Session Goal (1.5–2 Hours)

By the end of this session, students will learn:

- Linux users and groups
- File permissions and ownership
- `sudo` and root access
- Process management
- Linux services
- System logs
- Networking basics
- SCP and file transfer
- Introduction to Bash scripting

---

# Part 1 — Quick Revision

```bash
pwd
ls -la
cd ~
mkdir test
touch file.txt
cat file.txt
```

---

# Part 2 — Users in Linux

### Check the Current User

```bash
whoami
```

Shows the currently logged-in username.

### Check User and Group Information

```bash
id
```

Displays the UID, GID, and group memberships.

### View the User Database

```bash
cat /etc/passwd
```

Linux stores local user account information in `/etc/passwd`.

**File format:**

```text
username:x:UID:GID:comment:/home:/shell
```

### Create a User

```bash
sudo adduser student1
```

Creates a new user and interactively prompts for account details.

### Switch User

```bash
su - student1
```

Switches to `student1` and loads that user's login environment.

Return to the previous shell:

```bash
exit
```

### Delete a User

```bash
sudo userdel -r student1
```

Deletes the user and attempts to remove their home directory and mail spool.

**Caution:** This is destructive. Use it only when you intend to delete that account and its associated data.

---

# Part 3 — Groups

Display the current user's groups:

```bash
groups
```

Create a group:

```bash
sudo groupadd devops
```

Add the `ubuntu` user to the `devops` group:

```bash
sudo usermod -aG devops ubuntu
```

Verify the user's group membership:

```bash
id ubuntu
```

**Note:** The user may need to log out and log back in for the new group membership to apply to their existing sessions.

---

# Part 4 — Deep File Permissions

Create a file:

```bash
touch demo.txt
```

View its permissions:

```bash
ls -l demo.txt
```

**Example permission:**

```text
-rw-r--r--
```

### Permission Symbols

| Symbol | Meaning |
|---|---|
| `r` | Read |
| `w` | Write |
| `x` | Execute |

### Numeric Permissions

| Number | Permission | Meaning |
|---|---|---|
| `7` | `rwx` | Read, write, execute |
| `6` | `rw-` | Read and write |
| `5` | `r-x` | Read and execute |
| `4` | `r--` | Read only |

### Change Permissions

Give everyone read, write, and execute permissions:

```bash
chmod 777 demo.txt
```

Set standard file permissions:

```bash
chmod 644 demo.txt
```

Change file ownership:

```bash
sudo chown ubuntu:ubuntu demo.txt
```

Create a secure directory:

```bash
mkdir secure
chmod 700 secure
```

With `700`, only the owner receives permissions; group and others receive none.

**Security note:** Avoid `chmod 777` unless absolutely necessary.

---

# Part 5 — Process Management

### View Running Processes

```bash
ps -ef
```

Displays a snapshot of running processes.

### Monitor Processes in Real Time

```bash
top
```

Press `q` to quit.

### Install and Run htop

```bash
sudo apt install htop -y
htop
```

Press `F10` to quit `htop`.

### Run a Process in the Background

```bash
sleep 300 &
```

Starts a process that sleeps for 300 seconds in the background.

### View Background Jobs

```bash
jobs
```

Lists jobs associated with the current shell.

### Find the sleep Process

```bash
ps -ef | grep '[s]leep 300'
```

### Terminate a Process

Replace `PID` with the actual process ID:

```bash
kill PID
```

If the process does not stop and you have verified the correct PID, force termination:

```bash
kill -9 PID
```

**Note:** Use normal `kill` first. `kill -9` prevents the process from performing normal cleanup.

---

# Part 6 — Services in Linux

### Check SSH Service Status

```bash
systemctl status ssh
```

### Start SSH

```bash
sudo systemctl start ssh
```

### Stop SSH

```bash
sudo systemctl stop ssh
```

**Warning:** If connected to the EC2 instance through SSH, stopping the SSH service can disconnect you and prevent new SSH connections.

### Restart SSH

```bash
sudo systemctl restart ssh
```

### Enable SSH at Boot

```bash
sudo systemctl enable ssh
```

### List Running Services

```bash
systemctl list-units --type=service
```

---

# Part 7 — Logs in Linux

Navigate to the log directory:

```bash
cd /var/log
ls
```

### View System Logs

On Ubuntu systems where `/var/log/syslog` exists:

```bash
sudo cat /var/log/syslog
```

### Follow Logs in Real Time

```bash
sudo tail -f /var/log/syslog
```

Press `Ctrl + C` to stop.

### Display the Last 20 Lines

```bash
sudo tail -n 20 /var/log/syslog
```

### Search Logs for SSH Entries

```bash
sudo grep -i ssh /var/log/syslog
```

**Note:** Log locations vary by configuration. If `/var/log/syslog` is unavailable, use `sudo journalctl` to inspect systemd-managed logs.

---

# Part 8 — Networking Basics

### Display IP Addresses

```bash
ip a
```

### Display Listening and Network Sockets

```bash
sudo ss -tulnp
```

### Test Network Connectivity

```bash
ping google.com
```

Press `Ctrl + C` to stop.

### Perform DNS Lookup

Install DNS utilities:

```bash
sudo apt install dnsutils -y
```

Look up Google's DNS records:

```bash
nslookup google.com
```

---

# Part 9 — SCP (File Transfer)

SCP allows files to be securely copied between systems over SSH.

### Upload a File to EC2

```bash
scp -i key.pem test.txt ubuntu@PUBLIC-IP:/home/ubuntu/
```

Copies `test.txt` from your local machine to the EC2 instance.

### Download a File from EC2

```bash
scp -i key.pem ubuntu@PUBLIC-IP:/home/ubuntu/test.txt .
```

Downloads `test.txt` to the current local directory.

Replace `key.pem` and `PUBLIC-IP` with your actual key file and EC2 public IP address.

---

# Part 10 — Introduction to Bash Scripting

Create a script:

```bash
nano script.sh
```

Add the following content:

```bash
#!/bin/bash

echo "Welcome Students"

date
whoami
pwd
```

### Save and Exit Nano

1. Press `Ctrl + O`.
2. Press `Enter`.
3. Press `Ctrl + X`.

Make the script executable:

```bash
chmod +x script.sh
```

Run the script:

```bash
./script.sh
```

---

# Part 11 — Variables in Bash

Create a variable:

```bash
name="GlobalAI"
```

Display its value:

```bash
echo "$name"
```

**Note:** Do not put spaces around `=` when assigning a Bash variable.

---

# Part 12 — User Input Script

Create a new script:

```bash
nano input.sh
```

Add the following content:

```bash
#!/bin/bash

echo "Enter your name"

read -r name

echo "Hello $name"
```

Save and exit Nano.

Make the script executable:

```bash
chmod +x input.sh
```

Run the script:

```bash
./input.sh
```

The script asks for the user's name and displays a greeting.

---

# Part 13 — Mini Tasks for Students

## Task 1: Directory Structure

Create the following directory structure:

```text
project/
├── docs/
├── scripts/
└── backups/
```

## Task 2: User Management

Create a user named `devuser`.

```bash
sudo adduser devuser
```

## Task 3: File Permissions

Create `notes.txt` and set its permissions to `644`.

```bash
touch notes.txt
chmod 644 notes.txt
ls -l notes.txt
```

## Task 4: Process Management

Start a background process:

```bash
sleep 500 &
```

Find its PID and terminate it using `kill`.

## Task 5: System Monitoring

Check:

- Memory usage using `free -h`
- Disk usage using `df -h`
- CPU information using `lscpu`
- System logs using `journalctl` or the available log files

---

# Important Concepts

| Command | Purpose |
|---|---|
| `su` | Switch user |
| `sudo` | Run a command with elevated privileges |
| `chmod` | Change file permissions |
| `chown` | Change file ownership |
| `ps` | Display a process snapshot |
| `top` | Monitor processes in real time |
| `systemctl` | Manage systemd services |
| `kill` | Send a signal to a process |
| `scp` | Securely copy files over SSH |

---

# Common Interview Questions

1. What is Linux?
2. What is the difference between root and a normal user?
3. What does `chmod 777` do?
4. What is the difference between a process and a service?
5. What is a PID?
6. What is SSH?
7. What is SCP?
8. What is a log file?

---

# Bonus — Install Nginx

Update the package repository:

```bash
sudo apt update
```

Install Nginx:

```bash
sudo apt install nginx -y
```

Check the service status:

```bash
systemctl status nginx
```

If the service is running, open the following URL in your browser:

```text
http://PUBLIC-IP
```

Replace `PUBLIC-IP` with your EC2 instance's public IP address.

Ensure that inbound HTTP traffic on TCP port `80` is allowed in the EC2 Security Group and, if enabled, the Ubuntu firewall.

---

# Day 3 Suggestion

Topics for the next session:

- Vim editor
- Advanced `grep` and `find`
- Cron jobs and scheduling
- Environment variables
- Advanced package management
- Shell scripting loops
- SSH keys in depth
- Linux troubleshooting

---

**Global AIIT — Linux Administration Training**
