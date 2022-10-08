# Virtual private clouds \(VPC\)<a name="configure-your-vpc"></a>

A *virtual private cloud* \(VPC\) is a virtual network dedicated to your AWS account\. It is logically isolated from other virtual networks in the AWS Cloud\. You can launch your AWS resources, such as Amazon EC2 instances, into your VPC\.

**Topics**
+ [VPC basics](#vpc-subnet-basics)
+ [VPC CIDR blocks](#vpc-cidr-blocks)
+ [Work with VPCs](working-with-vpcs.md)
+ [Default VPCs](default-vpc.md)
+ [DHCP option sets in Amazon VPC](VPC_DHCP_Options.md)
+ [DNS attributes for your VPC](vpc-dns.md)
+ [Network Address Usage for your VPC](network-address-usage.md)
+ [Share your VPC with other accounts](vpc-sharing.md)
+ [Extend a VPC to a Local Zone, Wavelength Zone, or Outpost](Extend_VPCs.md)

## VPC basics<a name="vpc-subnet-basics"></a>

When you create a VPC, you must specify a range of IPv4 addresses for the VPC in the form of a Classless Inter\-Domain Routing \(CIDR\) block\. For example, 10\.0\.0\.0/16\. This is the primary CIDR block for your VPC\. For more information, see [IP addressing](how-it-works.md#vpc-ip-addressing)\.

A VPC spans all of the Availability Zones in the Region\. The following diagram shows a new VPC\. After you create a VPC, you can add one or more subnets in each Availability Zone\. For more information, see [Subnets for your VPC](configure-subnets.md)\.

![\[A VPC that spans the Availability Zones for its Region.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-diagram.png)

## VPC CIDR blocks<a name="vpc-cidr-blocks"></a>

Amazon VPC supports IPv4 and IPv6 addressing\. A VPC must have an IPv4 CIDR block associated with it\. You can optionally associate multiple IPv4 CIDR blocks and multiple IPv6 CIDR blocks to your VPC\. For more information about IP addressing, see [IP addressing](how-it-works.md#vpc-ip-addressing)\.

**Topics**
+ [IPv4 VPC CIDR blocks](#vpc-sizing-ipv4)
+ [Manage IPv4 CIDR blocks for a VPC](#vpc-resize)
+ [IPv4 CIDR block association restrictions](#add-cidr-block-restrictions)
+ [IPv6 VPC CIDR blocks](#vpc-sizing-ipv6)

### IPv4 VPC CIDR blocks<a name="vpc-sizing-ipv4"></a>

When you create a VPC, you must specify an IPv4 CIDR block for the VPC\. The allowed block size is between a `/16` netmask \(65,536 IP addresses\) and `/28` netmask \(16 IP addresses\)\. After you've created your VPC, you can associate additional IPv4 CIDR blocks with the VPC\. For more information, see [Associate additional IPv4 CIDR blocks with your VPC](working-with-vpcs.md#add-ipv4-cidr)\.

When you create a VPC, we recommend that you specify a CIDR block from the private IPv4 address ranges as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)\.


| RFC 1918 range | Example CIDR block | 
| --- | --- | 
| 10\.0\.0\.0 \- 10\.255\.255\.255 \(10/8 prefix\) | 10\.0\.0\.0/16 | 
| 172\.16\.0\.0 \- 172\.31\.255\.255 \(172\.16/12 prefix\) | 172\.31\.0\.0/16 | 
| 192\.168\.0\.0 \- 192\.168\.255\.255 \(192\.168/16 prefix\) | 192\.168\.0\.0/20 | 

You can create a VPC with a publicly routable CIDR block that falls outside of the private IPv4 address ranges specified in RFC 1918\. However, for the purposes of this documentation, we refer to *private IP addresses* as the IPv4 addresses that are within the CIDR range of your VPC\.

When you create a VPC for use with an AWS service, check the service documentation to verify if there are specific requirements for its configuration\.

If you create a VPC using a command line tool or the Amazon EC2 API, the CIDR block is automatically modified to its canonical form\. For example, if you specify 100\.68\.0\.18/18 for the CIDR block, we create a CIDR block of 100\.68\.0\.0/18\.

### Manage IPv4 CIDR blocks for a VPC<a name="vpc-resize"></a>

You can associate secondary IPv4 CIDR blocks with your VPC\. When you associate a CIDR block with your VPC, a route is automatically added to your VPC route tables to enable routing within the VPC \(the destination is the CIDR block and the target is `local`\)\. 

In the following example, the VPC has both a primary and a secondary CIDR block\. The CIDR blocks for Subnet A and Subnet B are from the primary VPC CIDR block\. The CIDR block for Subnet C is from the secondary VPC CIDR block\.

![\[VPCs with single and multiple CIDR blocks\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-multiple-cidrs.png)

The following route table shows the local routes for the VPC\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 10\.2\.0\.0/16 | Local | 

To add a CIDR block to your VPC, the following rules apply:
+ The allowed block size is between a `/28` netmask and `/16` netmask\.
+ The CIDR block must not overlap with any existing CIDR block that's associated with the VPC\.
+ There are restrictions on the ranges of IPv4 addresses you can use\. For more information, see [IPv4 CIDR block association restrictions](#add-cidr-block-restrictions)\.
+ You cannot increase or decrease the size of an existing CIDR block\.
+ You have a quota on the number of CIDR blocks you can associate with a VPC and the number of routes you can add to a route table\. You cannot associate a CIDR block if this results in you exceeding your quotas\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.
+ The CIDR block must not be the same or larger than a destination CIDR range in a route in any of the VPC route tables\. For example, in a VPC where the primary CIDR block is `10.2.0.0/16`, you have an existing route in a route table with a destination of `10.0.0.0/24` to a virtual private gateway\. You want to associate a secondary CIDR block in the `10.0.0.0/16` range\. Because of the existing route, you cannot associate a CIDR block of `10.0.0.0/24` or larger\. However, you can associate a secondary CIDR block of `10.0.0.0/25` or smaller\.
+ If you've enabled your VPC for ClassicLink, you can associate CIDR blocks from the `10.0.0.0/16` and `10.1.0.0/16` ranges, but you cannot associate any other CIDR block from the `10.0.0.0/8` range\. 
+ The following rules apply when you add IPv4 CIDR blocks to a VPC that's part of a VPC peering connection:
  + If the VPC peering connection is `active`, you can add CIDR blocks to a VPC provided they do not overlap with a CIDR block of the peer VPC\.
  + If the VPC peering connection is `pending-acceptance`, the owner of the requester VPC cannot add any CIDR block to the VPC, regardless of whether it overlaps with the CIDR block of the accepter VPC\. Either the owner of the accepter VPC must accept the peering connection, or the owner of the requester VPC must delete the VPC peering connection request, add the CIDR block, and then request a new VPC peering connection\.
  + If the VPC peering connection is `pending-acceptance`, the owner of the accepter VPC can add CIDR blocks to the VPC\. If a secondary CIDR block overlaps with a CIDR block of the requester VPC, the VPC peering connection request fails and cannot be accepted\.
+ If you're using AWS Direct Connect to connect to multiple VPCs through a Direct Connect gateway, the VPCs that are associated with the Direct Connect gateway must not have overlapping CIDR blocks\. If you add a CIDR block to one of the VPCs that's associated with the Direct Connect gateway, ensure that the new CIDR block does not overlap with an existing CIDR block of any other associated VPC\. For more information, see [Direct Connect gateways](https://docs.aws.amazon.com/directconnect/latest/UserGuide/direct-connect-gateways.html) in the *AWS Direct Connect User Guide*\.
+ When you add or remove a CIDR block, it can go through various states: `associating` \| `associated` \| `disassociating` \| `disassociated` \| `failing` \| `failed`\. The CIDR block is ready for you to use when it's in the `associated` state\.

You can disassociate a CIDR block that you've associated with your VPC; however, you cannot disassociate the CIDR block with which you originally created the VPC \(the primary CIDR block\)\. To view the primary CIDR for your VPC in the Amazon VPC console, choose **Your VPCs**, select the checkbox for your VPC, and choose the **CIDRs** tab\. To view the primary CIDR using the AWS CLI, use the [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) command as follows\. The primary CIDR is returned in the top\-level `CidrBlock element`\.

```
aws ec2 describe-vpcs --vpc-id vpc-1a2b3c4d --query Vpcs[*].CidrBlock
```

The following is example output\.

```
[
           "10.0.0.0/16", 
       ]
```

### IPv4 CIDR block association restrictions<a name="add-cidr-block-restrictions"></a>

The following table provides an overview of permitted and restricted VPC CIDR block associations\.


| IP address range | Restricted associations | Permitted associations | 
| --- | --- | --- | 
|  10\.0\.0\.0/8  |  CIDR blocks from other RFC 1918\* ranges \(172\.16\.0\.0/12 and 192\.168\.0\.0/16\)\. If any of the CIDR blocks associated with the VPC are from the 10\.0\.0\.0/15 range \(10\.0\.0\.0 to 10\.1\.255\.255\), you cannot add a CIDR block from the 10\.0\.0\.0/16 range \(10\.0\.0\.0 to 10\.0\.255\.255\)\. CIDR blocks from the 198\.19\.0\.0/16 range\.  |  Any other CIDR block from the 10\.0\.0\.0/8 range that's not restricted\. Any publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 
|  172\.16\.0\.0/12  |  CIDR blocks from other RFC 1918\* ranges \(10\.0\.0\.0/8 and 192\.168\.0\.0/16\)\. CIDR blocks from the 172\.31\.0\.0/16 range\. CIDR blocks from the 198\.19\.0\.0/16 range\.  |  Any other CIDR block from the 172\.16\.0\.0/12 range that's not restricted\. Any publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 
|  192\.168\.0\.0/16  |  CIDR blocks from other RFC 1918\* ranges \(10\.0\.0\.0/8 and 172\.16\.0\.0/12\)\. CIDR blocks from the 198\.19\.0\.0/16 range\.  |  Any other CIDR block from the 192\.168\.0\.0/16 range\. Any publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 
|  198\.19\.0\.0/16  |  CIDR blocks from the RFC 1918\* ranges\.  |  Any publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 
|  Publicly routable CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range  |  CIDR blocks from the RFC 1918\* ranges\. CIDR blocks from the 198\.19\.0\.0/16 range\.  |  Any other publicly routable IPv4 CIDR block \(non\-RFC 1918\), or a CIDR block from the 100\.64\.0\.0/10 range\.  | 

\* RFC 1918 ranges are the private IPv4 address ranges specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)\.

### IPv6 VPC CIDR blocks<a name="vpc-sizing-ipv6"></a>

You can associate a single IPv6 CIDR block when you create a new VPC with an existing VPC in your account or you can associate up to five by modifying an existing VPC\. The CIDR block is a fixed prefix length of `/56`\. You can request an IPv6 CIDR block from Amazon's pool of IPv6 addresses\. For more information, see [Associate IPv6 CIDR blocks with your VPC](working-with-vpcs.md#vpc-associate-ipv6-cidr)\.

If you've associated an IPv6 CIDR block with your VPC, you can associate an IPv6 CIDR block with an existing subnet in your VPC or when you create a new subnet\. For more information, see [Subnet sizing for IPv6](configure-subnets.md#subnet-sizing-ipv6)\.

For example, you create a VPC and specify that you want to associate an Amazon\-provided IPv6 CIDR block with the VPC\. Amazon assigns the following IPv6 CIDR block to your VPC: `2001:db8:1234:1a00::/56`\. You cannot choose the range of IP addresses yourself\. You can create a subnet and associate an IPv6 CIDR block from this range; for example, `2001:db8:1234:1a00::/64`\.

You can disassociate an IPv6 CIDR block from a VPC\. After you've disassociated an IPv6 CIDR block from a VPC, you cannot expect to receive the same CIDR if you associate an IPv6 CIDR block with your VPC again later\.