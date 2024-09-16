# Cooking section
## Unix commands 
### 1. 
```sh
pgrep vi -l | awk '{print $2}' | sort -u
- pgrep: looks through currently running processes
  -> list processes ID that contain "vi"
- print the process name
- sort results in unique + ascending order


free -h | grep Mem | awk '{print $4}'
- displays  the total amount of free and used physical and swap memory in the system
  "-h": display in human readable form
- grep: filter lines that contain "mem"
- awk: display how many free memory left
```

### 2. Change script's shell with VIM
```sh
1. Open vim at the script.
Since the script is configed to run with fish, the shebang is usually #!/bin/fish

2. Move to first line with
gg

3. enter insert mode
cw

4. change the shell to zsh
#!/bin/zsh

5. save script
:wq
```

### 3. For vs while loops
```sh
- for loops when you know the exact list of items or a specific range of numbers to iterate over.
- while loops when you need to iterate based on a condition that might change during execution or when processing input until a specific condition is met.
```

### 4. get the manual path of a command
```sh
man -w [command]

e.g., man -w ls
/usr/share/man/man1/ls.1.gz
```

### 5. system load average 
```sh
It refers to the number of processes which are either currently being executed by the CPU or are waiting for execution.

E.g., 
$ uptime
12:36:09 up 1 day, 15:51,  3 users,  load average: 1.02, 0.90, 0.80
- 1-minute load average: Reflects the average system load over the last minute.
- 5-minute load average: Depicts the average system load over the past five minutes.
- 15-minute load average: This represents the average system load over the past fifteen minutes. 
```

### 6. command not found
```sh
possible reasons: 
- not add to PATH
- permissions

1. find the path of variables (in machine u could run it)
$ where sl

2. export binary path to PATH
export PATH=$PATH:/path/to/sl

3. set suitable permission 
#e.g., you are in other group 
sudo chmod o+x /path/to/sl
```

## Scripting
### 7. cat command
```sh
a. Display file name with space= "./file name"
cat ./file\ name

b. number the lines
cat -n [file]

c. display tabs as ^I
cat -T [file]

d. display non-printing characters
cat -v [file]
```

### 8. unbound variables
```sh
#!/bin/bash

#enforce error on unbound var
set -u

#test var
name=joun
echo "good morning, $name"

# error
echo "good morning, $name2"

```

### 9. cat command
```sh
problem
- change "}" to ")" at line 3
- append "tmp" to rm command at line 10


#!/bin/zsh
# create a random string for a filename
filename=$(cat /dev/urandom | tr -dc 'a-zA-Z'| fold -w 30 | head -n 1)
# test that the file exists \
if [[ ! -e “${filename}” ]]; then 
  # use dd to write zeroes 
  dd if=/dev/zero of=${filename}.tmp bs=4M count=80; sync 
  #do something with the file 
  #delete the file 
  rm ${filename}.tmp
fi
```

### 10. 
```sh
#!/bin/sh

cat /etc/group | \
  awk -v GROUP=$1 \
  'BEGIN {FS=":"} \
  {if ($1 == GROUP) \
    {print ">> members"; \
    split($4,array,","); \
    for (i in array) \
      {print array[i]}}} \
  END{}'
```

### 11. 
```sh
- Avoiding Errors with Custom PATH Variables
- avoid running malicious commands that might exist in directories earlier in your PATH.  
  e.g., Attackers could place malicious scripts with common names like ls or cat

example 
#!/bin/bash
# Full path to executables
LS="/bin/ls"
GREP="/usr/bin/grep"
CAT="/bin/cat"
# Use the full path to call each command
$LS -l /some/directory
$GREP "some-pattern" /some/file
$CAT /some/file
```

### 12. heredoc example
```sh
cat<<end>heredoc_example
heredoc> foo                             
heredoc> bar 
heredoc> baz 
heredoc> end 
```

### 13. 
```sh
readonly VAR=123
```

### 14. 
```sh
chmod u=r,g=rx,o=r file.txt
```

## System Config
### 15. RAID levels 
```sh
RAID 0: striping
- data is splitted across multiple disks. 
- performance rely on all disks performance
- no recovery for data loss

RAID 1: mirroring
- duplicate data across multiple drives
- faster on read (since we can pick any drive), but slower on write (since copy)
- data is more available / but less capacity

RAID 4: striping + dedicated parity
- data is splitted across all drives, EXCEPT ONE.
- that one drive contains paritiy bit for error-correcting. 
- faster read, lower write (since also write the parity) 
- we can now recover data 

RAID 5: striping + distributed parity
- distribute data + distribute parity
- write is faster than RAID 4 as the paritiy bit is distributed. 
```

### 16. lvm 
```sh
read lecture of week 3
```

### 17. sshfs 
```sh
Concept: mount remote directory securely (using SSH)

When we mount a file on an sshfs mount
1. You open the file in the SSHFS-mounted directory.
2. SSHFS sends an SFTP request over the SSH connection to the remote server to access the file.
3. server responds by sending the file's data to your local system.
4. You interact with the file as though it were local, but all data is transmitted securely over the SSH connection. All changes exchanged via SFTP. 
```

### 18. ssh + scp vs. tenet + ftp
```sh
- Telnet/FTP transmit data in plaintext. This makes them highly vulnerable to man-in-the-middle (MITM) attacks
  SSH, SCP encrypt transmitted data using symmetric encryption (e.g., AES)
  
- SSH, SCP provides stronger, more flexible authentication methods
  - password-based
  - 2FA 
  - public key / private key
  
- Telnet/ FTP Neither protocol offers built-in data integrity checks 
  SSH/SCP ensure data integrity with hashing (MD5, SHA)
```

### 19. /bin directory
```sh
Purpose: contains essential binaries or executable files that are needed for the system to boot, operate, and allow basic user interaction

Not recommended to mount /bin in separate partition
- unlike /home (user data) or /var (hostile files), separate binaries from the system folder does not provide any advantages
- The system boot process relies on the /bin directory for essential commands needed during the initial boot phases. Having /bin as a separate partition adds complexity and increases the risk of failure during system startup.
```

### 20. dd command
```sh
purpose: used for low-level copying and conversion of raw data. It is commonly used for tasks such as:
- copy data between block devices
- create disk image
- copy disk image to bootable usb drive

dd if=/dev/random of=./blah.dat status=progress bs=4M; sync
- if: input source, in this case is a special file that generate random data
- of: output source. in this case is a file named blah.dat 
- status: display progress during copy
- bs: block size = 4MB
- sync: ensure data integrity. 
```

## Docker 
### 21. docker group 
``` sh
Impact: 
- grants that user the ability to run Docker commands (docker) without requiring sudo. 
- This allows the user to manage Docker containers, images, and volumes as if they had root (administrator) privileges.

Drawbacks:
- Users in the docker group can execute Docker commands with root-level access because Docker runs as the root user, thus execute arbitrary commands within containers, and potentially access sensitive system files.
- If a user pulls and runs an untrusted or malicious Docker image, that container could potentially exploit vulnerabilities in the host system or other containers running on it.
```
### 22. Port re-direct
```sh
# download docker image
sudo docker pull ubuntu

# run docker 
sudo docker run -it -p 56782:22 ubuntu
-it: run with interactive shell
-p: [local port]:[container port]
```

### 23. restart container
```sh
sudo docker restart joyous_jackaloupe
```
