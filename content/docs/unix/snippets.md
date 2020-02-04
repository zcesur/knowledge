### find
```
find . -name '*.ts' -print0 | sort -z
find . -type f -print0 | xargs -0 cmd
find . -type d -print0 | while IFS= read -r -d '' dir; do
    echo "$dir"
done
```

### while/for
```
while IFS= read -r line || [[ -n "$line" ]]; do
    echo "$line"
done
```

### sed
Now, proceed with a sed dry run. The sed option -n is a synonym for --quiet and the p at the very end of the sed expression will print the current pattern space.
```
$ find . -exec sed -n 's/term1/term2/gp' {} \;
```

You can do the task without a loop using GNU parallel Install parallel:

```
parallel sed -i.old s/example/{.}/ {} ::: *.conf
```
This is especially useful if you have a lot of files to edit, as parallel runs one job per CPU core.

-i.old means: edit the file in-place and make a backup adding the .old extension to the original filename (remove .old if you don't want a backup, but remember you don't have a backup then)
s/example/{.}/g means substitute example with the input filename without its extension ({.}) and do it globally (= to every occurence)
{} is replaced with the input filename
::: separates the command to run from the arguments to pass to it
*.conf matches every .conf file in the current directory

### ffmpeg
```
ffmpeg -i input -c:v libx265 -crf 28 -c:a aac -b:a 128k output.mp4

chunks=$(
    find . -name '*.ts' -print0 |
        sort -z |
        sed -e 's/\x0$//' |
        sed -e 's/\x0/|/g'
)
ffmpeg -i "concat:$chunks" -c copy output.ts

ffmpeg -v error -i input.ts -f null - 2>error.log
```

### ssh
```
ssh -P port user@host
ssh -t user@host /bin/sh
scp -P port doc host:path
ssh-copy-id -i ~/.ssh/id_rsa.pub user@host

ssh -TNfL port:host:hostport user@host
ssh -TND 4711 user@host
sshuttle --dns -r user@host 0/0
```

### openssl
```
openssl rand -hex 32
openssl enc -base64    <<< str
openssl enc -base64 -d <<< str
```

### gpg
```
gpg --import signing_key.pub
gpg --verify signed_file.sig
```

* Release the passwords
```
echo RELOADAGENT | gpg-connect-agent
```

### git
```
git submodule add $repo $path
git submodule foreach git pull origin master
git log --oneline | nl
```

### tar
```
tar -xvzf file.tar.gz -C $dir
tar -xvjf file.tar.bz2
tar -xvf file.tar

tar -cvf file.tar -C $dir .
```

### systemctl
```
systemctl daemon-reload
systemctl enable $unit
systemctl start $unit

systemctl get-default
systemctl set-default rescue.target

systemctl list-unit-files | grep enabled
systemctl list-dependencies graphical.target
```

### test
#### supported by [ and [[
* `-e FILE:` True if file exists.
* `-f FILE:` True if file is a regular file.
* `-d FILE:` True if file is a directory.
* `-h FILE:` True if file is a symbolic link.
* `-p PIPE:` True if pipe exists.
* `-r FILE:` True if file is readable by you.
* `-s FILE:` True if file exists and is not empty.
* `-t FD :` True if FD is opened on a terminal.
* `-w FILE:` True if the file is writable by you.
* `-x FILE:` True if the file is executable by you.
* `-O FILE:` True if the file is effectively owned by you.
* `-G FILE:` True if the file is effectively owned by your group.
* `FILE -nt FILE:` True if the first file is newer than the second.
* `FILE -ot FILE:` True if the first file is older than the second.

* `-z STRING:` True if the string is empty (it's length is zero).
* `-n STRING:` True if the string is not empty (it's length is not zero).
* `STRING = STRING:` True if the first string is identical to the second.
* `STRING != STRING:` True if the first string is not identical to the second.
* `STRING < STRING:` True if the first string sorts before the second.
* `STRING > STRING:` True if the first string sorts after the second.
* `! EXPR:` Inverts the result of the expression (logical NOT).

* `INT -eq INT:` True if both integers are identical.
* `INT -ne INT:` True if the integers are not identical.
* `INT -lt INT:` True if the first integer is less than the second.
* `INT -gt INT:` True if the first integer is greater than the second.
* `INT -le INT:` True if the first integer is less than or equal to the second.
* `INT -ge INT:` True if the first integer is greater than or equal to the second.

#### supported only by [[
* `STRING = (or ==) PATTERN:` Not string comparison like with [ (or test), but pattern matching is performed. True if the string matches the glob pattern.
* `STRING != PATTERN:` Not string comparison like with [ (or test), but pattern matching is performed. True if the string does not match the glob pattern.
* `STRING =~ REGEX:` True if the string matches the regex pattern.
* `( EXPR ):` Parentheses can be used to change the evaluation precedence.
* `EXPR && EXPR:` Much like the '-a' operator of test, but does not evaluate the second expression if the first already turns out to be false.
* `EXPR || EXPR:` Much like the '-o' operator of test, but does not evaluate the second expression if the first already turns out to be true.

#### supported only by [
* `EXPR -a EXPR:` True if both expressions are true (logical AND).
* `EXPR -o EXPR:` True if either expression is true (logical OR).

### man
```
# 1 Executable programs or shell commands
# 2 System calls (functions provided by the kernel)
# 3 Library calls (functions within program libraries)
# 4 Special files (usually found in /dev)
# 5 File formats and conventions eg /etc/passwd
# 6 Games
# 7 Miscellaneous (including macro packages and conventions), e.g.  man(7), groff(7)
# 8 System administration commands (usually only for root)
# 9 Kernel routines [Non standard]

man 7 hier
```

### I/O
**File Descriptor:** A numeric index referring to one of a process's open files. Each command has at least three basic descriptors: FD 0 is `stdin`, FD 1 is `stdout` and FD 2 is `stderr`.

```
echo 'Something bad happened' >&2
exit 1
```

### Disk
```
lsblk
parted -l
df -h
cat /proc/mounts
cat /proc/partitions
lshw -class disk

mount -t vfat /dev/sdb1 /media/usbstick

dd if=/dev/sda1 of=~/data.dd
mount -t ext4 -o loop,ro,noexec data.dd /mnt/data
```

### rsync
```
rsync -a --delete src dst
rsync remote:src dst
rsync src remote:dst
```

### VNC
```
x11vnc -display :0
```

### gcloud
```
gcloud config set project $project_id
gcloud config set compute/zone $zone
gcloud config set container/cluster $cluster
```

```
gcloud container clusters list
gcloud container node-pools list
gcloud container clusters update $cluster \
    --enable-autoscaling \
    --min-nodes 1 \
    --max-nodes 10 \
    --node-pool $node-pool
gcloud container clusters resize $cluster \
    --size 3 \
    --node-pool $node-pool
```

```
instance=$(gcloud compute instances list | awk 'NR==2{print $1}')
gcloud compute instances list
gcloud compute ssh "$instance" -- -TND 5000
```

```
gcloud builds submit --tag gcr.io/$PROJECT_ID/$IMAGE_NAME:$TAG path/to/dir
```

### kubectl
```
kubectl port-forward $pod 3000:3000
```

* Get number of available images in each node
```
for node in $(kubectl get nodes | awk '/gke-cluster/ {print $1}'); do
    kubectl get node $node -o json | grep --count "sizeBytes"
done
```

### netstat
```
netstat -tunaplz
```

### nc
```
nc -vz $remote $port
```
