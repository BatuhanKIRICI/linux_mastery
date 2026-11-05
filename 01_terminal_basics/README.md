# 01 – Terminal Basics

Course sections 30–37 (Mastering Linux: The Comprehensive Guide) + extra notes.

## 1. Quoting
- Double quotes `"..."` expand variables: `echo "$HOME"` → `/home/user`
- Single quotes `'...'` print everything literally: `echo '$HOME'` → `$HOME`
- Use single quotes for strings containing `$` (e.g. passwords, prices).

## 2. Environment variables
- `$HOME` – home directory
- `$PATH` – directories searched (left to right) when running a command
- `$SHELL` – the user's **default login shell**, not necessarily the shell running right now
- Running shell: `ps -p $$ -o comm=` (`$$` = PID of the current shell)

## 3. echo vs printf
- `echo -n` → no trailing newline
- `echo -e` → interprets `\t`, `\n`; behaviour differs between shells
- `printf` is portable and preferred in scripts (add `\n` yourself)

## 4. Hidden files
- Any file/directory whose name starts with `.` is hidden (dotfile).
- `ls -a` shows them, including `.` (current dir) and `..` (parent dir).
- Security: attackers often hide files as dotfiles → use `ls -la` when investigating.

## 5. Reading `ls -l`
| Field | Meaning |
|---|---|
| `-` | type: `-` file, `d` directory, `l` symlink, `c` character device |
| `rw-rw-r--` | permissions: owner / group / others |
| `1` | hard link count |
| `user group` | owner and group |
| `0` | size in bytes (`-h` → human-readable, binary units: 5.0M = 5 MiB) |
| date | last modification time (mtime) |

## 6. Timestamps (`stat`)
| Name | Updated when |
|---|---|
| atime | file is read (not always — `relatime`/`noatime` mount options) |
| mtime | **content** changes |
| ctime | **metadata** changes (permissions, owner, rename on ext4) — also on content change. NOT creation time |
| btime | file is created (Birth; filesystem-dependent) |

- `touch` on an existing file updates atime/mtime/ctime without changing content.
- Forensics: `touch` can fake mtime, but ctime cannot be set back by normal means.

## 7. Paths
- Absolute: starts with `/` → same target from anywhere.
- Relative: resolved from the current working directory (`.`, `..`).
- `~` is expanded by the shell to the absolute home path.
- In scripts, don't rely on the caller's working directory (use absolute paths or the script's own directory).

## 8. Exit codes and command chaining
- Every command returns an exit code: `0` = success, non-zero = failure.
- `$?` holds the exit code of the **last** command only.

| Operator | Runs the next command |
|---|---|
| `;` | always |
| `&&` | only if the previous one succeeded |
| `\|\|` | only if the previous one failed |
Caveats:
- `&&` doesn't protect you if `cd` succeeds into the **wrong** directory → verify the target first.
- `*` does not match hidden files.
- `A && B || C` is **not** a real if/else: if `B` fails, `C` also runs.

## 9. Getting help
| Tool | Use for |
|---|---|
| `cmd --help` | quick usage summary |
| `man cmd` | full manual (`/word` search, `n` next, `q` quit) |
| `man -k keyword` | search manuals when you don't know the command name |
| `type cmd` / `type -a cmd` | is it an alias, builtin or program? where is it? |
| `help cmd` | help for shell builtins (e.g. `cd`, `echo`) |

- `cd` must be a builtin: a separate process can't change the parent shell's directory.
- Man sections: `1` user commands, `5` file formats, `8` admin commands (`man 1 passwd` vs `man 5 passwd`).

## Key terms
absolute / relative path · working directory · hidden file (dotfile) · environment variable · login shell · PID · exit code / exit status · command chaining · shell builtin · alias · man page · character device · inode metadata
