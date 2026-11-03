# DOJO Q

An insurance company utilizes SAP HANA for its day-to-day ERP operations. Since it can’t migrate this database due to customer preferences, it needs to integrate it with the current AWS workload in the VPC, in which it is required to establish a site-to-site VPN connection.

What needs to be configured outside of the VPC for an AWS Virtual Private Network connection to succeed?

- An EIP to the Virtual Private Gateway
- **An Internet-routable IP address (static) of the customer gateway's external interface for the on-premises network**
- A dedicated NAT instance in a public subnet
- The main route table in your VPC to route traffic through a NAT instance
