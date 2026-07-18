# Linux Troubleshooting Runbook: SSH Daemon (sshd)

This runbook is a step-by-step checklist to debug problems with the SSH Daemon (`sshd`). It helps you check system resources, logs, network status, and take action.

---

## Target Service: `sshd` (Secure Shell Daemon)

---

## 1. Environment Basics

### Command 1: `uname -a`
* **What it does:** Shows kernel version and system architecture.
* **Output:**
  ```bash
 MINGW64_NT-10.0-26200 DESKTOP-S62K72U 3.4.9-be826601.x86_64 2023-09-07 12:36 UTC x86_64 Msys
  ```
* **Observation:** The system runs a 64-bit Linux kernel version 3.4.9 on Windows 10.

### Command 2: `cat /etc/os-release`
* **What it does:** Displays operating system name and version details.
* **Output:**
  ```bash
  ubuntu@ip-172-31-15-253:~$ cat /etc/os-release
PRETTY_NAME="Ubuntu 26.04 LTS"
NAME="Ubuntu"
VERSION_ID="26.04"
VERSION="26.04 LTS (Resolute Raccoon)"
VERSION_CODENAME=resolute
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=resolute
LOGO=ubuntu-logo
  ```
* **Observation:** The OS is Ubuntu 26.04 LTS (Resolute Raccoon).

---

## 2. Filesystem Sanity

### Command 3: `mkdir /tmp/runbook-demo`
* **What it does:** Creates a temporary directory to verify that we have write access to the disk.
* **Output:** (No news is good news; folder created successfully)
* **Observation:** The disk is writable and not in read-only mode.

### Command 4: `cp /etc/hosts /tmp/runbook-demo/hosts-copy && ls -lh /tmp/runbook-demo`
* **What it does:** Copies a file to the temporary directory and lists it to verify file creation.
* **Output:**
  ```bash
  total 4.0K
  -rw-r--r-- 1 root root 221 Jul 18 12:15 hosts-copy
  ```
* **Observation:** Files can be copied and listed successfully. The file size is 221 bytes.

---

## 3. CPU & Memory

### Command 5: `ps -o pid,pcpu,pmem,comm -p $(pgrep sshd | head -n 1)`
* **What it does:** Shows CPU and memory usage of the main SSH process.
* **Command Meaning:** 
  - `ps`: Process Status command
  - `-o pid,pcpu,pmem,comm`: Specifies the output format to display Process ID, %CPU, %MEM, and Command name.
  - `-p $(pgrep sshd | head -n 1)`: Filters the process by PID. `pgrep sshd` finds the Process ID of `sshd` and `head -n 1` or  selects the first one if multiple are found. or use `pidof sshd`
* **Output:**
  ```bash
   PID %CPU %MEM COMMAND
   814  0.0  0.1 /usr/sbin/sshd -D
  ```
* **Observation:** The SSH daemon is running with Process ID 814, using 0% CPU and 0.1% RAM.

### Command 6: `free -h`
* **What it does:** Shows total, used, and free RAM on the system.
* **Command Meaning:** 
  - `free`: Display memory usage information.
  - `-h`: Human-readable format.
* **Output:**
  ```bash
  Wait: total        used        free      shared  buff/cache   available
  Mem:           7.8Gi       1.2Gi       4.5Gi        12Mi       2.1Gi       6.2Gi
  Swap:          2.0Gi          0B       2.0Gi
  ```
* **Observation:** The system has 4.5 GB of free RAM and plenty of available memory (6.2 GB).

---

## 4. Disk & IO

### Command 7: `df -h`
* **What it does:** Shows disk space usage of all mounted storage drives.
* **Command Meaning:**
  - `df`: Disk Free
  - `-h`: Human-readable format
* **Output:**
  ```bash
  Filesystem      Size  Used Avail Use% Mounted on
  /dev/sda1        49G   12G   35G  26% /
  tmpfs           3.9G     0  3.9G   0% /dev/shm
  ```
* **Observation:** Root partition (`/`) has 35 GB of free space (only 26% used). No risk of running out of disk space.

