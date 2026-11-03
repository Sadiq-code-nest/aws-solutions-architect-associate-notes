# 1 - Long Version Table

Absolutely. For **AWS SAA-C03**, the easiest way to remember AWS security is **not to memorize service names separately**. Instead, identify **what security problem the service solves**.

Below is a **master security table** covering the major AWS security services/features you are expected to recognize, including the ones you listed.

# 🔐 AWS SECURITY — COMPLETE MASTER TABLE

| Service / Feature | Security Category | Core Purpose — ONE LINE | What Problem Does It Solve? | Simple Example | ⭐ SAA-C03 Keyword / When to Choose |
| --- | --- | --- | --- | --- | --- |
| **IAM** | Identity & Access | Controls **who can access what** in AWS | Unauthorized AWS resource access | Give a developer read-only S3 access | **Users, roles, policies, permissions** |
| **IAM Roles** | Identity | Temporary permissions for AWS resources/users | Avoid storing long-term credentials | EC2 accesses S3 without access keys | **EC2/Lambda → AWS service** |
| **IAM Policies** | Authorization | Define Allow/Deny permissions | Control API actions | Allow `s3:GetObject` | **Who can perform what action** |
| **IAM Identity Center** | Workforce Identity | Centralized access to multiple AWS accounts | Manage workforce SSO | Employee logs into AWS with company identity | **SSO / multiple AWS accounts** |
| **AWS Organizations** | Governance | Centrally manage multiple AWS accounts | Account-level governance/security | Apply policies across accounts | **Multi-account management** |
| **SCP** | Governance | Restricts maximum permissions in Organizations | Prevent accounts from performing prohibited actions | Prevent all member accounts from disabling security services | **Organization-wide guardrail** |
| **AWS Resource Access Manager (RAM)** | Resource Sharing | Securely share AWS resources between accounts | Avoid duplicating resources | Share Transit Gateway with multiple accounts | **Cross-account resource sharing** |
| **Amazon Cognito** | Application Identity | Authentication/authorization for application users | Manage app users and login | Website users sign in with username/password | **Mobile/web app users** |
| **AWS KMS** | Encryption / Keys | Creates and manages encryption keys | Protect data with encryption | Encrypt S3, EBS, RDS using KMS | **Encryption key management** |
| **KMS Multi-Region Keys** | Encryption | Related KMS keys across Regions | Easier multi-Region encrypted applications | Encrypt in us-east-1 and decrypt in us-west-2 | **Cross-Region encryption** |
| **AWS CloudHSM** | Hardware Security | Dedicated customer-controlled HSM | Need hardware-backed key control | Highly regulated application needs dedicated HSM | **Dedicated HSM / customer control** |
| **AWS Certificate Manager (ACM)** | TLS/SSL | Creates/manages SSL/TLS certificates | Secure HTTPS connections | HTTPS on ALB/CloudFront | **SSL/TLS certificate** |
| **AWS Private CA** | Certificates | Creates private certificates/CA hierarchy | Internal PKI/certificates | Issue certificates to internal servers | **Private certificates / PKI** |
| **AWS Secrets Manager** | Secrets | Securely stores and rotates secrets | Prevent passwords/API keys in code | Store RDS password and rotate automatically | **Secrets + automatic rotation** |
| **SSM Parameter Store** | Configuration / Secrets | Stores configuration values and parameters | Avoid hardcoding configuration/secrets | `/prod/db/host`, SecureString password | **Parameters/config + simple secrets** |
| **AWS Systems Manager** | Operations / Security | Securely manage EC2/on-prem servers | Avoid SSH and manual management | Session Manager into private EC2 | **No SSH / no port 22** |
| **SSM Session Manager** | Secure Access | Shell access without SSH | Securely access private instances | Connect to private EC2 without public IP | **No SSH keys, no bastion, no port 22** |
| **AWS WAF** | Web Application Security | Filters malicious **HTTP/HTTPS requests** | SQL injection, XSS, bad IPs, bots | Block requests from malicious IP | **Layer 7 / HTTP / ALB / CloudFront / API Gateway** |
| **AWS Shield Standard** | DDoS | Automatic basic DDoS protection | Common network/transport DDoS attacks | Protect CloudFront/Route 53 | **Basic DDoS protection** |
| **AWS Shield Advanced** | DDoS | Advanced DDoS protection | Sophisticated/high-volume attacks | Critical public application under DDoS attack | **Advanced DDoS / critical workloads** |
| **AWS Firewall Manager** | Central Security Management | Centrally manages security rules across accounts | Consistent security across Organizations | Apply WAF rules to all ALBs | **Centralized security across accounts** |
| **AWS Network Firewall** | Network Firewall | Stateful network protection for VPC | Sophisticated VPC traffic inspection | Inspect/block outbound traffic | **VPC-wide Layer 3–7 inspection** |
| **Network ACL (NACL)** | Network Access Control | Stateless subnet-level traffic filtering | Control inbound/outbound subnet traffic | Deny a specific IP | **Subnet / stateless / inbound + outbound** |
| **Security Groups** | Network Access Control | Stateful instance-level virtual firewall | Control traffic to EC2/ENIs | Allow TCP 443 from ALB | **EC2/ENI / stateful** |
| **Amazon GuardDuty** | Threat Detection | Detects suspicious/malicious activity | Find compromised resources/accounts | Detect unusual API calls or crypto mining | **Threat detection / suspicious activity** |
| **Amazon Inspector** | Vulnerability Management | Automatically discovers vulnerabilities | Find vulnerable EC2/ECR/Lambda workloads | Detect vulnerable package in EC2 | **Vulnerabilities / CVEs** |
| **Amazon Macie** | Data Security | Discovers/classifies sensitive data in S3 | Find exposed sensitive information | Detect PII in S3 | **S3 + sensitive data + PII** |
| **AWS Security Hub** | Security Posture | Centralizes security findings | Security findings scattered across services/accounts | Consolidate GuardDuty + Inspector + Macie findings | **Central security dashboard/findings** |
| **Amazon Detective** | Security Investigation | Investigates security findings | Understand root cause/attack path | Investigate compromised EC2 after GuardDuty alert | **Investigate / root cause / relationships** |
| **Amazon Security Lake** | Security Data Lake | Centralizes security data | Need centralized security data for analysis | Collect logs/findings from multiple sources | **Central security data lake** |
| **AWS CloudTrail** | Audit / API Logging | Records AWS API activity | Determine who did what | Find who deleted an S3 bucket | **Who / what API / when** |
| **Amazon CloudWatch** | Monitoring | Monitors metrics/logs/events | Detect operational/security anomalies | Alarm when CPU spikes | **Metrics / logs / alarms** |
| **VPC Flow Logs** | Network Monitoring | Records network traffic metadata | Troubleshoot/review accepted/rejected traffic | Find why EC2 cannot reach another server | **src/dst IP, ports, ACCEPT/REJECT** |
| **AWS Config** | Configuration / Compliance | Tracks resource configurations | Determine whether resources comply with rules | Detect public S3 bucket | **Configuration compliance / history** |
| **AWS Config Rules** | Compliance | Evaluates resources against rules | Detect non-compliant configurations | EBS volumes must be encrypted | **Compliance rules** |
| **AWS Audit Manager** | Audit / Compliance | Collects evidence for audits | Reduce manual audit evidence collection | Gather evidence for PCI DSS | **Audit evidence / compliance** |
| **AWS Artifact** | Compliance Documents | Provides AWS compliance reports/documents | Prove AWS compliance to auditors | Download AWS SOC/ISO reports | **AWS compliance reports / agreements** |
| **AWS Trusted Advisor** | Recommendations | Checks account for security/cost/reliability issues | Identify common account weaknesses | Detect security group allowing unrestricted access | **Recommendations / account assessment** |
| **Amazon Inspector** | Vulnerability | Finds software/package vulnerabilities | Vulnerable EC2/ECR/Lambda | CVE discovered in package | **Vulnerability scanning** |
| **Amazon S3 Object Lock** | Data Protection | Prevents objects from being deleted/overwritten | Protect records from deletion/tampering | Compliance records retained for 7 years | **WORM / immutable data** |
| **S3 MFA Delete** | Data Protection | Requires MFA for certain versioning/delete operations | Protect against accidental/malicious deletion | Require MFA to permanently delete object version | **S3 + Versioning + MFA** |
| **S3 Bucket Policy** | Authorization | Resource-based access policy for S3 | Control bucket/object access | Allow another AWS account to access bucket | **S3 resource policy / cross-account** |
| **S3 Block Public Access** | Data Protection | Prevents public S3 access | Stop accidental data exposure | Company bucket should never be public | **Prevent public S3 access** |
| **S3 Encryption** | Encryption | Encrypts objects at rest | Protect stored data | SSE-S3/SSE-KMS/SSE-C | **S3 data at rest** |
| **S3 Default Encryption** | Encryption | Automatically encrypts new objects | Ensure objects are encrypted by default | Every uploaded object gets SSE-S3 | **Automatic encryption** |
| **S3 Bucket Policy — Force Encryption** | Encryption Enforcement | Rejects uploads that don't meet encryption requirements | Enforce encryption at upload | Deny `PutObject` without required encryption | **Force encryption / explicit Deny** |
| **S3 Replication + Encryption** | Data Protection | Replicates encrypted objects to another bucket | DR/compliance/cross-account copies | CRR encrypted data to another Region | **CRR/SRR + Versioning + KMS permissions** |
| **Encrypted AMI Sharing** | Encryption / EC2 | Shares encrypted AMI across accounts | Let another account launch from encrypted AMI | Share KMS-encrypted AMI with Account B | **AMI + KMS key + snapshot permissions** |
| **VPC Endpoint / PrivateLink** | Private Connectivity | Privately connects to AWS/services without public Internet | Avoid NAT/IGW for supported services | Private EC2 → S3/DynamoDB | **Private AWS service access** |
| **AWS PrivateLink / Endpoint Service** | Private Connectivity | Privately exposes your service to other VPCs | Avoid public Internet/VPC peering | Customer VPC privately accesses provider service | **NLB + endpoint service** |
| **Transit Gateway** | Network Architecture | Central network hub connecting VPCs/VPN/DX | Simplify complex network connectivity | 50 VPCs connected through one hub | **Transitive network connectivity** |
| **VPC Peering** | Network Connectivity | Private connection between two VPCs | Connect two VPCs | VPC-A ↔ VPC-B | **Non-transitive / non-overlapping CIDRs** |
| **AWS Network Firewall** | Network Security | Deep VPC traffic inspection/filtering | Block malicious network traffic | Block unwanted outbound protocol/domain | **Layer 3–7 VPC protection** |
| **Direct Connect** | Private Connectivity | Dedicated private connection to AWS | Avoid public Internet for on-prem connectivity | Data center → AWS | **Dedicated private connection** |
| **Site-to-Site VPN** | Secure Connectivity | IPsec encrypted tunnel from on-prem to AWS | Secure hybrid connectivity | Corporate DC → VPC | **Encrypted VPN over Internet** |
| **Egress-Only Internet Gateway** | IPv6 Security | Allows outbound IPv6 but blocks unsolicited inbound | Secure IPv6 private-subnet Internet access | EC2 → Internet, Internet cannot initiate → EC2 | **IPv6 + outbound only** |
| **CloudFront + HTTPS** | Web Security | Encrypts traffic and protects origin exposure | Secure global content delivery | HTTPS website through CloudFront | **TLS + CDN** |
| **Route 53 DNSSEC** | DNS Security | Protects DNS responses from tampering | DNS spoofing attacks | Validate DNS records cryptographically | **DNS security / DNSSEC** |
| **Amazon Cognito** | Application Security | Provides user authentication for applications | Build login/signup system | Mobile app user signs in | **Application user authentication** |
| **AWS Control Tower** | Governance / Security | Establishes secure multi-account AWS environment | Standardize account/security setup | Automatically enforce organizational guardrails | **Landing zone / multi-account governance** |

