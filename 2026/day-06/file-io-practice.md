# Day 06 – Linux File Input/Output (I/O) Practice

In Linux, managing files (creating, writing, appending, and reading) is done directly from the command line. This is crucial for DevOps engineers who configure servers, deploy scripts, and read logs.

---

## 1. Step-by-Step Practice Flow

Here is the step-by-step command flow run to practice file operations:

### Step 1: Create an empty file
* **Command:**
  ```bash
  touch notes.txt
  ```
* **Explanation:** `touch` creates a brand new, empty file named `notes.txt`. If the file already exists, it updates the file's timestamp without changing its content.

### Step 2: Write text to the file (Overwriting)
* **Command:**
  ```bash
  echo "Line 1: Linux is awesome." > notes.txt
  ```
* **Explanation:** The `>` operator redirects the output of `echo` into `notes.txt`. If there was any old text in the file, it is deleted (overwritten) and replaced with this new line.

### Step 3: Append new lines to the file
* **Command:**
  ```bash
  echo "Line 2: DevOps is all about automation." >> notes.txt
  ```
* **Explanation:** The `>>` operator appends the output to the end of `notes.txt`. It does **not** delete the existing content; it just adds a new line.

### Step 4: Write and display at the same time
* **Command:**
  ```bash
  echo "Line 3: Always check your logs." | tee -a notes.txt
  ```
* **Explanation:** The pipe `|` sends the text to the `tee` command. The `tee` command writes the text to `notes.txt` (using `-a` to append) and also prints it on the terminal screen at the same time.
* **Output on Screen:**
  ```text
  Line 3: Always check your logs.
  ```

### Step 5: Read the entire file
* **Command:**
  ```bash
  cat notes.txt
  ```
* **Explanation:** `cat` (concatenate) displays all the content inside `notes.txt` at once.
* **Output:**
  ```text
  Line 1: Linux is awesome.
  Line 2: DevOps is all about automation.
  Line 3: Always check your logs.
  ```

### Step 6: Read the top part of the file
* **Command:**
  ```bash
  head -n 2 notes.txt
  ```
* **Explanation:** `head -n 2` reads and displays only the first 2 lines from the top of `notes.txt`.
* **Output:**
  ```text
  Line 1: Linux is awesome.
  Line 2: DevOps is all about automation.
  ```

### Step 7: Read the bottom part of the file
* **Command:**
  ```bash
  tail -n 2 notes.txt
  ```
* **Explanation:** `tail -n 2` reads and displays only the last 2 lines from the bottom of `notes.txt`.
* **Output:**
  ```text
  Line 2: DevOps is all about automation.
  Line 3: Always check your logs.
  ```

---

## 2. Core Concepts Explained Simply

* **Redirection `>` vs `>>`**
  * `>` is a **destructive** write. It empties the file first, then writes.
  * `>>` is a **safe** write. It adds content to the bottom of the file without deleting anything.
* **Tee Command**
  * Think of `tee` like a water pipe splitter. It splits the output: one flow goes into a file, and the other flow goes to your screen.

---

## 3. Best Practices for File operations
* **Always check before overwriting:** Be very careful before using `>`. Running `echo "" > config.conf` will completely wipe out your configurations.
* **Use `tail -f` to monitor logs in real-time:** When debugging services, use `tail -f /var/log/syslog` to watch logs scroll in real-time as events happen.
* **Do not use `cat` on huge files:** If you run `cat` on a 5 GB log file, it will freeze your terminal session. Use `less`, `tail`, or `grep` instead to view parts of large files.

---

## 4. Common Mistakes Juniors & New Learners Make
* **Accidental Overwrite:** Writing `echo "new setting" > configuration.txt` instead of `>>` and wiping out all previous server settings.
* **Sudo Redirection Error:** Running:
  ```bash
  sudo echo "some config" > /etc/protected.conf
  ```
  * *Why this is a mistake:* The redirection `>` is executed by the regular shell user, not `sudo`, resulting in a "Permission denied" error.
  * *The correct way:* Use `tee` with sudo to write to protected files:
    ```bash
    echo "some config" | sudo tee /etc/protected.conf
    ```
