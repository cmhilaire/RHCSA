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
r → List contents
w → Add/remove files
x → Enter directory

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
Setuid → Run as file owner.  
Setgid → New files inherit group.  
Sticky bit → Only owner can delete files in directory.  

Example:

```bash
chmod g+s /company/finance
```

---   


✅ Summary
* `chown` → Change ownership
* `chmod` → Change permissions (symbolic/numeric)
* `ls -l` → Check permissions
* `umask` → Default permissions
* Special bits (`setuid, setgid, sticky`) control advanced access
   
   
---