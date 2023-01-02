# Work with customer\-managed prefix lists<a name="working-with-managed-prefix-lists"></a>

You can create and manage customer\-managed prefix lists\. You can view AWS\-managed prefix lists\.

**Topics**
+ [Create a prefix list](#create-managed-prefix-list)
+ [View prefix lists](#view-managed-prefix-lists)
+ [View the entries for a prefix list](#view-managed-prefix-list-entries)
+ [View associations \(references\) for your prefix list](#view-managed-prefix-list-associations)
+ [Modify a prefix list](#modify-managed-prefix-list)
+ [Resize a prefix list](#resize-managed-prefix-list)
+ [Restore a previous version of a prefix list](#restore-managed-prefix-list)
+ [Delete a prefix list](#delete-managed-prefix-list)

## Create a prefix list<a name="create-managed-prefix-list"></a>

When you create a prefix list, you must specify the maximum number of entries that the prefix list can support\.

**Limitation**  
You can't add a prefix list to a security group rule if the number of rules plus the max entries for the prefix list exceeds the quota for rules per security group for your account\.

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

## View prefix lists<a name="view-managed-prefix-lists"></a>

You can view your prefix lists, prefix lists that are shared with you, and AWS\-managed prefix lists\.

**To view prefix lists using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. The **Owner ID** column shows the AWS account ID of the prefix list owner\. For AWS\-managed prefix lists, the **Owner ID** is **AWS**\.

**To view prefix lists using the AWS CLI**  
Use the [describe\-managed\-prefix\-lists](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-managed-prefix-lists.html) command\.

## View the entries for a prefix list<a name="view-managed-prefix-list-entries"></a>

You can view the entries for your prefix lists, prefix lists that are shared with you, and AWS\-managed prefix lists\.

**To view the entries for a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\. 

1. Select the checkbox for the prefix list\.

1. In the lower pane, choose **Entries** to view the entries for the prefix list\.

**To view the entries for a prefix list using the AWS CLI**  
Use the [get\-managed\-prefix\-list\-entries](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-managed-prefix-list-entries.html) command\.

## View associations \(references\) for your prefix list<a name="view-managed-prefix-list-associations"></a>

You can view the IDs and owners of the resources that are associated with your prefix list\. Associated resources are resources that reference your prefix list in their entries or rules\.

**Limitation**  
You cannot view associated resources for an AWS\-managed prefix list\.

**To view prefix list associations using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\. 

1. Select the checkbox for the prefix list\.

1. In the lower pane, choose **Associations** to view the resources that are referencing the prefix list\.

**To view prefix list associations using the AWS CLI**  
Use the [get\-managed\-prefix\-list\-associations](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-managed-prefix-list-associations.html) command\.

## Modify a prefix list<a name="modify-managed-prefix-list"></a>

You can modify the name of your prefix list, and you can add or remove entries\. To modify the maximum number of entries, see [Resize a prefix list](#resize-managed-prefix-list)\.

Updating the entries of a prefix list creates a new version of the prefix list\. Updating the name or maximum number of entries for a prefix list does not create a new version of the prefix list\.

**Considerations**
+ You cannot modify an AWS\-managed prefix list\.
+ When you increase the maximum number of entries in a prefix list, the increased maximum size is applied to the quota of entries for the resources that reference the prefix list\. If any of these resources can't support the increased maximum size, the modify operation fails and the previous maximum size is restored\.

**To modify a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Select the checkbox for prefix list, and choose **Actions**, **Modify prefix list**\.

1. For **Prefix list name**, enter a new name for the prefix list\.

1. For **Prefix list entries**, choose **Remove** to remove an existing entry\. To add a new entry, choose **Add new entry** and enter the CIDR block and a description for the entry\.

1. Choose **Save prefix list**\.

**To modify a prefix list using the AWS CLI**  
Use the [modify\-managed\-prefix\-list](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-managed-prefix-list.html) command\.

## Resize a prefix list<a name="resize-managed-prefix-list"></a>

You can resize a prefix list and modify the maximum number of entries for the prefix list up to 1000\. For more information about customer\-managed prefix list quotas, see [Customer\-managed prefix lists](amazon-vpc-limits.md#vpc-quotas-managed-prefix-lists)\.

**To resize a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Select the checkbox for the prefix list, and choose **Actions**, **Resize prefix list**\.

1. For **New max entries**, enter a value\.

1. Choose **Resize**\.

**To resize a prefix list using the AWS CLI**  
Use the [modify\-managed\-prefix\-list](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-managed-prefix-list.html) command\.

## Restore a previous version of a prefix list<a name="restore-managed-prefix-list"></a>

You can restore the entries from a previous version of your prefix list\. This creates a new version of the prefix list\.

If you decreased the size of the prefix list, you must ensure that the prefix list is large enough to contain the entries from the previous version\.

**To restore a previous version of a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Select the checkbox for the prefix list, and choose **Actions**, **Restore prefix list**\.

1. For **Select prefix list version**, choose a previous version\. The entries for the selected version are displayed in **Prefix list entries**\.

1. Choose **Restore prefix list**\.

**To restore a previous version of a prefix list using the AWS CLI**  
Use the [restore\-managed\-prefix\-list\-version](https://docs.aws.amazon.com/cli/latest/reference/ec2/restore-managed-prefix-list-version.html) command\.

## Delete a prefix list<a name="delete-managed-prefix-list"></a>

To delete a prefix list, you must first remove any references to it in your resources \(such as in your route tables\)\. If you've shared the prefix list using AWS RAM, any references in consumer\-owned resources must first be removed\.

**Limitation**  
You cannot delete an AWS\-managed prefix list\.

**To delete a prefix list using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Select the prefix list, and choose **Actions**, **Delete prefix list**\.

1. In the confirmation dialog box, enter `delete`, and choose **Delete**\.

**To delete a prefix list using the AWS CLI**  
Use the [delete\-managed\-prefix\-list](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-managed-prefix-list.html) command\.