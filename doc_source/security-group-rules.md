# Security group rules<a name="security-group-rules"></a>

The rules of a security group control the inbound traffic that's allowed to reach the resources that are associated with the security group\. The rules also control the outbound traffic that's allowed to leave them\.

You can add or remove rules for a security group \(also referred to as *authorizing* or *revoking* inbound or outbound access\)\. A rule applies either to inbound traffic \(ingress\) or outbound traffic \(egress\)\. You can grant access to a specific source or destination\.

**Warning**  
When you add rules for ports 22 \(SSH\) or 3389 \(RDP\) so that you can access your EC2 instances, we recommend that you authorize only specific IP address ranges\. If you specify 0\.0\.0\.0/0 \(IPv4\) and ::/ \(IPv6\), this enables anyone to access your instances from any IP address using the specified protocol\.

**Topics**
+ [Characteristics of security group rules](#security-group-rule-characteristics)
+ [Components of a security group rule](#security-group-rule-components)
+ [Security group referencing](#security-group-referencing)
+ [Security group size](#security-group-size)
+ [Stale security group rules](#vpc-stale-security-group-rules)
+ [Example rules](#security-group-rule-examples)
+ [Work with security group rules](#working-with-security-group-rules)

## Characteristics of security group rules<a name="security-group-rule-characteristics"></a>
+ You can specify allow rules, but not deny rules\.
+ When you first create a security group, it has no inbound rules\. Therefore, no inbound traffic is allowed until you add inbound rules to the security group\.
+ When you first create a security group, it has an outbound rule that allows all outbound traffic from the resource\. You can remove the rule and add outbound rules that allow specific outbound traffic only\. If your security group has no outbound rules, no outbound traffic is allowed\.
+ When you associate multiple security groups with a resource, the rules from each security group are aggregated to form a single set of rules that are used to determine whether to allow access\.
+ When you add, update, or remove rules, your changes are automatically applied to all resources associated with the security group\. The effect of some rule changes can depend on how the traffic is tracked\. For more information, see [Connection tracking](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#security-group-connection-tracking) in the *Amazon EC2 User Guide for Linux Instances*\.
+ When you create a security group rule, AWS assigns a unique ID to the rule\. You can use the ID of a rule when you use the API or CLI to modify or delete the rule\.

**Note**  
Security groups cannot block DNS requests to or from the Route 53 Resolver, sometimes referred to as the 'VPC\+2 IP address' \(see [Amazon Route 53 Resolver](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver.html) in the *Amazon Route 53 Developer Guide*, or as [AmazonProvidedDNS](DHCPOptionSet.md)\. To filter DNS requests through the Route 53 Resolver, use [Route 53 Resolver DNS Firewall](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall.html)\.

## Components of a security group rule<a name="security-group-rule-components"></a>
+ **Protocol**: The protocol to allow\. The most common protocols are 6 \(TCP\), 17 \(UDP\), and 1 \(ICMP\)\.
+ **Port range**: For TCP, UDP, or a custom protocol, the range of ports to allow\. You can specify a single port number \(for example, `22`\), or range of port numbers \(for example, `7000-8000`\)\.
+ **ICMP type and code**: For ICMP, the ICMP type and code\. For example, use type 8 for ICMP Echo Request or type 128 for ICMPv6 Echo Request\.
+ **Source or destination**: The source \(inbound rules\) or destination \(outbound rules\) for the traffic to allow\. Specify one of the following:
  + A single IPv4 address\. You must use the `/32` prefix length\. For example, `203.0.113.1/32`\. 
  + A single IPv6 address\. You must use the `/128` prefix length\. For example, `2001:db8:1234:1a00::123/128`\.
  + A range of IPv4 addresses, in CIDR block notation\. For example, `203.0.113.0/24`\.
  + A range of IPv6 addresses, in CIDR block notation\. For example, `2001:db8:1234:1a00::/64`\.
  + The ID of a prefix list\. For example, `pl-1234abc1234abc123`\. For more information, see [Group CIDR blocks using managed prefix lists](managed-prefix-lists.md)\.
  + The ID of a security group\. For example, `sg-1234567890abcdef0`\. For more information, see [Security group referencing](#security-group-referencing)\.
+ **\(Optional\) Description**: You can add a description for the rule, which can help you identify it later\. A description can be up to 255 characters in length\. Allowed characters are a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=;\{\}\!$\*\.

## Security group referencing<a name="security-group-referencing"></a>

When you specify a security group as the source or destination for a rule, the rule affects all instances that are associated with the security groups\. The instances can communicate in the specified direction, using the private IP addresses of the instances, over the specified protocol and port\.

For example, the following table shows an inbound rule for security group sg\-11111111111111111 that references security group sg\-22222222222222222 and allows SSH access\.


|  |  |  | 
| --- |--- |--- |
|  Source  |  Protocol  |  Port range  | 
|  sg\-22222222222222222  |  TCP  |  22  | 

When referencing a security group in a security group rule, note the following:
+ Both security groups must belong to the same VPC or to peered VPCs\.
+ No rules from the referenced security group \(sg\-22222222222222222\) are added to the security group that references it \(sg\-11111111111111111\)\.
+ For inbound rules, the EC2 instances associated with security group sg\-11111111111111111 can receive inbound traffic from the private IP addresses of the EC2 instances associated with security group sg\-22222222222222222\.
+ For outbound rules, the EC2 instances associated with security group sg\-11111111111111111 can send outbound traffic to the private IP addresses of the EC2 instances associated with security group sg\-22222222222222222\.

**Limitation**  
If you configure routes to forward the traffic between two instances in different subnets through a middlebox appliance, you must ensure that the security groups for both instances allow traffic to flow between the instances\. The security group for each instance must reference the private IP address of the other instance or the CIDR range of the subnet that contains the other instance as the source\. If you reference the security group of the other instance as the source, this does not allow traffic to flow between the instances\.

## Security group size<a name="security-group-size"></a>

The type of source or destination determines how each rule counts toward the maximum number of rules that you can have per security group\.
+ A rule that references a CIDR block counts as one rule\.
+ A rule that references another security group counts as one rule, no matter the size of the referenced security group\.
+ A rule that references a customer\-managed prefix list counts as the maximum size of the prefix list\. For example, if the maximum size of your prefix list is 20, a rule that references this prefix list counts as 20 rules\.
+ A rule that references an AWS\-managed prefix list counts as its weight\. For more information, see [Available AWS\-managed prefix lists](working-with-aws-managed-prefix-lists.md#available-aws-managed-prefix-lists)\.

## Stale security group rules<a name="vpc-stale-security-group-rules"></a>

If your VPC has a VPC peering connection with another VPC, or if it uses a VPC shared by another account, a security group rule in your VPC can reference a security group in that peer VPC or shared VPC\. This allows resources that are associated with the referenced security group and those that are associated with the referencing security group to communicate with each other\.

If the security group in the shared VPC is deleted, or if the VPC peering connection is deleted, the security group rule is marked as stale\. You can delete stale security group rules as you would any other security group rule\. For more information, see [Work with stale security group rules](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html#vpc-peering-stale-groups) in the *Amazon VPC Peering Guide*\.

## Example rules<a name="security-group-rule-examples"></a>

The rules that you add to a security group often depend on the purpose of the security group\. The following table describes example rules for a security group that's associated with web servers\. Your web servers can receive HTTP and HTTPS traffic from all IPv4 and IPv6 addresses and send SQL or MySQL traffic to your database servers\.


| 
| 
| Inbound | 
| --- |
|  Source  |  Protocol  |  Port range  |  Description  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allows inbound HTTP access from all IPv4 addresses  | 
| ::/0 | TCP | 80 | Allows inbound HTTP access from all IPv6 addresses | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allows inbound HTTPS access from all IPv4 addresses  | 
| ::/0 | TCP | 443 | Allows inbound HTTPS access from all IPv6 addresses | 
|  Public IPv4 address range of your network  |  TCP  |  22  |  Allows inbound SSH access from IPv4 IP addresses in your network  | 
|  Public IPv4 address range of your network  |  TCP  |  3389  |  Allows inbound RDP access from IPv4 IP addresses in your network  | 
|   Outbound   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Description  | 
|  ID of the security group for instances running Microsoft SQL Server  |  TCP  |  1433  |  Allow outbound Microsoft SQL Server access  | 
|  ID of the security group for instances running MySQL  |  TCP  |  3306  |  Allow outbound MySQL access  | 

Database servers need rules that allow inbound specific protocols, such as MySQL or Microsoft SQL Server\. For examples, see [Database server rules](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/security-group-rules-reference.html#sg-rules-db-server) in the *Amazon EC2 User Guide*\. For more information about security groups for Amazon RDS DB instances, see [Controlling access with security groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.RDSSecurityGroups.html) in the *Amazon RDS User Guide*\.

## Work with security group rules<a name="working-with-security-group-rules"></a>

The following tasks show you how to work with security group rules\.

**Required permissions**
+ [Manage security group rules](vpc-policy-examples.md#vpc-security-group-rules-iam)

**Topics**
+ [Add rules to a security group](#adding-security-group-rules)
+ [Update security group rules](#updating-security-group-rules)
+ [Tag security group rules](#tagging-security-group-rules)
+ [Delete security group rules](#deleting-security-group-rules)

### Add rules to a security group<a name="adding-security-group-rules"></a>

When you add a rule to a security group, the new rule is automatically applied to any resources that are associated with the security group\.

If you have a VPC peering connection, you can reference security groups from the peer VPC as the source or destination in your security group rules\. For more information, see [Updating your security groups to reference peer VPC security groups](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html) in the *Amazon VPC Peering Guide*\.

For information about the permissions required to manage security group rules, see [Manage security group rules](vpc-policy-examples.md#vpc-security-group-rules-iam)\.

**To add a rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security groups**\.

1. Select the security group\.

1. Choose **Actions**, **Edit inbound rules** or **Actions**, **Edit outbound rules**\.

1. For each rule, choose **Add rule** and do the following\.

   1. For **Type**, choose the type of protocol to allow\.
      + For TCP or UDP, you must enter the port range to allow\.
      + For custom ICMP, you must choose the ICMP type name from **Protocol**, and, if applicable, the code name from **Port range**\.
      + For any other type, the protocol and port range are configured automatically\.

   1. For **Source type** \(inbound rules\) or **Destination type** \(outbound rules\), do one of the following to allow traffic:
      + Choose **Custom** and then enter an IP address in CIDR notation, a CIDR block, another security group, or a prefix list\.
      + Choose **Anywhere\-IPv4** to allow traffic from any IPv4 address \(inbound rules\) or to allow traffic to reach all IPv4 addresses \(outbound rules\)\. This automatically adds a rule for the 0\.0\.0\.0/0 IPv4 CIDR block\.
**Warning**  
If you choose **Anywhere\-IPv4**, you enable all IPv4 addresses to access your instance using the specified protocol\. If you are adding rules for ports 22 \(SSH\) or 3389 \(RDP\), you should authorize only a specific IP address or range of addresses to access your instance\.
      + Choose **Anywhere\-IPv6** to allow traffic from any IPv6 address \(inbound rules\) or to allow traffic to reach all IPv6 addresses \(outbound rules\)\. This automatically adds a rule for the ::/0 IPv6 CIDR block\.
**Warning**  
If you choose **Anywhere\-IPv6**, you enable all IPv6 addresses to access your instance using the specified protocol\. If you are adding rules for ports 22 \(SSH\) or 3389 \(RDP\), you should authorize only a specific IP address or range of addresses to access your instance\.
      + Choose **My IP** to allow traffic only from \(inbound rules\) or to \(outbound rules\) your local computer's public IPv4 address\.

   1. \(Optional\) For **Description**, specify a brief description for the rule\.

1. Choose **Save rules**\.

**To add a rule to a security group using the AWS CLI**  
Use the [authorize\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) and [authorize\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-egress.html) commands\.

### Update security group rules<a name="updating-security-group-rules"></a>

When you update a rule, the updated rule is automatically applied to any resources that are associated with the security group\.

For information about the permissions required to manage security group rules, see [Manage security group rules](vpc-policy-examples.md#vpc-security-group-rules-iam)\.

**To update a rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security groups**\.

1. Select the security group\.

1. Choose **Actions**, **Edit inbound rules** or **Actions**, **Edit outbound rules**\.

1. Update the rule as required\.

1. Choose **Save rules**\.

**To update a security group rule using the AWS CLI**  
Use the [modify\-security\-group\-rules](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-security-group-rules.html), [update\-security\-group\-rule\-descriptions\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-ingress.html), and [update\-security\-group\-rule\-descriptions\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-egress.html) commands\.

### Tag security group rules<a name="tagging-security-group-rules"></a>

Add tags to your resources to help organize and identify them, such as by purpose, owner, or environment\. You can add tags to security group rules\. Tag keys must be unique for each security group rule\. If you add a tag with a key that is already associated with the security group rule, it updates the value of that tag\.

**To tag a rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security groups**\.

1. Select the security group\.

1. On the **Inbound rules** or **Outbound rules** tab, select the check box for the rule and then choose **Manage tags**\.

1. The **Manage tags** page displays any tags that are assigned to the rule\. To add a tag, choose **Add tag** and enter the tag key and value\. To delete a tag, choose **Remove** next to the tag that you want to delete\.

1. Choose **Save changes**\.

**To tag a rule using the AWS CLI**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command\.

### Delete security group rules<a name="deleting-security-group-rules"></a>

When you delete a rule from a security group, the change is automatically applied to any instances that are associated with the security group\.

**To delete a security group rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security groups**\.

1. Select the security group\.

1. Choose **Actions**, and then choose **Edit inbound rules** to remove an inbound rule or **Edit outbound rules** to remove an outbound rule\.

1. Choose the **Delete** button next to the rule to delete\.

1. Choose **Save rules**\.

**To delete a security group rule using the AWS CLI**  
Use the [revoke\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-ingress.html) and [revoke\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-egress.html) commands\.