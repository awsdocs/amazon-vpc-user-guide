# Subnets for your VPC<a name="configure-subnets"></a>

A *subnet* is a range of IP addresses in your VPC\. You can create AWS resources, such as EC2 instances, in specific subnets\.

**Topics**
+ [Subnet basics](#subnet-basics)
+ [Subnet security](#subnet-security)
+ [Create a subnet](create-subnets.md)
+ [Configure your subnets](modify-subnets.md)
+ [Subnet CIDR reservations](subnet-cidr-reservation.md)
+ [Route tables](VPC_Route_Tables.md)
+ [Delete a subnet](subnet-deleting.md)

## Subnet basics<a name="subnet-basics"></a>

Each subnet must reside entirely within one Availability Zone and cannot span zones\. By launching AWS resources in separate Availability Zones, you can protect your applications from the failure of a single Availability Zone\.

**Topics**
+ [Subnet IP address range](#subnet-ip-address-range)
+ [Subnet types](#subnet-types)
+ [Subnet diagram](#subnet-diagram)
+ [Subnet routing](#subnet-routing)
+ [Subnet settings](#subnet-settings)

### Subnet IP address range<a name="subnet-ip-address-range"></a>

When you create a subnet, you specify its IP addresses, depending on the configuration of the VPC:
+ **IPv4 only** – The subnet has an IPv4 CIDR block but does not have an IPv6 CIDR block\. Resources in an IPv4\-only subnet must communicate over IPv4\.
+ **Dual stack** – The subnet has both an IPv4 CIDR block and an IPv6 CIDR block\. The VPC must have both an IPv4 CIDR block and an IPv6 CIDR block\. Resources in a dual\-stack subnet can communicate over IPv4 and IPv6\.
+ **IPv6 only** – The subnet has an IPv6 CIDR block but does not have an IPv4 CIDR block\. The VPC must have an IPv6 CIDR block\. Resources in an IPv6\-only subnet must communicate over IPv6\.

Regardless of the type of subnet, the internal IPv4 address range of the subnet is always private—we do not announce the address block to the internet\. For more information, see [IP addressing for your VPCs and subnets](vpc-ip-addressing.md)\.

### Subnet types<a name="subnet-types"></a>

Depending on how you configure routing for your subnets, they are considered either public, private, or VPN\-only:
+ **Public subnet** – The subnet has a direct route to an [internet gateway](VPC_Internet_Gateway.md)\. Resources in a public subnet can access the public internet\.
+ **Private subnet** – The subnet does not have a direct route to an internet gateway\. Resources in a private subnet require a [NAT device](vpc-nat.md) to access the public internet\.
+ **VPN\-only subnet** – The subnet has a route to a [Site\-to\-Site VPN connection](https://docs.aws.amazon.com/vpn/latest/s2svpn/) through a virtual private gateway\. The subnet does not have a route to an internet gateway\.

### Subnet diagram<a name="subnet-diagram"></a>

The following diagram shows two VPCs in a Region\. Each VPC has public and private subnets and an internet gateway\. You can optionally add subnets in a Local Zone, as shown in the diagram\.

![\[A VPC with subnets in Availability Zones and a Local Zone.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/subnet-diagram.png)

A Local Zone is an AWS infrastructure deployment that places compute, storage, and database services closer to your end users\. A Local Zone enables your end users to run applications that require single\-digit millisecond latencies\. For more information, see [AWS Local Zones](https://docs.aws.amazon.com/local-zones/latest/ug/)\.

### Subnet routing<a name="subnet-routing"></a>

Each subnet must be associated with a route table, which specifies the allowed routes for outbound traffic leaving the subnet\. Every subnet that you create is automatically associated with the main route table for the VPC\. You can change the association, and you can change the contents of the main route table\. For more information, see [Configure route tables](VPC_Route_Tables.md)\.

### Subnet settings<a name="subnet-settings"></a>

All subnets have a modifiable attribute that determines whether a network interface created in that subnet is assigned a public IPv4 address and, if applicable, an IPv6 address\. This includes the primary network interface \(eth0\) that's created for an instance when you launch an instance in that subnet\. Regardless of the subnet attribute, you can still override this setting for a specific instance during launch\.

After you create a subnet, you can modify the following settings for the subnet:
+ **Auto\-assign IP settings**: Enables you to configure the auto\-assign IP settings to automatically request a public IPv4 or IPv6 address for a new network interface in this subnet\.
+ **Resource\-based Name \(RBN\) settings**: Enables you to specify the hostname type for EC2 instances in this subnet and configure how DNS A and AAAA record queries are handled\. For more information, see [Amazon EC2 instance hostname types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-naming.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Subnet security<a name="subnet-security"></a>

To protect your AWS resources, we recommend that you use private subnets\. Use a bastion host or NAT device to provide internet access to resources, such as EC2 instances, in a private subnet\.

AWS provides features that you can use to increase security for the resources in your VPC\. *Security groups* allow inbound and outbound traffic for associated resources, such as EC2 instances\. *Network ACLs* allow or deny inbound and outbound traffic at the subnet level\. In most cases, security groups can meet your needs\. However, you can use network ACLs if you want an additional layer of security\. For more information, see [Compare security groups and network ACLs](VPC_Security.md#VPC_Security_Comparison)\.

By design, each subnet must be associated with a network ACL\. Every subnet that you create is automatically associated with the default network ACL for the VPC\. The default network ACL allows all inbound and outbound traffic\. You can update the default network ACL, or create custom network ACLs and associate them with your subnets\. For more information, see [Control traffic to subnets using Network ACLs](vpc-network-acls.md)\.

You can create a flow log on your VPC or subnet to capture the traffic that flows to and from the network interfaces in your VPC or subnet\. You can also create a flow log on an individual network interface\. For more information, see [Logging IP traffic using VPC Flow Logs](flow-logs.md)\.