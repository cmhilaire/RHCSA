# User & Group Management

This guide explains the **basic commands** to manage users and groups in Linux, with definitions of commonly used options and practical examples. It also includes a company scenario with different types of users and groups.

---

## 🔑 User Management Commands

### 1. `useradd` – Add a User
```bash
useradd [options] username
```

Options:
* -m → Create home directory for the user.
* -s SHELL → Specify login shell (e.g., /bin/bash, /sbin/nologin).
* -d DIR → Specify custom home directory.
* -e YYYY-MM-DD → Set account expiration date.
* -G group1,group2 → Add user to additional groups.


Example:
```bash
useradd -m -s /bin/bash alice
```
Creates user alice with a home directory and /bin/bash shell.

---

### 2. `passwd` – Set or Change User Password
```bash
passwd username
```

Prompts to enter a new password.

Example:
```bash
passwd alice
```

### 2.1 `chpasswd` – Set or Change User Password
An unsafe approch is to use `chpasswd`. It provides a non-interactive way to set passwords, which is useful in a devlopement environment.
Example:
```bash
# Single user
echo "john:Secret123" | chpasswd

# Multi user
echo -e "alice:Passw0rd\nbob:MyPass123" | chpasswd

# here-string (Requires Bash)
chpasswd <<< "john:Secret123"
```

🔍 Explanation:
* echo "username:password" → Prints the string username:password.
* | (pipe) → Sends that string to the standard input of the next command.
* chpasswd → Reads username:password pairs from standard input and updates the password for the specified user.
* <<< is a here-string in Bash, which sends the given string directly to the standard input of the command.


🔧 Notes: Avoid Hardcoding Passwords
* Writing plain-text passwords directly in scripts `(echo "user:password" | chpasswd)` is unsafe.
* Anyone with access to the script or shell history can read the password.


### 3. `usermod` – Modify an Existing User
```bash
usermod [options] username
```

Options:
* -aG group → Add user to a supplementary group (keep existing groups).
* -L → Lock account.
* -U → Unlock account.
* -e YYYY-MM-DD → Set account expiration date.
* -s SHELL → Change login shell.

Examples:
```bash
usermod -aG finance alice
usermod -L bob
usermod -U bob
usermod -e 2025-12-31 charlie
```

### 4. `userdel` – Delete a User
```bash
userdel [options] username
```

Options:
* -r → Remove home directory and mail spool.

Example:
```bash
userdel -r alice
```

## 👥 Group Management Commands
### 1. `groupadd` – Create a Group
```bash
groupadd groupname
```

Example:
```bash
groupadd finance
```
### 2. `groupdel` – Delete a Group
```bash
groupdel groupname
```


Example:
```bash
groupdel finance
```

### 3. `gpasswd` – Set Group Admin & Password
```bash
gpasswd [options] group
```

Options:
* -a user → Add user to group.
* -d user → Remove user from group.
* -A user → Set group administrator.

Example:
```bash
gpasswd -a alice finance
```

## 🔒 Password & Expiration Management
### 1. `chage` – Manage Password Expiry
```bash
chage [options] username
```


Options:
* -d 0 → Force password change at next login.
* -M days → Set maximum password validity in days.
* -W days → Set warning days before password expires.
* -E YYYY-MM-DD → Set account expiration date.

Examples:
```bash
chage -d 0 alice      # Force password reset on next login
chage -M 90 bob       # Password expires every 90 days
chage -E 2025-12-31 charlie  # Account expires on 2025-12-31
```

---
## 🏢 Practice Scenario: Company Setup
---

A company has **3 departments (groups)**:
* finance
* hr
* it

There are **3 types of users**:
1. **Admin users** – Can manage accounts.
2. **Standard user**s – Belong to 1, 2, or 3 departments.
3. **No-login users** – System/service accounts.

### Step 1: Create Groups
```bash
groupadd finance
groupadd hr
groupadd it
```

### Step 2: Create Admin Users

Admins should be able to manage users, so add them to the wheel group.
```bash
useradd -m -s /bin/bash -G wheel admin1
passwd admin1


useradd -m -s /bin/bash -G wheel admin2
passwd admin2
```

### Step 3: Create Standard Users

Each standard user:

Must change password at first login.

Password must expire every 90 days.

At least one user per department has an expiration date.
```bash
# Finance Department
useradd -m -s /bin/bash -G finance alice
passwd alice
chage -d 0 alice
chage -M 90 alice
usermod -e 2025-12-31 alice   # Expiration

# HR Department
useradd -m -s /bin/bash -G hr bob
passwd bob
chage -d 0 bob
chage -M 90 bob
usermod -e 2025-11-30 bob

# IT Department
useradd -m -s /bin/bash -G it charlie
passwd charlie
chage -d 0 charlie
chage -M 90 charlie
usermod -e 2025-10-31 charlie
```

### Step 4: Users in Multiple Departments

Some users can access more than one department:
```bash
useradd -m -s /bin/bash -G finance,it david
passwd david
chage -d 0 david
chage -M 90 david

useradd -m -s /bin/bash -G hr,finance,it emma
passwd emma
chage -d 0 emma
chage -M 90 emma
```

### Step 5: Create No-Login Users

For service/system accounts:
```bash
useradd -s /sbin/nologin backupsvc
useradd -s /sbin/nologin websvc
```

### Step 6: Lock/Unlock User Accounts
```bash
usermod -L bob    # Lock HR user bob
usermod -U bob    # Unlock HR user bob
```
---
## 📊 Company Structure Overview
| User      | Type     | Department(s)     | Password Policy          | Expiration Date | Notes                  |
| --------- | -------- | ----------------- | ------------------------ | --------------- | ---------------------- |
| admin1    | Admin    | All (wheel group) | Manual                   | None            | Can manage accounts    |
| admin2    | Admin    | All (wheel group) | Manual                   | None            | Can manage accounts    |
| alice     | Standard | Finance           | Change on login, 90 days | 2025-12-31      | Expiration set         |
| bob       | Standard | HR                | Change on login, 90 days | 2025-11-30      | Can be locked/unlocked |
| charlie   | Standard | IT                | Change on login, 90 days | 2025-10-31      | Expiration set         |
| david     | Standard | Finance, IT       | Change on login, 90 days | None            | Multi-department user  |
| emma      | Standard | Finance, HR, IT   | Change on login, 90 days | None            | Multi-department user  |
| backupsvc | No-login | None              | N/A                      | None            | Service account        |
| websvc    | No-login | None              | N/A                      | None            | Service account        |

---
## ✅ Summary
   - **useradd, usermod, userdel** → Manage users.
   - **groupadd, groupdel, gpasswd** → Manage groups.
   - **passwd, chage** → Manage password and expiry.
   - **usermod -L/-U** → Lock/unlock accounts.
   - Admins enforce password policies, expirations, and departmental group assignments.

This ensures secure and organized user & group management across departments.