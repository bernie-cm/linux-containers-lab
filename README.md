# Using Linux Containers (LXC)
Linux Containers, or LXC, is an interface for Linux kernel vritualisation. You can create Linux containers that enable persistence, and system-level functionality. When comparing LXC to Docker, they both serve different use cases. Docker is more suitable for **application containerisation** whereas LXC is more relevant for **system-level functionality** and also **persistent environments**.

## Installing LXC
```bash
$ sudo apt update
$ sudp apt install -y lxc
```

Since we need to create an **unprivileged container** (for security reasons), the user that will be attached to this container needs to have permissions to create network devices.

```bash
$ sudo bash -c 'echo <username> veth lxcbr0 10 >> /etc/lxc/lxc-usernet'
$ cat /etc/lxc/lxc-usernet

# USERNAME TYPE BRIDGE COUNT
<username> veth lxcbr0 10
```
## Setting up the config file
The configuration file for `lxc` may not exist. So create the directory inside the `.config` directory and copy the `default.conf` located in `/etc/lxc/`.
```bash
$ mkdir -p ~/.config/lxc
$ cp /etc/lxc/default.conf ~/.config/lxc/default.conf
$ chmod 664 ~/.config/lxc/default.conf
```

The configuration file has to be updated with the UID and GID of the unprivileged user. They can both be extracted from the `/etc/subuid` and `/etc/subgid` files.
```bash
$ cat /etc/subuid
ubuntu:100000:65536
<username>:165536:65536

$ cat /etc/subgid
ubuntu:100000:65536
<username>:165536:65536

$ echo lxc.idmap = u 0 165536 65536 >> ~/.config/lxc/default.conf
$ echo lxc.idmap = g 0 165536 65536 >> ~/.config/lxc/default.conf

$ cat ~/.config/lxc/default.conf
lxc.net.0.type = veth
lxc.net.0.link = lxcbr0
lxc.net.0.flags = up
lxc.net.0.hwaddr = 00:16:3e:xx:xx:xx
lxc.idmap = u 0 165536 65536
lxc.idmap = g 0 165536 65536
```