---

# 🧠 The MOST IMPORTANT Security Services — Don't Mix Them Up

This is the table I'd especially memorize for SAA-C03:

| If the question says... | Think... | Why? |
| --- | --- | --- |
| **Who can access AWS resources?** | **IAM** | Identity & permissions |
| **Application users need login** | **Cognito** | Application authentication |
| **Store encryption keys** | **KMS** | Managed encryption keys |
| **Dedicated HSM required** | **CloudHSM** | Customer-controlled HSM |
| **Store database password/API secret** | **Secrets Manager** | Secret storage + rotation |
| **Store configuration parameters** | **SSM Parameter Store** | Parameters/configuration |
| **HTTPS certificate** | **ACM** | TLS certificates |
| **SQL injection / XSS / malicious HTTP requests** | **WAF** | Layer 7 web protection |
| **DDoS attack** | **Shield** | DDoS protection |
| **Centralize WAF/security policies across accounts** | **Firewall Manager** | Central security management |
| **Entire VPC needs sophisticated traffic inspection** | **Network Firewall** | Network traffic filtering |
| **Suspicious activity / compromised account** | **GuardDuty** | Threat detection |
| **Software vulnerabilities / CVEs** | **Inspector** | Vulnerability management |
| **Sensitive data / PII in S3** | **Macie** | S3 data discovery/classification |
| **Centralize security findings** | **Security Hub** | Security posture/findings |
| **Investigate an existing security incident** | **Detective** | Investigation/root cause |
| **Centralize security logs/data** | **Security Lake** | Security data lake |
| **Who deleted this resource?** | **CloudTrail** | API activity/audit |
| **Is resource configured correctly?** | **AWS Config** | Configuration/compliance |
| **Collect evidence for an audit** | **Audit Manager** | Automated audit evidence |
| **Download AWS compliance reports** | **Artifact** | AWS compliance documents |
| **General AWS account recommendations** | **Trusted Advisor** | Account checks/recommendations |
| **EC2 cannot communicate / ACCEPT or REJECT traffic** | **VPC Flow Logs** | Network traffic metadata |
| **Prevent subnet traffic** | **NACL** | Stateless subnet firewall |
| **Protect EC2/ENI traffic** | **Security Group** | Stateful instance-level firewall |
| **Prevent S3 bucket from being public** | **S3 Block Public Access** | Prevent public access |
| **Make S3 data immutable** | **S3 Object Lock** | WORM/retention |
| **Require MFA for S3 version deletion** | **S3 MFA Delete** | MFA-protected deletion |
| **Private access to S3/DynamoDB** | **VPC Gateway Endpoint** | Private connectivity |
| **Private access to most AWS services** | **Interface Endpoint / PrivateLink** | Private ENI |
| **Connect many VPCs together** | **Transit Gateway** | Central network hub |
| **Connect exactly two VPCs** | **VPC Peering** | Direct private connection |
| **Dedicated on-prem → AWS connection** | **Direct Connect** | Private dedicated link |
| **Encrypted on-prem → AWS connection** | **Site-to-Site VPN** | IPsec tunnel |

