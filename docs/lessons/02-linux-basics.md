# 02 -- Linux Basics

**Topics:** Unix history, OS architecture, kernel, filesystem hierarchy, distributions, LVM, users, groups, permissions, monitoring

---

## From Unix to Linux

- **1969** -- Ken Thompson & Dennis Ritchie create **Unix** at Bell Labs
- Core Unix philosophy: modularity, text streams, "everything is a file"
- **1991** -- Linus Torvalds writes the **Linux kernel** (10,239 lines; today: 33M+ lines)
- Linux = Unix-like kernel + GNU tools + package ecosystems

---

## What Does an OS Do?

1. **Manage hardware** -- CPU scheduling, memory allocation, device I/O
2. **Provide a user interface** -- shell (CLI) or desktop (GUI)
3. **Run and manage programs** -- process lifecycle, multitasking
4. **Enforce security** -- users, permissions, access control

### Key components

| Component | Role |
|-----------|------|
| **Kernel** | Core -- process scheduling, memory management, filesystem, device drivers |
| **System libraries** | Standard API for programs (libc, system calls) |
| **System tools** | CLI utilities for managing the system (`ps`, `mount`, `ip`, ...) |

---

## Kernel Space vs User Space

| | Kernel space | User space |
|---|---|---|
| **Access** | Full hardware access | Restricted, isolated |
| **What runs here** | Kernel, drivers, core subsystems | Applications, shells, services |
| **Failure impact** | Kernel panic (full crash) | Only that process crashes |

Programs in user space communicate with the kernel via **system calls** (syscalls):

| Syscall category | Examples |
|-----------------|----------|
| Process management | `fork()`, `exec()`, `exit()` |
| File operations | `open()`, `read()`, `write()`, `close()` |
| Networking | `socket()`, `connect()`, `bind()` |
| Memory | `mmap()`, `brk()` |

---

## Linux Distributions

### Debian family

| Distro | Character |
|--------|-----------|
| **Debian** | Rock-solid stability, strict free software policy, long support cycles |
| **Ubuntu** | Regular releases, wide hardware support, large community |
| **Linux Mint** | End-user focused, conservative updates |

Package manager: **`apt`**

```bash
sudo apt update          # refresh package index
sudo apt upgrade         # upgrade installed packages
sudo apt install <pkg>   # install a package
sudo apt remove <pkg>    # remove (keep config)
sudo apt purge <pkg>     # remove + delete config
```

### Red Hat family

| Distro | Character |
|--------|-----------|
| **RHEL** | Enterprise standard, long support, hardware certification |
| **Fedora** | Bleeding edge, testing ground for RHEL |
| **CentOS / Rocky / Alma** | Free RHEL-compatible rebuilds |

Package manager: **`dnf`** (formerly `yum`)

```bash
sudo dnf update
sudo dnf install <pkg>
sudo dnf remove <pkg>
```

---

## Filesystem Hierarchy

```
/                   Root -- everything starts here
├── bin/            Essential binaries (ls, cp, cat)
├── boot/           Bootloader + kernel image
├── dev/            Device files (/dev/sda, /dev/null)
├── etc/            System-wide configuration files
├── home/           User home directories
├── lib/            Shared libraries
├── proc/           Virtual FS -- running process info
├── sys/            Virtual FS -- kernel/hardware info
├── tmp/            Temporary files (cleared on reboot)
├── usr/            User programs, libraries, docs
│   ├── bin/        User binaries
│   ├── lib/        User libraries
│   └── share/      Shared data (man pages, etc.)
└── var/            Variable data
    ├── log/        Log files
    ├── tmp/        Persistent temp files
    └── www/        Web server content (convention)
```

!!! tip "Key directories to remember"
    - `/etc` -- configuration lives here
    - `/var/log` -- check here when debugging
    - `/home` -- user data
    - `/proc` and `/sys` -- virtual, kernel-generated

---

## LVM -- Logical Volume Management

LVM adds a flexible abstraction layer over physical disks:

```
Physical Volumes (PV)  →  Volume Groups (VG)  →  Logical Volumes (LV)
   /dev/sdb1                  data_vg               data_lv
   /dev/sdc1                                        logs_lv
```

**Advantages:** resize volumes live, span multiple disks, create snapshots, migrate data.

```bash
# Create physical volume
pvcreate /dev/sdb1

# Create volume group from one or more PVs
vgcreate data_vg /dev/sdb1

# Create logical volume
lvcreate -L 100G -n data_lv data_vg

# Format and mount
mkfs.ext4 /dev/data_vg/data_lv
mount /dev/data_vg/data_lv /mnt/data
```

