# dekH command catalogue

This document lists the commands available in the current dekH 0.4.2
development tree. They belong to four distinct groups:

1. normal base-system commands;
2. shell built-ins;
3. recovery and diagnostic commands;
4. FreeBSD compatibility commands installed only in disposable test images.

The `bsd*` and `dyn*` commands are not installed in the release VM. They are
test fixtures created by `scripts/test-freebsd-libc.sh` and are documented
separately below so that they are not confused with native dekH commands.

## Shell and session control

| Command | Typical use | Description |
|---|---|---|
| `sh [FILE]` | `sh backup.sh` | Runs the interactive shell or a line-oriented shell script. |
| `help` | `help` | Prints the shell command and feature summary. |
| `man COMMAND` | `man ls` | Displays a compact manual page. `man -k` lists the available pages. |
| `cd [PATH]` | `cd /tmp` | Changes the working directory. With no argument, uses `$HOME`. |
| `export NAME=VALUE` | `export PATH=/bin` | Creates or changes an environment variable. |
| `unset NAME` | `unset TEMP` | Removes an environment variable. |
| `status` | `status` | Prints the previous command's exit status. |
| `exit [STATUS]` | `exit 0` | Exits the shell and, from a login shell, ends the session. |
| `logout [STATUS]` | `logout` | Alias of `exit`. |
| `jobs` | `jobs` | Lists jobs started in the background by this shell. |
| `fg [PID]` | `fg` | Brings the selected or most recent job to the foreground. |
| `wait [PID]` | `wait` | Waits for one job or all pending jobs. |
| `rescue` | `rescue` | Requests the authenticated root recovery console. |
| `NAME=VALUE` | `TEMP=/tmp` | Assigns an environment variable directly. |
| `COMMAND &` | `sleep 5 &` | Runs a command in the background. |

The shell supports pipelines of up to three stages, quoting and escaping,
`$NAME`, `${NAME}`, `$?`, and the redirections `<`, `>`, `>>`, `2>`, and
`2>&1`.

## Files and directories

| Command | Typical use | Description |
|---|---|---|
| `echo [TEXT...]` | `echo hello` | Writes its arguments separated by spaces and adds a newline. |
| `pwd` | `pwd` | Prints the current working directory. |
| `ls [-l] [PATH]` | `ls -l /etc` | Lists a directory or entry. `-l` includes type, mode, owner, and size. |
| `cat [FILE...]` | `cat /etc/hostname` | Copies files to standard output. With no file or `-`, reads standard input. |
| `cp [-r] SOURCE DESTINATION` | `cp a b` | Atomically copies one file. `-r` performs a transactional tree copy. |
| `mv SOURCE DESTINATION` | `mv old new` | Renames or moves a dekHFS entry. |
| `mkdir DIRECTORY` | `mkdir data` | Creates a directory. |
| `rmdir DIRECTORY` | `rmdir data` | Removes an empty directory. |
| `rm FILE` | `rm temporary` | Removes a regular file; it does not remove directories. |
| `touch FILE` | `touch new` | Creates an empty file if it does not already exist. |
| `chmod MODE PATH` | `chmod 600 secret` | Sets permissions using an octal mode. |
| `env [NAME]` | `env PATH` | Lists the environment or prints one variable. |
| `printenv [NAME]` | `printenv HOME` | Functional alias of `env`. |
| `edit FILE` | `edit notes.txt` | Opens the native line-oriented text editor. |

Editor commands are `a` to append, `i N` to insert before a line, `d N` to
delete a line, `p` to print, `w` to save, `q` to quit when unchanged, and
`q!` to discard changes. A single dot ends inserted text.

## Processes

| Command | Typical use | Description |
|---|---|---|
| `ps` | `ps` | Lists the PID, UID, state, and name of active processes. |
| `sleep SECONDS` | `sleep 2` | Suspends a task for 0 through 86400 seconds. |
| `kill PID` | `kill 12` | Requests termination of a process, subject to ownership and privilege checks. |

The kernel supports eight task contexts and a three-second user CPU budget per
process. Waiting for input, a pipe, or a timer does not count as active CPU
execution.

## Text utilities

These commands accept at most 65536 input bytes. A path of `-` means standard
input. Matching and ordering are byte-oriented; there is no regular-expression
or full locale engine.

| Command | Typical use | Description |
|---|---|---|
| `grep [-n] [-i] [--] TEXT [FILE]` | `grep -n root /etc/passwd` | Prints lines containing literal text. `-i` folds ASCII case. |
| `head [-n N] [FILE]` | `head -n 5 notes` | Prints the first lines; the default is ten. |
| `tail [-n N] [FILE]` | `tail -n 5 notes` | Prints the last lines; the default is ten. |
| `wc [-l|-w|-c] [FILE]` | `wc -l notes` | Counts lines, words, or bytes. With no option, prints all three. |
| `sort [-r] [FILE]` | `sort -r names` | Sorts lines by byte value; `-r` reverses the order. |
| `uniq [-c] [FILE]` | `uniq -c names` | Coalesces adjacent equal lines; `-c` prints their counts. |
| `tee [-a] FILE` | `command | tee output` | Copies standard input to standard output and a file. `-a` appends. |

