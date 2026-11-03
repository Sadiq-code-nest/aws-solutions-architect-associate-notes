# Amazon AppStream 2.0

## Amazon AppStream 2.0 — SAA-C03 Short Note

**Amazon AppStream 2.0** is a **fully managed application streaming service** that lets users run desktop applications from AWS **without installing the application on their local computer**.

Think:

> **AppStream 2.0 = Stream Windows applications from AWS to users.**
> 

### Simple example

A company has a heavy Windows application:

```
Company Application
        ↓
   AppStream 2.0
        ↓
      AWS
        ↓
Employee's Browser
```

The application runs on AWS, while the user interacts with it through a browser.

### Why use it?

- Centralize application management
- Avoid installing software on every user's computer
- Users can access applications from different devices
- Useful for graphics-heavy or specialized Windows applications
- Scale applications for many users

### 🧠 Exam shortcut

If the question says:

> **"Users need to access/run a Windows desktop application without installing it locally."**
> 

👉 **Amazon AppStream 2.0**

### Don't confuse:

- **AppStream 2.0** → Stream **applications/desktops to users**
- **WorkSpaces** → Provide users with **persistent virtual desktops**
- **EC2** → General-purpose virtual servers

**Memory trick:**

> **AppStream = Application streaming**.
>