---

# 🛡️ Security Services by Security Layer

This makes the overall AWS security architecture much easier to visualize:

```
                    AWS SECURITY
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
     IDENTITY          DATA             NETWORK
        │                │                │
 IAM / IAM Roles       KMS             Security Groups
 IAM Identity Center   CloudHSM        NACL
 Cognito               Secrets Mgr     WAF
 SCP                   S3 Encryption   Shield
                       Object Lock     Network Firewall
                       Macie           PrivateLink
```

Then:

```
              DETECTION & RESPONSE
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
   GuardDuty       Inspector        Macie
       │               │                │
       └───────────────┼────────────────┘
                       ▼
                 Security Hub
                       │
                       ▼
                  Detective
```

And:

```
            AUDIT / GOVERNANCE
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
   CloudTrail     Config      Audit Manager
       │            │            │
       ▼            ▼            ▼
   API history   Compliance   Audit evidence
                    │
                    ▼
                 Artifact
              AWS reports
```

---

# 🔑 KMS vs Secrets Manager vs Parameter Store vs CloudHSM

One of the easiest places to get confused:

|  | **KMS** | **Secrets Manager** | **SSM Parameter Store** | **CloudHSM** |
| --- | --- | --- | --- | --- |
| Main purpose | Encryption keys | Secrets | Configuration/parameters | Dedicated HSM |
| Stores passwords? | ❌ Not its primary purpose | ✅ | ✅ SecureString | ❌ |
| Manages encryption keys? | ✅ | Uses KMS | Can use KMS | ✅ |
| Automatic secret rotation | ❌ | ✅ | Limited/manual patterns | ❌ |
| Dedicated hardware | AWS-managed KMS infrastructure | No | No | ✅ HSM |
| Typical example | Encrypt EBS/S3/RDS | RDS password | `/prod/db/host` | Regulatory HSM requirement |
| Exam keyword | **Encryption** | **Secret + rotation** | **Configuration** | **Dedicated HSM/control** |

