# Systemd Services (systemctl, journalctl)

This guide explains how to **manage services in Linux using systemd**, focusing on the commands `systemctl` and `journalctl`. These tools are essential for **service management, startup configuration, troubleshooting, and log analysis**.

---

## 🔑 What is systemd?

- **systemd** is the default **init system** on modern **Linux distributions** (**CentOS 7+, RHEL 7+, Ubuntu 16.04+, Debian 8+**).  
- It manages:
  - **System services (daemons)**  
  - **Startup/shutdown process**  
  - **Logs and journaling**  

---

## ⚙️ systemctl Commands (Service Management)

The `systemctl` command is used to **start, stop, enable, disable, and check the status of services**.

---

### 1. Start a Service
```bash
systemctl start service_name
```
Starts a service immediately.   
Example:
```bash
systemctl start nginx
```
This command initiates the specified service immediately. No output typically means the command executed successfully.

### 2. Stop a Service
```bash
systemctl stop service_name
```
Stops a running service.   
Example:
```bash
systemctl stop nginx
```
This command terminates the specified service. Use this when you need to stop a service temporarily.

### 3. Restart a Service
```bash
systemctl restart service_name
```
Stops and starts the service again (useful after configuration changes).   
Example:
```bash
systemctl restart sshd
```
This command stops and then starts the specified service. Useful for applying configuration changes.

### 4. Reload a Service
```bash
systemctl reload service_name
```
Reloads configuration without fully restarting the service (if supported).   
Example:
```bash
systemctl reload httpd
```
This command reloads the service's configuration files without interrupting current connections. Not all services support this option.


### 5. Enable a Service at Boot
```bash
systemctl enable service_name
```
Ensures the service starts automatically at boot.   
Example:
```bash
systemctl enable docker
```
This command configures the service to start automatically during system boot.

### 6. Disable a Service at Boot
```bash
systemctl disable service_name
```

Prevents service from starting at boot.   
Example:
```bash
systemctl disable docker
```
This command prevents the service from starting automatically during system boot.


### 7. Check Service Status
```bash
systemctl status service_name
```

Shows service status, logs, and PID.   
Example:
```bash
systemctl status firewalld
```
This command displays the current status of the specified service, including whether it's active, enabled, or failed, along with recent log entries.


### 8. Check if Service is Enabled
```bash
systemctl is-enabled service_name
```

Example:
```bash
systemctl is-enabled sshd
```
This command returns the enablement status of a service (enabled, disabled, or static).

### 9. Check if Service is Active
```bash
systemctl is-active service_name
```

Example:
```bash
systemctl is-active crond
```

### 10. Mask/Unmask Service
* `mask` → Prevents a service from being started, either manually or automatically, by creating a symbolic link to `/dev/null`.
* `unmask` → Restores normal behavior, allowing the service to be started again.

```bash
systemctl mask service_name
systemctl unmask service_name
```

Example:
```bash
systemctl mask telnet
```

---
## 📊 System State & Targets

`systemctl` also manages system runlevels (targets).
* `systemctl isolate multi-user.target` → Switch to multi-user mode (like runlevel 3).
* `systemctl isolate graphical.target` → Switch to graphical mode (like runlevel 5).
* `systemctl get-default` → Show default target.
* `systemctl set-default multi-user.target` → Set default boot target.
---

## 📜 journalctl Commands (Log Management)

The `journalctl` command is used to **query and analyze system logs** managed by systemd’s journal.

### 1. Show All Logs
```bash
journalctl
```
This command displays all journal logs, starting with the oldest entries.

### 2. Show Logs for a Service
```bash
journalctl -u service_name
```
Options:
- `-u` or `--unit`: Filters logs by specific service unit
Example:
```bash
journalctl -u nginx
```

### 3. Follow Logs in Real-Time
```bash
journalctl -f
```
Options:
- `-f` or `--follow`: Follows the journal in real-time (Similar to `tail -f /var/log/messages`).

Example:
```bash
journalctl -u mysql -f
```

### 4. Show Logs Since Boot
```bash
journalctl -b
```
Options:
- `-b` or `--boot`: Shows logs from the current boot
- `-b -1`: Shows logs from the previous boot

