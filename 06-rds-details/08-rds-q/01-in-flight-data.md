# In-flight data

An application is hosted in an Auto Scaling group of EC2 instances and a Microsoft SQL Server on Amazon RDS. There is a requirement that all in-flight data between your web servers and RDS should be secured.

Which of the following options is the MOST suitable solution that you should implement? (Select TWO.)

- **Force all connections to your DB instance to use SSL by setting the `rds.force_ssl` parameter to true. Once done, reboot your DB instance.**
- Specify the TDE option in an RDS option group that is associated with that DB instance to enable transparent data encryption (TDE).
- Enable the IAM DB authentication in RDS using the AWS Management Console.
- **Download the Amazon RDS Root CA certificate. Import the certificate to your servers and configure your application to use SSL to encrypt the connection to RDS.**
- Configure the security groups of your EC2 instances and RDS to only allow traffic to and from port 443.
