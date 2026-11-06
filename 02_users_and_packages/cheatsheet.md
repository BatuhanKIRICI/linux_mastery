# Cheatsheet – Users, sudo, Packages

```bash
# Users
whoami
id                                  # uid, gid, groups
id someuser
getent passwd someuser              # local + LDAP/AD users
awk -F: '$3 == 0' /etc/passwd       # all UID 0 accounts
grep -E '^(UID_MIN|UID_MAX|SYS_UID_MIN|SYS_UID_MAX)' /etc/login.defs

# User management
sudo useradd -m -s /bin/bash testuser
sudo passwd testuser
sudo usermod -aG sudo testuser      # ALWAYS -a with -G
su - testuser
sudo userdel -r testuser

# sudo
sudo whoami
sudo -l                             # my sudo rights
sudo -k                             # drop cached credentials
sudo visudo                         # the only safe way to edit sudoers
sudo grep sudo /var/log/auth.log | tail

# apt
sudo apt update
apt list --upgradable
sudo apt upgrade
apt search pkg ; apt show pkg ; apt policy pkg
sudo apt install pkg
sudo apt remove pkg ; sudo apt purge pkg ; sudo apt autoremove
dpkg -L pkg                         # files of a package
dpkg -S /path/to/file               # which package owns a file
dpkg -l | grep '^rc'                # removed, configs left
uname -r                            # running kernel

# dnf
dnf check-update ; echo $?          # 0 none, 100 updates, 1 error
dnf upgrade -y
dnf search pkg ; dnf info pkg ; dnf repolist
dnf install -y pkg ; dnf remove -y pkg
rpm -ql pkg ; rpm -qf /path/to/file
dnf history ; dnf history info ID ; dnf history undo ID
```
