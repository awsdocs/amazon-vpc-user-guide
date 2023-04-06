# Virtual private clouds \(VPC\)<a name="configure-your-vpc"></a>

A *virtual private cloud* \(VPC\) is a virtual network dedicated to your AWS account\. It is logically isolated from other virtual networks in the AWS Cloud\. You can launch AWS resources, such as Amazon EC2 instances, into your VPC\.

Your account contains a default VPC for each AWS Region\. You can also create additional VPCs\.

**Topics**
+ [VPC basics](#vpc-subnet-basics)
+ [Default VPCs](default-vpc.md)
+ [Create a VPC](create-vpc.md)
+ [Configure your VPC](modify-vpcs.md)
+ [DHCP option sets](VPC_DHCP_Options.md)
+ [DNS attributes](vpc-dns.md)
+ [Network Address Usage](network-address-usage.md)
+ [Share your VPC](vpc-sharing.md)
+ [Extend a VPC to another Zone](Extend_VPCs.md)
+ [Delete your VPC](delete-vpc.md)

## VPC basics<a name="vpc-subnet-basics"></a>

A VPC spans all of the Availability Zones in a Region\. After you create a VPC, you can add one or more subnets in each Availability Zone\. For more information, see [Subnets for your VPC](configure-subnets.md)\.

### VPC IP address range<a name="vpc-ip-address-range"></a>

When you create a VPC, you specify its IP addresses as follows:
+ **IPv4 only** – The VPC has an IPv4 CIDR block but does not have an IPv6 CIDR block\.
+ **Dual stack** – The VPC has both an IPv4 CIDR block and an IPv6 CIDR block\.

For more information, see [IP addressing for your VPCs and subnets](vpc-ip-addressing.md)\.

### VPC diagram<a name="vpc-diagram"></a>

The following diagram shows a VPC with no additional VPC resources\.

![\[A VPC that spans the Availability Zones for its Region.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-diagram.png)

### VPC resources<a name="vpc-resources"></a>

Each VPC automatically comes with the following resources:
+ [Default DHCP option set](DHCPOptionSetConcepts.md#ArchitectureDiagram)
+ [Default network ACL](vpc-network-acls.md#default-network-acl)
+ [Default security group](default-security-group.md)
+ [Main route table](VPC_Route_Tables.md#main-route-table)

You can create the following resources for your VPC:
+ [Custom network ACL](vpc-network-acls.md)
+ [Custom route table](VPC_Route_Tables.md)
+ [Custom security group](security-groups.md)
+ [Internet gateway](VPC_Internet_Gateway.md)
+ [NAT gateways](vpc-nat-gateway.md)