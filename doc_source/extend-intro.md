# Connect your VPC to other networks<a name="extend-intro"></a>

You can connect your virtual private cloud \(VPC\) to other networks\. For example, other VPCs, the internet, or your on\-premises network\.

The following diagram demonstrates some of these connectivity options\. VPC A is connected to the internet through an internet gateway\. The EC2 instance in the private subnet of VPC A can connect to the internet using the NAT gateway in the public subnet of VPC A\. VPC B is connected to the internet through an internet gateway\. The EC2 instance in the public subnet of VPC B can connect to the internet using the internet gateway\. VPC A and VPC B are connected to each other through a VPC peering connection and a transit gateway\. The transit gateway has a VPN attachment to a data center\. VPC B has a AWS Direct Connect connection to a data center\.

![\[VPC connectivity options\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/connectivity-overview.png)

For more information, see [Amazon Virtual Private Cloud Connectivity Options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/)\.

**Topics**
+ [Connect to the internet using an internet gateway](VPC_Internet_Gateway.md)
+ [Enable outbound IPv6 traffic using an egress\-only internet gateway](egress-only-internet-gateway.md)
+ [Connect to the internet or other networks using NAT devices](vpc-nat.md)
+ [Connect your VPC to other VPCs and networks using a transit gateway](extend-tgw.md)
+ [Connect your VPC to remote networks using AWS Virtual Private Network](vpn-connections.md)
+ [Connect VPCs using VPC peering](vpc-peering.md)