# POSIX (Q48)

## What is a POSIX-compliant shared file system?

**POSIX** is a set of standards that define how operating systems, especially **Linux/Unix**, work with files and directories.

So when AWS says:

> **POSIX-compliant shared file system**
> 

think:

> **A Linux-compatible filesystem that multiple servers can mount and access at the same time.**
> 

### Simple example

You have multiple EC2 servers:

```
        CMS
         │
   ┌─────┼─────┐
   ▼     ▼     ▼
 EC2-1 EC2-2 EC2-3
   │     │     │
   └─────┼─────┘
         ▼
       EFS
    /shared/files
```

All EC2 instances can access:

```
/shared/files
   ├── document1.pdf
   ├── image.jpg
   └── report.docx
```

They can use normal Linux filesystem operations such as:

```
read
write
create
delete
rename
directories
file permissions
```

That's what **POSIX-compliant** means in this context.
