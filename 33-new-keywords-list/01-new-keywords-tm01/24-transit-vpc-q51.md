# What's a Transit VPC?

This is an additional concept hidden inside Option 2.

A **Transit VPC** was an older architectural pattern for connecting multiple VPCs through a central transit VPC using VPN connections.

Today, you should generally think of:

### AWS Transit Gateway

for centralized VPC/network connectivity at scale.

For SAA:

```
Multiple VPCs
     ↓
Transit Gateway
```

is generally the modern pattern.

But this question isn't really testing Transit VPC.

It's trying to tempt you into building a complicated multi-region architecture.
