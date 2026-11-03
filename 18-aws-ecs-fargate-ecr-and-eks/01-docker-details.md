# Docker Complete Notes (AWS SAA-C03 + Beginner Friendly)

These notes are written for someone who is **completely new to Docker**, while also covering the concepts needed for the **AWS Certified Solutions Architect – Associate (SAA-C03)** exam.

---

# 1. Why Docker Exists

Imagine you built a Node.js application.

Your computer has:

- Node.js 20
- Python 3.12
- Ubuntu
- Nginx

Everything works perfectly.

You send the project to your friend.

Your friend says:

> "It doesn't work on my computer."
> 

This is called the

> **"Works on my machine" problem.**
> 

Different computers have:

- Different Operating Systems
- Different libraries
- Different software versions
- Different configurations

Docker solves this.

---

# 2. What is Docker?

Docker is a

> **Containerization Platform**
> 

It packages everything an application needs into one portable package called a **Container**.

That package contains:

- Application
- Runtime
- Libraries
- Dependencies
- Configuration
- Environment Variables

So wherever Docker runs,

the application behaves exactly the same.

---

Imagine a lunch box.

```
Lunch Box

🍚 Rice
🍗 Chicken
🥤 Juice
🥄 Spoon
```

Everything needed is inside.

Docker Container is exactly like that.

---

# 3. What is a Container?

A container is simply

> A lightweight isolated environment that runs an application.
> 

Example

```
Container

NodeJS
Express
Application
Libraries
```

Another

```
Container

Python
Flask
Application
Libraries
```

They don't interfere with each other.

---

# 4. Before Docker (Traditional Deployment)

Suppose one server hosts three applications.

```
Server

Application A
Application B
Application C
```

Problem:

Application A needs

```
Python 3.9
```

Application B needs

```
Python 3.12
```

Conflict!

One application may break another.

---

# 5. Docker Solution

```
Server

Docker

├── Container A
├── Container B
└── Container C
```

Each container has its own dependencies.

No conflicts.

---

# 6. How Docker Works

```
Application

↓

Docker Image

↓

Docker Container
```

Think

Image = Recipe

Container = Cooked Food

---

Example

Recipe

```
Chicken Curry
```

Cook it.

Now you have

```
Chicken Curry 🍛
```

Similarly

Docker Image

↓

Run

↓

Docker Container

---

# 7. Docker Image

Image is a blueprint.

It cannot run itself.

Example

```
Ubuntu Image

Python Image

Nginx Image
```

---

# 8. Docker Container

A running instance of an image.

Example

```
Ubuntu Image

↓

docker run

↓

Ubuntu Container
```

You can create

```
Ubuntu Container 1

Ubuntu Container 2

Ubuntu Container 3
```

from the same image.

---

# 9. Docker Engine

Docker Engine is the software that runs Docker.

It includes:

```
Docker CLI

Docker Daemon

REST API
```

---

# 10. Docker Daemon

The Docker Daemon (`dockerd`) is the background service that does the actual work.

It:

- Pulls images
- Builds images
- Creates containers
- Starts containers
- Stops containers
- Removes containers
- Manages networking
- Manages volumes

Example

You type

```bash
docker run nginx
```

What happens?

```
You

↓

Docker CLI

↓

Docker Daemon

↓

Container Starts
```

The CLI sends the request; the daemon performs it.

---

# 11. Docker CLI

The commands you type.

Example

```bash
docker ps

docker run

docker stop

docker images
```

CLI = Command Line Interface

---

# 12. Docker Hub

Docker Hub is

> GitHub for Docker Images.
> 

Millions of images are stored there.

Example

Need Ubuntu?

```
Docker Hub

↓

ubuntu
```

Need MySQL?

```
Docker Hub

↓

mysql
```

Need Redis?

```
Docker Hub

↓

redis
```

You pull them with

```bash
docker pull nginx
```

---

# 13. Docker Hub Example

```
Developer

↓

docker pull nginx

↓

Docker Hub

↓

Downloads Image
```

---

# 14. What is Amazon ECR?

Amazon ECR

=

Elastic Container Registry

It is AWS's private Docker registry.

Think

Docker Hub

↓

Public

Amazon ECR

↓

Private (or public if configured)

---

Example

Company builds an application.

Instead of uploading it to Docker Hub,

they upload it to ECR.

```
Developer

↓

Build Image

↓

Amazon ECR

↓

ECS

↓

Run Container
```

---

# Docker Hub vs Amazon ECR

| Docker Hub | Amazon ECR |
| --- | --- |
| Global Docker registry | AWS container registry |
| Public by default | Private by default |
| Anyone can download public images | IAM controls access |
| Used by everyone | Mostly AWS workloads |
| Not tightly integrated with AWS | Deep AWS integration |

---

# 15. Docker on an Operating System

Imagine Windows.

```
Windows

↓

Docker Engine

↓

Containers
```

Or Ubuntu.

```
Ubuntu

↓

Docker Engine

↓

Containers
```

Containers all share the same OS kernel.

---

# 16. How Containers Share the Kernel

```
Application A

Application B

Application C

↓

Docker

↓

Linux Kernel

↓

Hardware
```

They don't each have their own operating system.

This is why they are lightweight.

---

# 17. What is Virtualization?

Virtualization means

> Running multiple virtual computers on one physical computer.
> 

Imagine

One powerful server.

```
64 GB RAM

16 CPU

2 TB SSD
```

Instead of buying four servers,

create four virtual ones.

---

# 18. Hypervisor

The Hypervisor creates Virtual Machines.

Examples

- VMware ESXi
- Microsoft Hyper-V
- KVM
- VirtualBox (desktop)

