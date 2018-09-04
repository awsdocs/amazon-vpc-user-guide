# Security<a name="VPC_Security"></a>

Amazon Virtual Private Cloud provides features that you can use to increase and monitor the security for your virtual private cloud \(VPC\):
+ Security groups — Act as a firewall for associated Amazon EC2 instances, controlling both inbound and outbound traffic at the instance level
+ Network access control lists \(ACLs\) — Act as a firewall for associated subnets, controlling both inbound and outbound traffic at the subnet level
+ Flow logs — Capture information about the IP traffic going to and from network interfaces in your VPC

When you launch an instance in a VPC, you can associate one or more security groups that you've created\. Each instance in your VPC could belong to a different set of security groups\. If you don't specify a security group when you launch an instance, the instance automatically belongs to the default security group for the VPC\. For more information about security groups, see [Security Groups for Your VPC](VPC_SecurityGroups.md)

You can secure your VPC instances using only security groups; however, you can add network ACLs as an additional layer of defense\. For more information, see [Network ACLs](vpc-network-acls.md)\.

You can monitor the accepted and rejected IP traffic going to and from your instances by creating a flow log for a VPC, subnet, or individual network interface\. Flow log data is published to CloudWatch Logs, and can help you diagnose overly restrictive or overly permissive security group and network ACL rules\. For more information, see [VPC Flow Logs](flow-logs.md)\.

You can use AWS Identity and Access Management to control who in your organization has permission to create and manage security groups, network ACLs and flow logs\. For example, you can give only your network administrators that permission, but not personnel who only need to launch instances\. For more information, see [Controlling Access to Amazon VPC Resources](VPC_IAM.md)\.

Amazon security groups and network ACLs don't filter traffic to or from link\-local addresses \(`169.254.0.0/16`\) or AWS\-reserved IPv4 addresses—these are the first four IPv4 addresses of the subnet \(including the Amazon DNS server address for the VPC\)\. Similarly, flow logs do not capture IP traffic to or from these addresses\. These addresses support the services: Domain Name Services \(DNS\), Dynamic Host Configuration Protocol \(DHCP\), Amazon EC2 instance metadata, Key Management Server \(KMS—license management for Windows instances\), and routing in the subnet\. You can implement additional firewall solutions in your instances to block network communication with link\-local addresses\.

## Comparison of Security Groups and Network ACLs<a name="VPC_Security_Comparison"></a>

The following table summarizes the basic differences between security groups and network ACLs\.


| Security Group | Network ACL | 
| --- | --- | 
|  Operates at the instance level  |  Operates at the subnet level  | 
|  Supports allow rules only  |  Supports allow rules and deny rules  | 
|  Is stateful: Return traffic is automatically allowed, regardless of any rules  |  Is stateless: Return traffic must be explicitly allowed by rules  | 
|  We evaluate all rules before deciding whether to allow traffic  |  We process rules in number order when deciding whether to allow traffic  | 
|  Applies to an instance only if someone specifies the security group when launching the instance, or associates the security group with the instance later on  |  Automatically applies to all instances in the subnets it's associated with \(therefore, you don't have to rely on users to specify the security group\)  | 

The following diagram illustrates the layers of security provided by security groups and network ACLs\. For example, traffic from an Internet gateway is routed to the appropriate subnet using the routes in the routing table\. The rules of the network ACL associated with the subnet control which traffic is allowed to the subnet\. The rules of the security group associated with an instance control which traffic is allowed to the instance\.

![\[Traffic is controlled using security groups and network ACLs\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/security-diagram.png)