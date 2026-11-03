# Reference Q

#### Question

A company is using Amazon VPC that has a CIDR block of `10.31.0.0/27` that is connected to the on-premises data center. There was a requirement to create a Lambda function that will process massive amounts of cryptocurrency transactions every minute and then store the results to EFS. After setting up the serverless architecture and connecting the Lambda function to the VPC, the Solutions Architect noticed an increase in invocation errors with EC2 error types such as `EC2ThrottledException` at certain times of the day.

Which of the following are the possible causes of this issue? (Select TWO.)

- Your VPC does not have a NAT gateway.
- The attached IAM execution role of your function does not have the necessary permissions to access the resources of your VPC.
- Your Lambda function exceeds the VPC quota for Elastic Network Interfaces (ENIs) or available IP addresses in the subnet.
- The associated security group of your function does not allow outbound connections.
- The Lambda function is placed in a VPC subnet with limited IP address capacity.
