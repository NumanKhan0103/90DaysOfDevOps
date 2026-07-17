# Linux Commands Cheat Sheet

## 1. What is this and Why do we need this?
* **What is this?**
  A Linux commands cheat sheet is a quick, easy-to-read list of commands used to manage the operating system, files, processes, and networks.
* **Why do we need this?**
  In DevOps, servers run on Linux. There are hundreds of commands and options. A cheat sheet helps you quickly find and run the right command during a system outage or setup without memorizing everything.

---

## 2. Best Practices for Using Linux Commands
* **Read the help page first:** If you don't know what a command flag does, run `command --help` or check the manual with `man command`.
* **Test in a safe environment:** Always run new or powerful commands in a testing virtual machine (VM) or Docker container first, never on live production servers.
* **Use autocomplete:** Press the **Tab** key to auto-complete file names, paths, and commands to avoid spelling mistakes.
* **Write comments in scripts:** If you automate commands, add short comments to explain *why* you are running each step.

---

## 3. Common Mistakes Juniors & New Learners Make
* **Running everything with `sudo`:** Using administrator permissions (`sudo`) for normal commands is dangerous. It can accidentally damage files or change ownership incorrectly.
* **Dangerous deletions:** Running `rm -rf` without checking the path can delete important system files. Always double-check before pressing enter.
* **Copy-pasting blindy:** Copying commands from Google or StackOverflow and running them on a server without understanding what they do.
* **Ignoring command output:** Ignoring error messages and trying to proceed anyway, which often makes the original problem worse.

---

## 4. The Commands Cheat Sheet

### Category A: File System Commands
1. `pwd` - Prints the exact directory path you are currently in.
2. `ls -lh` - Lists files and folders with human-readable sizes (KB, MB, GB).
3. `cd /path/to/folder` - Changes your current directory to the specified path.
4. `mkdir -p project/src` - Creates a new folder and any missing parent folders.
5. `touch file.txt` - Creates a new empty file.
6. `cp -r folder1 folder2` - Copies a folder and all its contents to a new place.
7. `mv file.txt new_folder/` - Moves or renames a file/folder.
8. `rm -rf unwanted_dir` - Forcefully deletes a folder and everything inside it (use carefully!).
9. `cat file.txt` - Shows the entire content of a file on the terminal screen.
10. `grep -i "error" log.txt` - Searches for a specific word inside a file (case-insensitive).

### Category B: Process Management Commands
11. `ps aux` - Lists all running processes on the system with details.
12. `top` - Displays a live, real-time list of running processes and system resources.
13. `kill <PID>` - Stops a running process using its Process ID number.
14. `killall nginx` - Stops all running processes that share the name "nginx".
15. `df -h` - Shows how much disk space is free and used on your system.
16. `free -m` - Displays the system's free and used RAM memory in Megabytes.
17. `systemctl status service_name` - Shows whether a specific service is running, stopped, or failed.

### Category C: Networking & Troubleshooting Commands
18. `ping google.com` - Checks if a website/server is online and measures response speed.
19. `ip addr show` - Displays your computer's IP addresses and network cards.
20. `curl -I https://example.com` - Fetches header details from a web address to verify if it is working.
21. `dig example.com` - Queries the DNS database to get the IP address of a domain name.
22. `ss -tuln` - Lists all network ports currently open and listening on the server.
