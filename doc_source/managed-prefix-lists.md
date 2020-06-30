# Managed prefix lists<a name="managed-prefix-lists"></a>

A prefix list is a set of one or more CIDR blocks\. There are two types of prefix lists:
+ **AWS\-managed prefix list** — Represents the IP address ranges for an AWS service\. You can reference an AWS\-managed prefix list in your VPC security group rules and in subnet route table entries\. For example, you can reference an AWS\-managed prefix list in an outbound VPC security group rule when connecting to an AWS service through a [gateway VPC endpoint](vpce-gateway.md)\. You cannot create, modify, share, or delete an AWS\-managed prefix list\.
+ **Customer\-managed prefix list** — A set of IPv4 or IPv6 CIDR blocks that you define and manage\. You can reference the prefix list in your VPC security group rules and in subnet route table entries\. This enables you to manage the IP addresses that you frequently use for these resources in a single group, instead of repeatedly referencing the same IP addresses in each resource\. You can share your prefix list with other AWS accounts, enabling those accounts to reference the prefix list in their own resources\.

The following topics describe how to create and work with customer\-managed prefix lists \(also shortened to 'prefix lists' in this documentation\)\.

**Topics**
+ [Prefix lists concepts and rules](#managed-prefix-lists-concepts)
+ [Working with prefix lists](#working-with-managed-prefix-lists)
+ [Identity and access management for prefix lists](#managed-prefix-lists-iam)
+ [Working with shared prefix lists](sharing-managed-prefix-lists.md)

## Prefix lists concepts and rules<a name="managed-prefix-lists-concepts"></a>

A prefix list consists of *entries*\. Each entry consists of a CIDR block and, optionally, a description for the CIDR block\. 

The following rules apply to customer\-managed prefix lists:
+ When you create a prefix list, you must specify the maximum number of entries that the prefix list can support\. You cannot modify the maximum number of entries later\.
+ When you reference a prefix list in a resource, the maximum number of entries for the prefix lists counts as the same number of rules or entries for the resource\. For example, if you create a prefix list with a maximum of 20 entries and you reference that prefix list in a security group rule, this counts as 20 rules for the security group\. 
+ You can modify a prefix list by adding or removing entries, or by changing its name\.
+ A prefix list supports a single type of IP addressing only \(IPv4 or IPv6\)\. You cannot combine IPv4 and IPv6 CIDR blocks in a single prefix list\.
+ There are quotas related to prefix lists\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.
+ When you reference a prefix list in a route table, route priority rules apply\. For more information, see [Route priority for prefix lists](VPC_Route_Tables.md#route-priority-managed-prefix-list)\.

The following rules apply to AWS\-managed prefix lists:
+ You cannot create, modify, share, or delete an AWS\-managed prefix list\.
+ When you reference an AWS\-managed prefix list in a resource, it counts as a single rule or entry for the resource\.
+ You cannot view the version number of an AWS\-managed prefix list\.

### Prefix list versions<a name="managed-prefix-lists-versions"></a>

A prefix list can have multiple versions\. Each time you add or remove entries for a prefix list, we create a new version of the prefix list\. The resources that reference the prefix always use the current \(latest\) version\. You can restore the entries from a previous version of prefix list to a new version\. 

## Working with prefix lists<a name="working-with-managed-prefix-lists"></a>

The following topics describe how to create and work with customer\-managed prefix lists\. You can work with prefix lists using the Amazon VPC console or the AWS CLI\.

**Topics**
+ [Creating a prefix list](#create-managed-prefix-list)
+ [Viewing prefix lists](#view-managed-prefix-lists)
+ [Viewing the entries for a prefix list](#view-managed-prefix-list-entries)
+ [Viewing associations \(references\) for your prefix list](#view-managed-prefix-list-associations)
+ [Modifying a prefix list \(adding and removing entries\)](#modify-managed-prefix-list)
+ [Restoring a previous version of a prefix list](#restore-managed-prefix-list)
+ [Deleting a prefix list](#delete-managed-prefix-list)
+ [Referencing prefix lists in your AWS resources](#managed-prefix-lists-referencing)

### Creating a prefix list<a name="create-managed-prefix-list"></a>

When you create a new prefix list, you must specify the maximum number of entries that the prefix list can support\. Ensure that you specify a maximum number of entries that will meet your needs, because you cannot change this number later\.

**To create a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Choose **Create prefix list**\.

1. For **Prefix list name**, enter a name for the prefix list\.

1. For **Max entries**, enter the maximum number of entries for the prefix list\.

1. For **Address family**, choose whether the prefix list supports IPv4 or IPv6 entries\.

1. For **Prefix list entries**, choose **Add new entry**, and enter the CIDR block and a description for the entry\. Repeat this step for each entry\.

1. \(Optional\) For **Tags**, add tags to the prefix list to help you identify it later\.

1. Choose **Create prefix list**\.

**To create a prefix list using the AWS CLI**  
Use the [create\-managed\-prefix\-list](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-managed-prefix-list.html) command\.

### Viewing prefix lists<a name="view-managed-prefix-lists"></a>

You can view your prefix lists, prefix lists that are shared with you, and AWS\-managed prefix lists using the Amazon VPC console or the AWS CLI\.

**To view prefix lists using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. The **Owner ID** column shows the AWS account ID of the prefix list owner\. For AWS\-managed prefix lists, the **Owner ID** is **AWS**\.

**To view prefix lists using the AWS CLI**  
Use the [describe\-managed\-prefix\-lists](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-managed-prefix-lists.html) command\.

### Viewing the entries for a prefix list<a name="view-managed-prefix-list-entries"></a>

You can view the entries for a prefix list using the Amazon VPC console or the AWS CLI\.

**To view the entries for a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\. 

1. Select the prefix list\.

1. In the lower pane, choose **Entries** to view the entries for the prefix list\.

**To view the entries for a prefix list using the AWS CLI**  
Use the [get\-managed\-prefix\-list\-entries](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-managed-prefix-list-entries.html) command\.

### Viewing associations \(references\) for your prefix list<a name="view-managed-prefix-list-associations"></a>

You can view the IDs and owners of the resources that are associated with your prefix list\. Associated resources are resources that reference your prefix list in their entries or rules\.

You cannot view associated resources for an AWS\-managed prefix list\.

**To view prefix list associations using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\. 

1. Select the prefix list\.

1. In the lower pane, choose **Associations** to view the resources that are referencing the prefix list\.

**To view prefix list associations using the AWS CLI**  
Use the [get\-managed\-prefix\-list\-associations](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-managed-prefix-list-associations.html) command\.

### Modifying a prefix list \(adding and removing entries\)<a name="modify-managed-prefix-list"></a>

You can modify the name of your prefix list, and you can add or remove entries\.

You cannot modify an AWS\-managed prefix list\.

**To modify a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Select the prefix list, and choose **Actions**, **Modify prefix list**\.

1. For **Prefix list name**, enter a new name for the prefix list\.

1. For **Prefix list entries**, choose **Remove** to remove an existing entry\. To add a new entry, choose **Add new entry** and enter the CIDR block and a description for the entry\.

1. Choose **Save prefix list**\.

**To modify a prefix list using the AWS CLI**  
Use the [modify\-managed\-prefix\-list](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-managed-prefix-list.html) command\.

### Restoring a previous version of a prefix list<a name="restore-managed-prefix-list"></a>

You can restore the entries from a previous version of your prefix list to a new version\.

**To restore a previous version of a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Select the prefix list, and choose **Actions**, **Restore prefix list**\.

1. In the drop\-down list, choose the prefix list version\.

1. Choose **Restore prefix list**\.

**To restore a previous version of a prefix list using the AWS CLI**  
Use the [restore\-managed\-prefix\-list\-version](https://docs.aws.amazon.com/cli/latest/reference/ec2/restore-managed-prefix-list-version.html) command\.

### Deleting a prefix list<a name="delete-managed-prefix-list"></a>

To delete a prefix list, you must first remove any references to it in your resources \(such as in your route tables\)\. If you've shared the prefix list using AWS RAM, any references in consumer\-owned resources must first be removed\. To view the references to your prefix list, see [Viewing associations \(references\) for your prefix list](#view-managed-prefix-list-associations)\.

You cannot delete an AWS\-managed prefix list\.

**To delete a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Select the prefix list, and choose **Actions**, **Delete prefix list**\.

1. In the confirmation dialog box, enter `delete`, and choose **Delete**\.

**To delete a prefix list using the AWS CLI**  
Use the [delete\-managed\-prefix\-list](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-managed-prefix-list.html) command\.

### Referencing prefix lists in your AWS resources<a name="managed-prefix-lists-referencing"></a>

You can reference a prefix list in the following AWS resources:
+ [**Subnet route tables**](VPC_Route_Tables.md) \- You can specify a prefix list as the destination for route table entry\. You cannot reference a prefix list in a gateway route table\.
+ [**VPC security groups**](VPC_SecurityGroups.md) \- You can specify a prefix list as the source for an inbound rule, or as the destination for an outbound rule\.

**To reference a prefix list in a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. To add a route, choose **Add route**\. For **Destination** enter the ID of a prefix list\. 

1. For **Target**, choose a target\.

1. Choose **Save routes**\.

**To reference a prefix list in a route table using the AWS CLI**  
Use the [create\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) \(AWS CLI\) command\. Use the `--destination-prefix-list-id` parameter to specify the ID of a prefix list\.

**To reference a prefix list in a security group rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update\. 

1. Choose **Actions**, **Edit inbound rules** or **Actions**, **Edit outbound rules**\.

1. Choose **Add rule**\. For **Type**, select the traffic type\. For **Source** \(inbound rules\) or **Destination** \(outbound rules\), choose the ID of the prefix list\. 

1. Choose **Save rules**\.

**To reference a prefix list in a security group rule using the AWS CLI**  
Use the [authorize\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) and [authorize\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-egress.html) commands\. For the `--ip-permissions` parameter, specify the ID of the prefix list using `PrefixListIds`\.

## Identity and access management for prefix lists<a name="managed-prefix-lists-iam"></a>

By default, IAM users do not have permission to create, view, modify, or delete prefix lists\. You can create an IAM policy that allows users to work with prefix lists\.

To see a list of Amazon VPC actions and the resources and condition keys that you can use in an IAM policy, see [Actions, Resources, and Condition Keys for Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html) in the *IAM User Guide*\.

The following example policy allows users to view and work with prefix list `pl-123456abcde123456` only\. Users cannot create or delete prefix lists\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeManagedPrefixLists",
        "ec2:ModifyManagedPrefixList",
        "ec2:GetManagedPrefixListEntries",
        "ec2:RestoreManagedPrefixListVersion",
        "ec2:GetManagedPrefixListAssociations"
      ],
      "Resource": "arn:aws:ec2:region:account:prefix-list/pl-123456abcde123456"
    }
   ]
}
```

For more information about working with IAM in Amazon VPC, see [Identity and access management for Amazon VPC](security-iam.md)\.