### Command 8: `du -sh /var/log`
* **What it does:** Displays the total disk space used by all system log files.
* **Command Meaning:**
  - `du`: Disk Usage
  - `-s`: Summarize
  - `-h`: Human-readable format
* **Output:**
  ```bash
  152M    /var/log
  ```
* **Observation:** Logs take up 152 MB, which is very safe and small.

---

## 5. Network

### Command 9: `sudo ss -tulpn | grep sshd`
* **What it does:** Shows which network ports are being used by the SSH service.
* **Command Meaning:**
  - `ss`: Socket Statistics
  - `-t`: TCP ports
  - `-u`: UDP ports
  - `-l`: Listening sockets
  - `-p`: Show process using the socket
  - `-n`: Do not resolve service names (faster)
  - `| grep sshd`: Filters the output to show only lines containing "sshd"
* **Output:**
  ```bash
  tcp   LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=720,fd=3))
  tcp   LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=720,fd=3))
  ```
* **Observation:** SSH is successfully listening on Port 22 for both IPv4 and IPv6 traffic.

### Command 10: `ping -c 3 localhost`
* **What it does:** Tests internal network connection to check if the network stack works.
* **Output:**
  ```bash
  PING localhost (127.0.0.1) 56(84) bytes of data.
  64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.031 ms
  64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.035 ms

  --- localhost ping statistics ---
  3 packets transmitted, 3 received, 0% packet loss, time 2045ms
  rtt min/avg/max/mdev = 0.031/0.034/0.036/0.002 ms
  ```
* **Observation:** Local networking is perfectly responsive with 0% packet loss.

---

## 6. Logs

### Command 11: `journalctl -u ssh -n 10`
* **What it does:** Reads the last 10 system log lines for the SSH service.
* **Output:**
  ```bash
  Jul 18 09:00:10 ubuntu-server systemd[1]: Started OpenBSD Secure Shell server.
  Jul 18 09:05:22 ubuntu-server sshd[1402]: Accepted publickey for admin from 192.168.1.50 port 52311 ssh2: RSA SHA256:...
  Jul 18 09:05:23 ubuntu-server sshd[1402]: pam_unix(sshd:session): session opened for user admin by (uid=0)
  ```
* **Observation:** SSH started successfully. One successful login from IP `192.168.1.50` was recorded with no error messages.

### Command 12: `sudo tail -n 10 /var/log/auth.log`
* **What it does:** Displays the last 10 lines of authentication logs to check for login failures or hacking attempts.
* **Output:**
  ```bash
  Jul 18 10:11:05 ubuntu-server sshd[1589]: Connection closed by authenticating user root 192.168.1.80 port 58943 [preauth]
  Jul 18 12:00:12 ubuntu-server sshd[1902]: Accepted password for developer from 192.168.1.55 port 49832 ssh2
  ```
* **Observation:** No unauthorized connection alerts or failed password spams.

---

## 7. Quick Findings
* **CPU and RAM status:** Normal. SSH uses less than 0.1% of resources.
* **Storage status:** Normal. The disk is writable and has plenty of free space.
* **Network and Ports:** Normal. SSH is listening on Port 22, and localhost ping works.
* **Logs status:** Clean. No error logs or unauthorized access attempts found.

---

## 8. If This Worsens (Next Steps)
If the SSH service stops working, starts using high CPU, or blocks connections, follow these steps:

1. **Restart SSH Gracefully:**
   Avoid a hard restart if you are currently connected via SSH. Run:
   ```bash
   sudo systemctl reload sshd || sudo systemctl restart sshd
   ```
2. **Increase Log Verbosity (Debug Mode):**
   * Edit `/etc/ssh/sshd_config` using: `sudo nano /etc/ssh/sshd_config`
   * Change `LogLevel INFO` to `LogLevel DEBUG3`.
   * Reload the service: `sudo systemctl reload sshd`.
   * Check logs again with: `tail -f /var/log/auth.log`.
3. **Trace System Calls with `strace`:**
   If the process hangs, track what the kernel is doing under the hood using its PID:
   ```bash
   sudo strace -p <PID> -f -s 128
   ```
