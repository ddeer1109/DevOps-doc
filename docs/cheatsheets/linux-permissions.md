# Linux Permissions Cheatsheet

## Permission Model

```
  owner   group   others        Octal
   rwx     rwx     rwx
   421     421     421          e.g. 755 = rwxr-xr-x
```

| Bit | File | Directory |
|-----|------|-----------|
| `r` (4) | Read contents | List files (`ls`) |
| `w` (2) | Modify contents | Create/delete files |
| `x` (1) | Execute | Enter (`cd`) |

## Common Patterns

| Octal | Symbolic | Use case |
|-------|----------|----------|
| `755` | rwxr-xr-x | Scripts, public dirs |
| `644` | rw-r--r-- | Config files, docs |
| `700` | rwx------ | Private dirs (`~/.ssh`) |
| `600` | rw------- | Private keys |
| `750` | rwxr-x--- | Shared project dirs |
| `775` | rwxrwxr-x | Group-writable dirs |

## chmod

```bash
# Symbolic
chmod u+x file        # add execute for owner
chmod g-w file        # remove write for group
chmod o= file         # clear all for others
chmod a+r file        # add read for all

# Octal
chmod 755 script.sh
chmod 644 config.yaml
chmod 600 secret.key
```

## chown / chgrp

```bash
chown john file.txt              # owner
chown john:devs file.txt         # owner + group
chown :devs file.txt             # group only
chgrp devs file.txt              # group only (alt)
chown -R john:devs /opt/app/     # recursive
```

## Special Permissions

| Bit | Octal | Effect |
|-----|-------|--------|
| **SUID** | `4xxx` | File executes as file owner (e.g. `/usr/bin/passwd`) |
| **SGID** | `2xxx` | File: runs as group. Dir: new files inherit group |
| **Sticky** | `1xxx` | Dir: only owner can delete their files (e.g. `/tmp`) |

```bash
chmod u+s /usr/bin/prog    # SUID
chmod g+s /opt/project/    # SGID on dir
chmod +t /tmp              # sticky bit
chmod 4755 prog            # SUID numerically
chmod 2775 dir/            # SGID numerically
chmod 1777 /tmp            # sticky numerically
```

## Users & Groups

```bash
# Info
id                         # current user
id john                    # specific user
groups john                # user's groups
cat /etc/passwd            # all users
cat /etc/group             # all groups

# Create / modify
useradd -m -s /bin/bash john
passwd john
usermod -aG docker john    # add to group
gpasswd -d john docker     # remove from group
userdel -r john            # delete + home

# Groups
groupadd devs
groupdel devs
```

## sudo

```bash
sudo command               # run as root
sudo -u postgres psql      # run as specific user
sudo -i                    # root shell
sudo -l                    # list allowed commands
visudo                     # edit /etc/sudoers safely
```

## ACLs (fine-grained)

```bash
getfacl file               # view ACLs
setfacl -m u:jane:r file   # grant read to user
setfacl -m g:devs:rw file  # grant rw to group
setfacl -d -m g:devs:rwx dir/  # default ACL for new files
setfacl -x u:jane file     # remove ACL entry
setfacl -b file             # remove all ACLs
```

`+` at end of `ls -l` output = ACLs are set.
