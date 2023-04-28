# Linux 101
*So, why would you need Bash and that strange (UNIX/Linux/Mac-)terminal? Because at some point, you will simply need those 🤷 You might need to connect to machines that have no GUI. Maybe you have to do deployments on a UNIX-based system or in Linux containers. Maybe you project makes use of tons of Bash scripts. Or maybe you simply want to look cool 🤙*

## Basic UNIX/Linux concepts & commands

Paths, navigation & file manipulation:
- `cd /path/to/dir/`: change the current directory
- `ls /path/to/dir/`: list the contents of the path
- `ll /path/to/dir/` or `ls -lG`: list the contents of the path along with permissions, creation date etc.
- `pwd`: print the current directory
- `'/'`: denotes the root of the filesystem
- `'~'`: denotes the user's home directory, e.g. `cd ~/Downloads`
  - Linux: `/home/username/`
  - Mac: `/Users/username/`
- `'.'`: denotes the current directory
- `..`: denotes the parent directory
- `tree`: show file structure (use `-L` to set max depth)
- `mkdir dirname`: create new directory called `dirname`
- `touch filename`: create a new file called `filename`
- `mv /path/to/source /path/to/file2`: rename/move a file/directory
- `cp /path/to/source /path/to/file2`: copy a file
- `rm myfile`: remove file
- `cat file`: show file
- `head file`: show first 10 lines of a file
- `tail file`: show last 10 lines of a file
- `less file`: display contents of a file (lazily)
- `open .`: open the cwd (Mac only?)
- `sed s/old/new/ file`: replace (substitute) all occurrences of 'old' with 'new' in 'file'
- `pushd/popd/dirs`: manipulate the directory stack (=list of recently-visited directories)
  - `pushd`: push a directory on the directory stack
  - `popd`: pull a directory from the directory stack
  - `dirs`: list the directory stack

Running commands:
- `cmd1; cmd2`: run `cmd1` then `cmd2`
- `cmd1 && cmd2`: run `cmd2` if `cmd1` is successful
- `cmd1 || cmd2`: run `cmd2` if `cmd1` fails
- `cmd &`: run `cmd` in a subshell
- `!!`: run previous command
- `history`: show command history
- `CTRL+R`: reverse search in command history
- `man someCommand`: description of a command
- `tldr someCommand`: more succinct description of a command; to be installed
- `which cmd`: display path to *cmd* or the alias
- `cmd < file`: use a file as input for a command
- `cmd2 < cmd1`: use the output of cmd1 as input for cmd
- `cmd > file`: write stdout of a command to a file
- `cmd >> file`: append stdout of a command to a file
- `cmd 2> file`: write stderr of a command to a file
- `cmd &> file`: output all the output of a command to a file

Running scripts:
- `chmod +x myScript.sh` make script executable
- `./myScript.sh`: execute script
- `. myScript.sh` or `source myScript.sh`: source script
- Difference between sourcing and executing script:
  - Sourcing script runs the commands of the script in the current shell process; changes to the environment happen in the current shell. E.g. sourcing file defining variables, like `.bashrc`
  - Executing a script launches the execution in a new shell process.
- Variables:
  ```bash
  >> COMPANY=dataroots
  >> echo $COMPANY
  dataroots
  ```
- `export $SOME_VARIABLE`: adds the variable to the environment of subsequently executed commands
- `#!/bin/sh`, `#!/bin/bash`, ... denotes the default shell that the script should use (zsh, bash, dash, ...)

grep, pipe, curl, wget:
- `grep 'word' file` find "word" in file
- `grep -i 'word' file` find "word" in file (case-insensitive)
- `grep -R 'word'` find "word" in any file in the current directory and all subdirectories
- `grep -c 'word' file` count occurrences of "word" in file
- `grep \(w\|c\)ord` you can use regular expressions..
- `egrep (w|c)ord`: you don't have to escape the special characters with `egrep`..
- Pipes (`|`) let you use two or more commands such that output of one command is input for the next
- `cat file | grep "some string"`: find lines containing 'some string'
- `top | grep Python `: show Python processes
- `alias myAlias='cd /some/frequently/used/dir/`
- `alias ll='ls -lG'` 😇
- `curl https://dataroots.io/`: transfer files via HTTP, HTTPS, SCP, FTP, SFTP (print output)
- `wget https://dataroots.io/`: transfer files via HTTP, HTTPS, FTP (store output to file)

Process management:
- `top` show active processes and their resource consumption (real-time display)
- `ps` show snapshot of processes
- `kill pid` kill process with PID `pid`
- `pkill pname` kill process with process name `pname`
- `killall pname` kill all processes with names beginning with `pname`

Terminal substitution:
- =use the output of multiple commands in another command
- General structure: `cmd1 <(cmd2) <(cmd3)`
  `<(cmd)` converts the output of command `cmd` into a file-like object
- e.g. `diff <(cat file1) <(cat file2)`


## Shells and terminal multiplexers
### bashrc / zshrc:
Shell script run by zsh or bash whenever it's started interactively. It's a place where you can set aliases, variables etc.

### Terminal multiplexers (tmux, screen, ...)
Allows you to create/access multiple terminal sessions from the same window and detach from those sessions without closing them. Especially useful when running time-consuming commands on a remote machine via SSH.
```
>> tldr screen

screen

Hold a session open on a remote server. Manage multiple windows with a single SSH connection.
See also `tmux` and `zellij`.
More information: <https://manned.org/screen>.

- Start a new screen session:
    screen

- Start a new named screen session:
    screen -S session_name

- Start a new daemon and log the output to `screenlog.x`:
    screen -dmLS session_name command

- Show open screen sessions:
    screen -ls

- Reattach to an open screen:
    screen -r session_name

- Detach from inside a screen:
    Ctrl + A, D

- Kill the current screen session:
    Ctrl + A, K

- Kill a detached screen:
    screen -X -S session_name quit
```

## SSH
Secure Shell (SSH) is a protocol to securely connect to remote machines and run commands on them, even when the network to which either the host machine or client are connected, is unsecure. A user can authenticate through either a password or secret key. The command to connect to a remote machine looks something like `ssh <username>@<address_of_host> [-p port ...]`. Configuration files, known hosts and keys are stored in `~/.ssh/`.

### Public & private keys
Using public-key cryptography (also called asymmetric cryptography) is generally considered to be more secure. Password-based authentication could be prone to man-in-the-middle attacks, whereas the private key of a public/private key pair is (normally) never at risk to be exposed. You can also secure your key with a password. To generate a public/private key pair, run `ssh-keygen`.
