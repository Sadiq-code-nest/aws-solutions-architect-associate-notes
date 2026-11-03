# EC2 Instance Connect

## EC2 Instance Connect — SAA-C03 Short Note

**Purpose:** Securely connect to an **EC2 instance through the AWS Console/browser** without using your downloaded `.pem` key.

| Point | SAA-C03 Key |
| --- | --- |
| **Connection** | SSH into EC2 **from browser** |
| **SSH key** | ❌ No permanent `.pem` key required |
| **How it works** | AWS temporarily pushes a **temporary public key** to the instance |
| **Security** | Temporary key → short-lived access |
| **OS** | Works **out-of-the-box with Amazon Linux 2** |
| **Network** | **TCP port 22 must be allowed** in Security Group |
| **Use case** | Quick browser-based SSH access/troubleshooting |

### 🧠 Exam Trigger

> **“Connect to EC2 from the AWS Console/browser without a key pair”**
> 
> 
> → **EC2 Instance Connect**
> 

### ⚠️ Don't confuse

- **EC2 Instance Connect** → browser-based SSH + temporary key
- **SSH** → normally requires your private `.pem` key
- **SSM Session Manager** → can connect **without opening port 22** and without SSH keys

**One-liner:**

**EC2 Instance Connect = Browser SSH + temporary key + Port 22 required.**
