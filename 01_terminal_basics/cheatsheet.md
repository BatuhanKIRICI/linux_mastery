# Cheatsheet – Terminal Basics

```bash
# Output
echo "Home: $HOME"              # variables expanded
echo 'Home: $HOME'              # literal
echo -n "no newline"
printf "a\tb\n"                 # portable formatting

# Shell & variables
echo "$PATH" | tr ':' '\n'      # one PATH entry per line
ps -p $$ -o comm=               # currently running shell

# Navigation
pwd                             # print working directory
cd                              # go home (same as cd ~)
cd -                            # previous directory ($OLDPWD)
cd ..                           # parent directory

# Listing
ls -la                          # long format, include hidden
ls -lh                          # human-readable sizes
ls -ltr                         # oldest first, newest last (logs)
ls -ld /etc                     # the directory itself, not its content

# Files & timestamps
touch file.txt                  # create empty file / update timestamps
mv old.txt new.txt              # rename
stat file.txt                   # atime, mtime, ctime, btime
stat -c '%z' file.txt           # only ctime
head -c 5M /dev/zero > test.bin # create a 5 MiB test file

# Exit codes & chaining
echo $?                         # exit code of last command
cmd1 ; cmd2                     # always run cmd2
cmd1 && cmd2                    # cmd2 only if cmd1 succeeded
cmd1 || cmd2                    # cmd2 only if cmd1 failed

# Help
ls --help
man ls
man -k "list directory"
type -a ls
help cd
```
