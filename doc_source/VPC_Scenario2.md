# VPC with public and private subnets \(NAT\)<a name="VPC_Scenario2"></a>

The configuration for this scenario includes a virtual private cloud \(VPC\) with a public subnet and a private subnet\. We recommend this scenario if you want to run a public\-facing web application, while maintaining back\-end servers that aren't publicly accessible\. A common example is a multi\-tier website, with the web servers in a public subnet and the database servers in a private subnet\. You can set up security and routing so that the web servers can communicate with the database servers\.

The instances in the public subnet can send outbound traffic directly to the Internet, whereas the instances in the private subnet can't\. Instead, the instances in the private subnet can access the Internet by using a network address translation \(NAT\) gateway that resides in the public subnet\. The database servers can connect to the Internet for software updates using the NAT gateway, but the Internet cannot establish connections to the database servers\.

**Note**  
You can also use the VPC wizard to configure a VPC with a NAT instance; however, we recommend that you use a NAT gateway\. For more information, see [NAT gateways](vpc-nat-gateway.md)\.

This scenario can also be optionally configured for IPv6—you can use the VPC wizard to create a VPC and subnets with associated IPv6 CIDR blocks\. Instances launched into the subnets can receive IPv6 addresses, and communicate using IPv6\. Instances in the private subnet can use an egress\-only Internet gateway to connect to the Internet over IPv6, but the Internet cannot establish connections to the private instances over IPv6\. For more information about IPv4 and IPv6 addressing, see [IP Addressing in your VPC](vpc-ip-addressing.md)\.

