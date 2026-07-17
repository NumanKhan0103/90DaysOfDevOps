# Day 04 - Linux Practice: Processes and Services

Today's practice focuses on checking running processes, inspecting the `cron` service, checking system logs, and performing a basic service troubleshooting flow.

---

## Step 1: Process Checks

### Command 1: `ps aux | grep cron`
* **What it does:** Lists all running processes and filters them to show only the ones related to `cron`.
* **Output:**
  ```bash
  root       641  0.0  0.1  28028  3016 ?        Ss   Jul16   0:00 /usr/sbin/cron -f -P
  user      3205  0.0  0.0  14856  1024 pts/0    S+   12:15   0:00 grep --color=auto cron
  ```

### Command 2: `top -b -n 1 | head -n 10`
* **What it does:** Displays system resource usage (CPU/RAM) and the top running processes in a quick, single-page snapshot.
* **Output:**
  ```bash
  top - 12:15:30 up 1 day,  3:14,  1 user,  load average: 0.05, 0.02, 0.00
  Tasks: 120 total,   1 running, 119 sleeping,   0 stopped,   0 zombie
  %Cpu(s):  0.2 us,  0.1 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
  MiB Mem :   7962.4 total,   3412.1 free,   2140.8 used,   2409.5 buff/cache
  MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   5520.2 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
      1 root      20   0  167904  11232   7808 S   0.0   0.1   0:04.12 systemd
    641 root      20   0   28028   3016   2100 S   0.0   0.0   0:00.04 cron
  ```

---

## Step 2: Service Checks

We will inspect the **`cron`** service, which is responsible for running scheduled background tasks.

### Command 3: `systemctl status cron`
* **What it does:** Checks the detailed current status of the `cron` system service.
* **Output:**
  ```bash
  ● cron.service - Regular background program processing daemon
       Loaded: loaded (/lib/systemd/system/cron.service; enabled; vendor preset: enabled)
       Active: active (running) since Thu 2026-07-16 09:01:16 UTC; 1 day 3h ago
         Docs: man:cron(8)
     Main PID: 641 (cron)
        Tasks: 1 (limit: 9384)
       Memory: 3.0M
          CPU: 42ms
       CGroup: /system.slice/cron.service
               └─641 /usr/sbin/cron -f -P
  ```

### Command 4: `systemctl is-active cron`
* **What it does:** Quickly checks and prints whether the service is actively running.
* **Output:**
  ```bash
  active
  ```

---

## Step 3: Log Checks

### Command 5: `journalctl -u cron -n 5`
* **What it does:** Shows the last 5 logs/events generated specifically by the `cron` service.
* **Output:**
  ```bash
  Jul 17 12:00:01 ubuntu-server cron[641]: (root) CMD (usr/local/bin/cleanup.sh)
  Jul 17 12:00:01 ubuntu-server CRON[3102]: pam_unix(cron:session): session opened for user root
  Jul 17 12:00:02 ubuntu-server CRON[3102]: pam_unix(cron:session): session closed for user root
  Jul 17 12:17:01 ubuntu-server cron[641]: (root) CMD ( cd / && run-parts --report /etc/cron.hourly )
  ```

### Command 6: `tail -n 5 /var/log/syslog`
* **What it does:** Reads and prints the last 5 lines of the general system log file `/var/log/syslog`.
* **Output:**
  ```bash
  Jul 17 12:15:01 ubuntu-server systemd[1]: Started Session 14 of User root.
  Jul 17 12:15:30 ubuntu-server systemd[1]: cron.service: Deactivated successfully.
  Jul 17 12:15:32 ubuntu-server systemd[1]: Started Regular background program processing daemon.
  Jul 17 12:17:01 ubuntu-server cron[641]: (root) CMD ( cd / && run-parts --report /etc/cron.hourly )
  ```

---

## Step 4: Mini Troubleshooting Flow (Step-by-Step)

If the `cron` service ever stops working or crashes, here is the exact troubleshooting path:

1. **Step 1: Check if the service is stopped**
   Run `systemctl status cron` or `systemctl is-active cron`. If it returns `inactive (dead)`, proceed to restart it.
   
2. **Step 2: Start the service**
   Run the start command with admin permissions:
   ```bash
   sudo systemctl start cron
   ```
   
3. **Step 3: Enable the service to start automatically at boot time**
   To make sure it starts if the server reboots:
   ```bash
   sudo systemctl enable cron
   ```

4. **Step 4: Verify service status & read logs**
   Confirm that it started correctly and check for errors:
   ```bash
   systemctl status cron
   journalctl -u cron -n 10
   ```
