# NAT<a name="vpc-nat"></a>

You can use a NAT device to enable instances in a private subnet to connect to the internet \(for example, for software updates\) or other AWS services, but prevent the internet from initiating connections with the instances\. A NAT device forwards traffic from the instances in the private subnet to the internet or other AWS services, and then sends the response back to the instances\. When traffic goes to the internet, the source IPv4 address is replaced with the NAT device’s address and similarly, when the response traffic goes to those instances, the NAT device translates the address back to those instances’ private IPv4 addresses\. 

NAT devices are not supported for IPv6 traffic—use an egress\-only Internet gateway instead\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.

**Note**  
We use the term *NAT* in this documentation to follow common IT practice, though the actual role of a NAT device is both address translation and port address translation \(PAT\)\. 

AWS offers two kinds of NAT devices—a *NAT gateway* or a *NAT instance*\. We recommend NAT gateways, as they provide better availability and bandwidth over NAT instances\. The NAT Gateway service is also a managed service that does not require your administration efforts\. A NAT instance is launched from a NAT AMI\. You can choose to use a NAT instance for special purposes\.
+ [NAT gateways](vpc-nat-gateway.md)
+ [NAT instances](VPC_NAT_Instance.md)
+ [Comparison of NAT instances and NAT gateways](vpc-nat-comparison.md)