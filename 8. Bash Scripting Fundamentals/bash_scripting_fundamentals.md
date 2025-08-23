## Table of Contents
- [Bash Scripting Fundamentals](#bash-scripting-fundamentals)
    - [1. Basics of a Bash Script](#1-basics-of-a-bash-script)
    - [2. Variables](#2-variables)
    - [3. Conditions (if/else)](#3-conditions-ifelse)
    - [4. Loops](#4-loops)
    - [5. Error Handling](#5-error-handling)
    - [6. Practical Example (Backup Script)](#6-practical-example-backup-script)
    - [7. ASCII Diagram (Control Flow)](#7-ascii-diagram-control-flow)
    - [Best Practices](#best-practices)


---


# Bash Scripting Fundamentals

A practical guide to **Bash scripting basics** — including **variables**, **conditions**, **loops**, and **error handling**.
Useful for **DevOps**, **Sysadmins**, **Linux beginners**, and **RHCSA exam prep**.


---


### 1. Basics of a Bash Script

* A Bash script is a text file with commands executed in sequence
* File should start with a shebang
* Make script executable
* Run script

```bash
#!/bin/bash         # Shebang, defines interpreter
chmod +x file.sh    # Make executable
./file.sh           # Run script
```

---

### 2. Variables
```bash
#!/bin/bash
name="Doe"
echo "Hello, $name!"
```

* No spaces around `=`.
* `$` is used to access variable values.

---

### 3. Conditions (if/else)
Example 1: Numeric Comparison
```bash
#!/bin/bash
num=10
age=18

# Basic if statement
if [ "$num" -le 10 ]; then
    echo "Number is greater than 10"
fi

# If-else
if [ $num -gt 5 ]; then
  echo "Number is greater than 5"
else
  echo "Number is less than or equal to 5"
fi

# If-elif-else
if [ "$age" -lt 18 ]; then
    echo "Minor"
elif [ "$age" -ge 18 ] && [ "$age" -lt 65 ]; then
    echo "Adult"
else
    echo "Senior"
fi
```

Common operators:
* `[ "$a" -eq "$b" ]` # Equal
* `[ "$a" -ne "$b" ]` # Not equal
* `[ "$a" -lt "$b" ]` # Less than
* `[ "$a" -le "$b" ]` # Less than or equal
* `[ "$a" -gt "$b" ]` # Greater than
* `[ "$a" -ge "$b" ]` # Greater than or equal

---

Example 2: String Comparison
```bash
#!/bin/bash
name="bash"

if [ "$name" = "bash" ]; then
  echo "The shell is bash"
else
  echo "Not bash"
fi
```

String operators:
* `[ -z "$str" ]`  # String is empty
* `[ -n "$str" ]`  # String is not empty
* `[ "$a" = "$b" ]` # Strings are equal
* `[ "$a" != "$b" ]` # Strings are not equal

   
Example 3: file test
```bash
#!/bin/bash
file="/does/not/exist"

# If-else
if [ -f "$file" ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

File tests:
* `[ -f file ]`    # File exists and is regular file
* `[ -d dir ]`     # Directory exists
* `[ -r file ]`    # File is readable
* `[ -w file ]`    # File is writable
* `[ -x file ]`    # File is executable
* `[ -s file ]`    # File exists and is not empty

Example 4: Case statements
```bash
#!/bin/bash

case "$1" in
    start)
        echo "Starting service..."
        ;;
    stop)
        echo "Stopping service..."
        ;;
    restart)
        echo "Restarting service..."
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        exit 1
        ;;
esac

# ./filename start
# ./filename stop
# ./filename restart
# ./filename
```

* `$0` is used to retrieve the filename
* `$1` is used to retrieve the first argument


---

### 4. Loops
For Loops
```bash
#!/bin/bash

# Basic for loop
for i in 1 2 3 4 5; do
    echo "Number: $i"
done

# Loop through files
for file in *.txt; do
    echo "Processing $file"
done

# C-style for loop
for ((i=0; i<10; i++)); do
    echo "Counter: $i"
done

# Loop through command output
for user in $(cat users.txt); do
    echo "Adding user: $user"
done
```

While Loops
```bash
#!/bin/bash

# Basic while loop
counter=1
while [ $counter -le 5 ]; do
    echo "Counter: $counter"
    ((counter++))
done


# Read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < "file.txt"


# Infinite loop with break
while true; do
    read -p "Enter command (quit to exit): " cmd
    if [ "$cmd" = "quit" ]; then
        break
    fi
    echo "Executing: $cmd"
done
```
* `IFS=`: This sets the Internal Field Separator to empty. This prevents read from performing word splitting and preserves leading/trailing whitespace in the line variable.
* `read -r line`: The `read` command reads a line from standard input and stores it in the `line` variable. The `-r` option prevents backslash escapes from being interpreted, ensuring the raw content of the line is read.
* `do ... done`: This defines the block of commands to be executed for each line read.
* `< "file.txt"`: This redirects the content of my_data.txt to the standard input of the while loop, allowing read to consume it line by line.

Until Loops
```bash
#!/bin/bash

# Until loop (runs until condition becomes true)
count=1
until [ $count -gt 5 ]; do
    echo "Count: $count"
    ((count++))
done
```

---

### 5. Error Handling
Example 1: Check Command Exit Status
```bash
#!/bin/bash
ls /notexist
if [ $? -ne 0 ]; then
  echo "Error: Directory does not exist"
fi

# Alternative: use && and ||
ls /notexist && echo "Success" || echo "Failed"

# Check if command exists
if ! command -v git &> /dev/null; then
    echo "Git is not installed"
    exit 1
fi
```

* `$?` holds the exit status of last command (0 = success, non-zero = failure).

Example 2: Using set
```bash
#!/bin/bash
set -e  # exit on any error
cp file1.txt file2.txt
echo "Copy succeeded"
```

Options:
* `set -e` → exit if any command fails
* `set -u` → treat unset variables as errors
* `set -x` → print commands before execution
* `set -o pipefail` → catch errors in piped commands


Example 3: Trap Errors
```bash
#!/bin/bash
trap 'echo "Error occurred on line $LINENO"' ERR

echo "Start"
false   # this command fails
echo "End"
```

* `trap` executes custom code on errors.

Example 4: Trap for Signal Handling
```bash
#!/bin/bash

# Cleanup function
cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/tempfile
    echo "Script terminated"
}

# Set trap for signals
trap cleanup EXIT INT TERM

# Main script
echo "Script running..."
sleep 3
echo "Script completed"
```

Example 5: Try-Catch Pattern (Using Functions)
```bash
#!/bin/bash

# Function to handle errors
error_handler() {
    echo "Error occurred in line $1"
    exit 1
}

# Set error trap
trap 'error_handler $LINENO' ERR

# Example that might fail
risky_operation() {
    # This will trigger error if file doesn't exist
    cat /nonexistent/file
    echo "This won't be reached if error occurs"
}

# Main execution
echo "Starting script..."
risky_operation
echo "Script completed successfully"
```

---

### 6. Practical Example (Backup Script)
Practical Combined Example
```bash
#!/bin/bash
# Backup script with error handling

set -euo pipefail

# Configuration
SRC_DIR="/home/user/data"
BACKUP_DIR="/home/user/backup"
DATE=$(date +%F)
LOG_FILE="/var/log/tty_backup.log"

# Function to log messages
log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a "$LOG_FILE"
}

# Function to handle errors
error_exit() {
    log_message "ERROR: $1"
    exit 1
}

# Check if backup directory exists
if [ ! -d "$BACKUP_DIR" ]; then
    mkdir -p "$BACKUP_DIR" || error_exit "Failed to create backup directory"
fi

# Alternative
# [ ! -d "$BACKUP_DIR" ] && mkdir -p "$BACKUP_DIR"

# Check if source directory exists
if [ ! -d "$SRC_DIR" ]; then
    error_exit "Source directory $SRC_DIR does not exist"
fi

log_message "Starting backup of $SRC_DIR"

# Loop through files and copy
for file in "$SRC_DIR"/*; do
  if [ -f "$file" ]; then
    cp "$file" "$BACKUP_DIR/${DATE}_$(basename "$file")"
    echo "Backed up $file"
    log_message "Backed up $file"
  else
    echo "Skipping non-regular file $file"
    log_message "Skipping non-regular file $file"
  fi
done

echo "Backup completed successfully."
log_message "Backup completed successfully."
# exit 0
```

---

### 7. ASCII Diagram (Control Flow)

 ┌───────────┐
 │  Script   │
 └─────┬─────┘
       │
 ┌─────▼─────┐
 │ Condition │──Yes──► Execute block
 └─────┬─────┘
       │No
       ▼
   Next Command


---

### Best Practices
* Always use `#!/bin/bash` at the beginning
* Quote variables: `"$variable"` instead of `$variable`
* Use `set -euo pipefail` for better error handling
* Check return codes of important commands
* Use functions to organize code
* Add comments for complex logic
* Validate user input
* Use meaningful variable names
* Test scripts with different scenarios
* Handle signals for clean termination
