# Linux and Bash Learning Log

## Environment and evidence

These exercises were completed interactively in the RHEL VM under a regular user account. The practice directory was `~/labs/linux-basics`. Results below are observations confirmed during the lab, not copied production logs. Raw screenshots and terminal transcripts were not retained.

## Files, directories, and metadata

| Commands / syntax practiced | What I learned |
|---|---|
| `whoami`, `id`, `pwd` | Effective username, UID/GID/group membership, and current directory. |
| `mkdir -p ~/labs/linux-basics` | Create a directory and missing parents; `~` expands to the user's home. |
| `cd ..`, `cd linux-basics` | Parent-directory navigation and relative versus absolute paths. |
| `printf 'My first Linux lab\n' > notes.txt` | Create/overwrite a file with normal output; `\n` adds a newline. |
| `printf 'Practicing file permissions\n' >> notes.txt` | Append while retaining the existing contents. |
| `cat notes.txt` | Read file contents. |
| `ls -l`, `stat private/test.txt` | Interpret permissions, owning user/group, link count, byte size, and timestamps. |
| `cp notes.txt notes-copy.txt`, `mv notes-copy.txt practice.txt` | Copy a file and rename the copy. Source comes before destination. |
| `diff notes.txt practice.txt` | Identical content gives no output; modifying the copy produces a difference. |
| `ls -a`, `ls -A` | Both include dotfiles; only `-a` also includes `.` and `..`. Hidden is a naming convention, not an access control. |
| `ls --help`, `man ls` | Read command help and search a manual with `/`; exit the viewer with `q`. |

`stat` change time concerns metadata changes, not creation time. Access-time reporting is affected by filesystem mount/update rules.

## Permissions

I set `notes.txt` to `600` and verified `-rw-------`. Numeric permissions use read=4, write=2, execute=1 for owner/group/others.

For the `private` directory, I tested:

```bash
mkdir private
chmod 700 private
chmod 600 private
cd private
```

Entering the directory failed after removing execute permission. Restoring `700` allowed entry. Back in the parent directory, `chmod u-w private` prevented `touch private/test.txt`; `chmod u+w private` restored file creation.

- Directory read permits listing entry names.
- Directory execute permits traversal/lookup.
- Directory write, normally together with execute, permits changing entries. Permissions, ownership, ACLs, SELinux, and other controls can also affect access.
- `ls -ld private` inspects the directory itself rather than listing its contents.
- `touch` created a zero-byte practice file; on an existing file it updates timestamps without clearing contents.

## Searches, streams, and exit status

```bash
grep -n 'Practicing' notes.txt practice.txt
cat practice.txt | grep -n 'copy'
wc -l practice.txt
grep 'copy' practice.txt | wc -l
find . -type f -name '*.txt'
find . -type f -empty
find . -type f -perm 600
find . -type f -name '*.txt' > file-list.log
ls missing-file 2> errors.log
```

`grep -n` adds line numbers; `|` sends one command's standard output to the next; `wc -l` counts newlines. `find` searches recursively from `.`, and `-type f` selects regular files. Quotes around `'*.txt'` let `find`, rather than the shell, interpret the pattern. `-perm 600` matches exact permission bits. The search result file uses `.log` so it does not match the `.txt` search.

I separated normal output (`>`) from error output (`2>`), read the captured error, and inspected `$?` immediately after successful and failed commands. `ls` returned 0 for the existing file and 2 for the missing path. Nonzero exit meanings vary by command.

`&&` runs the next command on success; `||` runs it on failure. `[ -f "$labfile" ]` is a `test` expression, with required spaces. `[ -d "$labfile" ]` checks a directory. A directory failing `-f` is not necessarily a missing path.

## Bash script

I wrote and executed a file-checking script, added execute permission, changed the loop to accept arguments, and tested names containing spaces. See [script](../scripts/bash/check-files.sh) and [usage notes](../scripts/bash/README.md).

| Feature | Meaning |
|---|---|
| `#!/bin/bash` | Interpreter used when the script is executed directly. |
| `labfile="notes.txt"` | Assign a variable; no spaces around `=`. |
| `"$labfile"` | Expand its value while keeping it one argument. |
| `'$labfile'` | Preserve literal dollar-sign text. |
| `lab_location=$(pwd)` | Capture a command's output in a variable. |
| `printf '%s\n' ...` | Print a string in place of `%s`, then a newline. |
| `"$@"` | All supplied arguments, preserving each one separately. |
| `$#` | Number of positional arguments. |
| `$0` | Invocation name/path. |
| `$?` | Exit status of the latest completed command. |
| `if` / `elif` / `else` / `fi` | Conditional branches and their closing keyword. |
| `for` / `do` / `done` | Repeat a block for each argument. |
| `result=0`, `result=1`, `exit "$result"` | Accumulate failures across the loop and return a meaningful status. |

I confirmed exit 0 when all arguments were regular files and exit 1 for no arguments or at least one non-file argument. I also distinguished directories from other paths. Redirecting a diagnostic with `>&2` was introduced as a next exercise; it is not claimed as independently completed in the final script.

## Process and job control

```bash
ps -u james -o pid,ppid,stat,comm
sleep 300 &
jobs -l
fg %1
# Press Ctrl+Z to suspend the foreground job.
bg %1
kill -TERM %1
top
```

These job-number examples assume the disposable `sleep` is job 1. In practice I checked `jobs -l` and targeted only that practice job.

- `PID` is the process ID and `PPID` its parent's ID.
- In `Ss`, uppercase `S` is interruptible sleep and lowercase `s` identifies a session leader. Bash can sleep while waiting for input.
- `&` starts a background job; `jobs -l` includes PIDs.
- `fg` brings a job forward; Ctrl+Z stops it; `bg` resumes it in the background.
- `kill -TERM` sends a termination request. The sleep job was confirmed terminated.
- `top` refreshes resource usage; Shift+M sorts by memory, Shift+P by CPU, and `q` quits. `gnome-shell` appeared highest by memory during observation.

An RSS-sorted `ps` command was introduced but its output was not confirmed before lessons were paused. These labs do not claim production process tuning, service deployment, or incident-response experience.