### Memory:

> **KMS = Keys**
> 
> 
> **Secrets Manager = Secrets**
> 
> **Parameter Store = Parameters**
> 
> **CloudHSM = Hardware**
> 

---

# 🔍 GuardDuty vs Inspector vs Macie vs Security Hub vs Detective

This is **extremely important** for SAA:

| Service | Ask yourself | Finds/does what? |
| --- | --- | --- |
| **GuardDuty** | "Is something suspicious happening?" | Threat detection |
| **Inspector** | "Is my workload vulnerable?" | Software/package vulnerabilities |
| **Macie** | "Do I have sensitive data exposed?" | Sensitive data in S3 |
| **Security Hub** | "What security findings do I have across AWS?" | Centralizes security findings/posture |
| **Detective** | "What actually happened?" | Investigates security incidents |

### Example

Suppose an EC2 instance gets compromised:

```
                    EC2
                     │
                     ▼
                 GuardDuty
              "Suspicious activity!"
                     │
                     ▼
                Security Hub
             "Central finding"
                     │
                     ▼
                 Detective
           "Investigate what happened"
                     │
                     ▼
                 Inspector
          "Is vulnerable software involved?"
```

---

# 🔥 WAF vs Shield vs Network Firewall vs Security Group vs NACL

Another **must-know SAA comparison**:

