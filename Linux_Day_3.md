# Global AIIT — Linux Day 3
## Advanced Cloud, Hadoop, Security & OS Internals

**Environment:** AWS Ubuntu Server 24.x  
**Cluster:** Single-Node Hadoop Cluster

---

## Session Goal

By the end of this session, students should understand:

- Linux internals
- Hadoop process behavior
- Security awareness
- Cloud concepts
- Process visibility
- Memory and networking
- Observability and monitoring
- Enterprise operations and troubleshooting

---

# Part 1 — Hadoop and Process Internals

## 1. Check Java Hadoop Services

**Command:**

```bash
jps
```

**Why:** Shows Java processes running in Hadoop.

**What to look for:**
- NameNode
- DataNode
- ResourceManager
- NodeManager

**Explanation:** Hadoop runs its services as separate Java Virtual Machine (JVM) processes.

**Application:** Hadoop administrators use this command to verify whether cluster services are running. The exact daemons depend on the Hadoop version and configuration.

---

## 2. Find Process Details

**Command:**

```bash
ps aux | grep -i namenode
```

**Why:** Finds the Linux process associated with the NameNode.

**What to look for:**
- USER
- PID
- CPU usage
- Memory usage

**Explanation:** Linux tracks running applications and services as processes.

**Application:** Used during production troubleshooting and incident investigation.

---

## 3. View the Process Tree

**Command:**

```bash
ps -ef --forest
```

**Why:** Shows parent-child process relationships.

**What to look for:** Process hierarchy.

**Explanation:** Linux organizes processes through parent-child relationships.

**Application:** Used by security and operations teams to investigate suspicious child processes and understand process execution.

---

## 4. Check the Current Shell PID

**Command:**

```bash
echo $$
```

**Why:** Displays the process ID of the current shell.

**What to look for:** The current shell's PID.

**Explanation:** A shell running inside a terminal is itself a Linux process.

**Application:** Useful when debugging shell-level behavior and process relationships.

---

## 5. Inspect Process Internals

**Command:**

```bash
cat /proc/<PID>/status
```

**Example:**

```bash
cat /proc/2345/status
```

Replace `2345` with the actual process ID.

**Why:** Displays information about a process.

**What to look for:**
- Threads
- Memory information
- UID
- Process state

**Explanation:** Linux exposes process information through the `/proc` virtual filesystem.

**Application:** Used by SRE and operations teams during process investigations.

---

## 6. Check Process Open File Descriptors

**Command:**

```bash
ls -l /proc/<PID>/fd
```

**Why:** Shows the file descriptors opened by a process.

**What to look for:**
- Log files
- File handles
- Socket references

**Explanation:** Linux represents many resources through file descriptors, including files, pipes and sockets.

**Application:** Useful for investigating resource leaks and open connections.

---

## 7. List Files Opened by a Process

**Command:**

Install `lsof` if it is not already available:

```bash
sudo apt update
sudo apt install lsof -y
```

Inspect a process:

```bash
sudo lsof -p <PID>
```

**Why:** Displays files and other resources opened by a process.

**What to look for:**
- TCP sockets
- Log files
- Shared libraries
- Open files

**Explanation:** Processes leave operational footprints that can be inspected.

**Application:** Used during security audits and process investigations.

---

## 8. Trace Live System Calls

**Install:**

```bash
sudo apt install strace -y
```

**Command:**

```bash
sudo strace -p <PID>
```

**Example:**

```bash
sudo strace -p 2345
```

**Why:** Observes system calls made by a running process.

**What to look for:**
- `read()`
- `write()`
- `socket()`

**Explanation:** Applications interact with the Linux kernel through system calls.

**Application:** Platform engineers use `strace` to investigate stuck applications and understand system-level behavior.

**Note:** Tracing a production process can affect performance and expose sensitive information. Use it carefully.

---

## 9. Hadoop System Call Demonstration

**Command:**

```bash
strace hdfs dfs -ls /
```

**Why:** Observes the behavior of the Hadoop command-line client.

**What to look for:**
- File access
- System calls
- Network-related activity

**Explanation:** Distributed systems depend on operating-system operations to access files and communicate over networks.

**Application:** Useful for investigating Hadoop filesystem communication issues.

---

# Part 2 — Memory and Performance Monitoring

## 10. Check Memory Usage

**Command:**

