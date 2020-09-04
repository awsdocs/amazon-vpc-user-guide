# VPCs and subnets<a name="VPC_Subnets"></a>

To get started with Amazon Virtual Private Cloud \(Amazon VPC\), you create a VPC and subnets\. For a general overview of Amazon VPC, see [What is Amazon VPC?](what-is-amazon-vpc.md)\.

**Topics**
+ [VPC and subnet basics](#vpc-subnet-basics)
+ [VPC and subnet sizing](#VPC_Sizing)
+ [Subnet routing](#SubnetRouting)
+ [Subnet security](#SubnetSecurity)
+ [Working with VPCs and subnets](working-with-vpcs.md)
+ [Working with shared VPCs](vpc-sharing.md)
+ [Extending Your VPCs](Extend_VPCs.md)

## VPC and subnet basics<a name="vpc-subnet-basics"></a>

A virtual private cloud \(VPC\) is a virtual network dedicated to your AWS account\. It is logically isolated from other virtual networks in the AWS Cloud\. You can launch your AWS resources, such as Amazon EC2 instances, into your VPC\.

When you create a VPC, you must specify a range of IPv4 addresses for the VPC in the form of a Classless Inter\-Domain Routing \(CIDR\) block; for example, `10.0.0.0/16`\. This is the primary CIDR block for your VPC\. For more information about CIDR notation, see [RFC 4632](https://tools.ietf.org/html/rfc4632)\.

The following diagram shows a new VPC with an IPv4 CIDR block\.

![\[VPC with the main route table\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-diagram.png)

The main route table has the following routes\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | local | 

A VPC spans all of the Availability Zones in the Region\. After creating a VPC, you can add one or more subnets in each Availability Zone\. You can optionally add subnets in a Local Zone, which is an AWS infrastructure deployment that places compute, storage, database, and other select services closer to your end users\. A Local Zone enables your end users to run applications that require single\-digit millisecond latencies\. For information about the Regions that support Local Zones, see [Available Regions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) in the *Amazon EC2 User Guide for Linux Instances*\. When you create a subnet, you specify the CIDR block for the subnet, which is a subset of the VPC CIDR block\. Each subnet must reside entirely within one Availability Zone and cannot span zones\. Availability Zones are distinct locations that are engineered to be isolated from failures in other Availability Zones\. By launching instances in separate Availability Zones, you can protect your applications from the failure of a single location\. We assign a unique ID to each subnet\.

You can also optionally assign an IPv6 CIDR block to your VPC, and assign IPv6 CIDR blocks to your subnets\.

The following diagram shows a VPC that has been configured with subnets in multiple Availability Zones\. 1A, 2A, and 3A are instances in your VPC\. An IPv6 CIDR block is associated with the VPC, and an IPv6 CIDR block is associated with subnet 1\. An internet gateway enables communication over the internet, and a virtual private network \(VPN\) connection enables communication with your corporate network\.

![\[VPC with multiple Availability Zones\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/subnets-diagram.png)

If a subnet's traffic is routed to an internet gateway, the subnet is known as a *public subnet*\. In this diagram, subnet 1 is a public subnet\. If you want your instance in a public subnet to communicate with the internet over IPv4, it must have a public IPv4 address or an Elastic IP address \(IPv4\)\. For more information about public IPv4 addresses, see [Public IPv4 addresses](vpc-ip-addressing.md#vpc-public-ipv4-addresses)\. If you want your instance in the public subnet to communicate with the internet over IPv6, it must have an IPv6 address\.

If a subnet doesn't have a route to the internet gateway, the subnet is known as a *private subnet*\. In this diagram, subnet 2 is a private subnet\.

If a subnet doesn't have a route to the internet gateway, but has its traffic routed to a virtual private gateway for a Site\-to\-Site VPN connection, the subnet is known as a *VPN\-only subnet*\. In this diagram, subnet 3 is a VPN\-only subnet\. Currently, we do not support IPv6 traffic over a Site\-to\-Site VPN connection\.

For more information, see [Examples for VPC](VPC_Scenarios.md), [Internet gateways](VPC_Internet_Gateway.md), and [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html) in the *AWS Site\-to\-Site VPN User Guide*\.

**Note**  
Regardless of the type of subnet, the internal IPv4 address range of the subnet is always private—we do not announce the address block to the internet\.

You have a quota on the number of VPCs and subnets you can create in your account\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

## VPC and subnet sizing<a name="VPC_Sizing"></a>

Amazon VPC supports IPv4 and IPv6 addressing, and has different CIDR block size quotas for each\. By default, all VPCs and subnets must have IPv4 CIDR blocks—you can't change this behavior\. You can optionally associate an IPv6 CIDR block with your VPC\. 

For more information about IP addressing, see [IP Addressing in your VPC](vpc-ip-addressing.md)\.

**Topics**
+ [VPC and subnet sizing for IPv4](#vpc-sizing-ipv4)
+ [Adding IPv4 CIDR blocks to a VPC](#vpc-resize)
+ [VPC and subnet sizing for IPv6](#vpc-sizing-ipv6)

### VPC and subnet sizing for IPv4<a name="vpc-sizing-ipv4"></a>

When you create a VPC, you must specify an IPv4 CIDR block for the VPC\. The allowed block size is between a `/16` netmask \(65,536 IP addresses\) and `/28` netmask \(16 IP addresses\)\. After you've created your VPC, you can associate secondary CIDR blocks with the VPC\. For more information, see [Adding IPv4 CIDR blocks to a VPC](#vpc-resize)\. 

When you create a VPC, we recommend that you specify a CIDR block \(of `/16` or smaller\) from the private IPv4 address ranges as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html):
+ `10.0.0.0` \- `10.255.255.255` \(10/8 prefix\)
+ `172.16.0.0` \- `172.31.255.255` \(172\.16/12 prefix\)
+ `192.168.0.0` \- `192.168.255.255` \(192\.168/16 prefix\) 

You can create a VPC with a publicly routable CIDR block that falls outside of the private IPv4 address ranges specified in RFC 1918; however, for the purposes of this documentation, we refer to *private IP addresses* as the IPv4 addresses that are within the CIDR range of your VPC\.

**Note**  
If you're creating a VPC for use with another AWS service, check the service documentation to verify if there are specific requirements for the IP address range or networking components\.

The CIDR block of a subnet can be the same as the CIDR block for the VPC \(for a single subnet in the VPC\), or a subset of the CIDR block for the VPC \(for multiple subnets\)\. The allowed block size is between a `/28` netmask and `/16` netmask\. If you create more than one subnet in a VPC, the CIDR blocks of the subnets cannot overlap\. 

For example, if you create a VPC with CIDR block `10.0.0.0/24`, it supports 256 IP addresses\. You can break this CIDR block into two subnets, each supporting 128 IP addresses\. One subnet uses CIDR block `10.0.0.0/25` \(for addresses `10.0.0.0` \- `10.0.0.127`\) and the other uses CIDR block `10.0.0.128/25` \(for addresses `10.0.0.128` \- `10.0.0.255`\)\.

There are tools available on the internet to help you calculate and create IPv4 subnet CIDR blocks; for example, see [subnet calculator](https://network00.com/NetworkTools/IPv4VisualSubnetCalculatorCreator)\. You can find other tools that suit your needs by searching for terms such as 'subnet calculator' or 'CIDR calculator'. Your network engineering group can also help you determine the CIDR blocks to specify for your subnets.

The first four IP addresses and the last IP address in each subnet CIDR block are not available for you to use, and cannot be assigned to an instance\. For example, in a subnet with CIDR block `10.0.0.0/24`, the following five IP addresses are reserved: 
+ `10.0.0.0`: Network address\.
+ `10.0.0.1`: Reserved by AWS for the VPC router\.
+ `10.0.0.2`: Reserved by AWS\. The IP address of the DNS server is the base of the VPC network range plus two\. For VPCs with multiple CIDR blocks, the IP address of the DNS server is located in the primary CIDR\. We also reserve the base of each subnet range plus two for all CIDR blocks in the VPC\. For more information, see [Amazon DNS server](VPC_DHCP_Options.md#AmazonDNS)\.
+ `10.0.0.3`: Reserved by AWS for future use\.
+ `10.0.0.255`: Network broadcast address\. We do not support broadcast in a VPC, therefore we reserve this address\. 

If you create a VPC or subnet using a command line tool or the Amazon EC2 API, the CIDR block is automatically modified to its canonical form\. For example, if you specify `100.68.0.18/18` for the CIDR block, we create a CIDR block of `100.68.0.0/18`\.

### Adding IPv4 CIDR blocks to a VPC<a name="vpc-resize"></a>

You can associate secondary IPv4 CIDR blocks with your VPC\. When you associate a CIDR block with your VPC, a route is automatically added to your VPC route tables to enable routing within the VPC \(the destination is the CIDR block and the target is `local`\)\. 

In the following example, the VPC on the left has a single CIDR block \(`10.0.0.0/16`\) and two subnets\. The VPC on the right represents the architecture of the same VPC after you've added a second CIDR block \(`10.2.0.0/16`\) and created a new subnet from the range of the second CIDR\.

![\[VPCs with single and multiple CIDR blocks\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-multiple-cidrs.png)

To add a CIDR block to your VPC, the following rules apply:
+ The allowed block size is between a `/28` netmask and `/16` netmask\.
+ The CIDR block must not overlap with any existing CIDR block that's associated with the VPC\.
+ There are restrictions on the ranges of IPv4 addresses you can use\. For more information, see [IPv4 CIDR block association restrictions](#add-cidr-block-restrictions)\.
+ You cannot increase or decrease the size of an existing CIDR block\.
+ You have a quota on the number of CIDR blocks you can associate with a VPC and the number of routes you can add to a route table\. You cannot associate a CIDR block if this results in you exceeding your quotas\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.
+ The CIDR block must not be the same or larger than the CIDR range of a route in any of the VPC route tables\. For example, in a VPC where the primary CIDR block is `10.2.0.0/16`, you want to associate a secondary CIDR block in the `10.0.0.0/16` range\. You already have a route with a destination of `10.0.0.0/24` to a virtual private gateway, therefore you cannot associate a CIDR block of the same range or larger\. However, you can associate a CIDR block of `10.0.0.0/25` or smaller\.
+ If you've enabled your VPC for ClassicLink, you can associate CIDR blocks from the `10.0.0.0/16` and `10.1.0.0/16` ranges, but you cannot associate any other CIDR block from the `10.0.0.0/8` range\. 
+ The following rules apply when you add IPv4 CIDR blocks to a VPC that's part of a VPC peering connection:
  + If the VPC peering connection is `active`, you can add CIDR blocks to a VPC provided they do not overlap with a CIDR block of the peer VPC\.
  + If the VPC peering connection is `pending-acceptance`, the owner of the requester VPC cannot add any CIDR block to the VPC, regardless of whether it overlaps with the CIDR block of the accepter VPC\. Either the owner of the accepter VPC must accept the peering connection, or the owner of the requester VPC must delete the VPC peering connection request, add the CIDR block, and then request a new VPC peering connection\.
  + If the VPC peering connection is `pending-acceptance`, the owner of the accepter VPC can add CIDR blocks to the VPC\. If a secondary CIDR block overlaps with a CIDR block of the requester VPC, the VPC peering connection request fails and cannot be accepted\.
+ If you're using AWS Direct Connect to connect to multiple VPCs through a Direct Connect gateway, the VPCs that are associated with the Direct Connect gateway must not have overlapping CIDR blocks\. If you add a CIDR block to one of the VPCs that's associated with the Direct Connect gateway, ensure that the new CIDR block does not overlap with an existing CIDR block of any other associated VPC\. For more information, see [Direct Connect gateways](https://docs.aws.amazon.com/directconnect/latest/UserGuide/direct-connect-gateways.html) in the *AWS Direct Connect User Guide*\.
+ When you add or remove a CIDR block, it can go through various states: `associating` \| `associated` \| `disassociating` \| `disassociated` \| `failing` \| `failed`\. The CIDR block is ready for you to use when it's in the `associated` state\.

The following table provides an overview of permitted and restricted CIDR block associations, which depend on the IPv4 address range in which your VPC's primary CIDR block resides\.


**IPv4 CIDR block association restrictions**  

| IP address range in which your primary VPC CIDR block resides | Restricted CIDR block associations | Permitted CIDR block associations | 
| --- | --- | --- | 
|  10\.0\.0\.0/8  |  CIDR blocks from other RFC 1918**\*** ranges \(172\.16\.0\.0/12 and 192\.168\.0\.0/16\)\. If your primary CIDR falls within the 10\.0\.0\.0/15 range, you cannot add a CIDR block from the 10\.0\.0\.0/16 range\. A CIDR block from the 198\.19\.0\.0/16 range\.  |  Any other CIDR from the 10\.0\.0\.0/8 range that's not restricted\. Any publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 
|  172\.16\.0\.0/12  |  CIDR blocks from other RFC 1918**\*** ranges \(10\.0\.0\.0/8 and 192\.168\.0\.0/16\)\. A CIDR block from the 172\.31\.0\.0/16 range\. A CIDR block from the 198\.19\.0\.0/16 range\.  |  Any other CIDR from the 172\.16\.0\.0/12 range that's not restricted\. Any publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 
|  192\.168\.0\.0/16  |  CIDR blocks from other RFC 1918**\*** ranges \(172\.16\.0\.0/12 and 10\.0\.0\.0/8\)\. A CIDR block from the 198\.19\.0\.0/16 range\.  |  Any other CIDR from the 192\.168\.0\.0/16 range\. Any publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 
|  198\.19\.0\.0/16  |  CIDR blocks from RFC 1918**\*** ranges\.  |  Any publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 
|  Publicly routable CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  |  CIDR blocks from the RFC 1918**\*** ranges\. A CIDR block from the 198\.19\.0\.0/16 range\.  |  Any other publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 

**\***RFC 1918 ranges are the private IPv4 address ranges specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)\.

You can disassociate a CIDR block that you've associated with your VPC; however, you cannot disassociate the CIDR block with which you originally created the VPC \(the primary CIDR block\)\. To view the primary CIDR for your VPC in the Amazon VPC console, choose **Your VPCs**, select your VPC, and take note of the first entry under **CIDR blocks**\. Alternatively, you can use the [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) command:

```
aws ec2 describe-vpcs --vpc-id vpc-1a2b3c4d
```

In the output that's returned, the primary CIDR is returned in the top\-level `CidrBlock` element \(the second\-last element in the example output below\)\.

```
{
    "Vpcs": [
        {
            "VpcId": "vpc-1a2b3c4d", 
            "InstanceTenancy": "default", 
            "Tags": [
                {
                    "Value": "MyVPC", 
                    "Key": "Name"
                }
            ], 
            "CidrBlockAssociations": [
                {
                    "AssociationId": "vpc-cidr-assoc-3781aa5e", 
                    "CidrBlock": "10.0.0.0/16", 
                    "CidrBlockState": {
                        "State": "associated"
                    }
                }, 
                {
                    "AssociationId": "vpc-cidr-assoc-0280ab6b", 
                    "CidrBlock": "10.2.0.0/16", 
                    "CidrBlockState": {
                        "State": "associated"
                    }
                }
            ], 
            "State": "available", 
            "DhcpOptionsId": "dopt-e0fe0e88", 
            "CidrBlock": "10.0.0.0/16", 
            "IsDefault": false
        }
    ]
}
```

### VPC and subnet sizing for IPv6<a name="vpc-sizing-ipv6"></a>

You can associate a single IPv6 CIDR block with an existing VPC in your account, or when you create a new VPC\. The CIDR block is a fixed prefix length of `/56`\. You can request an IPv6 CIDR block from Amazon's pool of IPv6 addresses\.

If you've associated an IPv6 CIDR block with your VPC, you can associate an IPv6 CIDR block with an existing subnet in your VPC, or when you create a new subnet\. A subnet's IPv6 CIDR block is a fixed prefix length of `/64`\.

For example, you create a VPC and specify that you want to associate an Amazon\-provided IPv6 CIDR block with the VPC\. Amazon assigns the following IPv6 CIDR block to your VPC: `2001:db8:1234:1a00::/56`\. You cannot choose the range of IP addresses yourself\. You can create a subnet and associate an IPv6 CIDR block from this range; for example, `2001:db8:1234:1a00::/64`\.

There are tools available on the internet to help you calculate and create IPv6 subnet CIDR blocks; for example, see [subnet calculator](https://network00.com/NetworkTools/IPv6VisualSubnetCalculatorCreator)\. You can find other tools that suit your needs by searching for terms such as 'IPv6 subnet calculator' or 'IPv6 CIDR calculator'. Your network engineering group can also help you determine the IPv6 CIDR blocks to specify for your subnets.

You can disassociate an IPv6 CIDR block from a subnet, and you can disassociate an IPv6 CIDR block from a VPC\. After you've disassociated an IPv6 CIDR block from a VPC, you cannot expect to receive the same CIDR if you associate an IPv6 CIDR block with your VPC again later\.

The first four IPv6 addresses and the last IPv6 address in each subnet CIDR block are not available for you to use, and cannot be assigned to an instance\. For example, in a subnet with CIDR block `2001:db8:1234:1a00/64`, the following five IP addresses are reserved: 
+ `2001:db8:1234:1a00::`
+ `2001:db8:1234:1a00::1`
+ `2001:db8:1234:1a00::2`
+ `2001:db8:1234:1a00::3`
+ `2001:db8:1234:1a00:ffff:ffff:ffff:ffff`

## Subnet routing<a name="SubnetRouting"></a>

Each subnet must be associated with a route table, which specifies the allowed routes for outbound traffic leaving the subnet\. Every subnet that you create is automatically associated with the main route table for the VPC\. You can change the association, and you can change the contents of the main route table\. For more information, see [Route tables](VPC_Route_Tables.md)\.

In the previous diagram, the route table associated with subnet 1 routes all IPv4 traffic \(`0.0.0.0/0`\) and IPv6 traffic \(`::/0`\) to an internet gateway \(for example, `igw-1a2b3c4d`\)\. Because instance 1A has an IPv4 Elastic IP address and an IPv6 address, it can be reached from the internet over both IPv4 and IPv6\. 

**Note**  
\(IPv4 only\) The Elastic IPv4 address or public IPv4 address that's associated with your instance is accessed through the internet gateway of your VPC\. Traffic that goes through an AWS Site\-to\-Site VPN connection between your instance and another network traverses a virtual private gateway, not the internet gateway, and therefore does not access the Elastic IPv4 address or public IPv4 address\. 

The instance 2A can't reach the internet, but can reach other instances in the VPC\. You can allow an instance in your VPC to initiate outbound connections to the internet over IPv4 but prevent unsolicited inbound connections from the internet using a network address translation \(NAT\) gateway or instance\. Because you can allocate a limited number of Elastic IP addresses, we recommend that you use a NAT device if you have more instances that require a static public IP address\. For more information, see [NAT](vpc-nat.md)\. To initiate outbound\-only communication to the internet over IPv6, you can use an egress\-only internet gateway\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.

The route table associated with subnet 3 routes all IPv4 traffic \(`0.0.0.0/0`\) to a virtual private gateway \(for example, `vgw-1a2b3c4d`\)\. Instance 3A can reach computers in the corporate network over the Site\-to\-Site VPN connection\.

## Subnet security<a name="SubnetSecurity"></a>

AWS provides two features that you can use to increase security in your VPC: *security groups* and *network ACLs*\. Security groups control inbound and outbound traffic for your instances, and network ACLs control inbound and outbound traffic for your subnets\. In most cases, security groups can meet your needs; however, you can also use network ACLs if you want an additional layer of security for your VPC\. For more information, see [Internetwork traffic privacy in Amazon VPC](VPC_Security.md)\. 

By design, each subnet must be associated with a network ACL\. Every subnet that you create is automatically associated with the VPC's default network ACL\. You can change the association, and you can change the contents of the default network ACL\. For more information, see [Network ACLs](vpc-network-acls.md)\.

You can create a flow log on your VPC or subnet to capture the traffic that flows to and from the network interfaces in your VPC or subnet\. You can also create a flow log on an individual network interface\. Flow logs are published to CloudWatch Logs or Amazon S3\. For more information, see [VPC Flow Logs](flow-logs.md)\.