---

## Users and Groups

Linux is a **multi-user** OS. Every process runs as a user; every file is owned by a user + group.

### Key files

| File | Purpose |
|------|---------|
| `/etc/passwd` | User accounts: `username:x:UID:GID:comment:home:shell` |
| `/etc/shadow` | Hashed passwords (root-readable only) |
| `/etc/group` | Group definitions: `groupname:x:GID:members` |

UID conventions: `0` = root, `1-999` = system/service accounts, `1000+` = regular users.

### User management

```bash
# Create user with home dir and bash shell
useradd -m -s /bin/bash john

# Set password
passwd john

# Add user to supplementary group (without removing existing groups)
usermod -aG docker john

# View user info
id john          # uid, gid, all groups
whoami           # current username

# Delete user and home directory
userdel -r john
```

### Group management

```bash
groupadd developers                # create group
usermod -aG developers john        # add user to group
gpasswd -d john developers         # remove user from group
groupdel developers                # delete group
groups john                        # list user's groups
```

!!! warning
    After changing groups, the user must **log out and back in** (or run `newgrp groupname`) for changes to take effect.

---

## File Permissions

Every file has three permission sets: **owner**, **group**, **others**.

```
  owner   group   others
   rwx     rwx     rwx
```

| Permission | On a file | On a directory |
|-----------|-----------|----------------|
| `r` (4) | Read contents | List contents (`ls`) |
| `w` (2) | Modify contents | Create/delete files inside |
| `x` (1) | Execute as program | Enter directory (`cd`) |

### Reading `ls -l`

```
-rwxr-xr-- 1 john developers 4096 Mar 05 10:00 deploy.sh
│└─┘└─┘└─┘
│ │   │  └── others: r-- (4) = read only
│ │   └───── group:  r-x (5) = read + execute
│ └───────── owner:  rwx (7) = full access
└─────────── type: - = file, d = dir, l = symlink
```

### `chmod` -- change permissions

```bash
# Symbolic
chmod u+x script.sh           # add execute for owner
chmod g-w file.txt             # remove write for group
chmod o= file.txt              # remove all for others

# Numeric (octal)
chmod 755 script.sh            # rwxr-xr-x
chmod 644 config.yaml          # rw-r--r--
chmod 700 ~/.ssh               # rwx------
chmod 600 ~/.ssh/id_rsa        # rw-------
```

### Common patterns

| Octal | Symbolic | Typical use |
|-------|----------|-------------|
| `755` | rwxr-xr-x | Executables, public directories |
| `644` | rw-r--r-- | Config files, documents |
| `700` | rwx------ | Private directories (~/.ssh) |
| `600` | rw------- | Private keys, secrets |

### `chown` / `chgrp` -- change ownership

```bash
chown john file.txt              # change owner
chown john:developers file.txt   # change owner + group
chown -R john:developers /opt/   # recursive
```

---

## System Monitoring

```bash
# Processes
top                   # live process viewer (q to quit)
htop                  # better top (install: apt install htop)
ps aux                # snapshot of all processes
ps aux | grep nginx   # find specific process

# Memory
free -h               # RAM usage (human-readable)
vmstat 1              # virtual memory stats, refresh every 1s

# Disk
df -h                 # filesystem disk usage
du -sh /var/log       # size of a specific directory

# Network
netstat -tuln         # listening ports (or: ss -tuln)
```

---

## Firewall (UFW)

UFW (Uncomplicated Firewall) -- simple frontend for iptables:

```bash
sudo ufw enable               # activate firewall
sudo ufw status               # check rules
sudo ufw allow ssh             # allow SSH (port 22)
sudo ufw allow 80/tcp          # allow HTTP
sudo ufw allow from 10.0.0.0/24  # allow a subnet
sudo ufw deny 3306             # block MySQL port
```

---

## Quick Reference

```bash
# System info
uname -a              # kernel version, architecture
lsb_release -a        # distro info
hostname              # machine name

# Package management (Debian/Ubuntu)
sudo apt update && sudo apt upgrade -y
sudo apt install <package>

# Users
id                    # current user info
useradd -m -s /bin/bash <name>
usermod -aG <group> <user>

# Permissions
chmod 755 dir/
chmod 644 file
chown user:group path

# Monitoring
top / htop / ps aux
free -h / df -h / du -sh

# Firewall
sudo ufw enable
sudo ufw allow <port>/tcp
```

---

[Previous: 01 -- DevOps Intro](01-devops-intro.md){ .md-button }