```bash
free -h
```

**Why:** Displays RAM and swap usage.

**What to look for:**
- Used memory
- Free memory
- Available memory
- Buffers and cache

**Explanation:** Linux manages memory across applications, the kernel and filesystem caches.

**Application:** Used to investigate memory pressure in big data systems.

---

## 11. Find High-Memory Processes

**Command:**

```bash
ps aux --sort=-%mem | head
```

**Why:** Identifies processes with high memory usage.

**What to look for:** Processes consuming the most resident memory.

**Explanation:** Big data workloads often require substantial RAM, particularly for JVM-based services.

**Application:** Used during JVM tuning, capacity planning and memory troubleshooting.

---

## 12. Live Process Monitoring

**Command:**

```bash
top
```

**Why:** Provides live process and system monitoring.

**What to look for:**
- CPU usage
- Memory usage
- Load average
- Running processes

**Explanation:** Linux continuously schedules workloads and allocates CPU time to processes.

**Application:** Used during real-time production monitoring.

Press `q` to quit.

---

## 13. Better Live Monitoring with htop

**Install:**

```bash
sudo apt update
sudo apt install htop -y
```

**Command:**

```bash
htop
```

**Why:** Provides an interactive process-monitoring interface.

**What to look for:**
- Process and thread information
- CPU usage
- Memory usage
- Process hierarchy

**Explanation:** Interactive monitoring makes it easier to inspect system activity.

**Application:** Used by NOC and SRE teams during live incidents.

Press `F10` to quit.

---

## 14. CPU Pressure Demonstration

**Command:**

```bash
yes > /dev/null
```

**Why:** Creates CPU load by continuously generating output that is discarded.

**What to observe:** CPU usage increases while the process runs.

**Explanation:** Even a small process can consume significant system resources.

**Application:** Demonstrates CPU contention and resource starvation.

**Stop the process:** Press `Ctrl + C` in the terminal running the command.

If it was started in the background, find its PID and terminate it:

```bash
pgrep -a yes
kill <PID>
```

Use this demonstration only on a test machine and stop it promptly.

---

## 15. Change Process Priority

Find the process:

```bash
ps aux | grep '[y]es'
```

Change its scheduling priority:

```bash
renice +10 <PID>
```

Replace `<PID>` with the actual process ID.

**Why:** Changes the process's nice value.

**What to observe:** A higher nice value generally makes a process less favored for CPU scheduling relative to competing processes.

**Explanation:** Linux uses scheduling policies and priorities to allocate CPU time.

**Application:** Useful for understanding workload prioritization in enterprise environments.

**Note:** Changing the nice value does not reserve CPU resources or guarantee a specific amount of CPU time.

---

# Part 3 — Networking and Packet Analysis

## 16. Check Network Ports

**Command:**

```bash
sudo ss -tulnp
```

**Why:** Displays listening TCP/UDP sockets and associated process information where available.

**What to look for:**
- Ports
- Listening services
- Process IDs

**Explanation:** Listening ports indicate services that may accept network connections.

**Application:** Used during security audits, firewall reviews and network troubleshooting.

---

## 17. Check Hadoop Ports

**Command:**

```bash
sudo ss -tulnp | grep java
```

**Why:** Helps identify listening ports used by Java-based Hadoop services.

**What to look for:** Depending on configuration, examples may include ports such as `9870`, `9864` and `8088`.

**Explanation:** Distributed systems communicate through network sockets.

**Application:** Used to validate Hadoop service availability and network exposure.

**Note:** Port numbers depend on the Hadoop version and configuration. Hadoop 1.x uses different defaults for several services.

---

## 18. Inspect Active TCP Connections

**Command:**

```bash
ss -tan
```

**Why:** Displays TCP sockets and their connection states.

**What to look for:**
- `LISTEN`
- `ESTAB`
- `TIME-WAIT`

**Explanation:** Linux tracks the lifecycle of TCP connections.

**Application:** Used during network troubleshooting and socket-exhaustion investigations.

---

## 19. Capture Network Packets

**Install:**

```bash
sudo apt update
sudo apt install tcpdump -y
```

**Command:**

```bash
sudo tcpdump -i any icmp
```

**Why:** Captures ICMP packets visible on the selected interfaces.

**What to look for:** ICMP request and response traffic, such as ping packets.

**Explanation:** Packet capture makes network traffic visible for analysis.

