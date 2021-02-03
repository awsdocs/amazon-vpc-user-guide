# NAT<a name="vpc-nat"></a>

You can use a NAT device to enable instances in a private subnet to connect to the internet \(for example, for software updates\) or other AWS services, but prevent the internet from initiating connections with the instances\. A NAT device forwards traffic from the instances in the private subnet to the internet or other AWS services, and then sends the response back to the instances\. When traffic goes to the internet, the source IPv4 address is replaced with the NAT device’s address and similarly, when the response traffic goes to those instances, the NAT device translates the address back to those instances’ private IPv4 addresses\. 

NAT devices are not supported for IPv6 traffic—use an egress\-only Internet gateway instead\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.

**Note**  
We use the term *NAT* in this documentation to follow common IT practice, though the actual role of a NAT device is both address translation and port address translation \(PAT\)\. 

You can either use a managed NAT device offered by AWS called a NAT gateway, or you can create your own NAT device in an EC2 instance, referred to here as a NAT instance\. We recommend NAT gateways, because they provide better availability and bandwidth over NAT instances\. The NAT gateway service is also a managed service that does not require your administration efforts\. 
+ [NAT gateways](vpc-nat-gateway.md)
+ [NAT instances](VPC_NAT_Instance.md)
+ [Comparison of NAT instances and NAT gateways](vpc-nat-comparison.md)

## NAT AMI \(end of support\)<a name="vpc-nat-ami"></a>

**Important**  
NAT AMI is built on the last version of Amazon Linux, 2018\.03 which reached the end of standard support on December 31, 2020\. For more information, see the following blog post: [Amazon Linux AMI end of life](http://aws.amazon.com/blogs/aws/update-on-amazon-linux-ami-end-of-life/)\. This feature will only receive critical security updates \(there will be no regular updates\)\.   
If you use an existing NAT AMI, AWS recommends that you migrate to a NAT gateway or create your own NAT AMI on Amazon Linux 2 as soon as possible\. For information about how to migrate your instance, see [Migrating from a NAT instance](vpc-nat-gateway.md#nat-instance-migrate)\.