# Subnets for your VPC<a name="configure-subnets"></a>

A *subnet* is a range of IP addresses in your VPC\. You can launch AWS resources into your subnets\.

**Topics**
+ [Subnet basics](#subnet-basics)
+ [Subnet sizing](#subnet-sizing)
+ [Subnet routing](#subnet-routing)
+ [Subnet security](#subnet-security)
+ [Work with subnets](working-with-subnets.md)
+ [Use subnet CIDR reservations](subnet-cidr-reservation.md)
+ [Group CIDR blocks using managed prefix lists](managed-prefix-lists.md)
+ [Configure route tables](VPC_Route_Tables.md)
+ [Control traffic to subnets using Network ACLs](vpc-network-acls.md)

## Subnet basics<a name="subnet-basics"></a>

A *subnet* is a range of IP addresses in your VPC\. You can launch AWS resources, such as EC2 instances, into a specific subnet\. When you create a subnet, you specify the IPv4 CIDR block for the subnet, which is a subset of the VPC CIDR block\. Each subnet must reside entirely within one Availability Zone and cannot span zones\. By launching instances in separate Availability Zones, you can protect your applications from the failure of a single zone\.

You can optionally add subnets in a Local Zone, which is an AWS infrastructure deployment that places compute, storage, database, and other select services closer to your end users\. A Local Zone enables your end users to run applications that require single\-digit millisecond latencies\. For more information, see [Local Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-local-zones) in the *Amazon EC2 User Guide for Linux Instances*\.

**Topics**
+ [Subnet types](#subnet-types)
+ [Subnet settings](#subnet-settings)
+ [Subnet diagram](#subnet-diagram)

### Subnet types<a name="subnet-types"></a>

Depending on how you configure routing for your subnets, they are considered either public, private, or VPN\-only:
+ **Public subnet**: The subnet has a direct route to an [internet gateway](VPC_Internet_Gateway.md)\. Resources in a public subnet can access the public internet\.
+ **Private subnet**: The subnet does not have a direct route to an internet gateway\. Resources in a private subnet require a [NAT device](vpc-nat.md) to access the public internet\.
+ **VPN\-only subnet**: The subnet has a route to a [Site\-to\-Site VPN connection](https://docs.aws.amazon.com/vpn/latest/s2svpn/) through a virtual private gateway\. The subnet does not have a route to an internet gateway\.

When you create a subnet, you specify its IP addresses, depending on the configuration of the VPC:
+ **IPv4 only**: The subnet has an IPv4 CIDR block but does not have an IPv6 CIDR block\. Resources in an IPv4\-only subnet must communicate over IPv4\.
+ **Dual stack**: The subnet has both an IPv4 CIDR block and an IPv6 CIDR block\. The VPC must have both an IPv4 CIDR block and an IPv6 CIDR block\. Resources in a dual\-stack subnet can communicate over IPv4 and IPv6\.
+ **IPv6 only**: The subnet has an IPv6 CIDR block but does not have an IPv4 CIDR block\. The VPC must have an IPv6 CIDR block\. Resources in an IPv6\-only subnet must communicate over IPv6\.

Regardless of the type of subnet, the internal IPv4 address range of the subnet is always private—we do not announce the address block to the internet\. For more information about private IP addressing in VPCs, see [IP addressing](how-it-works.md#vpc-ip-addressing)\.

### Subnet settings<a name="subnet-settings"></a>

All subnets have a modifiable attribute that determines whether a network interface created in that subnet is assigned a public IPv4 address and, if applicable, an IPv6 address\. This includes the primary network interface \(eth0\) that's created for an instance when you launch an instance in that subnet\. Regardless of the subnet attribute, you can still override this setting for a specific instance during launch\.

After you create a subnet, you can modify the following settings for the subnet\.
+ **Auto\-assign IP settings**: Enables you to configure the auto\-assign IP settings to automatically request a public IPv4 or IPv6 address for a new network interface in this subnet\.
+ **Resource\-based Name \(RBN\) settings**: Enables you to specify the hostname type for EC2 instances in this subnet and configure how DNS A and AAAA record queries are handled\. For more information about these settings, see [Amazon EC2 instance hostname types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-naming.html) in the *Amazon EC2 User Guide for Linux Instances*\.

### Subnet diagram<a name="subnet-diagram"></a>

The following diagram shows two VPCs in a Region\. Each VPC has public and private subnets and an internet gateway\. The VPC also spans a [Local Zone](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-local-zones)\.

![\[A VPC with subnets in Availability Zones and a Local Zone.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/subnet-diagram.png)

## Subnet sizing<a name="subnet-sizing"></a>

The CIDR block of a subnet can be the same as the CIDR block for the VPC \(for a single subnet in the VPC\), or a subset of the CIDR block for the VPC \(to create multiple subnets in the VPC\)\. The allowed block size is between a `/28` netmask and `/16` netmask\. If you create more than one subnet in a VPC, the CIDR blocks of the subnets cannot overlap\. 

For example, if you create a VPC with CIDR block `10.0.0.0/24`, it supports 256 IP addresses\. You can break this CIDR block into two subnets, each supporting 128 IP addresses\. One subnet uses CIDR block `10.0.0.0/25` \(for addresses `10.0.0.0` \- `10.0.0.127`\) and the other uses CIDR block `10.0.0.128/25` \(for addresses `10.0.0.128` \- `10.0.0.255`\)\.

There are tools available on the internet to help you calculate and create IPv4 subnet CIDR blocks\. You can find tools that suit your needs by searching for terms such as 'subnet calculator' or 'CIDR calculator'\. Your network engineering group can also help you determine the CIDR blocks to specify for your subnets\.

The first four IP addresses and the last IP address in each subnet CIDR block are not available for your use, and they cannot be assigned to a resource, such as an EC2 instance\. For example, in a subnet with CIDR block `10.0.0.0/24`, the following five IP addresses are reserved: 
+ 10\.0\.0\.0: Network address\.
+ 10\.0\.0\.1: Reserved by AWS for the VPC router\.
+ 10\.0\.0\.2: Reserved by AWS\. The IP address of the DNS server is the base of the VPC network range plus two\. For VPCs with multiple CIDR blocks, the IP address of the DNS server is located in the primary CIDR\. We also reserve the base of each subnet range plus two for all CIDR blocks in the VPC\. For more information, see [Amazon DNS server](vpc-dns.md#AmazonDNS)\.
+ 10\.0\.0\.3: Reserved by AWS for future use\.
+ 10\.0\.0\.255: Network broadcast address\. We do not support broadcast in a VPC, therefore we reserve this address\. 

If you create a subnet using a command line tool or the Amazon EC2 API, the CIDR block is automatically modified to its canonical form\. For example, if you specify 100\.68\.0\.18/18 for the CIDR block, we create a CIDR block of 100\.68\.0\.0/18\.

### Subnet sizing for IPv6<a name="subnet-sizing-ipv6"></a>

If you've associated an IPv6 CIDR block with your VPC, you can associate an IPv6 CIDR block with an existing subnet in your VPC, or when you create a new subnet\. A subnet's IPv6 CIDR block is a fixed prefix length of `/64`\.

There are tools available on the internet to help you calculate and create IPv6 subnet CIDR blocks; for example, [IPv6 Address Planner](https://network00.com/NetworkTools/IPv6VisualSubnetCalculatorCreator)\. You can find other tools that suit your needs by searching for terms such as 'IPv6 subnet calculator' or 'IPv6 CIDR calculator'\. Your network engineering group can also help you determine the IPv6 CIDR blocks to specify for your subnets\.

The first four IPv6 addresses and the last IPv6 address in each subnet CIDR block are not available for your use, and they cannot be assigned to an EC2 instance\. For example, in a subnet with CIDR block `2001:db8:1234:1a00/64`, the following five IP addresses are reserved:
+ `2001:db8:1234:1a00::`
+ `2001:db8:1234:1a00::1`: Reserved by AWS for the VPC router\.
+ `2001:db8:1234:1a00::2`
+ `2001:db8:1234:1a00::3`
+ `2001:db8:1234:1a00:ffff:ffff:ffff:ffff`

In addition to the IP address reserved by AWS for the VPC router in the example above, the following IPv6 addresses are reserved for the default VPC router:
+ A link\-local IPv6 address in the FE80::/10 range generated using EUI\-64\. For more information about link\-local addresses, see [Link\-local address](https://en.wikipedia.org/wiki/Link-local_address)\.
+ The link\-local IPv6 address `FE80:ec2::1`\.

If you need to communicate with the VPC router over IPv6, you can configure your applications to communicate with whichever address best fits your need\.

## Subnet routing<a name="subnet-routing"></a>

Each subnet must be associated with a route table, which specifies the allowed routes for outbound traffic leaving the subnet\. Every subnet that you create is automatically associated with the main route table for the VPC\. You can change the association, and you can change the contents of the main route table\. For more information, see [Configure route tables](VPC_Route_Tables.md)\.

## Subnet security<a name="subnet-security"></a>

To protect your AWS resources, we recommend that you use private subnets\. Use a bastion host or NAT device to provide internet access to resources, such as EC2 instances, in a private subnet\.

AWS provides features that you can use to increase security for the resources in your VPC\. *Security groups* allow inbound and outbound traffic for associated resources, such as EC2 instances\. *Network ACLs* allow or deny inbound and outbound traffic at the subnet level\. In most cases, security groups can meet your needs\. However, you can use network ACLs if you want an additional layer of security\. For more information, see [Compare security groups and network ACLs](VPC_Security.md#VPC_Security_Comparison)\.

By design, each subnet must be associated with a network ACL\. Every subnet that you create is automatically associated with the default network ACL for the VPC\. The default network ACL allows all inbound and outbound traffic\. You can update the default network ACL, or create custom network ACLs and associate them with your subnets\. For more information, see [Control traffic to subnets using Network ACLs](vpc-network-acls.md)\.

You can create a flow log on your VPC or subnet to capture the traffic that flows to and from the network interfaces in your VPC or subnet\. You can also create a flow log on an individual network interface\. For more information, see [Logging IP traffic using VPC Flow Logs](flow-logs.md)\.