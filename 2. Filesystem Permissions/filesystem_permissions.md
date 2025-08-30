```markdown
# Filesystem Permissions

This document explains the **basic commands for file permissions** in Linux, with a practice scenario based on the company’s user/group setup.
```
---   

## 🔑 Basic Commands

### 1. Check Permissions
```bash
ls -l
```

### 2. Change Ownership
```bash
chown user:group filename
```

Example:
```bash
chown alice:finance report.txt
```

### 3. Change Permissions (Symbolic)
```bash
chmod u+rwx,g+rx,o-r file
```

Example:
```bash
chmod u+rw,g+r,o-r report.txt
```

### 4. Change Permissions (Numeric)
```bash
chmod 640 file
```
Example:

```bash
chmod 750 script.sh
```

### 5. Default Permissions for New Files (umask)
```bash
umask 027
```

### 6. Directory Access Control
* `r` → List contents
* `w` → Add/remove files
* `x` → Enter directory


### 7. Listing classify
List files with type indicators, making it easier to distinguish directories, executables, and special files. If you run:
```bash
ls -F
```

If you see something like:
```bash
Documents/  script.sh*  my_socket=  link@  notes.txt  my_shared_file+  my_secured_file.
```

- `Documents/` → a directory
- `script.sh*` → an executable file
- `my_socket=` a socket
- `link@` → a symbolic link
- `notes.txt` → a regular file
- `my_shared_file+` → an extended attributes (like ACLs)
- `my_secured_file.` → a SELinux security context associated with it

---

## 🏢 Practice Scenario: File Permissions
Using the same finance, hr, it departments:


### Step 1: Department Directories
```bash
mkdir /company
mkdir /company/finance
mkdir /company/hr
mkdir /company/it
# OR
mkdir -p /company/{finance,hr,it}
```


### Step 2: Assign Ownership
```bash
chown :finance /company/finance
chown :hr /company/hr
chown :it /company/it
```


### Step 3: Set Group Permissions
```bash
chmod 770 /company/finance
chmod 770 /company/hr
chmod 770 /company/it
```
Now only group members can access their department folders.


### Step 4: Multi-Department Access
Example: charlie (finance + it)  
Finance files → accessible.  
IT files → accessible.  
HR files → restricted.  


### Step 5: Create Shared File
```bash
touch /company/finance/budget.txt
chown alice:finance /company/finance/budget.txt
chmod 640 /company/finance/budget.txt
```
alice (owner) → read/write.  
finance group → read.  
others → no access.  


### Step 6: Restricting Access
```bash
chmod 000 /company/finance/secret.txt
```
Now nobody (not even owner) can access until permissions are reset.


### Step 7: Special Permissions
#### 1. setuid (Set User ID)
🔹 What it does:

When a binary with setuid is executed, it runs with the permissions of the file owner, not the user who launched it.

Example: /usr/bin/passwd has setuid so normal users can change their own password (which updates /etc/shadow, a root-owned file).

🔹 Example Commands:   
Symbolic:
```bash
touch /company/hr/my_file.txt
chmod u+s /company/hr/my_file.txt
ls -l /company/hr/my_file.txt
# -rwsr-xr-x 1 root root ...
# Note the "s" in place of "x" for the user.
```
Numeric:
```bash
chmod 4755 /company/hr/my_file.txt
```
* `4` = setuid
* `755` = rwxr-xr-x normal permissions

#### 2. setgid (Set Group ID)
🔹 What it does:

On files: runs with the group’s permissions of the file.

On directories: new files inside inherit the directory’s group, not the creator’s primary group.

🔹 Example Commands:   
Symbolic:
```bash
chmod g+s /company/finance
ls -ld /company/finance
# drwxr-sr-x 2 user devs ...
# Note the "s" in the group field.
```

Numeric:
```bash
chmod 2755 /company/finance
```
* `2` = setgid
* `755` = rwxr-xr-x normal permissions

#### 3. Sticky Bit
🔹 What it does:

On directories: prevents users from deleting/renaming files unless they own the file or are root.

Common example: /tmp has sticky bit.

🔹 Example Commands:   
Symbolic:
```bash
chmod +t /company
ls -ld /company
# drwxrwxrwt 9 root root ...
# Note the "t" at the end.
```

Numeric:
```bash
chmod 1777 /company
```
* `1` = sticky bit
* `777` = rwxrwxrwx normal permissions


#### Quick reference table
| Special Bit | Symbolic         | Numeric           | Example Usage                                   |
| ----------- | ---------------- | ----------------- | ----------------------------------------------- |
| **setuid**  | `chmod u+s file` | `chmod 4755 file` | Run program as file owner (e.g., `passwd`)      |
| **setgid**  | `chmod g+s dir`  | `chmod 2755 dir`  | Files inherit group; programs run with group ID |
| **sticky**  | `chmod +t dir`   | `chmod 1777 dir`  | Only owner can delete (e.g., `/tmp`)            |


---   


✅ Summary
* `chown` → Change ownership
* `chmod` → Change permissions (symbolic/numeric)
* `ls -l` → Check permissions
* `umask` → Default permissions
* Special bits (`setuid, setgid, sticky`) control advanced access
   
   
---