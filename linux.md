## Package management
* Sources List Generators
    - Ubuntu: https://repogen.simplylinux.ch/
    - Debian: https://debgen.simplylinux.ch/

* Correct broken dependencies
```
sudo apt-get clean
sudo apt-get update
sudo apt-get install -f
sudo dpkg -a --configure
sudo apt-get dist-upgrade
```

## Network
* Kernel routing table
```
netstat -r
```
* Network interfaces
```
ifconfig
```
* Allow some TCP traffic from LAN
```
ufw allow proto tcp from $subnet to any port $range0:$range1
```

## Filesystem
* [Filesystem Hierarchy Standard](http://www.pathname.com/fhs/)

* NFS mount options
```console
root@nfs-server:/# cat /proc/fs/nfs/exports
user@nfs-client:~$ cat /proc/mounts
```
* POSIX ACLs
```
getfacl $path
setfacl -m u:$user:rwx $path
```

* NFSv4 ACLs
```
nfs4_getfacl $path
nfs4_setfacl -a A::$user:RWX $path
```
