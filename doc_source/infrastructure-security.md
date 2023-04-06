# Infrastructure security in Amazon VPC<a name="infrastructure-security"></a>

You use AWS published API calls to access Amazon VPC through the network\. Clients must support Transport Layer Security \(TLS\) 1\.1 or later\. We recommend TLS 1\.2 or later\. Clients must also support cipher suites with perfect forward secrecy \(PFS\), such as Ephemeral Diffie\-Hellman \(DHE\) or Elliptic Curve Ephemeral Diffie\-Hellman \(ECDHE\)\. Most modern systems, such as Java 7, and later support these modes\.

Additionally, requests must be signed using an access key ID and a secret access key that is associated with an IAM principal\. Or you can use the [AWS Security Token Service](https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html) \(AWS STS\) to generate temporary security credentials to sign requests\.

For more information, see [ Infrastructure protection](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/infrastructure-protection.html) in the *Security Pillar – AWS Well\-Architected Framework*\.

## Network isolation<a name="network-isolation"></a>

A virtual private cloud \(VPC\) is a virtual network in your own logically isolated area in the AWS Cloud\. Use separate VPCs to isolate infrastructure by workload or organizational entity\.

A subnet is a range of IP addresses in a VPC\. When you launch an instance, you launch it into a subnet in your VPC\. Use subnets to isolate the tiers of your application \(for example, web, application, and database\) within a single VPC\. Use private subnets for your instances if they should not be accessed directly from the internet\.

You can use [AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/privatelink/) to enable resources in your VPC to connect to AWS services using private IP addresses, as if those services were hosted directly in your VPC\. Therefore, you do not need to use an internet gateway or NAT device to access AWS services\.

## Control network traffic<a name="control-network-traffic"></a>

Consider the following options for controlling network traffic to your EC2 instances:
+ Restrict access to your subnets using [security groups](vpc-security-groups.md)\. For example, you can allow traffic only from the address ranges for your corporate network\.
+ Leverage security groups as the primary mechanism for controlling network access to VPCs\. When necessary, use network ACLs sparingly to provide stateless, coarse\-grain network control\. Security groups are more versatile than network ACLs due to their ability to perform stateful packet filtering and create rules that reference other security groups\. However, network ACLs can be effective as a secondary control for denying a specific subset of traffic or providing high\-level subnet guard rails\. Also, because network ACLs apply to an entire subnet, they can be used as defense\-in\-depth in case an instance is ever launched unintentionally without a correct security group\.
+ Use private subnets for your instances if they should not be accessed directly from the internet\. Use a bastion host or NAT gateway for internet access from an instance in a private subnet\.
+ Configure subnet route tables with the minimal required network routes\. For example, place only the Amazon EC2 instances that require direct internet access into subnets with routes to an internet gateway, and place only the Amazon EC2 instances that need direct access to internal networks into subnets with routes to a virtual private gateway\.
+ Consider using additional security groups or network interfaces to control and audit Amazon EC2 instance management traffic separately from regular application traffic\. This approach allows customers to implement special IAM policies for change control, making it easier to audit changes to security group rules or automated rule\-verification scripts\. Multiple network interfaces also provide additional options for controlling network traffic including the ability to create host\-based routing policies or leverage different VPC subnet routing rules based on an network interfaces assigned to a subnet\.
+ Use AWS Virtual Private Network or AWS Direct Connect to establish private connections from your remote networks to your VPCs\. For more information, see [Network\-to\-Amazon VPC connectivity options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/network-to-amazon-vpc-connectivity-options.html)\.
+ Use [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) to monitor the traffic that reaches your instances\.
+ Use [AWS Security Hub](http://aws.amazon.com/security-hub/) to check for unintended network accessibility from your instances\.
+ Use [AWS Network Firewall](network-firewall.md) to protect the subnets in your VPC from common network threats\.