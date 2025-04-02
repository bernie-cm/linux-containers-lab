# Using Linux Containers (LXC)
Linux Containers, or LXC, is an interface for Linux kernel vritualisation. You can create Linux containers that enable persistence, and system-level functionality. When comparing LXC to Docker, they both serve different use cases. Docker is more suitable for **application containerisation** whereas LXC is more relevant for **system-level functionality** and also **persistent environments**.

## Installing LXC
```bash
$ sudo apt update
$ sudp apt install -y lxc
```

Since we need to create an **unprivileged container** (for security reasons), the user that will be attached to this container needs to have permissions to create network devices.

```bash
$ sudo bash -c 'echo <username veth lxcbr0 10 >> /etc/lxc/lxc-usernet'
$ cat /etc/lxc/lxc-usernet

# USERNAME TYPE BRIDGE COUNT
<username> veth lxcbr0 10
```
