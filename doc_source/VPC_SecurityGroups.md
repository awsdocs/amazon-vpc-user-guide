# Security groups for your VPC<a name="VPC_SecurityGroups"></a>

A *security group* acts as a virtual firewall for your instance to control inbound and outbound traffic\. When you launch an instance in a VPC, you can assign up to five security groups to the instance\. Security groups act at the instance level, not the subnet level\. Therefore, each instance in a subnet in your VPC can be assigned to a different set of security groups\. 

If you launch an instance using the Amazon EC2 API or a command line tool and you don't specify a security group, the instance is automatically assigned to the default security group for the VPC\. If you launch an instance using the Amazon EC2 console, you have an option to create a new security group for the instance\.

For each security group, you add *rules* that control the inbound traffic to instances, and a separate set of rules that control the outbound traffic\. This section describes the basic things that you need to know about security groups for your VPC and their rules\.

You might set up network ACLs with rules similar to your security groups in order to add an additional layer of security to your VPC\. For more information about the differences between security groups and network ACLs, see [Comparison of security groups and network ACLs](VPC_Security.md#VPC_Security_Comparison)\.

**Topics**
+ [Security group basics](#VPCSecurityGroups)
+ [Default security group for your VPC](#DefaultSecurityGroup)
+ [Security group rules](#SecurityGroupRules)
+ [Differences between security groups for EC2\-Classic and EC2\-VPC](#VPC_Security_Group_Differences)
+ [Working with security groups](#WorkingWithSecurityGroups)
+ [Centrally manage VPC security groups using AWS Firewall Manager](#aws-firewall-manager)

## Security group basics<a name="VPCSecurityGroups"></a>

The following are the basic characteristics of security groups for your VPC:
+ There are quotas on the number of security groups that you can create per VPC, the number of rules that you can add to each security group, and the number of security groups that you can associate with a network interface\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.
+ You can specify allow rules, but not deny rules\.
+ You can specify separate rules for inbound and outbound traffic\. 
+ When you create a security group, it has no inbound rules\. Therefore, no inbound traffic originating from another host to your instance is allowed until you add inbound rules to the security group\.
+ By default, a security group includes an outbound rule that allows all outbound traffic\. You can remove the rule and add outbound rules that allow specific outbound traffic only\. If your security group has no outbound rules, no outbound traffic originating from your instance is allowed\.
+ Security groups are stateful — if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules\. Responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules\.
**Note**  
Some types of traffic are tracked differently from other types\. For more information, see [Connection tracking](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#security-group-connection-tracking) in the *Amazon EC2 User Guide for Linux Instances*\.
+ Instances associated with a security group can't talk to each other unless you add rules allowing the traffic \(exception: the default security group has these rules by default\)\.
+ Security groups are associated with network interfaces\. After you launch an instance, you can change the security groups that are associated with the instance, which changes the security groups associated with the primary network interface \(eth0\)\. You can also specify or change the security groups associated with any other network interface\. By default, when you create a network interface, it's associated with the default security group for the VPC, unless you specify a different security group\. For more information about network interfaces, see [Elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html)\.
+ When you create a security group, you must provide it with a name and a description\. The following rules apply:
  + Names and descriptions can be up to 255 characters in length\.
  + Names and descriptions are limited to the following characters: a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=&;\{\}\!$\*\.
  + When the name contains trailing spaces, we trim the spaces when we save the name\. For example, if you enter "Test Security Group " for the name, we store it as "Test Security Group"\.
  + A security group name cannot start with `sg-` as these indicate a default security group\.
  + A security group name must be unique within the VPC\.
+ A security group can only be used in the VPC that you specify when you create the security group\.

## Default security group for your VPC<a name="DefaultSecurityGroup"></a>

Your VPC automatically comes with a default security group\. If you don't specify a different security group when you launch the instance, we associate the default security group with your instance\. 

**Note**  
If you launch an instance in the Amazon EC2 console, the launch instance wizard automatically defines a "launch\-wizard\-*xx*" security group, which you can associate with the instance instead of the default security group\.

The following table describes the default rules for a default security group\.


| 
| 
| Inbound | 
| --- |
|  Source  |  Protocol  |  Port range  |  Description  | 
|  The security group ID \(sg\-*xxxxxxxx*\)  |  All  |  All  |  Allow inbound traffic from network interfaces \(and their associated instances\) that are assigned to the same security group\.  | 
|   Outbound   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Description  | 
|  0\.0\.0\.0/0  |  All  |  All  |  Allow all outbound IPv4 traffic\.  | 
| ::/0 | All | All | Allow all outbound IPv6 traffic\. This rule is added by default if you create a VPC with an IPv6 CIDR block or if you associate an IPv6 CIDR block with your existing VPC\.  | 

You can change the rules for the default security group\.

You can't delete a default security group\. If you try to delete the default security group, you get the following error: `Client.CannotDelete: the specified group: "sg-51530134" name: "default" cannot be deleted by a user`\. 

**Note**  
If you've modified the outbound rules for your security group, we do not automatically add an outbound rule for IPv6 traffic when you associate an IPv6 block with your VPC\.

## Security group rules<a name="SecurityGroupRules"></a>

You can add or remove rules for a security group \(also referred to as *authorizing* or *revoking* inbound or outbound access\)\. A rule applies either to inbound traffic \(ingress\) or outbound traffic \(egress\)\. You can grant access to a specific CIDR range, or to another security group in your VPC or in a peer VPC \(requires a VPC peering connection\)\.

The following are the basic parts of a security group rule in a VPC:
+ \(Inbound rules only\) The source of the traffic and the destination port or port range\. The source can be another security group, an IPv4 or IPv6 CIDR block, a single IPv4 or IPv6 address, or a prefix list ID\.
+ \(Outbound rules only\) The destination for the traffic and the destination port or port range\. The destination can be another security group, an IPv4 or IPv6 CIDR block, a single IPv4 or IPv6 address, or a prefix list ID\.
+ Any protocol that has a standard protocol number \(for a list, see [Protocol Numbers](http://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)\)\. If you specify ICMP as the protocol, you can specify any or all of the ICMP types and codes\.
+ An optional description for the security group rule to help you identify it later\. A description can be up to 255 characters in length\. Allowed characters are a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=;\{\}\!$\*\.
+ If you add a security group rule using the AWS CLI, the console, or the API, we automatically set the source or destination CIDR block to the canonical form\. For example, if you specify 100\.68\.0\.18/18 for the CIDR block, we create a rule with a CIDR block of 100\.68\.0\.0/18\. 

When you specify a CIDR block as the source for a rule, traffic is allowed from the specified addresses for the specified protocol and port\.

When you specify a security group as the source for a rule, traffic is allowed from the network interfaces that are associated with the source security group for the specified protocol and port\. Incoming traffic is allowed based on the private IP addresses of the network interfaces that are associated with the source security group \(and not the public IP or Elastic IP addresses\)\. Adding a security group as a source does not add rules from the source security group\. For an example, see [Default security group for your VPC](#DefaultSecurityGroup)\.

If you specify a single IPv4 address, specify the address using the /32 prefix length\. If you specify a single IPv6 address, specify it using the /128 prefix length\.

Some systems for setting up firewalls let you filter on source ports\. Security groups let you filter only on destination ports\.

When you add or remove rules, they are automatically applied to all instances associated with the security group\. 

The kind of rules that you add can depend on the purpose of the security group\. The following table describes example rules for a security group that's associated with web servers\. The web servers can receive HTTP and HTTPS traffic from all IPv4 and IPv6 addresses, and can send SQL or MySQL traffic to a database server\.


| 
| 
| Inbound | 
| --- |
|  Source  |  Protocol  |  Port range  |  Description  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow inbound HTTP access from all IPv4 addresses  | 
| ::/0 | TCP | 80 | Allow inbound HTTP access from all IPv6 addresses | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow inbound HTTPS access from all IPv4 addresses  | 
| ::/0 | TCP | 443 | Allow inbound HTTPS access from all IPv6 addresses | 
|  Your network's public IPv4 address range  |  TCP  |  22  |  Allow inbound SSH access to Linux instances from IPv4 IP addresses in your network \(over the internet gateway\)  | 
|  Your network's public IPv4 address range  |  TCP  |  3389  |  Allow inbound RDP access to Windows instances from IPv4 IP addresses in your network \(over the internet gateway\)  | 
|   Outbound   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Description  | 
|  The ID of the security group for your Microsoft SQL Server database servers  |  TCP  |  1433  |  Allow outbound Microsoft SQL Server access to instances in the specified security group  | 
|  The ID of the security group for your MySQL database servers  |  TCP  |  3306  |  Allow outbound MySQL access to instances in the specified security group  | 

A database server would need a different set of rules\. For example, instead of inbound HTTP and HTTPS traffic, you can add a rule that allows inbound MySQL or Microsoft SQL Server access\. For an example of security group rules for web servers and database servers, see [Security](VPC_Scenario3.md#VPC_Scenario3_Security)\. For more information about security groups for Amazon RDS DB instances, see [Controlling access with security groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.RDSSecurityGroups.html) in the *Amazon RDS User Guide*\.

For examples of security group rules for specific kinds of access, see [Security group rules reference](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/security-group-rules-reference.html) in the *Amazon EC2 User Guide for Linux Instances*\.

### Stale security group rules<a name="vpc-stale-security-group-rules"></a>

If your VPC has a VPC peering connection with another VPC, a security group rule can reference another security group in the peer VPC\. This allows instances that are associated with the referenced security group and those that are associated with the referencing security group to communicate with each other\. 

If the owner of the peer VPC deletes the referenced security group, or if you or the owner of the peer VPC deletes the VPC peering connection, the security group rule is marked as `stale`\. You can delete stale security group rules as you would any other security group rule\.

For more information, see [Working with stale security groups](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html#vpc-peering-stale-groups) in the *Amazon VPC Peering Guide*\.

## Differences between security groups for EC2\-Classic and EC2\-VPC<a name="VPC_Security_Group_Differences"></a>

You can't use the security groups that you've created for use with EC2\-Classic with instances in your VPC\. You must create security groups specifically for use with instances in your VPC\. The rules that you create for use with a security group for a VPC can't reference a security group for EC2\-Classic, and vice versa\. For more information about the differences between security groups for use with EC2\-Classic and those for use with a VPC, see [Differences between EC2\-Classic and a VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-classic-platform.html#differences-ec2-classic-vpc) in the *Amazon EC2 User Guide for Linux Instances*\.

## Working with security groups<a name="WorkingWithSecurityGroups"></a>

The following tasks show you how to work with security groups using the Amazon VPC console\.

For example IAM policies for working with security groups, see [Managing security groups](vpc-policy-examples.md#vpc-security-groups-iam)\.

**Topics**
+ [Modifying the default security group](#ModifyingSecurityGroups)
+ [Creating a security group](#CreatingSecurityGroups)
+ [Adding, removing, and updating rules](#AddRemoveRules)
+ [Changing an instance's security groups](#SG_Changing_Group_Membership)
+ [Deleting a security group](#DeleteSecurityGroup)
+ [Deleting the 2009\-07\-15\-default security group](#DeleteSecurityGroup-2009)

### Modifying the default security group<a name="ModifyingSecurityGroups"></a>

Your VPC includes a [default security group](#DefaultSecurityGroup)\. You can't delete this group; however, you can change the group's rules\. The procedure is the same as modifying any other security group\. For more information, see [Adding, removing, and updating rules](#AddRemoveRules)\.

### Creating a security group<a name="CreatingSecurityGroups"></a>

Although you can use the default security group for your instances, you might want to create your own groups to reflect the different roles that instances play in your system\.

**To create a security group using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Enter a name for the security group \(for example, `my-security-group`\), and then provide a description\. 

1. From **VPC**, select the ID of your VPC\.

1. \(Optional\) Add or remove a tag\.

   \[Add a tag\] Choose **Add tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose **Remove** to the right of the tag’s Key and Value\.

1. Choose **Create**\.

**To create a security group using the command line**
+ [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) \(AWS CLI\)
+ [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

**To describe one or more security groups using the command line**
+ [describe\-security\-groups](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html) \(AWS CLI\)
+ [Get\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

By default, new security groups start with only an outbound rule that allows all traffic to leave the instances\. You must add rules to enable any inbound traffic or to restrict the outbound traffic\.

### Adding, removing, and updating rules<a name="AddRemoveRules"></a>

When you add or remove a rule, any instances already assigned to the security group are subject to the change\. 

If you have a VPC peering connection, you can reference security groups from the peer VPC as the source or destination in your security group rules\. For more information, see [Updating your security groups to reference peer VPC security groups](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html) in the *Amazon VPC Peering Guide*\.

**To add a rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update\. 

1. Choose **Actions**, **Edit inbound rules** or **Actions**, **Edit outbound rules**\.

1. Choose **Add rule**\. For **Type**, select the traffic type, and then specify the source \(inbound rules\) or destination \(outbound rules\)\. For example, for a public web server, choose **HTTP** or **HTTPS** and specify a value for **Source** as `0.0.0.0/0`\. 

   If you use `0.0.0.0/0`, you enable all IPv4 addresses to access your instance using HTTP or HTTPS\. To restrict access, enter a specific IP address or range of addresses\.

1. You can also allow communication between all instances that are associated with this security group\. Create an inbound rule with the following options:
   + **Type**: **All Traffic**
   + **Source**: Enter the ID of the security group\.

1. Choose **Save rules**\.

**To delete a rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update\.

1. Choose **Actions**, **Edit inbound rules** or **Actions**, **Edit outbound rules**\.

1. Choose the delete button \(“x”\) to the right of the rule that you want to delete\.

1. Choose **Save rules**\.

When you modify the protocol, port range, or source or destination of an existing security group rule using the console, the console deletes the existing rule and adds a new one for you\. 

**To update a rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update\.

1. Choose **Actions**, **Edit inbound rules** or **Actions**, **Edit outbound rules**\.

1. Modify the rule entry as required\.

1. Choose **Save rules**\.

If you are updating the protocol, port range, or source or destination of an existing rule using the Amazon EC2 API or a command line tool, you cannot modify the rule\. Instead, you must delete the existing rule and add a new rule\. To update the rule description only, you can use the [update\-security\-group\-rule\-descriptions\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-ingress.html) and [update\-security\-group\-rule\-descriptions\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-egress.html) commands\. 

**To add a rule to a security group using the command line**
+ [authorize\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) and [authorize\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-egress.html) \(AWS CLI\)
+ [Grant\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupIngress.html) and [Grant\-EC2SecurityGroupEgress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupEgress.html) \(AWS Tools for Windows PowerShell\)

**To delete a rule from a security group using the command line**
+ [revoke\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-ingress.html) and [revoke\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-egress.html)\(AWS CLI\)
+ [Revoke\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Revoke-EC2SecurityGroupIngress.html) and [Revoke\-EC2SecurityGroupEgress](https://docs.aws.amazon.com/powershell/latest/reference/items/Revoke-EC2SecurityGroupEgress.html) \(AWS Tools for Windows PowerShell\)

**To update the description for a security group rule using the command line**
+ [update\-security\-group\-rule\-descriptions\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-ingress.html) and [update\-security\-group\-rule\-descriptions\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-egress.html) \(AWS CLI\)
+ [Update\-EC2SecurityGroupRuleIngressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleIngressDescription.html) and [Update\-EC2SecurityGroupRuleEgressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleEgressDescription.html) \(AWS Tools for Windows PowerShell\)

### Changing an instance's security groups<a name="SG_Changing_Group_Membership"></a>

After you launch an instance into a VPC, you can change the security groups that are associated with the instance\. You can change the security groups for an instance when the instance is in the `running` or `stopped` state\.

**Note**  
This procedure changes the security groups that are associated with the primary network interface \(eth0\) of the instance\. To change the security groups for other network interfaces, see [Changing the security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#eni_security_group)\.

**To change the security groups for an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Open the context \(right\-click\) menu for the instance and choose **Networking**, **Change Security Groups**\. 

1. In the **Change Security Groups** dialog box, select one or more security groups from the list and choose **Assign Security Groups**\.

**To change the security groups for an instance using the command line**
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Deleting a security group<a name="DeleteSecurityGroup"></a>

You can delete a security group only if there are no instances assigned to it \(either running or stopped\)\. You can assign the instances to another security group before you delete the security group \(see [Changing an instance's security groups](#SG_Changing_Group_Membership)\)\. You can't delete a default security group\.

If you're using the console, you can delete more than one security group at a time\. If you're using the command line or the API, you can only delete one security group at a time\.

**To delete a security group using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select one or more security groups and choose **Security Group Actions**, **Delete Security Group**\.

1. In the **Delete Security Group** dialog box, choose **Yes, Delete**\.

**To delete a security group using the command line**
+ [delete\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-security-group.html) \(AWS CLI\)
+ [Remove\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

### Deleting the 2009\-07\-15\-default security group<a name="DeleteSecurityGroup-2009"></a>

Any VPC created using an API version older than 2011\-01\-01 has the `2009-07-15-default` security group\. This security group exists in addition to the regular `default` security group that comes with every VPC\. You can't attach an internet gateway to a VPC that has the `2009-07-15-default` security group\. Therefore, you must delete this security group before you can attach an internet gateway to the VPC\.

**Note**  
If you assigned this security group to any instances, you must assign these instances a different security group before you can delete the security group\.

**To delete the `2009-07-15-default` security group**

1. Ensure that this security group is not assigned to any instances\.

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Network Interfaces**\.

   1. Select the network interface for the instance from the list, and choose **Change Security Groups**, **Actions**\. 

   1. In the **Change Security Groups** dialog box, select a new security group from the list, and choose **Save**\.

      When changing an instance's security group, you can select multiple groups from the list\. The security groups that you select replace the current security groups for the instance\.

   1. Repeat the preceding steps for each instance\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose the `2009-07-15-default` security group, then choose **Security Group Actions**, **Delete Security Group**\.

1. In the **Delete Security Group** dialog box, choose **Yes, Delete**\.

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
+ [Security group policy use cases](https://docs.aws.amazon.com/waf/latest/developerguide/security-group-policies-use-cases.html)