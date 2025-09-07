
## Table of Contents
- [Introduction](#-introduction)
- [SELinux Basics](#-selinux-basics)
- [Firewalld Basics](#firewalld-basics)
- [Practice Scenario](#practice-scenario)
- [Summary](#summary)


# SELinux & Firewalld (RHEL 9)


## Introduction
On RHEL 9, **SELinux** and **firewalld** are two critical security mechanisms:

- **SELinux (Security-Enhanced Linux):** Provides **mandatory access control (MAC)** policies for processes, files, and ports.
- **firewalld:** A **dynamic firewall manager** that controls network access via zones and services.
   

---

## SELinux Basics


### Key Concepts
- **Enforcing**: SELinux enforces policies and denies unauthorized access
- **Permissive**: SELinux logs violations but doesn't deny access
- **Disabled**: SELinux is turned off
- **Context**: Security labels on files, processes, and ports

---  

### Essential Commands

#### Check SELinux Status
```bash
# View current status
sestatus

# Check enforcement mode
getenforce
```

#### Temporarily Change Mode
```bash
setenforce 0   # permissive
setenforce 1   # enforcing
```

#### Permanently Change Mode

Edit /etc/selinux/config:
```ini
SELINUX=enforcing   # or permissive OR disabled
```

#### Manage File Contexts with restorecon
```bash
# Restore default SELinux context on a file/directory
restorecon -v /path/to/file

# Recursively restore context
restorecon -Rv /path/to/directory

# View current context
ls -Z /path/to/file
```

#### SELinux Boolean Management
```bash
# List all booleans
getsebool -a

# Set boolean temporarily
setsebool httpd_can_network_connect on

# Set boolean permanently
setsebool -P httpd_can_network_connect on
```

--- 

## Firewalld Basics


### Key Concepts
- **Zones**: Predefined sets of rules for different trust levels
- **Services**: Predefined configurations for common applications
- **Rich Rules**: Advanced firewall rules with multiple conditions

---

### Essential Commands

#### Check Firewalld Status
```bash
# Check firewalld status
systemctl status firewalld

# View default zone
firewall-cmd --get-default-zone

# List all zones
firewall-cmd --get-zones
```

#### Manage Services and Ports
```bash
# Add HTTP service to permanent configuration
firewall-cmd --permanent --add-service=http

# Add custom port (8080/tcp)
firewall-cmd --permanent --add-port=8080/tcp

# Remove service
firewall-cmd --permanent --remove-service=ftp

# Reload firewall to apply changes
firewall-cmd --reload
```

#### Zone Management
```bash
# Change default zone
firewall-cmd --set-default-zone=public

# List all services in current zone
firewall-cmd --list-services

# List all ports in current zone
firewall-cmd --list-ports

# List all current rules
firewall-cmd --list-all
```

#### Emergency mode
```bash
# Emergency mode
firewall-cmd --panic-on   # Block all traffic
firewall-cmd --panic-off  # Restore normal operation
```

---

## Practice Scenario

Scenario:
You are the administrator of a RHEL 9 server. The company wants to host a website on Apache (httpd), served from /webdata. The firewall must allow access, and SELinux must be configured correctly.

Tasks:

1. Install Apache
```bash
dnf install httpd -y
systemctl enable --now httpd
```

2. Create custom content directory
```bash
mkdir /webdata
echo "Welcome to RHCSA practice!" > /webdata/index.html
```

3. Configure Apache to use `/webdata`
Edit `/etc/httpd/conf/httpd.conf` and change:
```ini
DocumentRoot "/webdata"
```

4. Fix SELinux Contexts
```bash
semanage fcontext -a -t httpd_sys_content_t "/webdata(/.*)?"
restorecon -Rv /webdata
```
- `httpd_sys_content_t` → correct context for Apache content.
- `restorecon` applies the context.

5. Open Firewall for HTTP
```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
```

6. Test
- Access from a browser: `http://<server-ip>`
- Or using `curl`:
```bash
curl http://localhost
```

Expected result: `Page displays Welcome to RHCSA practice!`

---

## Summary
- **SELinux** ensures files and processes follow strict security rules (`ls -Z`, `restorecon`, `semanage fcontext`).
- **firewalld** controls network access (`firewall-cmd --add-service`, `--add-port`).
- Together, they secure services.