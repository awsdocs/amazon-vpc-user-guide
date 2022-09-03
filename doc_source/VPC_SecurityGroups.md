# Control traffic to resources using security groups<a name="VPC_SecurityGroups"></a>

A *security group* controls the traffic that is allowed to reach and leave the resources that it is associated with\. For example, after you associate a security group with an EC2 instance, it controls the inbound and outbound traffic for the instance\.

When you create a VPC, it comes with a default security group\. You can create additional security groups for each VPC\. You can associate a security group only with resources in the VPC for which it is created\.

For each security group, you add *rules* that control the traffic based on protocols and port numbers\. There are separate sets of rules for inbound traffic and outbound traffic\.

You might set up network ACLs with rules similar to your security groups in order to add an additional layer of security to your VPC\. For more information about the differences between security groups and network ACLs, see [Compare security groups and network ACLs](VPC_Security.md#VPC_Security_Comparison)\.

**Topics**
+ [Security group basics](#VPCSecurityGroups)
+ [Default security groups for your VPCs](#DefaultSecurityGroup)
+ [Security group rules](#SecurityGroupRules)
+ [Work with security groups](#working-with-security-groups)
+ [Work with security group rules](#working-with-security-group-rules)
+ [Centrally manage VPC security groups using AWS Firewall Manager](#aws-firewall-manager)

## Security group basics<a name="VPCSecurityGroups"></a>

The following are the characteristics of security groups:
+ When you create a security group, you must provide it with a name and a description\. The following rules apply:
  + A security group name must be unique within the VPC\.
  + Names and descriptions can be up to 255 characters in length\.
  + Names and descriptions are limited to the following characters: a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=&;\{\}\!$\*\.
  + When the name contains trailing spaces, we trim the space at the end of the name\. For example, if you enter "Test Security Group " for the name, we store it as "Test Security Group"\.
  + A security group name cannot start with `sg-`\.
+ Security groups are stateful\. For example, if you send a request from an instance, the response traffic for that request is allowed to reach the instance regardless of the inbound security group rules\. Responses to allowed inbound traffic are allowed to leave the instance, regardless of the outbound rules\.
+ There are quotas on the number of security groups that you can create per VPC, the number of rules that you can add to each security group, and the number of security groups that you can associate with a network interface\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

The following are the characteristics of security group rules:
+ You can specify allow rules, but not deny rules\.
+ When you first create a security group, it has no inbound rules\. Therefore, no inbound traffic is allowed until you add inbound rules to the security group\.
+ When you first create a security group, it has an outbound rule that allows all outbound traffic from the resource\. You can remove the rule and add outbound rules that allow specific outbound traffic only\. If your security group has no outbound rules, no outbound traffic is allowed\.
+ When you associate multiple security groups with a resource, the rules from each security group are aggregated to form a single set of rules that are used to determine whether to allow access\.
+ When you add, update, or remove rules, your changes are automatically applied to all resources associated with the security group\. The effect of some rule changes can depend on how the traffic is tracked\. For more information, see [Connection tracking](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#security-group-connection-tracking) in the *Amazon EC2 User Guide for Linux Instances*\.
+ When you create a security group rule, AWS assigns a unique ID to the rule\. You can use the ID of a rule when you use the API or CLI to modify or delete the rule\.

## Default security groups for your VPCs<a name="DefaultSecurityGroup"></a>

Your default VPCs and any VPCs that you create come with a default security group\. With some resources, if you don't associate a security group when you create the resource, we associate the default security group\. For example, if you do not specify a security group when you launch an EC2 instance, we associate the default security group\.

You can change the rules for a default security group\. You can't delete a default security group\. If you try to delete the default security group, you get the following error: `Client.CannotDelete`\.

The following table describes the default rules for a default security group\.


| 
| 
| Inbound | 
| --- |
|  Source  |  Protocol  |  Port range  |  Description  | 
|  The security group ID \(its own resource ID\)  |  All  |  All  |  Allows inbound traffic from resources that are assigned to the same security group\.  | 
|   Outbound   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Description  | 
|  0\.0\.0\.0/0  |  All  |  All  |  Allows all outbound IPv4 traffic\.  | 
| ::/0 | All | All | Allows all outbound IPv6 traffic\. This rule is added only if your VPC has an associated IPv6 CIDR block\. | 

## Security group rules<a name="SecurityGroupRules"></a>

The rules of a security group control the inbound traffic that's allowed to reach the resources that are associated with the security group\. The rules also control the outbound traffic that's allowed to leave them\.

You can add or remove rules for a security group \(also referred to as *authorizing* or *revoking* inbound or outbound access\)\. A rule applies either to inbound traffic \(ingress\) or outbound traffic \(egress\)\. You can grant access to a specific CIDR range, or to another security group in your VPC or in a peer VPC \(requires a VPC peering connection\)\.

For each rule, you specify the following:
+ **Protocol**: The protocol to allow\. The most common protocols are 6 \(TCP\), 17 \(UDP\), and 1 \(ICMP\)\.
+ **Port range**: For TCP, UDP, or a custom protocol, the range of ports to allow\. You can specify a single port number \(for example, `22`\), or range of port numbers \(for example, `7000-8000`\)\.
+ **ICMP type and code**: For ICMP, the ICMP type and code\. For example, use type 8 for ICMP Echo Request or type 128 for ICMPv6 Echo Request\.
+ **Source or destination**: The source \(inbound rules\) or destination \(outbound rules\) for the traffic to allow\. Specify one of the following:
  + A single IPv4 address\. You must use the `/32` prefix length\. For example, `203.0.113.1/32`\. 
  + A single IPv6 address\. You must use the `/128` prefix length\. For example, `2001:db8:1234:1a00::123/128`\.
  + A range of IPv4 addresses, in CIDR block notation\. For example, `203.0.113.0/24`\.
  + A range of IPv6 addresses, in CIDR block notation\. For example, `2001:db8:1234:1a00::/64`\.
  + The ID of a prefix list\. For example, `pl-1234abc1234abc123`\. For more information, see [Group CIDR blocks using managed prefix lists](managed-prefix-lists.md)\.
  + The ID of a security group \(referred to here as the specified security group\)\. For example, the current security group, a security group from the same VPC, or a security group for a peered VPC\. This allows traffic based on the private IP addresses of the resources associated with the specified security group\. This does not add rules from the specified security group to the current security group\. †
+ **\(Optional\) Description**: You can add a description for the rule, which can help you identify it later\. A description can be up to 255 characters in length\. Allowed characters are a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=;\{\}\!$\*\.

† If you configure routes to forward the traffic between two instances in different subnets through a middlebox appliance, you must ensure that the security groups for both instances allow traffic to flow between the instances\. The security group for each instance must reference the private IP address of the other instance, or the CIDR range of the subnet that contains the other instance, as the source\. If you reference the security group of the other instance as the source, this does not allow traffic to flow between the instances\.

### Example rules<a name="security-group-rule-examples"></a>

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
|  Your network's public IPv4 address range  |  TCP  |  22  |  Allows inbound SSH access from IPv4 IP addresses in your network  | 
|  Your network's public IPv4 address range  |  TCP  |  3389  |  Allows inbound RDP access from IPv4 IP addresses in your network  | 
|   Outbound   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Description  | 
|  The ID of the security group for your Microsoft SQL Server database servers  |  TCP  |  1433  |  Allow outbound Microsoft SQL Server access  | 
|  The ID of the security group for your MySQL database servers  |  TCP  |  3306  |  Allow outbound MySQL access  | 

A database server needs a different set of rules\. For example, instead of inbound HTTP and HTTPS traffic, you can add a rule that allows inbound MySQL or Microsoft SQL Server access\. For examples, see [Security](VPC_Scenario3.md#VPC_Scenario3_Security)\. For more information about security groups for Amazon RDS DB instances, see [Controlling access with security groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.RDSSecurityGroups.html) in the *Amazon RDS User Guide*\.

For additional examples, see [Security group rules reference](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/security-group-rules-reference.html) in the *Amazon EC2 User Guide for Linux Instances*\.

### Stale security group rules<a name="vpc-stale-security-group-rules"></a>

If your VPC has a VPC peering connection with another VPC, or if it uses a VPC shared by another account, a security group rule in your VPC can reference a security group in that peer VPC or shared VPC\. This allows resources that are associated with the referenced security group and those that are associated with the referencing security group to communicate with each other\.

If the security group in the shared VPC is deleted, or if the VPC peering connection is deleted, the security group rule is marked as stale\. You can delete stale security group rules as you would any other security group rule\. For more information, see [Work with stale security group rules](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html#vpc-peering-stale-groups) in the *Amazon VPC Peering Guide*\.

## Work with security groups<a name="working-with-security-groups"></a>

The following tasks show you how to work with security groups using the Amazon VPC console\.

**Required permissions**
+ [Manage security groups](vpc-policy-examples.md#vpc-security-groups-iam)

**Topics**
+ [Create a security group](#creating-security-groups)
+ [View your security groups](#viewing-security-groups)
+ [Tag your security groups](#tagging-security-groups)
+ [Delete a security group](#deleting-security-groups)

### Create a security group<a name="creating-security-groups"></a>

By default, new security groups start with only an outbound rule that allows all traffic to leave the resource\. You must add rules to enable any inbound traffic or to restrict the outbound traffic\.

A security group can be used only in the VPC for which it is created\.

For information about the permissions required to create security groups and manage security group rules, see [Manage security groups](vpc-policy-examples.md#vpc-security-groups-iam) and [Manage security group rules](vpc-policy-examples.md#vpc-security-group-rules-iam)\.

**To create a security group using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create security group**\.

1. Enter a name and description for the security group\. You cannot change the name and description of a security group after it is created\.

1. From **VPC**, choose the VPC\.

1. You can add security group rules now, or you can add them later\. For more information, see [Add rules to a security group](#adding-security-group-rules)\.

1. You can add tags now, or you can add them later\. To add a tag, choose **Add new tag** and enter the tag key and value\.

1. Choose **Create security group**\.

**To create a security group using the command line**
+ [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) \(AWS CLI\)
+ [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

### View your security groups<a name="viewing-security-groups"></a>

You can view information about your security groups as follows\.

For information about the permissions required to view security groups, see [Manage security groups](vpc-policy-examples.md#vpc-security-groups-iam)\.

**To view your security groups using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Your security groups are listed\. To view the details for a specific security group, including its inbound and outbound rules, select the security group\.

**To view your security groups using the command line**
+ [describe\-security\-groups](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html) and [describe\-security\-group\-rules](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-group-rules.html) \(AWS CLI\)
+ [Get\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SecurityGroup.html) and [Get\-EC2SecurityGroupRules](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SecurityGroupRules.html) \(AWS Tools for Windows PowerShell\)

**To view all of your security groups across Regions**  
Open the Amazon EC2 Global View console at [ https://console\.aws\.amazon\.com/ec2globalview/home](https://console.aws.amazon.com/ec2globalview/home)\.

For more information about using Amazon EC2 Global View, see [List and filter resources using the Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view) in the Amazon EC2 User Guide for Linux Instances\.

### Tag your security groups<a name="tagging-security-groups"></a>

Add tags to your resources to help organize and identify them, such as by purpose, owner, or environment\. You can add tags to your security groups\. Tag keys must be unique for each security group\. If you add a tag with a key that is already associated with the rule, it updates the value of that tag\.

**To tag a security group using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the check box for the security group\.

1. Choose **Actions**, **Manage tags**\.

1. The **Manage tags** page displays any tags that are assigned to the security group\. To add a tag, choose **Add tag** and enter the tag key and value\. To delete a tag, choose **Remove** next to the tag that you want to delete\.

1. Choose **Save changes**\.

**To tag a security group using the command line**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

### Delete a security group<a name="deleting-security-groups"></a>

You can delete a security group only if it is not associated with any resources\. You can't delete a default security group\.

If you're using the console, you can delete more than one security group at a time\. If you're using the command line or the API, you can delete only one security group at a time\.

**To delete a security group using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select one or more security groups and choose **Actions**, **Delete security groups**\.

1. When prompted for confirmation, enter **delete** and then choose **Delete**\.

**To delete a security group using the command line**
+ [delete\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-security-group.html) \(AWS CLI\)
+ [Remove\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

## Work with security group rules<a name="working-with-security-group-rules"></a>

The following tasks show you how to work with security group rules using the Amazon VPC console\.

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

1. In the navigation pane, choose **Security Groups**\.

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

**To add a rule to a security group using the command line**
+ [authorize\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) and [authorize\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-egress.html) \(AWS CLI\)
+ [Grant\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupIngress.html) and [Grant\-EC2SecurityGroupEgress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupEgress.html) \(AWS Tools for Windows PowerShell\)

### Update security group rules<a name="updating-security-group-rules"></a>

When you update a rule, the updated rule is automatically applied to any resources that are associated with the security group\.

For information about the permissions required to manage security group rules, see [Manage security group rules](vpc-policy-examples.md#vpc-security-group-rules-iam)\.

**To update a rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group\.

1. Choose **Actions**, **Edit inbound rules** or **Actions**, **Edit outbound rules**\.

1. Update the rule as required\.

1. Choose **Save rules**\.

**To update the description for a security group rule using the command line**
+ [modify\-security\-group\-rules](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-security-group-rules.html), [update\-security\-group\-rule\-descriptions\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-ingress.html), and [update\-security\-group\-rule\-descriptions\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-egress.html) \(AWS CLI\)
+ [Update\-EC2SecurityGroupRuleIngressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleIngressDescription.html) and [Update\-EC2SecurityGroupRuleEgressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleEgressDescription.html) \(AWS Tools for Windows PowerShell\)

### Tag security group rules<a name="tagging-security-group-rules"></a>

Add tags to your resources to help organize and identify them, such as by purpose, owner, or environment\. You can add tags to security group rules\. Tag keys must be unique for each security group rule\. If you add a tag with a key that is already associated with the security group rule, it updates the value of that tag\.

**To tag a rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group\.

1. On the **Inbound rules** or **Outbound rules** tab, select the check box for the rule and then choose **Manage tags**\.

1. The **Manage tags** page displays any tags that are assigned to the rule\. To add a tag, choose **Add tag** and enter the tag key and value\. To delete a tag, choose **Remove** next to the tag that you want to delete\.

1. Choose **Save changes**\.

**To tag a rule using the command line**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

### Delete security group rules<a name="deleting-security-group-rules"></a>

When you delete a rule from a security group, the change is automatically applied to any instances that are associated with the security group\.

**To delete a security group rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group\.

1. Choose **Actions**, and then choose **Edit inbound rules** to remove an inbound rule or **Edit outbound rules** to remove an outbound rule\.

1. Choose the **Delete** button next to the rule that you want to delete\.

1. Choose **Save rules**\.

**To delete a security group rule using the command line**
+ [revoke\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-ingress.html) and [revoke\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-egress.html)\(AWS CLI\)
+ [Revoke\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Revoke-EC2SecurityGroupIngress.html) and [Revoke\-EC2SecurityGroupEgress](https://docs.aws.amazon.com/powershell/latest/reference/items/Revoke-EC2SecurityGroupEgress.html) \(AWS Tools for Windows PowerShell\)

## Centrally manage VPC security groups using AWS Firewall Manager<a name="aws-firewall-manager"></a>

AWS Firewall Manager simplifies your VPC security groups administration and maintenance tasks across multiple accounts and resources\. With Firewall Manager, you can configure and audit your security groups for your organization from a single central administrator account\. Firewall Manager automatically applies the rules and protections across your accounts and resources, even as you add new resources\. Firewall Manager is particularly useful when you want to protect your entire organization, or if you frequently add new resources that you want to protect from a central administrator account\.

You can use Firewall Manager to centrally manage security groups in the following ways:
+ **Configure common baseline security groups across your organization**: You can use a common security group policy to provide a centrally controlled association of security groups to accounts and resources across your organization\. You specify where and how to apply the policy in your organization\.
+ **Audit existing security groups in your organization**: You can use an audit security group policy to check the existing rules that are in use in your organization's security groups\. You can scope the policy to audit all accounts, specific accounts, or resources tagged within your organization\. Firewall Manager automatically detects new accounts and resources and audits them\. You can create audit rules to set guardrails on which security group rules to allow or disallow within your organization, and to check for unused or redundant security groups\. 
+ **Get reports on non\-compliant resources and remediate them**: You can get reports and alerts for non\-compliant resources for your baseline and audit policies\. You can also set auto\-remediation workflows to remediate any non\-compliant resources that Firewall Manager detects\.

To learn more about using Firewall Manager to manage your security groups, see the following topics in the *AWS WAF Developer Guide*:
+ [AWS Firewall Manager prerequisites](https://docs.aws.amazon.com/waf/latest/developerguide/fms-prereq.html)
+ [Getting started with AWS Firewall Manager Amazon VPC security group policies](https://docs.aws.amazon.com/waf/latest/developerguide/getting-started-fms-security-group.html)
+ [How security group policies work in AWS Firewall Manager](https://docs.aws.amazon.com/waf/latest/developerguide/security-group-policies.html)
+ [Security group policy use cases](https://docs.aws.amazon.com/waf/latest/developerguide/security-group-policies.html#security-group-policies-use-cases)