Think of the hypervisor as a **manager** that divides one physical machine into multiple virtual machines.

---

# 19. Virtual Machine Architecture

```
Hardware

↓

Hypervisor

↓

VM 1

VM 2

VM 3

↓

Each has its own OS
```

Every VM installs its own operating system.

---

# 20. Example

Your laptop

```
Windows
```

VirtualBox creates

```
Ubuntu VM
```

Inside Ubuntu

You install

```
Python

NodeJS

Nginx
```

Ubuntu behaves like a completely separate computer.

---

# 21. How Virtualization Works

```
Physical CPU

↓

Hypervisor

↓

Virtual CPU

↓

Guest OS

↓

Application
```

The hypervisor presents virtual hardware (CPU, memory, disks, network interfaces) to each VM and shares the physical resources among them.

---

# 22. Docker vs Virtual Machine

## Virtual Machine

```
Hardware

↓

Hypervisor

↓

Guest OS

↓

Application
```

Each VM includes an entire operating system.

---

## Docker

```
Hardware

↓

Host OS

↓

Docker Engine

↓

Containers
```

Containers share the host operating system kernel.

---

# 23. Why Docker is Faster

VM

```
Boot OS

↓

Start App
```

May take minutes.

Docker

```
Start Container
```

Often starts in seconds or less.

---

# 24. Memory Comparison

VM

```
Ubuntu

2 GB RAM

Application
```

Each VM needs memory for the OS.

Docker

```
Shared Linux Kernel

↓

Container

100 MB
```

Much lighter.

---

# 25. Resource Comparison

| Virtual Machine | Docker |
| --- | --- |
| Full OS | Shared kernel |
| Heavy | Lightweight |
| Slower startup | Fast startup |
| More RAM | Less RAM |
| Strong isolation | Process-level isolation |
| Minutes to boot | Seconds (or faster) |

---

# 26. Security

VMs provide stronger isolation because each VM has its own OS.

Containers share the host kernel, so they have a smaller isolation boundary (though modern container security is strong for many workloads).

---

# 27. Docker in AWS

AWS provides multiple services to run containers.

```
Docker Image

↓

Amazon ECR

↓

Amazon ECS

↓

EC2 or AWS Fargate
```

Or

```
Docker Image

↓

Amazon EKS (Kubernetes)
```

---

# 28. AWS Container Services

### Amazon ECR

Stores container images.

---

### Amazon ECS

AWS's managed container orchestration service.

---

### AWS Fargate

Runs containers without managing EC2 servers.

---

### Amazon EKS

Managed Kubernetes service.

---

# 29. Typical AWS Workflow

```
Developer

↓

Build Docker Image

↓

Push to Amazon ECR

↓

ECS / EKS

↓

Run Containers

↓

Users Access Application
```

---

# 30. Real-World Example

You build an online shopping application.

```
Shopping App

↓

Docker Image

↓

Amazon ECR

↓

Amazon ECS

↓

AWS Fargate

↓

Application Running
```

When traffic increases:

```
1 Container

↓

5 Containers

↓

20 Containers
```

ECS/Fargate can scale the application by running more container instances.

---

# Docker vs Virtual Machines (Exam Comparison)

| Feature | Docker | Virtual Machine |
| --- | --- | --- |
| Technology | Containerization | Virtualization |
| Isolation | Process-level | Hardware-level (via hypervisor) |
| OS | Shares host kernel | Each VM has its own OS |
| Startup | Seconds | Minutes (typically) |
| Memory | Low | High |
| Performance | Near native | Slight virtualization overhead |
| Portability | Excellent | Good |
| Density | High (many containers per host) | Lower |
| Best Use | Microservices, APIs, CI/CD | Running different OSes, legacy apps, stronger isolation |

---

# Docker Hub vs Amazon ECR

| Docker Hub | Amazon ECR |
| --- | --- |
| Public image registry | AWS-managed image registry |
| Community images | Organization images |
| Public by default | Private by default |
| General-purpose | Integrated with AWS |
| Login via Docker account | Login via AWS IAM |

---

# Hypervisor vs Docker Daemon

| Hypervisor | Docker Daemon |
| --- | --- |
| Creates and manages VMs | Creates and manages containers |
| Used for virtualization | Used for containerization |
| Runs full operating systems | Runs applications in containers |
| Examples: ESXi, Hyper-V, KVM | `dockerd` |

---

# Virtualization vs Containerization

| Virtualization | Containerization |
| --- | --- |
| Creates virtual computers | Creates isolated application environments |
| Needs a guest OS per VM | Shares the host OS kernel |
| More resource usage | Less resource usage |
| Best for multiple operating systems | Best for packaging and deploying applications |

---

# AWS SAA-C03 Exam Tips

Remember these common clues:

- **Need to store Docker images privately in AWS** → **Amazon ECR**
- **Need to run containers without managing servers** → **AWS Fargate**
- **Need AWS-managed container orchestration** → **Amazon ECS**
- **Need managed Kubernetes** → **Amazon EKS**
- **Need community/public container images** → **Docker Hub**
- **Need strong isolation or different operating systems** → **Virtual Machines**
- **Need lightweight, fast, portable application deployment** → **Docker Containers**

## One-line Memory Trick

- **Docker Image** = Blueprint 📘
- **Docker Container** = Running application 🚀
- **Docker Hub** = Public app store for images 🌍
- **Amazon ECR** = AWS's secure image repository 🔒
- **Docker Daemon** = The engine that creates and manages containers ⚙️
- **Hypervisor** = The manager that creates and runs virtual machines 🖥️
- **Virtual Machine** = A complete computer inside another computer 💻
- **Container** = An isolated application sharing the host OS kernel 📦
