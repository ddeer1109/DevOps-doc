# Linux Security: Users, Groups & Authorization

## 1. Core Concepts

Linux is a **multi-user** operating system. Security is built around three pillars:

| Pillar | Question it answers |
|--------|-------------------|
| **Identification** | Who are you? (username, UID) |
| **Authentication** | Prove it. (password, SSH key) |
| **Authorization** | What are you allowed to do? (permissions, ACLs) |

Every process runs as a specific user. Every file is owned by a user and a group. The kernel enforces access based on these ownerships.

---

## 2. Users

### Key files

| File | Purpose |
|------|---------|
| `/etc/passwd` | User account info (name, UID, GID, home, shell) |
| `/etc/shadow` | Hashed passwords (readable only by root) |
| `/etc/login.defs` | Defaults for user creation (UID ranges, password aging) |

### Anatomy of `/etc/passwd`

```
username:x:UID:GID:comment:home_directory:login_shell
```

```
john:x:1001:1001:John Doe:/home/john:/bin/bash
```

- `x` — password is stored in `/etc/shadow`
- UID 0 = root, UIDs 1–999 = system/service accounts, UIDs 1000+ = regular users

### User management commands

```bash
# Create a user with home directory and default shell
useradd -m -s /bin/bash john

# Create user with specific UID and group membership
useradd -m -u 1050 -G docker,sudo john

# Set or change password
passwd john

# Modify existing user (add to supplementary group without removing others)
usermod -aG docker john

# Lock / unlock account
usermod -L john     # lock
usermod -U john     # unlock

# Delete user and their home directory
userdel -r john

# View current user info
id                  # uid, gid, groups
whoami              # just the username
```

### System users (service accounts)

```bash
# Create a system user with no login shell and no home
useradd -r -s /usr/sbin/nologin nginx
```

System users run daemons (nginx, mysql, etc.) with minimal privileges — principle of least privilege.

---

## 3. Groups

Groups simplify permission management — assign permissions to a group once, then add users to it.

### Key file

| File | Purpose |
|------|---------|
| `/etc/group` | Group definitions (name, GID, member list) |

### Anatomy of `/etc/group`

```
groupname:x:GID:member1,member2
```

```
docker:x:998:john,jane
```

### Primary vs supplementary groups

- **Primary group** — assigned at user creation (stored in `/etc/passwd`). New files are owned by this group.
- **Supplementary groups** — additional group memberships (stored in `/etc/group`). Grant extra access.

```bash
# Check a user's groups
groups john
id john

# Create a group
groupadd developers

# Add user to supplementary group
usermod -aG developers john

# Remove user from a group (use gpasswd)
gpasswd -d john developers

# Delete a group
groupdel developers

# Change a user's primary group
usermod -g developers john
```

> After changing groups, the user must log out and back in (or use `newgrp`) for changes to take effect.

---

## 4. File Permissions (DAC — Discretionary Access Control)

### Permission model

Every file/directory has three permission sets:

```
  owner   group   others
   rwx     rwx     rwx
```

| Symbol | File | Directory |
|--------|------|-----------|
| `r` (4) | Read contents | List contents (`ls`) |
| `w` (2) | Modify contents | Create/delete files inside |
| `x` (1) | Execute as program | Enter directory (`cd`) |

### Reading `ls -l` output

```
-rwxr-xr-- 1 john developers 4096 Mar 05 10:00 deploy.sh
│└┬┘└┬┘└┬┘       │    │
│ │   │   │       │    └── group owner
│ │   │   │       └─────── user owner
│ │   │   └── others: r-- (read only)
│ │   └────── group:  r-x (read + execute)
│ └────────── owner:  rwx (full access)
└──────────── file type: - = regular file, d = directory, l = symlink
```

### Changing permissions — `chmod`

```bash
# Symbolic mode
chmod u+x script.sh          # add execute for owner
chmod g-w file.txt            # remove write for group
chmod o= file.txt             # remove all permissions for others
chmod u=rwx,g=rx,o=r file.txt # set explicitly

# Numeric (octal) mode
chmod 755 script.sh           # rwxr-xr-x
chmod 644 config.yaml         # rw-r--r--
chmod 700 ~/.ssh              # rwx------
chmod 600 ~/.ssh/id_rsa       # rw-------
```

### Common permission patterns

| Octal | Symbolic | Typical use |
|-------|----------|-------------|
| `755` | rwxr-xr-x | Executables, public directories |
| `644` | rw-r--r-- | Config files, documents |
| `700` | rwx------ | Private directories (`~/.ssh`) |
| `600` | rw------- | Private keys, secrets |
| `750` | rwxr-x--- | Shared project directories |

### Changing ownership — `chown` and `chgrp`

```bash
chown john file.txt              # change owner
chown john:developers file.txt   # change owner and group
chown :developers file.txt       # change group only
chgrp developers file.txt        # change group only (alternative)

# Recursive (apply to directory and all contents)
chown -R john:developers /opt/app/
```

---

## 5. Special Permissions

### SUID (Set User ID) — `4xxx`

When set on an executable, it runs as the **file owner** (not the calling user).

```bash
chmod u+s /usr/bin/passwd
# or
chmod 4755 /usr/bin/passwd

ls -l /usr/bin/passwd
# -rwsr-xr-x 1 root root ...
#    ^ 's' instead of 'x' = SUID
```

This is why normal users can change their own password — `passwd` runs as root.

### SGID (Set Group ID) — `2xxx`