**Application:** Used for network debugging and packet analysis.

Press `Ctrl + C` to stop the capture.

---

## 20. Observe Hadoop NameNode UI Traffic

**Command:**

```bash
sudo tcpdump -i any port 9870
```

**Why:** Captures traffic involving port `9870`, commonly used by the Hadoop 3.x NameNode web interface.

**What to look for:** TCP traffic involving that port.

**Explanation:** Applications communicate through network packets.

**Application:** Helps demonstrate application-level networking and diagnose connectivity problems.

**Note:** If using Hadoop 1.2.1, the NameNode web UI typically uses port `50070` instead. Use the port configured in your environment.

Press `Ctrl + C` to stop.

---

# Part 4 — Logs and Observability

## 21. View Live System Logs

**Command:**

```bash
sudo journalctl -f
```

**Why:** Displays systemd journal entries in real time.

**What to look for:** System events, service messages and errors.

**Explanation:** Logs provide a record of system and application activity.

**Application:** SOC, SRE and operations teams use logs during incidents and outage investigations.

Press `Ctrl + C` to stop.

---

## 22. Create a Test Log Entry

**Command:**

```bash
logger "security test event"
```

**Why:** Generates a custom log message.

**What to look for:** The message in the system journal or configured system logs.

**Explanation:** Logs record operational events.

**Application:** Used to test logging pipelines, monitoring systems and alerts.

To search for the message in the journal:

```bash
sudo journalctl -g "security test event"
```

---

## 23. Monitor Hadoop Logs

**Command:**

```bash
tail -f "$HADOOP_HOME"/logs/*.log
```

**Why:** Follows Hadoop log files.

**What to look for:**
- `INFO`
- `WARN`
- `ERROR`

**Explanation:** Hadoop administrators use logs to understand service behavior and investigate failures.

**Application:** Used during Hadoop troubleshooting and performance analysis.

**Note:** This command may fail if no matching log files exist. Check the configured Hadoop log directory first.

---

# Part 5 — File Permissions and Linux Security

## 24. Check File Permissions

**Command:**

```bash
ls -l
```

**Why:** Displays file permissions and ownership.

**What to look for:** The `rwx` permission model.

**Explanation:** Linux permissions control access to files and directories.

**Application:** Used to secure enterprise files, application directories and configuration files.

---

## 25. Demonstrate Dangerous Permissions

**Command:**

```bash
chmod 777 demo.txt
```

**Why:** Demonstrates overly permissive file access.

**What to observe:** The owner, group and others can read, write and execute the file.

**Explanation:** Permissions of `777` grant full permissions to everyone.

**Application:** Used to demonstrate security misconfiguration risks.

**Security note:** Run this only on a disposable test file. Avoid granting broad permissions in production.

---

## 26. Set More Restrictive File Permissions

**Command:**

```bash
chmod 640 demo.txt
```

**Why:** Restricts access.

**What to observe:**
- Owner: read and write
- Group: read
- Others: no permissions

**Explanation:** Least privilege limits access to what users actually need.

**Application:** Useful for protecting configuration files and sensitive information.

---

## 27. Change File Ownership

**Command:**

```bash
sudo chown ubuntu:ubuntu demo.txt
```

**Why:** Changes the file owner and group.

**What to observe:** The owner and group displayed by `ls -l`.

**Explanation:** Linux uses ownership and permissions to determine access rights.

**Application:** Used to establish application access boundaries.

---

## 28. Check User Identity

**Command:**

```bash
id
```

**Why:** Displays user identity information.

**What to look for:**
- UID
- GID
- Supplementary groups

**Explanation:** Linux security relies on user and group identities.

**Application:** Used to verify access control and group membership.

---

## 29. Check the Current User

**Command:**

```bash
whoami
```

**Why:** Displays the effective username of the current session.

**Explanation:** Identity determines which permissions apply to many operations.

**Application:** Useful before executing privileged commands or troubleshooting access problems.

---

## 30. Check Sudo Access

**Command:**

```bash
sudo -l
```

**Why:** Displays the sudo privileges available to the current user.

**What to look for:** Commands the user is allowed to execute with elevated privileges.

**Explanation:** Excessive sudo privileges can create security risks.

**Application:** Used during privilege audits and access reviews.

---

## 31. Inspect Shadow File Permissions

**Command:**

