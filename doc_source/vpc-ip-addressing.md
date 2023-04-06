# IP addressing for your VPCs and subnets<a name="vpc-ip-addressing"></a>

IP addresses enable resources in your VPC to communicate with each other, and with resources over the internet\.

Classless Inter\-Domain Routing \(CIDR\) notation is a way of representing an IP address and its network mask\. The format of these addresses is as follows:
+ An individual IPv4 address is 32 bits, with 4 groups of up to 3 decimal digits\. For example, 10\.0\.1\.0\.
+ An IPv4 CIDR block has four groups of up to three decimal digits, 0\-255, separated by periods, followed by a slash and a number from 0 to 32\. For example, 10\.0\.0\.0/16\.
+ An individual IPv6 address is 128 bits, with 8 groups of 4 hexadecimal digits\. For example, 2001:0db8:85a3:0000:0000:8a2e:0370:7334\.
+ An IPv6 CIDR block has four groups of up to four hexadecimal digits, separated by colons, followed by a double colon, followed by a slash and a number from 1 to 128\. For example, 2001:db8:1234:1a00::/56\.

**Topics**
+ [Compare IPv4 and IPv6](#ipv4-ipv6-comparison)
+ [Private IPv4 addresses](#vpc-private-ipv4-addresses)
+ [Public IPv4 addresses](#vpc-public-ipv4-addresses)
+ [IPv6 addresses](#vpc-ipv6-addresses)
+ [Use your own IP addresses](#vpc-using-own-ip-address)
+ [VPC CIDR blocks](vpc-cidr-blocks.md)
+ [Subnet CIDR blocks](subnet-sizing.md)
+ [Managed prefix lists](managed-prefix-lists.md)
+ [AWS IP address ranges](aws-ip-ranges.md)
+ [Migrate from IPv4 to IPv6](vpc-migrate-ipv6.md)
+ [IPv6 support on AWS](aws-ipv6-support.md)

## Compare IPv4 and IPv6<a name="ipv4-ipv6-comparison"></a>

The following table summarizes the differences between IPv4 and IPv6 in Amazon EC2 and Amazon VPC\.


| Characteristic | IPv4 | IPv6 | 
| --- | --- | --- | 
| VPC size | Up to 5 CIDRs from /16 to /28\. This [quota](amazon-vpc-limits.md#vpc-limits-vpcs-subnets) is adjustable\. | Up to 5 CIDRs fixed at /56\. This quota is not adjustable\. | 
| Subnet size | From /16 to /28 | Fixed at /64 | 
| Address selection | You can choose the IPv4 CIDR block for your VPC or you can allocate a CIDR block from Amazon VPC IP Address Manager \(IPAM\)\. For more information, see [What is IPAM?](https://docs.aws.amazon.com/vpc/latest/ipam/what-is-it-ipam.html) in the Amazon VPC IPAM User Guide\.  | You can bring your own IPv6 CIDR block to AWS for your VPC, choose an Amazon\-provided IPv6 CIDR block, or you can allocate a CIDR block from Amazon VPC IP Address Manager \(IPAM\)\. For more information, see [What is IPAM?](https://docs.aws.amazon.com/vpc/latest/ipam/what-is-it-ipam.html) in the Amazon VPC IPAM User Guide\.  | 
| Elastic IP addresses | Supported | Not supported | 
| NAT gateways | Supported | Not supported | 
| VPC endpoints | Supported | Not supported | 
| EC2 instances | Supported for all instance types | Supported on all [current generation instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#current-gen-instances) plus C3, R3, and I2 instances\. | 
| AMIs | Supported on all AMIs | Supported on AMIs that are configured for DHCPv6 | 
| DNS names | Instances receive Amazon\-provided IPBN or RBN\-based DNS names\. The DNS name resolves to the DNS records selected for the instance\. | Instance receive Amazon\-provided IPBN or RBN\-based DNS names\. The DNS name resolves to the DNS records selected for the instance\. | 

## Private IPv4 addresses<a name="vpc-private-ipv4-addresses"></a>

Private IPv4 addresses \(also referred to as *private IP addresses* in this topic\) are not reachable over the internet, and can be used for communication between the instances in your VPC\. When you launch an instance into a VPC, a primary private IP address from the IPv4 address range of the subnet is assigned to the default network interface \(eth0\) of the instance\. Each instance is also given a private \(internal\) DNS hostname that resolves to the private IP address of the instance\. The hostname can be of two types: resource\-based or IP\-based\. For more information, see [EC2 instance naming](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-naming.html)\. If you don't specify a primary private IP address, we select an available IP address in the subnet range for you\. For more information about network interfaces, see [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.

You can assign additional private IP addresses, known as secondary private IP addresses, to instances that are running in a VPC\. Unlike a primary private IP address, you can reassign a secondary private IP address from one network interface to another\. A private IP address remains associated with the network interface when the instance is stopped and restarted, and is released when the instance is terminated\. For more information about primary and secondary IP addresses, see [Multiple IP Addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/MultipleIP.html) in the *Amazon EC2 User Guide for Linux Instances*\.

We refer to private IP addresses as the IP addresses that are within the IPv4 CIDR range of the VPC\. Most VPC IP address ranges fall within the private \(non\-publicly routable\) IP address ranges specified in RFC 1918; however, you can use publicly routable CIDR blocks for your VPC\. Regardless of the IP address range of your VPC, we do not support direct access to the internet from your VPC's CIDR block, including a publicly\-routable CIDR block\. You must set up internet access through a gateway; for example, an internet gateway, virtual private gateway, a AWS Site\-to\-Site VPN connection, or AWS Direct Connect\.

## Public IPv4 addresses<a name="vpc-public-ipv4-addresses"></a>

All subnets have an attribute that determines whether a network interface created in the subnet automatically receives a public IPv4 address \(also referred to as a *public IP address* in this topic\)\. Therefore, when you launch an instance into a subnet that has this attribute enabled, a public IP address is assigned to the primary network interface \(eth0\) that's created for the instance\. A public IP address is mapped to the primary private IP address through network address translation \(NAT\)\. 

You can control whether your instance receives a public IP address by doing the following: 
+ Modifying the public IP addressing attribute of your subnet\. For more information, see [Modify the public IPv4 addressing attribute for your subnet](modify-subnets.md#subnet-public-ip)\.
+ Enabling or disabling the public IP addressing feature during instance launch, which overrides the subnet's public IP addressing attribute\.

A public IP address is assigned from Amazon's pool of public IP addresses; it's not associated with your account\. When a public IP address is disassociated from your instance, it's released back into the pool, and is no longer available for you to use\. You cannot manually associate or disassociate a public IP address\. Instead, in certain cases, we release the public IP address from your instance, or assign it a new one\. For more information, see [Public IP addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#concepts-public-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.

If you require a persistent public IP address allocated to your account that can be assigned to and removed from instances as you require, use an Elastic IP address instead\. For more information, see [Associate Elastic IP addresses with resources in your VPC](vpc-eips.md)\.

If your VPC is enabled to support DNS hostnames, each instance that receives a public IP address or an Elastic IP address is also given a public DNS hostname\. We resolve a public DNS hostname to the public IP address of the instance outside the instance network, and to the private IP address of the instance from within the instance network\. For more information, see [DNS attributes for your VPC](vpc-dns.md)\.

## IPv6 addresses<a name="vpc-ipv6-addresses"></a>

You can optionally associate an IPv6 CIDR block with your VPC and subnets\. For more information, see [Add an IPv6 CIDR block to your subnet](modify-subnets.md#subnet-associate-ipv6-cidr)\.

Your instance in a VPC receives an IPv6 address if an IPv6 CIDR block is associated with your VPC and your subnet, and if one of the following is true:
+ Your subnet is configured to automatically assign an IPv6 address to the primary network interface of an instance during launch\.
+ You manually assign an IPv6 address to your instance during launch\.
+ You assign an IPv6 address to your instance after launch\.
+ You assign an IPv6 address to a network interface in the same subnet, and attach the network interface to your instance after launch\. 

When your instance receives an IPv6 address during launch, the address is associated with the primary network interface \(eth0\) of the instance\. You can disassociate the IPv6 address from the primary network interface\. We do not support IPv6 DNS hostnames for your instance\.

An IPv6 address persists when you stop and start your instance, and is released when you terminate your instance\. You cannot reassign an IPv6 address while it's assigned to another network interface—you must first unassign it\.

You can assign additional IPv6 addresses to your instance by assigning them to a network interface attached to your instance\. The number of IPv6 addresses you can assign to a network interface, and the number of network interfaces you can attach to an instance varies per instance type\. For more information, see [IP Addresses Per Network Interface Per Instance Type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI) in the *Amazon EC2 User Guide*\.

IPv6 addresses are globally unique and can be configured to remain private or reachable over the Internet\. You can control whether instances are reachable via their IPv6 addresses by controlling the routing for your subnet, or by using security group and network ACL rules\. For more information, see [Internetwork traffic privacy in Amazon VPC](VPC_Security.md)\.

For more information about reserved IPv6 address ranges, see [IANA IPv6 Special\-Purpose Address Registry](http://www.iana.org/assignments/iana-ipv6-special-registry/iana-ipv6-special-registry.xhtml) and [RFC4291](https://tools.ietf.org/html/rfc4291)\.

## Use your own IP addresses<a name="vpc-using-own-ip-address"></a>

You can bring part or all of your own public IPv4 address range or IPv6 address range to your AWS account\. You continue to own the address range, but AWS advertises it on the internet by default\. After you bring the address range to AWS, it appears in your account as an address pool\. You can create an Elastic IP address from your IPv4 address pool, and you can associate an IPv6 CIDR block from your IPv6 address pool with a VPC\.

For more information, see [Bring your own IP addresses \(BYOIP\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-byoip.html) in the *Amazon EC2 User Guide for Linux Instances*\.