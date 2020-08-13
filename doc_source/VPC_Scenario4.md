# VPC with a private subnet only and AWS Site\-to\-Site VPN access<a name="VPC_Scenario4"></a>

The configuration for this scenario includes a virtual private cloud \(VPC\) with a single private subnet, and a virtual private gateway to enable communication with your own network over an IPsec VPN tunnel\. There is no Internet gateway to enable communication over the Internet\. We recommend this scenario if you want to extend your network into [the cloud](https://aws.amazon.com/what-is-cloud-computing/) using Amazon's infrastructure without exposing your network to the Internet\.

This scenario can also be optionally configured for IPv6—you can use the VPC wizard to create a VPC and subnet with associated IPv6 CIDR blocks\. Instances launched into the subnet can receive IPv6 addresses\. We do not support IPv6 communication over a AWS Site\-to\-Site VPN connection on a virtual private gateway; however, instances in the VPC can communicate with each other via IPv6\. For more information about IPv4 and IPv6 addressing, see [IP Addressing in your VPC](vpc-ip-addressing.md)\.

For information about managing your EC2 instance software, see [Managing software on your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Overview](#Configuration-4)
+ [Routing](#VPC_Scenario4_Routing)
+ [Security](#VPC_Scenario4_Security)

## Overview<a name="Configuration-4"></a>

The following diagram shows the key components of the configuration for this scenario\.

![\[Diagram for scenario 4: VPC with only a virtual private gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/case-4.png)

**Important**  
For this scenario, see [Your customer gateway device](https://docs.aws.amazon.com/vpn/latest/s2svpn/your-cgw.html) to configure the customer gateway device on your side of the Site\-to\-Site VPN connection\.

The configuration for this scenario includes the following:
+ A virtual private cloud \(VPC\) with a size /16 CIDR \(example: 10\.0\.0\.0/16\)\. This provides 65,536 private IP addresses\.
+ A VPN\-only subnet with a size /24 CIDR \(example: 10\.0\.0\.0/24\)\. This provides 256 private IP addresses\.
+ A Site\-to\-Site VPN connection between your VPC and your network\. The Site\-to\-Site VPN connection consists of a virtual private gateway located on the Amazon side of the Site\-to\-Site VPN connection and a customer gateway located on your side of the Site\-to\-Site VPN connection\.
+ Instances with private IP addresses in the subnet range \(examples: 10\.0\.0\.5, 10\.0\.0\.6, and 10\.0\.0\.7\), which enables the instances to communicate with each other and other instances in the VPC\.
+ The main route table contains a route that enables instances in the subnet to communicate with other instances in the VPC\. Route propagation is enabled, so a route that enables instances in the subnet to communicate directly with your network appears as a propagated route in the main route table\.

For more information about subnets, see [VPCs and subnets](VPC_Subnets.md) and [IP Addressing in your VPC](vpc-ip-addressing.md)\. For more information about your Site\-to\-Site VPN connection, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html) in the *AWS Site\-to\-Site VPN User Guide*\. For more information about configuring a customer gateway device, see [Your customer gateway device](https://docs.aws.amazon.com/vpn/latest/s2svpn/your-cgw.html)\.

### Overview for IPv6<a name="vpc-scenario-4-overview-ipv6"></a>

You can optionally enable IPv6 for this scenario\. In addition to the components listed above, the configuration includes the following:
+ A size /56 IPv6 CIDR block associated with the VPC \(example: 2001:db8:1234:1a00::/56\)\. AWS automatically assigns the CIDR; you cannot choose the range yourself\.
+ A size /64 IPv6 CIDR block associated with the VPN\-only subnet \(example: 2001:db8:1234:1a00::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the IPv6 CIDR\.
+ IPv6 addresses assigned to the instances from the subnet range \(example: 2001:db8:1234:1a00::1a\)\.
+ A route table entry in the main route table that enables instances in the private subnet to use IPv6 to communicate with each other\.

![\[IPv6-enabled VPC with a VPN-only subnet\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/scenario-4-ipv6-diagram.png)

## Routing<a name="VPC_Scenario4_Routing"></a>

Your VPC has an implied router \(shown in the configuration diagram for this scenario\)\. In this scenario, the VPC wizard creates a route table that routes all traffic destined for an address outside the VPC to the AWS Site\-to\-Site VPN connection, and associates the route table with the subnet\. 

The following describes the route table for this scenario\. The first entry is the default entry for local routing in the VPC; this entry enables the instances in this VPC to communicate with each other\. The second entry routes all other subnet traffic to the virtual private gateway \(for example, `vgw-1a2b3c4d`\)\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  0\.0\.0\.0/0  |  *vgw\-id*  | 

The AWS Site\-to\-Site VPN connection is configured either as a statically\-routed Site\-to\-Site VPN connection or as a dynamically routed Site\-to\-Site VPN connection \(using BGP\)\. If you select static routing, you'll be prompted to manually enter the IP prefix for your network when you create the Site\-to\-Site VPN connection\. If you select dynamic routing, the IP prefix is advertised automatically to your VPC through BGP\.

The instances in your VPC can't reach the Internet directly; any Internet\-bound traffic must first traverse the virtual private gateway to your network, where the traffic is then subject to your firewall and corporate security policies\. If the instances send any AWS\-bound traffic \(for example, requests to Amazon S3 or Amazon EC2\), the requests must go over the virtual private gateway to your network and then to the Internet before reaching AWS\.

### Routing for IPv6<a name="vpc-scenario-4-routing-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, your route table includes separate routes for IPv6 traffic\. The following describes the custom route table for this scenario\. The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. 


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *vgw\-id*  | 

## Security<a name="VPC_Scenario4_Security"></a>

AWS provides two features that you can use to increase security in your VPC: *security groups* and *network ACLs*\. Security groups control inbound and outbound traffic for your instances, and network ACLs control inbound and outbound traffic for your subnets\. In most cases, security groups can meet your needs; however, you can also use network ACLs if you want an additional layer of security for your VPC\. For more information, see [Internetwork traffic privacy in Amazon VPC](VPC_Security.md)\. 

For scenario 4, you'll use the default security group for your VPC but not a network ACL\. If you'd like to use a network ACL, see [Recommended network ACL rules for a VPC with a private subnet only and AWS Site\-to\-Site VPN access](#nacl-rules-scenario-4)\.

Your VPC comes with a default security group whose initial settings deny all inbound traffic, allow all outbound traffic, and allow all traffic between the instances assigned to the security group\. For this scenario, we recommend that you add inbound rules to the default security group to allow SSH traffic \(Linux\) and Remote Desktop traffic \(Windows\) from your network\.

**Important**  
The default security group automatically allows assigned instances to communicate with each other, so you don't have to add a rule to allow this\. If you use a different security group, you must add a rule to allow this\.

The following table describes the inbound rules that you should add to the default security group for your VPC\.


**Default security group: recommended rules**  

| 
| 
|  **Inbound**  | 
| --- |
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  Private IPv4 address range of your network  |  TCP  |  22  |  \(Linux instances\) Allow inbound SSH traffic from your network\.  | 
|  Private IPv4 address range of your network  |  TCP  |  3389  |  \(Windows instances\) Allow inbound RDP traffic from your network\.  | 

**Security group rules for IPv6**  
If you associate an IPv6 CIDR block with your VPC and subnets, you must add separate rules to your security group to control inbound and outbound IPv6 traffic for your instances\. In this scenario, the database servers cannot be reached over the Site\-to\-Site VPN connection using IPv6; therefore, no additional security group rules are required\.

### Recommended network ACL rules for a VPC with a private subnet only and AWS Site\-to\-Site VPN access<a name="nacl-rules-scenario-4"></a>

The following table shows the rules that we recommend\. They block all traffic except that which is explicitly required\.


| 
| 
|  Inbound  | 
| --- |
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  Private IP address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic to the subnet from your home network\.  | 
|  110  |  Private IP address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic to the subnet from your home network\.  | 
|  120  |  Private IP address range of your home network  |  TCP  |  32768\-65535  |  ALLOW  |  Allows inbound return traffic from requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all inbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
| --- |
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  Private IP address range of your home network  |  All  |  All  |  ALLOW  |  Allows all outbound traffic from the subnet to your home network\. This rule also covers rule 120\. However, you can make this rule more restrictive by using a specific protocol type and port number\. If you make this rule more restrictive, you must include rule 120 in your network ACL to ensure that outbound responses are not blocked\.   | 
|  120  |  Private IP address range of your home network  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients in the home network\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 

#### Recommended network ACL rules for IPv6<a name="vpc-nacls-scenario-4-ipv6"></a>

If you implemented scenario 4 with IPv6 support and created a VPC and subnet with associated IPv6 CIDR blocks, you must add separate rules to your network ACL to control inbound and outbound IPv6 traffic\. 

In this scenario, the database servers cannot be reached over the VPN communication via IPv6, therefore no additional network ACL rules are required\. The following are the default rules that deny IPv6 traffic to and from the subnet\.


**ACL rules for the VPN\-only subnet**  

| 
| 
|  Inbound  | 
| --- |
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
| --- |
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  \*  | ::/0 |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 