## Network and downloads

| Command | Typical use | Description |
|---|---|---|
| `ifconfig` | `ifconfig` | Shows link state, IPv4 configuration, DNS, gateway, and Intel 82540EM counters. |
| `dhclient` | `dhclient` | Restarts DHCP configuration; root is required. |
| `nslookup HOST` | `nslookup example.com` | Resolves a name through DNS and prints its IPv4 address. |
| `ping [-c N] HOST` | `ping -c 3 gateway` | Sends ICMP echo requests and prints replies and a summary. The default count is four. |
| `fetch [-o FILE|-] URL` | `fetch -o page https://example.com/` | Downloads HTTP or HTTPS. HTTPS validates certificates, hostnames, and dates. File replacement is atomic and the response limit is 65536 bytes. |

## Accounts and identity

| Command | Typical use | Description |
|---|---|---|
| `whoami` | `whoami` | Prints the current account name. |
| `id` | `id` | Prints the current UID, account name, and GID. |
| `users` | `users` | Lists registered accounts. |
| `passwd [NAME]` | `passwd` | Changes the current password; root may select another account. |
| `useradd NAME` | `useradd alice` | Creates an account and home directory; root is required. |
| `userdel NAME` | `userdel alice` | Removes an account after system checks; root is required. |

The account database currently holds up to eight accounts. Run `sync` to make
changes persistent on an installed disk.

## Administration and system information

| Command | Typical use | Description |
|---|---|---|
| `sync` | `sync` | Writes a new verified dekHFS snapshot generation. |
| `install ada0` | `install ada0` | Installs dekH on the primary IDE disk. Use only a disposable target disk. |
| `shutdown -p now` | `shutdown -p now` | Synchronizes when necessary and requests VM power-off. |
| `reboot` | `reboot` | Synchronizes and restarts the machine. |
| `clear` | `clear` | Clears the display. |
| `uname [-s|-r|-m|-a]` | `uname -a` | Prints the system name, release, architecture, or all fields. |
| `mem` | `mem` | Shows protected-execution status, free frames, and open descriptions. |
| `mount` | `mount` | Reports whether `/` is volatile dekHFS or an `ada0` snapshot. Other mounts are unsupported. |
| `diskinfo` | `diskinfo` | Shows IDE disk detection and properties. |
| `keymap de|us` | `keymap de` | Selects the German or US keyboard map. |
| `df` | `df` | Shows entry use, stored bytes, the per-file limit, and persistence mode. |
| `hostname [NAME]` | `hostname dekh-lab` | Shows or changes the hostname and updates `/etc/hostname`. |
| `date` | `date` | Prints the hardware-clock date and time. |
| `uptime` | `uptime` | Prints seconds elapsed since boot. |

## Boot programs

| Path | Description |
|---|---|
| `/sbin/init` | Initial ring-3 process. It starts another login after a session ends. |
| `/bin/login` | Authenticates an account, sets `PATH`, `HOME`, `USER`, and starts `/bin/sh`. |

## Embedded FreeBSD reference program

| Command | Description |
|---|---|
| `bsdprobe` | A libc-free FreeBSD 15 AMD64 ELF used to verify the compatibility ABI, syscalls, files, environment, anonymous memory, and TLS. `--memory` runs its memory workload. |

## FreeBSD commands in the disposable v4 fixture

These are unchanged FreeBSD binaries or controlled compatibility fixtures.
They are not part of the release VM or the native dekH command set.

### Static FreeBSD fixtures

“Static” means that the executable contains the libc code it needs.

| Command | Description |
|---|---|
| `truebsd` | FreeBSD `true`; exits with status zero. |
| `falsebsd` | FreeBSD `false`; exits with status one. |
| `bsdecho` | Static FreeBSD `echo`. |
| `bsdcat` | Static FreeBSD `cat`. |
| `bsdls` | Static FreeBSD `ls`. |
| `bsdmkdir` | Static FreeBSD `mkdir`. |
| `bsdrm` | Static FreeBSD `rm`. |
| `bsddyn` | Small controlled PIE/`PT_INTERP` fixture that verifies handoff, stack layout, and auxiliary vectors. |

### Dynamically linked FreeBSD fixtures

These programs use the real FreeBSD `ld-elf.so.1` and shared libraries stored
in the disposable fixture.

