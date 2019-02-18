# Networking
### [iptables](https://wiki.archlinux.org/index.php/iptables) - administration tool for IPv4 packet filtering and NAT
> iptables is used to inspect, modify, forward, redirect, and/or drop IP packets. The code for filtering IP packets is already built into the kernel and is organized into a collection of tables, each with a specific purpose. The tables are made up of a set of predefined chains, and the chains contain rules which are traversed in order. Each rule consists of a predicate of potential matches and a corresponding action (called a target) which is executed if the predicate is true; i.e. the conditions are matched.

* Port forwarding
```bash
sysctl -w net.ipv4.ip_forward=1

iptables -t nat -A PREROUTING -s 127.0.0.1 -p tcp --dport 80 -j REDIRECT --to 8080
iptables -t nat -A OUTPUT -s 127.0.0.1 -p tcp --dport 80 -j REDIRECT --to 8080
iptables -t nat -A PREROUTING -s 127.0.0.1 -p tcp --dport 443 -j REDIRECT --to 8443
iptables -t nat -A OUTPUT -s 127.0.0.1 -p tcp --dport 443 -j REDIRECT --to 8443
```

* List the rules in all chains
```
iptables -t nat -L --line-numbers
```

* Delete rule the specified rule from chain
```
iptables -t nat -D $chain $rulenum
```

### [ssh](https://wiki.archlinux.org/index.php/OpenSSH) - OpenSSH SSH client (remote login program)
> Secure Shell is a network protocol that allows data to be exchanged over a secure channel between two computers. Encryption provides confidentiality and integrity of data. SSH uses public-key cryptography to authenticate the remote computer and allow the remote computer to authenticate the user, if necessary.
>
> SSH is typically used to log into a remote machine and execute commands, but it also supports tunneling, forwarding arbitrary TCP ports and X11 connections; file transfer can be accomplished using the associated SFTP or SCP protocols.
>
> An SSH server, by default, listens on the standard TCP port 22. An SSH client program is typically used for establishing connections to an sshd daemon accepting remote connections. Both are commonly present on most modern operating systems, including macOS, GNU/Linux, Solaris and OpenVMS. Proprietary, freeware and open source versions of various levels of complexity and completeness exist.
>
* Encrypted SOCKS tunnel
```
ssh -TND 4711 user@host
```
* Port forwarding
```
ssh -TNfL port:host:hostport user@host
```

### [tcpdump](https://en.wikipedia.org/wiki/Tcpdump) - dump traffic on a network
> tcpdump is a common packet analyzer that runs under the command line. It allows the user to display TCP/IP and other packets being transmitted or received over a network to which the computer is attached.

### [ufw](https://wiki.archlinux.org/index.php/Uncomplicated_Firewall) - program for managing a netfilter firewall
* Allow some TCP traffic from LAN
```
ufw allow proto tcp from $subnet to any port $range0:$range1
```
* Allow all incoming SSH connections
```
ufw allow 22/tcp
```

### [iw](https://wiki.archlinux.org/index.php/Wireless_network_configuration#iw) - show / manipulate wireless devices and their configuration
* List all network interfaces for wireless hardware
```
iw dev
```

* Print information about the current link, if any  
```
iw dev wlp4s0 link
```

* Scan for available access points
```
iw dev wlp4s0 scan
```

### [wpa_supplicant](https://wiki.archlinux.org/index.php/WPA_supplicant) - Wi-Fi Protected Access client and IEEE 802.1X supplicant
* Generate a WPA PSK from an ASCII passphrase for a SSID
```
wpa_passphrase $ssid $passphrase
```

### [ifdown/ifup](http://manpages.ubuntu.com/manpages/xenial/man8/ifup.8.html) - bring up/take down a network interface
```
ifdown wlp4s0
ifup wlp4s0
```

### [dhclient](https://wiki.archlinux.org/index.php?title=Dhclient&redirect=no) - Dynamic Host Configuration Protocol Client
* Release the current lease and stop the running DHCP client as previously recorded in the PID file.
```
dhclient -r
```
* Configure DHCP client to use Google's DNS servers
```
echo "supersede domain-name-servers 8.8.8.8, 8.8.4.4;" | sudo tee -a /etc/dhcp/dhclient.conf
```

### [ip](http://manpages.ubuntu.com/manpages/xenial/man8/ip.8.html) - show / manipulate routing, devices, policy routing and tunnels
* Display details on specified network interface(s)
```
ip a
ip a show dev wlp4s0
```
* Display kernel routing tables
```
ip route
```

### [ss](https://wiki.archlinux.org/index.php/Network_configuration#Investigate_sockets) - a utility to investigate sockets
* Display all TCP Sockets with service names
```
ss -at
```

### [lsof](http://manpages.ubuntu.com/manpages/xenial/man8/lsof.8.html) - list open files
* Find out which process is using the specified port
```
lsof i :53
```

# System
### [systemd](https://wiki.archlinux.org/index.php/systemd) - systemd system and service manager
> systemd is a suite of basic building blocks for a Linux system. It provides a system and service manager that runs as PID 1 and starts the rest of the system. systemd provides aggressive parallelization capabilities, uses socket and D-Bus activation for starting services, offers on-demand starting of daemons, keeps track of processes using Linux control groups, maintains mount and automount points, and implements an elaborate transactional dependency-based service control logic.

* **systemctl** - Control the systemd system and service manager
```
systemctl stop NetworkManager.service
systemctl disable NetworkManager.service
systemctl list-unit-files | grep enabled
```

* **journalctl** - Query the systemd journal
```
journalctl -f
journalctl /usr/lib/systemd/systemd
journalctl _PID=1
```

### [sysctl](https://wiki.archlinux.org/index.php/sysctl) - configure kernel parameters at runtime
```
sysctl net.ipv4.ip_forward
sysctl -w net.ipv4.ip_forward=1
```

### Drivers
```
ubuntu-drivers devices
ubuntu-drivers autoinstall
```

# Access Control
### [User management](https://wiki.archlinux.org/index.php/users_and_groups#User_management)
```
adduser $user
usermod -aG $group $user
```

### [ACL](https://wiki.archlinux.org/index.php/Access_Control_Lists) - an additional, more flexible permission mechanism for file systems
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

# Filesystem
### Status of mounted/exported filesystems
```console
root@nfs-server:/# cat /proc/fs/nfs/exports
user@nfs-client:~$ cat /proc/mounts
```

### [Filesystem Hierarchy Standard](fhs.md)

# Misc.
### apt-get
* Sources List Generators
    - Ubuntu: https://repogen.simplylinux.ch/
    - Debian: https://debgen.simplylinux.ch/

* Fix broken dependencies
```
apt-get clean
apt-get update
apt-get install -f
dpkg -a --configure
apt-get dist-upgrade
```

### Configuration files
* `/etc/network/interfaces`
* `/etc/network/if-up.d/`
* `/etc/dhcp/dhclient.conf`

