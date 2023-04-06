# Default security groups for your VPCs<a name="default-security-group"></a>

Your default VPCs and any VPCs that you create come with a default security group\. The name of the default security group is "default"\.

**Default security group basics**
+ You can change the rules for a default security group\.
+ You can't delete a default security group\. If you try to delete it, we return the following error: `Client.CannotDelete`\.
+ With some resources, if you don't associate a security group when you create the resource, we associate the default security group\. For example, if you don't specify a security group when you launch an EC2 instance, we associate the instance with the default security group\.

**Default rules**  
The following table describes the default rules for a default security group\.


| 
| 
| Inbound | 
| --- |
| Source | Protocol | Port range | Description | 
| sg\-1234567890abcdef0  | All | All | Allows inbound traffic from all resources that are assigned to this security group\. The source is the ID of this security group\. | 
|  Outbound  | 
| --- |
| Destination | Protocol | Port range | Description | 
| 0\.0\.0\.0/0 | All | All | Allows all outbound IPv4 traffic\. | 
| ::/0 | All | All | Allows all outbound IPv6 traffic\. This rule is added only if your VPC has an associated IPv6 CIDR block\. | 

**Example**  
The following diagram shows a VPC with a default security group, an internet gateway, and a NAT gateway\. The default security contains only its default rules, and it is associated with two EC2 instances running in the VPC\. In this scenario, each instance can receive inbound traffic from the other instance on all ports and protocols\. The instances can't receive traffic from the internet gateway or the NAT gateway\.

![\[A VPC with two subnets, a default security group, two EC2 instances associated with the default security group, an internet gateway, and a NAT gateway.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/default-security-group.png)