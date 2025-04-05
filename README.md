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

## Setting access control list
To prevent possible permission errors, we need to set up an access control list on our `.local` directory.
```bash
$ sudo apt update
$ sudo apt install -y acl
$ setfacl -R -m u:165536:x ~/.local
```
This command sets an ACL (Access Control List) for a specific user (UID 165536), recursively on the ~/.local directory, giving that user execute (x) permission.

## Creating an unprivileged container
Once the setup is complete, we can create a container using the `download` template. This gives us all available images designed to work **without privileges**.

```bash
$ lxc-create --template download --name unpriv-cont-user
```
Once the image index is downloaded, the CLI tool will display the images and await for the user to provide the distro, release and architecture required. In this case, **ubuntu**, **jammy** and **amd64** will be used.

```bash
---

Distribution:
ubuntu
Release:
jammy
Architecture:
amd64

Downloading the image index
Downloading the rootfs
Downloading the metadata
The image cache is now ready
Unpacking the rootfs

---
You just created an Ubuntu jammy amd64 (20250404_20:34) container.
```

## Starting the container
Now the container has been created, we can start it.
```bash
$ lxc-start -n unpriv-cont-user -d
```
With the container running we can interact with its environment.
```bash
$ lxc-attach -n unpriv-cont-user
# hostname
unpriv-cont-user
# exit
$ lxc-stop -n unpriv-cont-user
```
