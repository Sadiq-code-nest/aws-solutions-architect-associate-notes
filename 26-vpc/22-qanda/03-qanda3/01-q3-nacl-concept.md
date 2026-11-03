# Q3 - NACL Concept

A company hosted a web application on a Linux Amazon EC2 instance in the public subnet that uses a non-default network ACL. The instance uses a default security group and has an attached Elastic IP address. The network ACL is configured to block all inbound and outbound traffic. The Solutions Architect must allow incoming traffic on port 443 to access the application from any source.

Which combination of steps will accomplish this requirement? (Select TWO.)

- *In the Network ACL, update the rule to allow both inbound and outbound TCP connection on port 443 from source **`0.0.0.0/0`** and to destination ****`0.0.0.0/0`**
- *In the Security Group, add a new rule to allow TCP connection on port 443 from source ****`0.0.0.0/0`**
- In the Network ACL, update the rule to allow outbound TCP connection on port `32768 - 65535` to destination `0.0.0.0/0`
- In the Security Group, create a new rule to allow TCP connection on port 443 to destination `0.0.0.0/0`
- *In the Network ACL, update the rule to allow inbound TCP connection on port 443 from source **`0.0.0.0/0`** and outbound TCP connection on port **`32768 - 65535`** to destination ****`0.0.0.0/0`**
