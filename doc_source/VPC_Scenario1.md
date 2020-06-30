# VPC with a single public subnet<a name="VPC_Scenario1"></a>

The configuration for this scenario includes a virtual private cloud \(VPC\) with a single public subnet, and an internet gateway to enable communication over the internet\. We recommend this configuration if you need to run a single\-tier, public\-facing web application, such as a blog or a simple website\.

This scenario can also be optionally configured for IPv6—you can use the VPC wizard to create a VPC and subnet with associated IPv6 CIDR blocks\. Instances launched into the public subnet can receive IPv6 addresses, and communicate using IPv6\. For more information about IPv4 and IPv6 addressing, see [IP Addressing in your VPC](vpc-ip-addressing.md)\.

For information about managing your EC2 instance software, see [Managing software on your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Overview](#Configuration)
+ [Routing](#VPC_Scenario1_Routing)
+ [Security](#VPC_Scenario1_Security)

## Overview<a name="Configuration"></a>

The following diagram shows the key components of the configuration for this scenario\.

![\[Diagram for scenario 1: VPC with a public subnet\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/case-1.png)

**Note**  
If you completed [Getting started with Amazon VPC](vpc-getting-started.md), then you've already implemented this scenario using the VPC wizard in the Amazon VPC console\.

The configuration for this scenario includes the following:
+ A virtual private cloud \(VPC\) with a size /16 IPv4 CIDR block \(example: 10\.0\.0\.0/16\)\. This provides 65,536 private IPv4 addresses\.
+ A subnet with a size /24 IPv4 CIDR block \(example: 10\.0\.0\.0/24\)\. This provides 256 private IPv4 addresses\.
+ An internet gateway\. This connects the VPC to the internet and to other AWS services\.
+ An instance with a private IPv4 address in the subnet range \(example: 10\.0\.0\.6\), which enables the instance to communicate with other instances in the VPC, and an Elastic IPv4 address \(example: 198\.51\.100\.2\), which is a public IPv4 address that enables the instance to connect to the internet and to be reached from the internet\.
+ A custom route table associated with the subnet\. The route table entries enable instances in the subnet to use IPv4 to communicate with other instances in the VPC, and to communicate directly over the internet\. A subnet that's associated with a route table that has a route to an internet gateway is known as a *public subnet*\.

For more information about subnets, see [VPCs and subnets](VPC_Subnets.md)\. For more information about internet gateways, see [Internet gateways](VPC_Internet_Gateway.md)\.

### Overview for IPv6<a name="vpc-scenario-1-overview-ipv6"></a>

You can optionally enable IPv6 for this scenario\. In addition to the components listed above, the configuration includes the following:
+ A size /56 IPv6 CIDR block associated with the VPC \(example: 2001:db8:1234:1a00::/56\)\. Amazon automatically assigns the CIDR; you cannot choose the range yourself\.
+ A size /64 IPv6 CIDR block associated with the public subnet \(example: 2001:db8:1234:1a00::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the subnet IPv6 CIDR block\.
+ An IPv6 address assigned to the instance from the subnet range \(example: 2001:db8:1234:1a00::123\)\.
+ Route table entries in the custom route table that enable instances in the VPC to use IPv6 to communicate with each other, and directly over the internet\.

![\[IPv6-enabled VPC with a public subnet\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/getting-started-ipv6-1.png)

## Routing<a name="VPC_Scenario1_Routing"></a>

Your VPC has an implied router \(shown in the configuration diagram above\)\. In this scenario, the VPC wizard creates a custom route table that routes all traffic destined for an address outside the VPC to the internet gateway, and associates this route table with the subnet\. 

The following table shows the route table for the example in the configuration diagram above\. The first entry is the default entry for local IPv4 routing in the VPC; this entry enables the instances in this VPC to communicate with each other\. The second entry routes all other IPv4 subnet traffic to the internet gateway \(for example, `igw-1a2b3c4d`\)\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  0\.0\.0\.0/0  |  *igw\-id*  | 

### Routing for IPv6<a name="vpc-scenario-1-routing-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnet, your route table must include separate routes for IPv6 traffic\. The following table shows the custom route table for this scenario if you choose to enable IPv6 communication in your VPC\. The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. The fourth entry routes all other IPv6 subnet traffic to the internet gateway\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *igw\-id*  | 
|  ::/0  | igw\-id | 

## Security<a name="VPC_Scenario1_Security"></a>

AWS provides two features that you can use to increase security in your VPC: *security groups* and *network ACLs*\. Security groups control inbound and outbound traffic for your instances, and network ACLs control inbound and outbound traffic for your subnets\. In most cases, security groups can meet your needs; however, you can also use network ACLs if you want an additional layer of security for your VPC\. For more information, see [Internetwork traffic privacy in Amazon VPC](VPC_Security.md)\. 

For this scenario, you use a security group but not a network ACL\. If you'd like to use a network ACL, see [Recommended network ACL rules for a VPC with a single public subnet](#nacl-rules-scenario-1)\.

Your VPC comes with a [default security group](VPC_SecurityGroups.md#DefaultSecurityGroup)\. An instance that's launched into the VPC is automatically associated with the default security group if you don't specify a different security group during launch\. You can add specific rules to the default security group, but the rules may not be suitable for other instances that you launch into the VPC\. Instead, we recommend that you create a custom security group for your web server\.

For this scenario, create a security group named `WebServerSG`\. When you create a security group, it has a single outbound rule that allows all traffic to leave the instances\. You must modify the rules to enable inbound traffic and restrict the outbound traffic as needed\. You specify this security group when you launch instances into the VPC\. 

The following are the inbound and outbound rules for IPv4 traffic for the WebServerSG security group\. 


| 
| 
| **Inbound** | 
| --- |
|  Source  |  Protocol  |  Port range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow inbound HTTP access to the web servers from any IPv4 address\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow inbound HTTPS access to the web servers from any IPv4 address  | 
|  Public IPv4 address range of your network   |  TCP  |  22  |  \(Linux instances\) Allow inbound SSH access from your network over IPv4\. You can get the public IPv4 address of your local computer using a service such as [http://checkip\.amazonaws\.com](http://checkip.amazonaws.com) or [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com)\. If you are connecting through an ISP or from behind your firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.   | 
|  Public IPv4 address range of your network   |  TCP  |  3389  |  \(Windows instances\) Allow inbound RDP access from your network over IPv4\.  | 
| The security group ID \(sg\-xxxxxxxx\) | All | All | \(Optional\) Allow inbound traffic from other instances associated with this security group\. This rule is automatically added to the default security group for the VPC; for any custom security group you create, you must manually add the rule to allow this type of communication\. | 
| **Outbound** \(Optional\) | 
| --- |
| Destination | Protocol | Port range | Comments | 
| 0\.0\.0\.0/0 | All | All | Default rule to allow all outbound access to any IPv4 address\. If you want your web server to initiate outbound traffic, for example, to get software updates, you can keep the default outbound rule\. Otherwise, you can remove this rule\. | 

**Security group rules for IPv6**  
If you associate an IPv6 CIDR block with your VPC and subnet, you must add separate rules to your security group to control inbound and outbound IPv6 traffic for your web server instance\. In this scenario, the web server will be able to receive all internet traffic over IPv6, and SSH or RDP traffic from your local network over IPv6\. 

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
| **Outbound** \(Optional\) | 
| --- |
| Destination | Protocol | Port range | Comments | 
| ::/0 | All | All | Default rule to allow all outbound access to any IPv6 address\. If you want your web server to initiate outbound traffic, for example, to get software updates, you can keep the default outbound rule\. Otherwise, you can remove this rule\. | 

### Recommended network ACL rules for a VPC with a single public subnet<a name="nacl-rules-scenario-1"></a>

The following table shows the rules that we recommend\. They block all traffic except that which is explicitly required\.


| 
| 
|  Inbound  | 
| --- |
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv4 address\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv4 address\.  | 
|  120  |  Public IPv4 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from your home network \(over the internet gateway\)\.  | 
|  130  |  Public IPv4 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from your home network \(over the internet gateway\)\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all inbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
| --- |
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  120  |  0\.0\.0\.0/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 

#### Recommended network ACL rules for IPv6<a name="vpc-nacls-scenario-1-ipv6"></a>

If you implemented IPv6 support and created a VPC and subnet with associated IPv6 CIDR blocks, you must add separate rules to your network ACL to control inbound and outbound IPv6 traffic\. 

The following are the IPv6\-specific rules for your network ACL \(which are in addition to the preceding rules\)\.


| 
| 
|  Inbound  | 
| --- |
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv6 address\.  | 
|  160  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv6 address\.  | 
|  170  |  IPv6 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from your home network \(over the internet gateway\)\.  | 
|  180  |  IPv6 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from your home network \(over the internet gateway\)\.  | 
|  190  |  ::/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
| --- |
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  130  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  140  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  150  |  ::/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 