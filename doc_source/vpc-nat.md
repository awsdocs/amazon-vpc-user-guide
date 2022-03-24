# Connect to the internet or other networks using NAT devices<a name="vpc-nat"></a>

You can use a NAT device to allow resources in private subnets to connect to the internet, other VPCs, or on\-premises networks\. These instances can communicate with services outside the VPC, but they cannot receive unsolicited connection requests\.

The NAT device replaces the source IPv4 address of the instances with the address of the NAT device\. When sending response traffic to the instances, the NAT device translates the addresses back to the original source IPv4 addresses\.

You can use a managed NAT device offered by AWS, called a *NAT gateway*, or you can create your own NAT device on an EC2 instance, called a *NAT instance*\. We recommend that you use NAT gateways because they provide better availability and bandwidth and require less effort on your part to administer\.

**Considerations**
+ NAT devices are not supported for IPv6 traffic—use an egress\-only internet gateway instead\. For more information, see [Enable outbound IPv6 traffic using an egress\-only internet gateway](egress-only-internet-gateway.md)\.
+ We use the term *NAT* in this documentation to follow common IT practice, though the actual role of a NAT device is both address translation and port address translation \(PAT\)\.

**Topics**
+ [NAT gateways](vpc-nat-gateway.md)
+ [NAT instances](VPC_NAT_Instance.md)
+ [Compare NAT gateways and NAT instances](vpc-nat-comparison.md)