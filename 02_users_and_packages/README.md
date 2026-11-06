# 02 – Users, sudo and Package Management

Course sections 39–45 (Mastering Linux: The Comprehensive Guide) + extra notes.

## 1. Who am I?
- `whoami` – current user name
- `id` – UID, primary GID and all groups
- Linux identifies users by **UID**, not by name. The name is for humans.
- Membership in the `sudo` group is what allows `sudo` on Ubuntu.

## 2. User database
`/etc/passwd` – one line per local user, 7 fields separated by `:`
```
user:x:1000:1000:Full Name,,,:/home/user:/bin/bash
```
| Field | Meaning |
|---|---|
| `user` | username |
| `x` | password is stored in `/etc/shadow` |
| `1000` | UID |
| `1000` | primary GID |
| `Full Name` | comment (GECOS) |
| `/home/user` | home directory |
| `/bin/bash` | login shell (source of `$SHELL`) |

- `/etc/passwd` lists **local** users only. Users can also come from LDAP / Active Directory → `getent passwd` queries everything the system knows (via NSS).

## 3. Account types (Ubuntu defaults)
| Type | UID | Example |
|---|---|---|
| Superuser | `0` | `root` |
| System account | `1–999` | `www-data`, `postgres`, `syslog` |
| Regular user | `1000+` | human users |

- Ranges are distro defaults, defined in `/etc/login.defs` (`UID_MIN`, `SYS_UID_MAX`, ...).
- What makes an account root is **UID 0**, not the name. Security check: `awk -F: '$3 == 0' /etc/passwd` → anything other than `root` is a red flag.
- Services run under their own accounts → **principle of least privilege**.
- `/usr/sbin/nologin` blocks interactive logins; processes can still run under that account.

## 4. sudo
- Runs a command as another user (default: root). Asks for **your own** password.
- On Ubuntu the root account is locked by default; privilege escalation goes through `sudo`.
- `sudo -k` – forget cached credentials
- `sudo -l` – what am I allowed to run?
- Rule in `/etc/sudoers`: `%sudo ALL=(ALL:ALL) ALL` → `%` = group
- **Never edit `/etc/sudoers` directly** → `sudo visudo` (syntax check before saving)
- Every sudo use is logged in `/var/log/auth.log`

## 5. Troubleshooting sudo (lab)
```bash
sudo useradd -m -s /bin/bash testuser   # -m home dir, -s login shell
sudo passwd testuser
su - testuser                           # switch user, full login env
sudo whoami                             # → "testuser is not in the sudoers file"
exit
sudo usermod -aG sudo testuser          # -a = append (keep other groups!)
su - testuser && sudo whoami            # → root
exit
sudo userdel -r testuser                # -r removes home dir
```
- `usermod -G` **without `-a`** removes the user from all other supplementary groups.
- Group changes apply only to **new sessions** → log out / log in.
- `userdel: mail spool not found` is a harmless warning.

| Symptom | Likely cause |
|---|---|
| "not in the sudoers file" | user not in `sudo` group |
| added to group, still fails | session not refreshed |
| password rejected | typing root's password instead of your own |
| sudo completely broken | sudoers edited without `visudo` → `pkexec visudo` / recovery mode |

## 6. Package management concepts
- **Package**: program files + install metadata + dependency list (`.deb` / `.rpm`)
- **Repository**: server that provides packages
- **Dependency**: other packages required; the package manager resolves them

## 7. apt (Ubuntu / Debian)
- `apt update` – refreshes the **package index** only, upgrades nothing
- `apt upgrade` – upgrades installed packages, never removes packages
- `apt full-upgrade` – may remove packages to resolve dependencies (use with care)
- Order: always `update` → `upgrade`
- `apt policy <pkg>` – installed version, candidate version, source repo
- Repos: `/etc/apt/sources.list.d/`
- `apt` for humans, `apt-get` for scripts. In automation rely on **exit codes**, not on parsing output.
- `apt` resolves and downloads; `dpkg` does the actual install.

| Command | Removes |
|---|---|
| `apt remove` | program, **keeps** config files |
| `apt purge` | program + config files |
| `apt autoremove` | auto-installed dependencies no longer needed |

`dpkg -l` status letters: 1st = desired state, 2nd = current state
- `ii` – install / installed
- `rc` – remove requested / only config files left

Cleaning leftover configs (old kernels, old drivers):
```bash
dpkg -l | awk '/^rc/ {print $2}'                 # preview
sudo apt purge $(dpkg -l | awk '/^rc/ {print $2}')
```
`autoremove` removes old kernels but does not purge them → `rc` entries pile up over time.

## 8. dnf (RHEL family: CentOS Stream, AlmaLinux, Rocky)
Practised in a disposable container: `docker run -it --rm almalinux:9 bash`
- No separate index refresh step; metadata is refreshed automatically.
- `dnf check-update` exit codes: `0` no updates, `100` updates available, `1` error.
- `dnf history` / `dnf history info <ID>` / `dnf history undo <ID>` – undo is not always possible (old versions may be gone from repos).
- No `purge`. Config handling depends on RPM metadata (`%config`, `%config(noreplace)`); modified configs may be saved as `.rpmsave`, new defaults as `.rpmnew`.
- `--rm` deletes the container, not the image → `docker rmi almalinux:9`.

| Task | apt | dnf |
|---|---|---|
| refresh index | `apt update` | automatic |
| list updates | `apt list --upgradable` | `dnf check-update` |
| upgrade | `apt upgrade` | `dnf upgrade` |
| search / info | `apt search` / `apt show` | `dnf search` / `dnf info` |
| install / remove | `apt install` / `apt remove` | `dnf install` / `dnf remove` |
| remove incl. config | `apt purge` | – |
| unused deps | `apt autoremove` | `dnf autoremove` |
| files of a package | `dpkg -L` | `rpm -ql` |
| package owning a file | `dpkg -S` | `rpm -qf` |
| repo config | `/etc/apt/sources.list.d/` | `/etc/yum.repos.d/` |

## Key terms
UID / GID · supplementary group · superuser · system account · principle of least privilege · privilege escalation · sudoers · package index · repository · dependency · config files · command substitution `$( )` · kernel release