- On a **file**: runs with the file's group.
- On a **directory**: new files inherit the directory's group (instead of the creator's primary group). Essential for shared project folders.

```bash
chmod g+s /opt/project/
# or
chmod 2775 /opt/project/

# Now all files created in /opt/project/ inherit the directory's group
```

### Sticky Bit — `1xxx`

On a directory: only the file **owner** (or root) can delete/rename files, even if others have write access.

```bash
chmod +t /tmp
# or
chmod 1777 /tmp

ls -ld /tmp
# drwxrwxrwt ...
#          ^ 't' = sticky bit
```

This is why users can't delete each other's files in `/tmp`.

---

## 6. sudo — Delegated Privilege Escalation

### How it works

`sudo` lets authorized users run commands **as another user** (default: root) without sharing the root password.

### Configuration: `/etc/sudoers`

Always edit with `visudo` (validates syntax before saving).

```bash
# User privilege lines
john    ALL=(ALL:ALL) ALL          # john can run any command as any user
jane    ALL=(ALL) /usr/bin/systemctl  # jane can only run systemctl

# Group-based (% prefix)
%developers ALL=(ALL) ALL          # all members of "developers" group
%ops        ALL=(ALL) NOPASSWD: ALL  # ops group: no password required

# Command aliases
Cmnd_Alias SERVICES = /usr/bin/systemctl, /usr/sbin/service
%devs ALL=(ALL) SERVICES
```

### sudo best practices

```bash
sudo command               # run single command as root
sudo -u postgres psql      # run as a specific user
sudo -i                    # root interactive shell (use sparingly)
sudo -l                    # list what you're allowed to run
```

> Prefer granting specific commands over blanket `ALL`. Audit `/var/log/auth.log` for sudo usage.

---

## 7. PAM (Pluggable Authentication Modules)

PAM is the framework that handles authentication for Linux services (login, ssh, sudo, etc.).

### Config location

```
/etc/pam.d/          # per-service config files
/etc/pam.d/common-*  # shared defaults (Debian/Ubuntu)
```

### Module types

| Type | Purpose |
|------|---------|
| `auth` | Verify identity (password, 2FA) |
| `account` | Access control (account expiry, time restrictions) |
| `password` | Password change rules (complexity, history) |
| `session` | Setup/teardown (mount home, set limits) |

### Practical examples

```bash
# Enforce password complexity (in /etc/pam.d/common-password)
password requisite pam_pwquality.so retry=3 minlen=12 difok=3

# Restrict SSH login to a specific group (in /etc/pam.d/sshd)
auth required pam_succeed_if.so user ingroup sshusers

# Account lockout after failed attempts
auth required pam_tally2.so deny=5 unlock_time=900
```

---

## 8. ACLs (Access Control Lists) — Fine-Grained Permissions

Standard permissions only cover owner/group/others. ACLs allow per-user or per-group rules on individual files.

```bash
# Check if filesystem supports ACLs
mount | grep acl

# View ACLs
getfacl /opt/project/config.yaml

# Grant read access to a specific user
setfacl -m u:jane:r /opt/project/config.yaml

# Grant read+write to a specific group
setfacl -m g:auditors:rw /var/log/app.log

# Set a default ACL on a directory (inherited by new files)
setfacl -d -m g:developers:rwx /opt/project/

# Remove an ACL entry
setfacl -x u:jane /opt/project/config.yaml

# Remove all ACLs
setfacl -b /opt/project/config.yaml
```

A `+` at the end of `ls -l` output indicates ACLs are set:

```
-rw-r--r--+ 1 john developers 4096 Mar 05 config.yaml
```

---

## 9. Security Hardening Checklist

- [ ] Disable root SSH login (`PermitRootLogin no` in `/etc/ssh/sshd_config`)
- [ ] Use SSH keys instead of passwords (`PasswordAuthentication no`)
- [ ] Enforce strong passwords via PAM (`pam_pwquality`)
- [ ] Remove unused user accounts (`userdel -r`)
- [ ] Audit sudo access regularly (`sudo -l`, review `/etc/sudoers.d/`)
- [ ] Set `umask 027` in `/etc/profile` for restrictive default permissions
- [ ] Apply principle of least privilege — no blanket `sudo ALL`
- [ ] Use groups for access management, not per-user permissions
- [ ] Set sticky bit on shared directories (`chmod +t`)
- [ ] Use SGID on project directories for consistent group ownership
- [ ] Review `/var/log/auth.log` for failed login attempts
- [ ] Set password expiry policies (`chage -M 90 username`)

---

## Quick Reference

```bash
# Who am I?
id                   # uid, gid, all groups
whoami               # current username
w                    # who is logged in and what they're doing

# User/group info
cat /etc/passwd      # all users
cat /etc/group       # all groups
getent passwd john   # lookup specific user
getent group docker  # lookup specific group

# Permission shortcuts
chmod 755 dir/       # standard public directory
chmod 700 private/   # owner-only directory
chmod 644 file.txt   # standard readable file
chmod 600 secret.key # owner-only file

# Ownership
chown -R user:group /path/

# Find files with special permissions
find / -perm -4000 -type f 2>/dev/null  # SUID files
find / -perm -2000 -type f 2>/dev/null  # SGID files
find / -perm -1000 -type d 2>/dev/null  # sticky bit dirs

# Password management
passwd username      # set password
chage -l username    # view password aging info
chage -M 90 user     # max password age: 90 days
```