For information about managing your EC2 instance software, see [Managing software on your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Overview](#Configuration-2)
+ [Routing](#VPC_Scenario2_Routing)
+ [Security](#VPC_Scenario2_Security)
+ [Implementing scenario 2](#VPC_Scenario2_Implementation)
+ [Implementing scenario 2 with a NAT instance](#vpc-scenario-2-nat-instance)
+ [Recommended network ACL rules for a VPC with public and private subnets \(NAT\)](#nacl-rules-scenario-2)

## Overview<a name="Configuration-2"></a>

The following diagram shows the key components of the configuration for this scenario\.

![\[Diagram for scenario 2: VPC with public and private subnets\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/nat-gateway-diagram.png)

The configuration for this scenario includes the following:
+ A VPC with a size /16 IPv4 CIDR block \(example: 10\.0\.0\.0/16\)\. This provides 65,536 private IPv4 addresses\.
+ A public subnet with a size /24 IPv4 CIDR block \(example: 10\.0\.0\.0/24\)\. This provides 256 private IPv4 addresses\. A public subnet is a subnet that's associated with a route table that has a route to an Internet gateway\.
+ A private subnet with a size /24 IPv4 CIDR block \(example: 10\.0\.1\.0/24\)\. This provides 256 private IPv4 addresses\.
+ An Internet gateway\. This connects the VPC to the Internet and to other AWS services\.
+ Instances with private IPv4 addresses in the subnet range \(examples: 10\.0\.0\.5, 10\.0\.1\.5\)\. This enables them to communicate with each other and other instances in the VPC\. 
+ Instances in the public subnet with Elastic IPv4 addresses \(example: 198\.51\.100\.1\), which are public IPv4 addresses that enable them to be reached from the Internet\. The instances can have public IP addresses assigned at launch instead of Elastic IP addresses\. Instances in the private subnet are back\-end servers that don't need to accept incoming traffic from the Internet and therefore do not have public IP addresses; however, they can send requests to the Internet using the NAT gateway \(see the next bullet\)\. 
+ A NAT gateway with its own Elastic IPv4 address\. Instances in the private subnet can send requests to the Internet through the NAT gateway over IPv4 \(for example, for software updates\)\.
+ A custom route table associated with the public subnet\. This route table contains an entry that enables instances in the subnet to communicate with other instances in the VPC over IPv4, and an entry that enables instances in the subnet to communicate directly with the Internet over IPv4\.
+ The main route table associated with the private subnet\. The route table contains an entry that enables instances in the subnet to communicate with other instances in the VPC over IPv4, and an entry that enables instances in the subnet to communicate with the Internet through the NAT gateway over IPv4\.

For more information about subnets, see [VPCs and subnets](VPC_Subnets.md)\. For more information about Internet gateways, see [Internet gateways](VPC_Internet_Gateway.md)\. For more information about NAT gateways, see [NAT gateways](vpc-nat-gateway.md)\.

### Overview for IPv6<a name="vpc-scenario-2-overview-ipv6"></a>

You can optionally enable IPv6 for this scenario\. In addition to the components listed above, the configuration includes the following:
+ A size /56 IPv6 CIDR block associated with the VPC \(example: 2001:db8:1234:1a00::/56\)\. Amazon automatically assigns the CIDR; you cannot choose the range yourself\.
+ A size /64 IPv6 CIDR block associated with the public subnet \(example: 2001:db8:1234:1a00::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the VPC IPv6 CIDR block\.
+ A size /64 IPv6 CIDR block associated with the private subnet \(example: 2001:db8:1234:1a01::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the subnet IPv6 CIDR block\.
+ IPv6 addresses assigned to the instances from the subnet range \(example: 2001:db8:1234:1a00::1a\)\.
+ An egress\-only Internet gateway\. This enables instances in the private subnet to send requests to the Internet over IPv6 \(for example, for software updates\)\. An egress\-only Internet gateway is necessary if you want instances in the private subnet to be able to initiate communication with the Internet over IPv6\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.
+ Route table entries in the custom route table that enable instances in the public subnet to use IPv6 to communicate with each other, and directly over the Internet\.
+ Route table entries in the main route table that enable instances in the private subnet to use IPv6 to communicate with each other, and to communicate with the Internet through an egress\-only Internet gateway\.

![\[IPv6-enabled VPC with a public and private subnet\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/scenario-2-ipv6-diagram.png)

## Routing<a name="VPC_Scenario2_Routing"></a>

In this scenario, the VPC wizard updates the main route table used with the private subnet, and creates a custom route table and associates it with the public subnet\.

In this scenario, all traffic from each subnet that is bound for AWS \(for example, to the Amazon EC2 or Amazon S3 endpoints\) goes over the Internet gateway\. The database servers in the private subnet can't receive traffic from the Internet directly because they don't have Elastic IP addresses\. However, the database servers can send and receive Internet traffic through the NAT device in the public subnet\. 

Any additional subnets that you create use the main route table by default, which means that they are private subnets by default\. If you want to make a subnet public, you can always change the route table that it's associated with\.

The following tables describe the route tables for this scenario\.

### Main route table<a name="scenario-2-main-route-table"></a>

The first entry is the default entry for local routing in the VPC; this entry enables the instances in the VPC to communicate with each other\. The second entry sends all other subnet traffic to the NAT gateway \(for example, `nat-12345678901234567`\)\.


| Destination | Target | 
| --- | --- | 
|  `10.0.0.0/16`  |  local  | 
|  `0.0.0.0/0`  |  *nat\-gateway\-id*  | 

### Custom route table<a name="scenario-2-custom-route-table"></a>

The first entry is the default entry for local routing in the VPC; this entry enables the instances in this VPC to communicate with each other\. The second entry routes all other subnet traffic to the Internet over the Internet gateway \(for example, `igw-1a2b3d4d`\)\.


| Destination | Target | 
| --- | --- | 
|  `10.0.0.0/16`  |  local  | 
|  `0.0.0.0/0`  |  *igw\-id*  | 

### Routing for IPv6<a name="vpc-scenario-2-routing-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, your route tables must include separate routes for IPv6 traffic\. The following tables show the route tables for this scenario if you choose to enable IPv6 communication in your VPC\. 

**Main route table**

The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. The fourth entry routes all other IPv6 subnet traffic to the egress\-only Internet gateway\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *nat\-gateway\-id*  | 
|  ::/0  | egress\-only\-igw\-id | 

**Custom route table**

The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. The fourth entry routes all other IPv6 subnet traffic to the Internet gateway\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *igw\-id*  | 
|  ::/0  | igw\-id | 

## Security<a name="VPC_Scenario2_Security"></a>

AWS provides two features that you can use to increase security in your VPC: *security groups* and *network ACLs*\. Security groups control inbound and outbound traffic for your instances, and network ACLs control inbound and outbound traffic for your subnets\. In most cases, security groups can meet your needs; however, you can also use network ACLs if you want an additional layer of security for your VPC\. For more information, see [Internetwork traffic privacy in Amazon VPC](VPC_Security.md)\. 

For scenario 2, you'll use security groups but not network ACLs\. If you'd like to use a network ACL, see [Recommended network ACL rules for a VPC with public and private subnets \(NAT\)](#nacl-rules-scenario-2)\.

Your VPC comes with a [default security group](VPC_SecurityGroups.md#DefaultSecurityGroup)\. An instance that's launched into the VPC is automatically associated with the default security group if you don't specify a different security group during launch\. For this scenario, we recommend that you create the following security groups instead of using the default security group:
+ **WebServerSG**: Specify this security group when you launch the web servers in the public subnet\.
+ **DBServerSG**: Specify this security group when you launch the database servers in the private subnet\.

The instances assigned to a security group can be in different subnets\. However, in this scenario, each security group corresponds to the type of role an instance plays, and each role requires the instance to be in a particular subnet\. Therefore, in this scenario, all instances assigned to a security group are in the same subnet\.

The following table describes the recommended rules for the WebServerSG security group, which allow the web servers to receive Internet traffic, as well as SSH and RDP traffic from your network\. The web servers can also initiate read and write requests to the database servers in the private subnet, and send traffic to the Internet; for example, to get software updates\. Because the web server doesn't initiate any other outbound communication, the default outbound rule is removed\.

**Note**  
These recommendations include both SSH and RDP access, and both Microsoft SQL Server and MySQL access\. For your situation, you might only need rules for Linux \(SSH and MySQL\) or Windows \(RDP and Microsoft SQL Server\)\.


**WebServerSG: recommended rules**  

| 
| 
| **Inbound** | 
| --- |
|  Source  |  Protocol  |  Port range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow inbound HTTP access to the web servers from any IPv4 address\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow inbound HTTPS access to the web servers from any IPv4 address\.  | 
|  Your home network's public IPv4 address range  |  TCP  |  22  |  Allow inbound SSH access to Linux instances from your home network \(over the Internet gateway\)\. You can get the public IPv4 address of your local computer using a service such as [http://checkip\.amazonaws\.com](http://checkip.amazonaws.com) or [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com)\. If you are connecting through an ISP or from behind your firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.   | 
|  Your home network's public IPv4 address range  |  TCP  |  3389  |  Allow inbound RDP access to Windows instances from your home network \(over the Internet gateway\)\.  | 
|   **Outbound**   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Comments  | 
|  The ID of your DBServerSG security group  |  TCP  |  1433  |  Allow outbound Microsoft SQL Server access to the database servers assigned to the DBServerSG security group\.  | 
|  The ID of your DBServerSG security group  |  TCP  |  3306  |  Allow outbound MySQL access to the database servers assigned to the DBServerSG security group\.  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow outbound HTTP access to any IPv4 address\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow outbound HTTPS access to any IPv4 address\.  | 

The following table describes the recommended rules for the DBServerSG security group, which allow read or write database requests from the web servers\. The database servers can also initiate traffic bound for the Internet \(the route table sends that traffic to the NAT gateway, which then forwards it to the Internet over the Internet gateway\)\.


**DBServerSG: recommended rules**  

| 
| 
| **Inbound** | 
| --- |
|  Source  |  Protocol  |  Port range  |  Comments  | 
|  The ID of your WebServerSG security group  |  TCP  |  1433  |  Allow inbound Microsoft SQL Server access from the web servers associated with the WebServerSG security group\.  | 
|  The ID of your WebServerSG security group  |  TCP  |  3306  |  Allow inbound MySQL Server access from the web servers associated with the WebServerSG security group\.  | 
|   **Outbound**   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow outbound HTTP access to the Internet over IPv4 \(for example, for software updates\)\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow outbound HTTPS access to the Internet over IPv4 \(for example, for software updates\)\.  | 

\(Optional\) The default security group for a VPC has rules that automatically allow assigned instances to communicate with each other\. To allow that type of communication for a custom security group, you must add the following rules:


| 
| 
| **Inbound** | 
| --- |
|  Source  |  Protocol  |  Port range  |  Comments  | 
|  The ID of the security group  |  All  |  All  |  Allow inbound traffic from other instances assigned to this security group\.  | 
| **Outbound** | 
| --- |
| Destination  |  Protocol  |  Port range  |  Comments  | 
| The ID of the security group | All | All | Allow outbound traffic to other instances assigned to this security group\. | 

\(Optional\) If you launch a bastion host in your public subnet to use as a proxy for SSH or RDP traffic from your home network to your private subnet, add a rule to the DBServerSG security group that allows inbound SSH or RDP traffic from the bastion instance or its associated security group\.

### Security group rules for IPv6<a name="vpc-scenario-2-security-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, you must add separate rules to your WebServerSG and DBServerSG security groups to control inbound and outbound IPv6 traffic for your instances\. In this scenario, the web servers will be able to receive all Internet traffic over IPv6, and SSH or RDP traffic from your local network over IPv6\. They can also initiate outbound IPv6 traffic to the Internet\. The database servers can initiate outbound IPv6 traffic to the Internet\.

The following are the IPv6\-specific rules for the WebServerSG security group \(which are in addition to the rules listed above\)\.


| 
| 
| **Inbound** | 
| --- |
|  Source  |  Protocol  |  Port range  |  Comments  | 
|  ::/0  |  TCP  |  80  |  Allow inbound HTTP access to the web servers from any IPv6 address\.  | 
|  ::/0  |  TCP  |  443  |  Allow inbound HTTPS access to the web servers from any IPv6 address\.  | 
|  IPv6 address range of your network   |  TCP  |  22  |  \(Linux instances\) Allow inbound SSH access over IPv6 from your network\.   | 
|  IPv6 address range of your network   |  TCP  |  3389  |  \(Windows instances\) Allow inbound RDP access over IPv6 from your network  | 
| **Outbound** | 
| --- |
| Destination | Protocol | Port range | Comments | 
| ::/0 | TCP | HTTP | Allow outbound HTTP access to any IPv6 address\. | 
| ::/0 | TCP | HTTPS | Allow outbound HTTPS access to any IPv6 address\. | 

The following are the IPv6\-specific rules for the DBServerSG security group \(which are in addition to the rules listed above\)\.


| 
| 
|   **Outbound**   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Comments  | 
|  ::/0  |  TCP  |  80  |  Allow outbound HTTP access to any IPv6 address\.  | 
|  ::/0  |  TCP  |  443  |  Allow outbound HTTPS access to any IPv6 address\.  | 

## Implementing scenario 2<a name="VPC_Scenario2_Implementation"></a>

You can use the VPC wizard to create the VPC, subnets, NAT gateway, and optionally, an egress\-only Internet gateway\. You must specify an Elastic IP address for your NAT gateway; if you don't have one, you must first allocate one to your account\. If you want to use an existing Elastic IP address, ensure that it's not currently associated with another instance or network interface\. The NAT gateway is automatically created in the public subnet of your VPC\.

## Implementing scenario 2 with a NAT instance<a name="vpc-scenario-2-nat-instance"></a>

You can implement scenario 2 using a NAT instance instead of a NAT gateway\. For more information about NAT instances, see [NAT instances](VPC_NAT_Instance.md)\. 

You can follow the same procedures as above; however, in the NAT section of the VPC wizard, choose **Use a NAT instance instead** and specify the details for your NAT instance\. You will also require a security group for your NAT instance \(`NATSG`\), which allows the NAT instance to receive Internet\-bound traffic from instances in the private subnet, as well as SSH traffic from your network\. The NAT instance can also send traffic to the Internet, so that instances in the private subnet can get software updates\. 

After you've created the VPC with the NAT instance, you must change the security group associated with the NAT instance to the new `NATSG` security group \(by default, the NAT instance is launched using the default security group\)\.


**NATSG: recommended rules**  

| 
| 
| **Inbound** | 
| --- |
|  Source  |  Protocol  |  Port range  |  Comments  | 
|  10\.0\.1\.0/24  |  TCP  |  80  |  Allow inbound HTTP traffic from database servers that are in the private subnet  | 
|  10\.0\.1\.0/24  |  TCP  |  443  |  Allow inbound HTTPS traffic from database servers that are in the private subnet  | 
|  Your network's public IP address range  |  TCP  |  22  |  Allow inbound SSH access to the NAT instance from your network \(over the Internet gateway\)  | 
|   **Outbound**   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow outbound HTTP access to the Internet \(over the Internet gateway\)  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow outbound HTTPS access to the Internet \(over the Internet gateway\)  | 

## Recommended network ACL rules for a VPC with public and private subnets \(NAT\)<a name="nacl-rules-scenario-2"></a>

For this scenario, you have a network ACL for the public subnet, and a separate network ACL for the private subnet\. The following table shows the rules that we recommend for each ACL\. They block all traffic except that which is explicitly required\. They mostly mimic the security group rules for the scenario\.


**ACL rules for the public subnet**  

| 
| 
|  Inbound  | 
| --- |
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv4 address\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv4 address\.  | 
|  120  |  Public IP address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from your home network \(over the internet gateway\)\.  | 
|  130  |  Public IP address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from your home network \(over the internet gateway\)\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all inbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
| --- |
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  120  |  10\.0\.1\.0/24  |  TCP  |  1433  |  ALLOW  |  Allows outbound MS SQL access to database servers in the private subnet\.  This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  150  | 10\.0\.1\.0/24 | TCP | 22 | ALLOW |  Allows outbound SSH access to instances in your private subnet \(from an SSH bastion, if you have one\)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 


**ACL rules for the private subnet**  

| 
| 
|  Inbound  | 
| --- |
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  10\.0\.0\.0/24  |  TCP  |  1433  |  ALLOW  |  Allows web servers in the public subnet to read and write to MS SQL servers in the private subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  120  |  10\.0\.0\.0/24  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from an SSH bastion in the public subnet \(if you have one\)\.  | 
|  130  |  10\.0\.0\.0/24  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from the Microsoft Terminal Services gateway in the public subnet\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from the NAT device in the public subnet for requests originating in the private subnet\. For information about specifying the correct ephemeral ports, see the important note at the beginning of this topic\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all IPv4 inbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
| --- |
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  120  |  10\.0\.0\.0/24  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to the public subnet \(for example, responses to web servers in the public subnet that are communicating with DB servers in the private subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 

### Recommended network ACL rules for IPv6<a name="vpc-nacls-scenario-2-ipv6"></a>

If you implemented IPv6 support and created a VPC and subnets with associated IPv6 CIDR blocks, you must add separate rules to your network ACLs to control inbound and outbound IPv6 traffic\. 

The following are the IPv6\-specific rules for your network ACLs \(which are in addition to the preceding rules\)\.


**ACL rules for the public subnet**  

| 
| 
|  Inbound  | 
| --- |
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv6 address\.  | 
|  160  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv6 address\.  | 
|  170  |  IPv6 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic over IPv6 from your home network \(over the internet gateway\)\.  | 
|  180  |  IPv6 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic over IPv6 from your home network \(over the internet gateway\)\.  | 
|  190  |  ::/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
| --- |
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  160  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  170  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet  | 
|  180  |  2001:db8:1234:1a01::/64  |  TCP  |  1433  |  ALLOW  |  Allows outbound MS SQL access to database servers in the private subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  200  |  ::/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  210  |  2001:db8:1234:1a01::/64  |  TCP  |  22  |  ALLOW  |  Allows outbound SSH access to instances in your private subnet \(from an SSH bastion, if you have one\)\.  | 
|  \*  | ::/0 |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 


**ACL rules for the private subnet**  

| 
| 
|  Inbound  | 
| --- |
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  2001:db8:1234:1a00::/64  |  TCP  |  1433  |  ALLOW  |  Allows web servers in the public subnet to read and write to MS SQL servers in the private subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  170  |  2001:db8:1234:1a00::/64  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from an SSH bastion in the public subnet \(if applicable\)\.  | 
|  180  |  2001:db8:1234:1a00::/64  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from a Microsoft Terminal Services gateway in the public subnet, if applicable\.  | 
|  190  |  ::/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from the egress\-only internet gateway for requests originating in the private subnet\.  This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  | ::/0 |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
| --- |
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  130  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  140  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  150  |  2001:db8:1234:1a00::/64  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to the public subnet \(for example, responses to web servers in the public subnet that are communicating with DB servers in the private subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 