```bash
ls -l /etc/shadow
```

**Why:** Inspects the permissions and ownership of the local password-hash file.

**Explanation:** `/etc/shadow` contains password-related authentication data and should be protected from unauthorized access.

**Application:** Used to teach Linux authentication security.

**Security note:** Inspect the file's permissions and ownership; do not publish or share its contents.

---

## 32. Find SUID Binaries

**Command:**

```bash
sudo find / -perm -4000 -type f 2>/dev/null | head
```

**Why:** Finds a sample of files with the SUID permission bit set.

**Explanation:** SUID executables can run with the effective privileges of their file owner, subject to system rules.

**Application:** Used during Linux hardening and security audits.

**Note:** SUID is not automatically a vulnerability. Investigate unexpected or unnecessary SUID executables.

---

## 33. Inspect Linux Capabilities

**Install:**

```bash
sudo apt update
sudo apt install libcap2-bin -y
```

**Command:**

```bash
sudo getcap -r / 2>/dev/null
```

**Why:** Searches for files with assigned Linux capabilities.

**Explanation:** Linux capabilities divide some traditional root privileges into smaller permissions.

**Application:** Used in container security, Kubernetes hardening and Linux security audits.

**Note:** A full filesystem scan can take time and may produce a large amount of output.

---

# Part 6 — Environment and Storage

## 34. Inspect Environment Variables

**Command:**

```bash
cat /proc/$$/environ | tr '\0' '\n'
```

**Why:** Displays environment variables for the current shell process when accessible through `/proc`.

**What to look for:** Variable names and configured values.

**Explanation:** Applications often use environment variables for configuration. Sensitive values stored in them may be exposed to processes or users with sufficient access.

**Application:** Used to understand cloud configuration and secret-management risks.

**Security note:** Avoid sharing the output publicly because it may contain credentials, tokens or other sensitive values.

---

## 35. View Mounted Filesystems

**Command:**

```bash
findmnt
```

**Why:** Displays mounted filesystems and their mount points.

**Explanation:** Linux makes filesystems available through a directory hierarchy using mounts.

**Application:** Used during storage and filesystem troubleshooting.

---

## 36. Check Disk Usage

**Command:**

```bash
df -Th
```

**Why:** Displays filesystem types and disk usage.

**What to look for:**
- Filesystem
- Type
- Size
- Used space
- Available space
- Mount point

**Explanation:** Full filesystems can prevent applications from writing data and cause service failures.

**Application:** Used in capacity planning and outage prevention.

---

# Part 7 — Text Processing and Automation

## 37. Search Logs for Errors

**Command:**

```bash
sudo grep -Ri "error" /var/log 2>/dev/null | head
```

**Why:** Searches log files for occurrences of the word `error`.

**Explanation:** `grep` is commonly used to search logs and troubleshoot operational issues.

**Application:** Used by operations teams during incident investigations.

**Note:** This searches text files under `/var/log`; rotated, compressed or journal-only logs may require different commands.

---

## 38. Demonstrate sed

**Command:**

```bash
echo "hadoop cluster" | sed 's/hadoop/kubernetes/'
```

**Expected output:**

```text
kubernetes cluster
```

**Why:** Replaces text in a stream.

**Explanation:** `sed` is a stream editor that can transform text.

**Application:** Used in shell scripting, automation and configuration management.

---

## 39. Update a Configuration File with sed

**Command:**

```bash
sed -i 's/8080/9090/' app.conf
```

**Why:** Replaces the first occurrence of `8080` with `9090` on each line of `app.conf`.

**Explanation:** Automation often relies on text-processing utilities to update configuration files.

**Application:** Used in deployment scripts and configuration management.

**Important:** Back up the file first and verify the intended change. This command modifies the file directly.

---

## 40. Demonstrate awk

**Command:**

```bash
df -h | awk '{print $1, $5}'
```

**Why:** Extracts selected columns from command output.

**What to look for:** Filesystem names and their reported usage percentages.

**Explanation:** `awk` processes structured text using fields and patterns.

**Application:** Used in reporting, shell scripting and monitoring automation.

---

## 41. Analyze Processes with awk

**Command:**

```bash
ps aux | awk '{print $1, $2, $3, $11}'
```

**Why:** Extracts selected fields from process information.

**What to look for:**
- USER
- PID
- CPU percentage
- Command field

