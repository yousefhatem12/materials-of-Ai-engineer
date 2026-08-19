# Ubuntu Server – Basic Command Cheat Sheet

A practical reference for managing an Ubuntu server: files, users, packages, services, networking, and logs.

---

## 1. Getting Around & Files

```bash
pwd                     # show current directory
ls -la                  # list files (with hidden files, details)
cd /path/to/dir         # change directory
cp file1 file2          # copy
mv file1 file2          # move/rename
rm file                 # delete file
rm -rf folder/          # delete folder recursively (be careful!)
mkdir newfolder         # create directory
find / -name "*.log"    # search for files
grep -r "text" /path    # search inside files
```

## 2. Package Management (APT)

```bash
sudo apt update                # refresh package lists
sudo apt upgrade               # upgrade installed packages
sudo apt install nginx         # install a package
sudo apt remove nginx          # uninstall
sudo apt autoremove            # clean unused dependencies
apt list --installed           # list installed packages
```

## 3. Users & Permissions

```bash
whoami                         # current user
sudo adduser john              # create user
sudo usermod -aG sudo john     # give sudo rights
su - john                      # switch user
chmod 755 file                 # change permissions
chown john:john file           # change owner
ls -l                          # view permissions
```

## 4. Process & Resource Management

```bash
top                     # live process/resource view
htop                    # nicer version (install with apt)
ps aux                  # list all processes
kill -9 PID             # force-kill a process
df -h                   # disk space usage
du -sh folder/          # size of a folder
free -h                 # RAM usage
uptime                  # system load & uptime
```

## 5. Services (systemd)

```bash
sudo systemctl status nginx     # check service status
sudo systemctl start nginx      # start service
sudo systemctl stop nginx       # stop service
sudo systemctl restart nginx    # restart service
sudo systemctl enable nginx     # start on boot
sudo systemctl disable nginx    # don't start on boot
journalctl -u nginx -f          # live logs for a service
```

## 6. Networking

```bash
ip a                        # show network interfaces/IPs
ping google.com             # test connectivity
curl -I https://example.com # check HTTP response headers
ss -tulpn                   # show open ports (modern netstat)
ssh user@server_ip          # connect to a remote server
scp file user@ip:/path      # copy file to remote server
```

## 7. Firewall (UFW)

```bash
sudo ufw status
sudo ufw allow 22           # allow SSH
sudo ufw allow 80,443/tcp   # allow HTTP/HTTPS
sudo ufw enable
sudo ufw deny 8080
```

## 8. Logs

```bash
tail -f /var/log/syslog         # live system log
tail -f /var/log/auth.log       # login/auth attempts
journalctl -xe                  # recent systemd logs with details
```

## 9. File Editing (no GUI)

```bash
nano file.conf      # simple editor
vim file.conf        # more powerful, steeper learning curve
```

## 10. Useful Combos

```bash
sudo apt update && sudo apt upgrade -y     # update + upgrade in one go
history | tail -20                         # see recent commands
crontab -e                                 # edit scheduled tasks
df -h && free -h && uptime                 # quick health check
```

---

## Suggested First-Setup Workflow

1. Update packages: `sudo apt update && sudo apt upgrade -y`
2. Create a non-root user with sudo access
3. Set up SSH key-based access
4. Configure the UFW firewall
5. Install what you need (nginx, docker, etc.)
6. Monitor with `systemctl`, `journalctl`, and `htop`
