# Linux Architecture, Processes, and systemd (Notes)

## 1. Core Components of Linux
* **Kernel:** The heart of the Linux operating system. It communicates directly with the computer hardware (CPU, Memory, Disk) and manages resources for applications.
* **User Space:** The area where all user programs and applications run (like a web server, python script, or terminal tools). These programs cannot talk directly to the hardware; they must ask the Kernel to do it.
* **Init (systemd):** The very first process that starts when the computer boots up (always has Process ID 1). It is responsible for starting and managing all other services and processes on the system.

---

## 2. Process Management & States
Processes in Linux are programs that are currently running.

### How Processes are Created:
* **Fork:** A process creates a copy of itself (called a child process).
* **Exec:** The child process replaces its own code with a new program's code.

### Process States:
* **Running (R):** The process is currently running or ready to run on the CPU.
* **Sleeping (S/D):** The process is waiting for something (like input/output or user action).
* **Stopped (T):** The process has been paused by the user or system (e.g., using `Ctrl+Z`).
* **Zombie (Z):** The process is dead/finished, but its parent has not cleaned it up yet. It still occupies a tiny spot in the process table.

---

## 3. What is systemd and Why it Matters
**systemd** is the system and service manager for modern Linux.
* **What it does:** It starts, stops, restarts, and monitors background services (called daemons). It starts services in parallel to make booting faster.
* **Why it matters for DevOps:** It helps you check if your application is running, makes sure crashed applications automatically restart, and lets you view logs easily.

---

## 4. 5 Daily Linux Commands
1. `ps aux` - Shows a detailed list of all running processes on the system.
2. `top` - Displays real-time CPU and memory usage, along with active processes.
3. `kill -9 <PID>` - Forcefully stops a process using its Process ID (PID).
4. `systemctl status <service>` - Checks if a specific background service (like nginx or docker) is running.
5. `journalctl -u <service>` - Shows the logs/history of a specific systemd service to help debug errors.
