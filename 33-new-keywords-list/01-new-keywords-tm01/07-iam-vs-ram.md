# IAM vs RAM

This is probably the most important conceptual distinction in this question.

Imagine a company owns a car.

### IAM

IAM asks:

> **Who is allowed to drive the car?**
> 

Example:

```
Alice → allowed to drive
Bob → not allowed
```

That's permissions.

---

### RAM

RAM asks:

> **Can another department use the company's shared car?**
> 

Example:

```
Central Department
       │
     Car
       │
      RAM
    /     \
Team A   Team B
```

That's resource sharing.

So:

> **IAM = permission**
> 

> **RAM = resource sharing**
>