| Command | Description |
|---|---|
| `dynecho` | Dynamically linked FreeBSD `echo`. |
| `dyncat` | Dynamically linked FreeBSD `cat`. |
| `dynls` | Dynamically linked FreeBSD `ls`, including `libutil` and `libtinfow`. |
| `dynmkdir` | Dynamically linked FreeBSD `mkdir`. |
| `dynrm` | Dynamically linked FreeBSD `rm`. |
| `dyncp` | Dynamically linked FreeBSD `cp`. |
| `dynmv` | Dynamically linked FreeBSD `mv`. |
| `dynpwd` | Dynamically linked FreeBSD `pwd`. |
| `dynsleep` | Dynamically linked FreeBSD `sleep`, exercising compatible `nanosleep`. |
| `dynprintf` | Dynamically linked FreeBSD `printf`. |

## Recovery console

`rescue` leaves the user shell and opens the authenticated kernel root console.
It provides direct administrative forms of many commands above and adds these
recovery-specific commands:

| Command | Description |
|---|---|
| `dmesg` | Prints a fixed summary of the boot, kernel, ABI, and disk state. |
| `stat PATH` | Prints the entry type, mode, UID, GID, and size. |
| `run PROGRAM [ARGUMENTS]` | Explicitly runs an existing ELF. A name without `/` is searched in `/bin`. |
| `halt` | Synchronizes when required and requests power-off. |
| `pkg` | Reports that package management is unavailable and installs nothing. |
| `man limitations` | Displays `/root/README` and the current system limitations. |

The recovery console also directly implements `help`, `mem`, `keymap`,
`clear`, `whoami`, `id`, `pwd`, `uname`, `hostname`, `date`, `uptime`,
`diskinfo`, `df`, `mount`, `sync`, `install`, `reboot`, `shutdown`, `echo`,
`ls`, `cd`, `cat`, `cp`, `mv`, `chmod`, `mkdir`, `touch`, `rm`, and `rmdir`.

## Development and diagnostic executables

These programs are seeded into development images to test the kernel. They
are not normal user tools, and several intentionally cause process faults.

| Command | Purpose |
|---|---|
| `hello` | Confirms execution of a C program in ring 3. |
| `fault` | Attempts a kernel-text write; only the process should be terminated. |
| `badptr` | Tests invalid pointers, sizes, descriptors, and unknown syscalls. |
| `privileged` | Attempts privileged `cli` from ring 3. |
| `spin` | Loops forever to test the three-second CPU limit. |
| `textwrite` | Attempts to modify read-only executable text. |
| `stackexec` | Attempts to execute code from the NX stack. |
| `bss` | Verifies zero-filled BSS and writable data. |
| `iotest` | Tests open/read offsets, descriptors, buffers, and ABI limits. |
| `ttyread` | Tests nonblocking terminal input. |
| `ucat` | Diagnostic implementation of the file/standard-input copy path used by `cat`. |
| `argtest` | Tests `argc`, `argv`, empty arguments, quoting, and limits. |
| `ucp` | Diagnostic implementation of the file/tree copy path used by `cp`. |
| `writetest` | Tests create, write, truncate, append, and access errors. |
| `copycheck A B` | Compares two files byte for byte. |
| `uexec PROGRAM [ARGS]` | Replaces the current process image through `exec`. |
| `exectest` | Tests exec validation, failure atomicity, and descriptor inheritance. |
| `execinherit` | Verifies inherited offsets and close-on-exec. |
| `execoom` | Tests exec rollback after frame exhaustion. |
| `execchain [N]` | Runs a chain of up to twenty image replacements. |
| `childtest` | Tests child status, isolation, faults, and resource recovery. |
| `uspawn PROGRAM [ARGS]` | Spawns a child, waits, and prints its status. |
| `waittest` | Tests blocking input, wakeup, and Ctrl-C interruption. |
| `depthtest` | Verifies the process-depth/context limit. |
| `cwdtest` | Tests working-directory inheritance and isolation across child and exec. |
| `budgettest` | Tests CPU-budget renewal around terminal waits. |
| `duptest` | Tests descriptor duplication, inheritance, rollback, and close-on-exec. |
| `streams` | Writes separate messages to standard output and standard error. |
| `pipetest` | Tests pipes, concurrency, blocking, preemption, and private memory. |
| `storagecheck` | Runs public large-file, boundary, and dekHFS persistence checks. |

## Important limits

- The base system supports at most 256 dekHFS entries.
- A dekHFS v4 file may be up to 2 MiB, backed by a shared 16 MiB pool.
- The editor, downloader, text utilities, and atomic replacement paths retain
  their documented 64 KiB operation limits.
- FreeBSD compatibility fixtures do not imply general FreeBSD package support.
- `pkg` and Vim are not available yet.