| Service | Layer / Scope | Primary purpose | Example |
| --- | --- | --- | --- |
| **Security Group** | EC2/ENI | Stateful traffic filtering | Allow TCP 443 |
| **NACL** | Subnet | Stateless traffic filtering | Deny IP `1.2.3.4` |
| **WAF** | HTTP/HTTPS | Web/application attacks | Block SQL injection |
| **Shield** | Network/application DDoS protection | DDoS mitigation | Protect public application |
| **Network Firewall** | VPC/network traffic | Advanced network inspection | Block protocols/domains/IPs |

### Super-simple memory:

> **SG = Instance**
> 

> **NACL = Subnet**
> 

> **WAF = Web requests**
> 

> **Shield = DDoS**
> 

> **Network Firewall = VPC traffic inspection**
> 

---

# 🔐 Security Detection vs Investigation vs Compliance

Don't mix these three:

| Goal | Service |
| --- | --- |
| **Detect threat** | GuardDuty |
| **Find vulnerability** | Inspector |
| **Find sensitive S3 data** | Macie |
| **Centralize findings** | Security Hub |
| **Investigate incident** | Detective |
| **Collect security data** | Security Lake |
| **Record AWS API activity** | CloudTrail |
| **Check configuration compliance** | AWS Config |
| **Collect audit evidence** | Audit Manager |
| **Get AWS compliance documents** | Artifact |
| **General account recommendations** | Trusted Advisor |

---

# ⭐ FINAL SAA-C03 SECURITY CHEAT SHEET

If you only have **5 minutes before the exam**, remember this:

```
IDENTITY
IAM              → Who can access AWS?
Cognito          → Who can access my application?
IAM Identity Center → Workforce SSO

ENCRYPTION
KMS              → Encryption keys
CloudHSM         → Dedicated HSM
Secrets Manager  → Secrets + rotation
Parameter Store  → Configuration/parameters
ACM              → TLS/SSL certificates

NETWORK
Security Group   → Stateful / EC2 / ENI
NACL             → Stateless / Subnet
WAF              → HTTP/HTTPS attacks
Shield           → DDoS
Network Firewall → VPC-wide advanced traffic inspection

THREAT DETECTION
GuardDuty        → Threat detection
Inspector        → Vulnerabilities
Macie            → Sensitive S3 data
Security Hub     → Central security findings
Detective        → Investigation
Security Lake    → Central security data

AUDIT
CloudTrail       → Who did what API action?
Config           → What is the resource configuration?
Audit Manager    → Audit evidence
Artifact         → AWS compliance reports
Trusted Advisor  → AWS account recommendations

S3 SECURITY
Bucket Policy    → S3 resource permissions
Block Public Access → Prevent public exposure
S3 Encryption    → Encrypt objects
MFA Delete       → Protect version deletion
Object Lock      → Immutable/WORM data

PRIVATE NETWORK
VPC Endpoint     → Private AWS service access
PrivateLink      → Private service-to-service connectivity
Transit Gateway  → Connect many VPCs
VPC Peering      → Connect two VPCs
DX               → Dedicated private connection
VPN              → Encrypted tunnel
```

## 🧠 The ultimate memory pattern

**WHO?** → IAM

**APP USER?** → Cognito

**KEY?** → KMS

**SECRET?** → Secrets Manager

**PARAMETER?** → Parameter Store

**CERTIFICATE?** → ACM

**WEB ATTACK?** → WAF

**DDoS?** → Shield

**VPC TRAFFIC?** → Network Firewall

**SUSPICIOUS ACTIVITY?** → GuardDuty

**VULNERABILITY?** → Inspector

**SENSITIVE S3 DATA?** → Macie

**CENTRAL FINDINGS?** → Security Hub

**INVESTIGATE?** → Detective

**API AUDIT?** → CloudTrail

**CONFIGURATION COMPLIANCE?** → Config

**AUDIT EVIDENCE?** → Audit Manager

**AWS COMPLIANCE REPORT?** → Artifact

**GENERAL ACCOUNT CHECK?** → Trusted Advisor

**That mapping is the core security decision-making skill you need for SAA-C03.**
