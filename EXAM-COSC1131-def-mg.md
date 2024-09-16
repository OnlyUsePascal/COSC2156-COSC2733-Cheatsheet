# COSC2156-COSC2733-Cheatsheet
## Unix commands
### 1. disk usage
```sh
du -sk * | sort -n

disk usage for all subfolders / files 
--> sort by size from smallest to largest

# nice trick
man [command] | grep '\-[option]'
```

### 2. awk count regular user
```sh
# ---------
cat /etc/passwd | \
  awk 'BEGIN {FIRSTUSER=1000; COUNT=0; FS=":"} \
    {if ($3 >= FIRSTUSER) COUNT++} \ # if uid >= 1000 then increase count
    END {print COUNT}'
```

### 2. vim shit
``` sh 
:33<enter> --> go to line 33
3dd --> delete 3 lines from current line
```

### 5. getopts command
```sh
# prone to positional error
case $1 in 
  opt1)
    echo 'you have enter option1'
    ;;
  opt2)
    echo 'you have enter option2'
    ;;
  *)
    echo 'bro, wrong choice'
    ;;
esac

# better catch argument
OPTS=':a:b'
while getopts $OPTS opt; do
  case $opt in
    a)
      echo "option -a triggered with arg $OPTARG"
      ;;
    b) 
      echo "option -b triggereed with $OPTARG"
      ;;
    ?)
      echo "invalid option $OPTARG"
      ;;
  esac
done
```

## Bash scripting
### 9. set noclobber
```sh
#before noclobber
echo 'Text 1' > file.txt #file.txt = text 1
echo 'Text 2' > file.txt #file.txt get overwrittern == text 2

#after noclobber
set -o noclobber
echo 'Text 1' > file.txt #file.txt = text 1
echo 'Text 2' > file.txt #error
echo 'Text 2' >| file.txt #force overwrite
```

### 10. temp file, dir
```sh
mktemp [filename].XXXXX # make temp file
mktemp -d [dirname].XXXXX # make temp dir
```

### 11. list out group that contain desired user
```sh
cat /etc/group | \
  awk -v TARGET=$1 \
    'BEGIN {FS=":"} \
    {if ($4 != "") \
      {split($4, array, ","); \
      for (i in array) \
        {if (array[i] == TARGET) \
        print $1}}} \
    END {}'
    
$ bashfile joun
```

### 14. using heredoc
```sh
cat<<[end mark]>[filename]
heredoc> fred
heredoc> barney
heredoc> betty 
heredoc> [end mark]
```

### 15. Why prefer calling program binaries than program scripts?
```sh
- bash scripts are interpreted text they are slower than compiled programs.
- script itself is a string --> running script involve converting to approriate machine code --> slow-down
```

### 18. readonly
```sh
# Create a variable
my_var="Hello"

# Make it read-only
readonly my_var

# Attempt to modify it
my_var="World"  # This will generate an error
```
## File System
### 18. inode structure
```sh
https://www.geeksforgeeks.org/inode-in-operating-system/
Userid of the owner
groupid of the owning group
deviceid of the device the file is on
file mode of the file
timestamps (ctime, mtime, atime)
link count → how many hard links
```

### 20. concept of a network based file system
```sh
- simulates being a local filesystem by providing standard file based interface (mount, write, read, etc.)
- all reads and writes are really network messages being sent.
```

### 21. purpose of /var
```sh
- contains many directories whose contents changes rapidly (log files, mail, cache, etc.)
- mount parts of this as separate partitions so that they do not overflow and take up storage space.
```

### 22. secure booting
```sh
-  is a way to ensure that only certain operating systems can be booted on given hardware
- implemented via keys so that an operating system that does not match the installed keys would not be able to boot.
- fear that OS vendor have a deal with hardware vendors --> can only boot their OS but not other
```

### 24. Striping, Mirroring and Parity 
```sh
- striping: splits the data across multiple disks in an array of disks
- mirroring: makes a full mirrored copy of the data, 
- parity uses an xored  partity check to allow reconstruction of the data.
```


## Rasperrbi Pi
### 25. explain command
```sh
xzcat image.xz | dd of=/dev/sdc status=progress; sync

- xzcat decompress a disk image (.xz file) to stdout 
- pipe output to dd command
  - write to disk at /dev/sdc
  - output progress for each byte written.
- sync command to ensure data loss
```

### 26. trigger 
```sh
1. identify the LED
ls /sys/class/leds/
led0
led1

2. Check Current Trigger
cat /sys/class/leds/led0/trigger
[none] mmc0 timer oneshot heartbeat default-on input

3. change the trigger
echo heartbeat | sudo tee /sys/class/leds/led0/trigger

Common triggers include:
- none: The LED is off unless manually controlled.
- default-on: The LED remains on.
- heartbeat: The LED blinks like a heartbeat, often used to indicate system health.
- timer: The LED blinks on a regular interval (you can customize the interval).
- mmc0: The LED blinks based on SD card activity.
```
## Docker
### 27. add to group
```sh
usermod -aG [group to add] [user]

- members of the docker group have access to managing docker images without being given unnecessary privileges to other parts of the system
```

### 28. export vs save
```sh
save: save the specified container to an image and compress it with tar.
export: export an image on the current system to specified location.
```

### 29. push container to dockerhub
```sh
- commit a container to local image
- login to docker hub
- push image to hub (just like github)
```