**Explanation:** Linux command output can be transformed into structured information.

**Application:** Used in reporting and monitoring scripts.

---

## 42. Use the watch Command

**Command:**

```bash
watch -n 1 "ps aux --sort=-%cpu | head"
```

**Why:** Refreshes the process listing every second.

**What to look for:** Processes currently appearing near the top of the CPU-sorted output.

**Explanation:** `watch` repeatedly executes a command and displays its output.

**Application:** Used during real-time troubleshooting.

Press `Ctrl + C` to stop.

---

# Part 8 — Advanced Process Internals

## 43. Inspect Process Threads

**Command:**

```bash
ps -T -p <PID>
```

Replace `<PID>` with the actual process ID.

**Why:** Displays threads belonging to a process.

**Explanation:** A single process can contain multiple threads. Hadoop's JVM-based services use threads to handle concurrent work.

**Application:** Used during thread debugging and JVM investigations.

---

## 44. Inspect Process Memory Maps

**Command:**

```bash
cat /proc/<PID>/maps | head
```

**Why:** Displays the beginning of a process's virtual memory map.

**What to look for:** Memory regions associated with executable files, shared libraries, stacks and other mappings.

**Explanation:** Linux tracks the virtual memory regions associated with each process.

**Application:** Used in memory debugging and forensic analysis.

---

## 45. Map a PID to a Command

**Command:**

```bash
ps -p <PID> -f
```

Replace `<PID>` with the actual process ID.

**Why:** Displays details about a specific process.

**What to look for:** Process owner, PID, parent PID, start time and command.

**Explanation:** Process IDs help administrators identify and investigate running applications.

**Application:** Used during operational debugging.

---

# Part 9 — Containers and Kernel Internals

## 46. Inspect Linux Namespaces

**Command:**

```bash
lsns
```

**Why:** Lists Linux namespaces.

**Explanation:** Namespaces provide isolation for resources such as processes, network interfaces and mount points.

**Application:** Used to understand Docker, Kubernetes and container internals.

---

## 47. Inspect Control Groups

**Command:**

```bash
systemd-cgls
```

**Why:** Displays the systemd control-group hierarchy.

**Explanation:** Linux control groups (cgroups) organize processes and can enforce resource limits, depending on their configuration.

**Application:** Used in container orchestration, resource management and cloud workload isolation.

---

## 48. Inspect Kernel Logs

**Command:**

```bash
sudo dmesg | tail
```

**Why:** Displays the latest messages from the kernel ring buffer.

**What to look for:** Hardware messages, driver events, warnings and other kernel-level activity.

**Explanation:** Kernel messages help reveal system-level issues that application logs may not show.

**Application:** Used during hardware, driver and operating-system troubleshooting.

---

# Part 10 — Cloud Networking

## 49. Check the Public IP Address

**Command:**

```bash
curl -s https://ifconfig.me
```

**Why:** Queries an external service to determine the public IP address seen by that service.

**Explanation:** Cloud instances may communicate with the internet through a public IP address, NAT gateway or another network egress path.

**Application:** Used during cloud networking and firewall validation.

**Note:** This requires internet connectivity and an external IP-check service. The returned address may represent a NAT gateway rather than the instance itself.

---

# Part 11 — Final Concept: How Applications Interact with Linux

## 50. Architecture Flow

```text
+----------------------+
|     Application      |
+----------------------+
           |
           v
+----------------------+
|    System Calls       |
+----------------------+
           |
           v
+----------------------+
|    Linux Kernel       |
+----------------------+
           |
           v
+----------------------+
| CPU | RAM | DISK | NET|
+----------------------+
```

**Explanation:** Applications rely on operating-system interfaces and the Linux kernel to access hardware resources and perform system operations.

**Application:** This is a foundational concept for understanding:

- Cloud computing
- Hadoop
- Kubernetes
- DevOps
- Security engineering
- Platform engineering

---

## Final Learning Outcome

After completing Day 3, students should be able to:

- Inspect Linux processes and their resource usage.
- Understand basic JVM and Hadoop process monitoring.
- Inspect files, permissions and user identities.
- Monitor logs and network sockets.
- Perform basic packet capture and system-call tracing.
- Understand Linux namespaces, cgroups and kernel interactions.
- Apply basic operational troubleshooting and security awareness.

**Global AIIT — Linux Administration Training**
