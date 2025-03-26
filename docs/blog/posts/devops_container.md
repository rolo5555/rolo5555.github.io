---
date: 2024-09-20  
categories:
- Devops
readtime: 20  
authors:
- rolo
---

## Containers: Solving the "It Works on My Machine" Problem

Have you ever built an app that ran perfectly on your machine, only to see it crash and burn on someone else’s? That’s the classic “It works on my machine” problem. Containers solve this by packaging **everything** your app needs—code, libraries, tools, and settings—so it behaves the same no matter where it's run.

### The Big Idea: Bundle Every Dependency

At the heart of containers is the idea of shipping all dependencies with your app. This isolates it from the host system and other apps, eliminating conflicts and surprises.

### But Containers Aren’t Magic

They feel magical, but they’re not. Containers are just regular Linux processes, given superpowers by kernel features.

### Containers = Processes

A container is just a process with some isolation. The Linux kernel makes this happen.

### Key Kernel Features

- **cgroups** (control groups): limit and isolate resource usage (CPU, memory, etc.)
- **namespaces**: isolate things like process trees, users, network interfaces, and filesystems

### Diving Deeper

- **pivot_root**: changes the root filesystem of a process
- **layers**: containers are built from stacked image layers, saving space and time
- **overlay filesystems**: combine multiple layers into one coherent view
- **container registries**: store and distribute container images (like Docker Hub)

### The Power of Isolation

- **cgroups** control resource limits
- **namespaces** isolate environments:
  - PID, user, network, and mount namespaces create that sandboxed feeling
- **how to make a namespace**: tools like `unshare` or `clone()` can do it

### Networking & Security

- **container IP addresses**: every container can get its own IP
- **capabilities**: fine-grained control over what a process can do
- **seccomp-BPF**: filter syscalls for tighter security

### Flexibility Through Config

- **configuration options**: containers are highly customizable through runtime configs