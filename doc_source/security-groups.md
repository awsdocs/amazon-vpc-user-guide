# Security groups<a name="security-groups"></a>

A security group acts as a firewall that controls the traffic allowed to and from the resources in your virtual private cloud \(VPC\)\. You can choose the ports and protocols to allow for inbound traffic and for outbound traffic\.

For each security group, you add separate sets of rules for inbound traffic and outbound traffic\. For more information, see [Security group rules](security-group-rules.md)\.

## Security group basics<a name="security-group-basics"></a>

**Characteristics of security groups**
+ When you create a security group, you must provide it with a name and a description\. The following rules apply:
  + A security group name must be unique within the VPC\.
  + Names and descriptions can be up to 255 characters in length\.
  + Names and descriptions are limited to the following characters: a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=&;\{\}\!$\*\.
  + When the name contains trailing spaces, we trim the space at the end of the name\. For example, if you enter "Test Security Group " for the name, we store it as "Test Security Group"\.
  + A security group name cannot start with `sg-`\.
+ Security groups are stateful\. For example, if you send a request from an instance, the response traffic for that request is allowed to reach the instance regardless of the inbound security group rules\. Responses to allowed inbound traffic are allowed to leave the instance, regardless of the outbound rules\.
+ There are quotas on the number of security groups that you can create per VPC, the number of rules that you can add to each security group, and the number of security groups that you can associate with a network interface\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

**Best practices**
+ Authorize only specific IAM principals to create and modify security groups\.
+ Create the minimum number of security groups that you need, to decrease the risk of error\. Use each security group to manage access to resources that have similar functions and security requirements\.
+ When you add inbound rules for ports 22 \(SSH\) or 3389 \(RDP\) so that you can access your EC2 instances, authorize only specific IP address ranges\. If you specify 0\.0\.0\.0/0 \(IPv4\) and ::/ \(IPv6\), this enables anyone to access your instances from any IP address using the specified protocol\.
+ Do not open large port ranges\. Ensure that access through each port is restricted to the sources or destinations that require it\.
+ Consider creating network ACLs with rules similar to your security groups, to add an additional layer of security to your VPC\. For more information about the differences between security groups and network ACLs, see [Compare security groups and network ACLs](VPC_Security.md#VPC_Security_Comparison)\.

## Work with security groups<a name="working-with-security-groups"></a>

The following tasks show you how to work with security groups\.

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

1. In the navigation pane, choose **Security groups**\.

1. Choose **Create security group**\.

1. Enter a name and description for the security group\. You cannot change the name and description of a security group after it is created\.

1. From **VPC**, choose the VPC\.

1. You can add security group rules now, or you can add them later\. For more information, see [Add rules to a security group](security-group-rules.md#adding-security-group-rules)\.

1. You can add tags now, or you can add them later\. To add a tag, choose **Add new tag** and enter the tag key and value\.

1. Choose **Create security group**\.

After you create a security group, you can assign it to an EC2 instance when you launch the instance or change the security group currently assigned to an instance\. For more information, see [Launch an instance using defined parameters](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html#liw-launch-instance-with-defined-parameters) or [Change an instance's security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html#changing-security-group) in the *Amazon EC2 User Guide for Linux Instances*\.

**To create a security group using the AWS CLI**  
Use the [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) command\.

### View your security groups<a name="viewing-security-groups"></a>

You can view information about your security groups as follows\.

For information about the permissions required to view security groups, see [Manage security groups](vpc-policy-examples.md#vpc-security-groups-iam)\.

**To view your security groups using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security groups**\.

1. Your security groups are listed\. To view the details for a specific security group, including its inbound and outbound rules, select the security group\.

**To view all of your security groups across Regions**  
Open the Amazon EC2 Global View console at [ https://console\.aws\.amazon\.com/ec2globalview/home](https://console.aws.amazon.com/ec2globalview/home)\. For more information, see [List and filter resources using the Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view) in the *Amazon EC2 User Guide for Linux Instances*\.

**To view your security groups using the AWS CLI**  
Use the [describe\-security\-groups](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html) and [describe\-security\-group\-rules](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-group-rules.html) command\.

### Tag your security groups<a name="tagging-security-groups"></a>

Add tags to your resources to help organize and identify them, such as by purpose, owner, or environment\. You can add tags to your security groups\. Tag keys must be unique for each security group\. If you add a tag with a key that is already associated with the rule, it updates the value of that tag\.

**To tag a security group using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security groups**\.

1. Select the check box for the security group\.

1. Choose **Actions**, **Manage tags**\. The **Manage tags** page displays any tags that are assigned to the security group\.

1. To add a tag, choose **Add new tag** and enter the tag key and tag value\. To delete a tag, choose **Remove** next to the tag to delete\.

1. Choose **Save changes**\.

**To tag a security group using the AWS CLI**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command\.

### Delete a security group<a name="deleting-security-groups"></a>

You can delete a security group only if it is not associated with any resources\. You can't delete a default security group\.

If you're using the console, you can delete more than one security group at a time\. If you're using the command line or the API, you can delete only one security group at a time\.

**To delete a security group using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security groups**\.

1. Select one or more security groups and choose **Actions**, **Delete security groups**\.

1. When prompted for confirmation, enter **delete** and then choose **Delete**\.

**To delete a security group using the AWS CLI**  
Use the [delete\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-security-group.html) command\.

## Centrally manage security groups using AWS Firewall Manager<a name="aws-firewall-manager"></a>

AWS Firewall Manager simplifies your security group administration and maintenance tasks across multiple accounts and resources\. With Firewall Manager, you can configure and audit the security groups for your organization from a single central administrator account\. Firewall Manager automatically applies the rules and protections across your accounts and resources, even as you add new resources\. Firewall Manager is particularly useful when you want to protect your entire organization, or if you frequently add new resources that you want to protect from a central administrator account\.

You can use Firewall Manager to centrally manage security groups in the following ways:
+ **Configure common baseline security groups across your organization**: You can use a common security group policy to provide a centrally controlled association of security groups to accounts and resources across your organization\. You specify where and how to apply the policy in your organization\.
+ **Audit existing security groups in your organization**: You can use an audit security group policy to check the existing rules that are in use in your organization's security groups\. You can scope the policy to audit all accounts, specific accounts, or resources tagged within your organization\. Firewall Manager automatically detects new accounts and resources and audits them\. You can create audit rules to set guardrails on which security group rules to allow or disallow within your organization, and to check for unused or redundant security groups\. 
+ **Get reports on non\-compliant resources and remediate them**: You can get reports and alerts for non\-compliant resources for your baseline and audit policies\. You can also set auto\-remediation workflows to remediate any non\-compliant resources that Firewall Manager detects\.

To learn more about using Firewall Manager to manage your security groups, see the following resources in the *AWS WAF Developer Guide*:
+ [AWS Firewall Manager prerequisites](https://docs.aws.amazon.com/waf/latest/developerguide/fms-prereq.html)
+ [Getting started with AWS Firewall Manager Amazon VPC security group policies](https://docs.aws.amazon.com/waf/latest/developerguide/getting-started-fms-security-group.html)
+ [How security group policies work in AWS Firewall Manager](https://docs.aws.amazon.com/waf/latest/developerguide/security-group-policies.html)
+ [Security group policy use cases](https://docs.aws.amazon.com/waf/latest/developerguide/security-group-policies.html#security-group-policies-use-cases)