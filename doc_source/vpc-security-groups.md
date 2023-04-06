# Control traffic to resources using security groups<a name="vpc-security-groups"></a>

A *security group* controls the traffic that is allowed to reach and leave the resources that it is associated with\. For example, after you associate a security group with an EC2 instance, it controls the inbound and outbound traffic for the instance\. You can associate a security group only with resources in the VPC for which it is created\.

When you create a VPC, it comes with a default security group\. You can create additional security groups for each VPC\.

There is no additional charge for using security groups\.

The following diagram shows a VPC with subnets in two Availability Zones, an internet gateway, and an Application Load Balancer\. Each Availability Zone has a public subnet for web servers and a private subnet for database servers\. There are separate security groups for the load balancer, the web servers, and the database servers\. You can add rules to the security group for the load balancer to allow HTTP and HTTPS traffic from the internet\. You can add rules to the security group for the web servers to allow traffic only from the load balancer\. You can add rules to the security group for the database servers to allow only database requests from the web servers\.

![\[A VPC with two security groups, servers in two Availability Zones, an internet gateway, and an Application Load Balancer. There is one security group for the web servers in the public subnets and another security group for the database servers in the private subnets.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/security-group.png)

For information about the differences between security groups and network ACLs, see [Compare security groups and network ACLs](VPC_Security.md#VPC_Security_Comparison)\.

**Topics**
+ [Security groups](security-groups.md)
+ [Security group rules](security-group-rules.md)
+ [Default security groups for your VPCs](default-security-group.md)