Example:
```bash
journalctl -b -0
```

### 5. Limit Number of Log Lines
```bash
journalctl -n 50
```
Shows the last 50 log lines.

### 6. Filtering Logs by Time
```bash
journalctl --since="YYYY-MM-DD HH:MM:SS" --until="YYYY-MM-DD HH:MM:SS"
```
Options:
- `--since`: Shows entries since the specified date
- `--until`: Shows entries until the specified date

Example:
```bash
journalctl --since="2023-10-01 09:00:00" --until="2023-10-01 17:00:00"
```

### 7. Show Kernel Logs
```bash
journalctl -k
```
Options:
- `-k` or `--dmesg`: Shows only kernel messages

Example:
```bash
journalctl -k --since="10 minutes ago"
```

### 8. Show Logs for a User
```bash
journalctl _UID=1000
```

### 9. Exporting Logs to JSON
```bash
journalctl -o json
```
Options:
- `-o` or `--output`: Specifies output format (short, json, json-pretty, etc.)

Example:
```bash
journalctl -u nginx -o json-pretty
```

## Practical Examples and Use Cases
Troubleshooting a Failed Service
```bash
# Check status
systemctl status failed_service

# View detailed logs
journalctl -u failed_service --since="today"

# Restart the service
systemctl restart failed_service
```

Monitoring Service Performance
```bash
# Follow logs in real-time
journalctl -u application_service -f

# Check for errors specifically
journalctl -u application_service -p err
```

## Notes & Recommendations
- Use `systemctl enable` + `systemctl start` to make services **persistent and active**.
- Use `systemctl status` to quickly verify running state and last log lines.
- Use `journalctl -u service -f` for **real-time troubleshooting**.
- Combine `journalctl` with `grep` for **filtered log analysis**.
- Manage log size with `journalctl --vacuum-size=1G` (limit journal size).


## Cheat Sheet Table
| Command                        | Description                   | Example                                   |
| ------------------------------ | ----------------------------- | ----------------------------------------- |
| `systemctl start service`      | Start a service immediately   | `systemctl start nginx`                   |
| `systemctl stop service`       | Stop a running service        | `systemctl stop nginx`                    |
| `systemctl restart service`    | Restart service               | `systemctl restart sshd`                  |
| `systemctl reload service`     | Reload configuration          | `systemctl reload httpd`                  |
| `systemctl enable service`     | Enable service at boot        | `systemctl enable docker`                 |
| `systemctl disable service`    | Disable service at boot       | `systemctl disable docker`                |
| `systemctl status service`     | Show service status           | `systemctl status firewalld`              |
| `systemctl is-enabled service` | Check if enabled              | `systemctl is-enabled sshd`               |
| `systemctl is-active service`  | Check if active               | `systemctl is-active crond`               |
| `systemctl mask service`       | Prevent service from starting | `systemctl mask telnet`                   |
| `systemctl unmask service`     | Restore service               | `systemctl unmask telnet`                 |
| `systemctl get-default`        | Show default target           | `systemctl get-default`                   |
| `systemctl set-default target` | Set default boot target       | `systemctl set-default multi-user.target` |
| `journalctl`                   | Show all logs                 | `journalctl`                              |
| `journalctl -u service`        | Logs for a service            | `journalctl -u nginx`                     |
| `journalctl -f`                | Follow logs in real time      | `journalctl -f`                           |
| `journalctl -b`                | Logs since last boot          | `journalctl -b`                           |
| `journalctl -n N`              | Show last N lines             | `journalctl -n 100`                       |
| `journalctl --since`           | Show logs since time          | `journalctl --since "2025-08-01"`         |
| `journalctl -k`                | Show kernel logs              | `journalctl -k`                           |


## Conclusion
Mastering `systemctl` and `journalctl` commands is essential for effective **Linux system administration**. These tools provide comprehensive control over services and detailed insights through system logs. Regular practice with these commands will significantly improve your ability to **manage, troubleshoot, and optimize Linux systems running systemd**.

Remember that many commands accept additional options - always consult the manual pages (`man systemctl`, `man journalctl`) for the most comprehensive and up-to-date information about these powerful system management tools.

