# Linux Commands Cheatsheet

## Navigation & Files

```bash
pwd                        # print working directory
ls -la                     # list all files with details
cd /path/to/dir            # change directory
cd ~                       # go to home
cd -                       # go to previous dir

mkdir -p dir/subdir        # create nested directories
rmdir dir                  # remove empty dir
rm file                    # remove file
rm -r dir                  # remove dir recursively
cp src dst                 # copy
cp -r srcdir dstdir        # copy directory
mv old new                 # move or rename
```

## Viewing & Editing Files

```bash
cat file                   # print entire file
less file                  # paginated viewer (q to quit)
head -n 20 file            # first 20 lines
tail -n 20 file            # last 20 lines
tail -f /var/log/syslog    # follow log in real time

nano file                  # simple editor
vim file                   # powerful editor
```

## Searching

```bash
find /path -name "*.log"              # find files by name
find /path -type d -name "config"     # find directories
find /path -mtime -7                  # modified in last 7 days

grep "pattern" file                   # search in file
grep -r "pattern" /path/              # recursive search
grep -i "pattern" file                # case-insensitive
grep -n "pattern" file                # show line numbers
```

## System Info

```bash
uname -a                   # kernel, arch, hostname
lsb_release -a             # distro info
hostname                   # machine name
uptime                     # system uptime + load
whoami                     # current user
id                         # uid, gid, groups
```

## Process Management

```bash
ps aux                     # all processes
ps aux | grep nginx        # find process
top                        # live process monitor
htop                       # better top
kill PID                   # terminate process
kill -9 PID                # force kill
```

## Memory & Disk

```bash
free -h                    # RAM usage
df -h                      # disk usage per filesystem
du -sh /path               # size of directory
du -sh /* 2>/dev/null       # size of top-level dirs
```

## Networking

```bash
ip a                       # show IP addresses
ip r                       # show routing table
ss -tuln                   # listening ports
ping -c 4 host             # test connectivity
curl -I https://example.com  # HTTP headers
wget https://example.com/file  # download file
```

## Package Management (Debian/Ubuntu)

```bash
sudo apt update            # refresh package index
sudo apt upgrade           # upgrade all packages
sudo apt install pkg       # install
sudo apt remove pkg        # remove (keep config)
sudo apt purge pkg         # remove + config
apt list --installed       # list installed packages
apt search keyword         # search available packages
```

## Archives

```bash
tar -czf archive.tar.gz dir/   # create .tar.gz
tar -xzf archive.tar.gz        # extract .tar.gz
tar -tf archive.tar.gz          # list contents
zip -r archive.zip dir/        # create .zip
unzip archive.zip              # extract .zip
```

## Redirection & Pipes

```bash
cmd > file                 # stdout to file (overwrite)
cmd >> file                # stdout to file (append)
cmd 2> file                # stderr to file
cmd &> file                # both stdout+stderr
cmd1 | cmd2                # pipe stdout to next command
cmd1 | tee file | cmd2     # pipe + save copy to file
```

## Useful Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+C` | Kill current process |
| `Ctrl+Z` | Suspend process (resume with `fg`) |
| `Ctrl+D` | EOF / exit shell |
| `Ctrl+R` | Reverse search history |
| `Ctrl+L` | Clear screen |
| `Tab` | Autocomplete |
| `!!` | Repeat last command |
| `!$` | Last argument of previous command |
