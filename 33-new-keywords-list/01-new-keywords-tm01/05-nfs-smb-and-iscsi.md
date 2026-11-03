# NFS, SMB, and iSCSI

| Topic | Simply means | Think |
| --- | --- | --- |
| **FSx for NetApp ONTAP** | Managed AWS **enterprise file storage** supporting NFS, SMB, iSCSI | 🗄️ **Storage system** |
| **NFS** | File-sharing protocol mainly for **Linux/Unix** | 🐧 **Linux → Files** |
| **SMB** | File-sharing protocol mainly for **Windows** | 🪟 **Windows → Files** |
| **iSCSI** | Protocol for **block storage over a network** | 💽 **Disk/Blocks** |

### Remember this:

```
FSx for NetApp ONTAP
        │
   ┌────┼────┐
   ↓    ↓    ↓
  NFS  SMB  iSCSI
   ↓    ↓    ↓
 Files Files Blocks
   ↓    ↓    ↓
Linux Windows Disk
```

**SAA-C03 shortcut:**

- 🐧 **Linux shared files → NFS**
- 🪟 **Windows shared files → SMB**
- 💽 **Block storage / LUN → iSCSI**
- 🗄️ **Need NetApp ONTAP capabilities → FSx for NetApp ONTAP**
