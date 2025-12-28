# The Anatomy of a Container

A container is built using two core Linux kernel features:

- **Namespaces**
- **Control Groups (cgroups)**

## Linux Namespaces

Namespaces provide **isolation** by giving processes a limited view of the system.

Each container believes it has:

- Its own users
- Its own file system
- Its own network
- Its own processes

### Common Namespaces Used by Docker

- **USER**: User and permission isolation
- **MOUNT**: File system isolation
- **NET**: Network isolation
- **IPC**: Inter-process communication
- **PID**: Process IDs
- **CGROUP**: Resource group visibility
- **UTS**: Hostname isolation

> Docker uses all namespaces **except TIME**, meaning containers cannot change system time.

## Control Groups (cgroups)

Control groups limit and monitor **resource usage**.

Docker uses cgroups to:

- Limit CPU usage
- Control memory consumption
- Restrict network bandwidth
- Restrict disk I/O

This prevents one container from:

- Consuming all system resources
- Slowing down other containers

## Why This Matters

Namespaces + cgroups provide:

- Lightweight isolation
- High performance
- Efficient resource sharing

This approach is much lighter than virtualizing full hardware.

## Platform Limitation

Because containers rely on Linux kernel features:

- Docker runs natively on Linux
- Linux containers require a Linux kernel
- Windows containers require Windows kernels

Images are **kernel-specific**.
