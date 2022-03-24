# Work with shared prefix lists<a name="sharing-managed-prefix-lists"></a>

With AWS Resource Access Manager \(AWS RAM\), the owner of a prefix list can share a prefix list with the following:
+ Specific AWS accounts inside or outside of its organization in AWS Organizations
+ An organizational unit inside its organization in AWS Organizations
+ An entire organization in AWS Organizations

Consumers with whom a prefix list has been shared can view the prefix list and its entries, and they can reference the prefix list in their AWS resources\.

For more information about AWS RAM, see the [AWS RAM User Guide](https://docs.aws.amazon.com/ram/latest/userguide/)\.

**Topics**
+ [Prerequisites for sharing prefix lists](#sharing-prereqs)
+ [Share a prefix list](#sharing-share)
+ [Identify a shared prefix list](#sharing-identify)
+ [Identify references to a shared prefix list](#sharing-identify-references)
+ [Unshare a shared prefix list](#sharing-unshare)
+ [Shared prefix list permissions](#sharing-perms)
+ [Billing and metering](#sharing-billing)
+ [Quotas for AWS RAM](#sharing-limits)

## Prerequisites for sharing prefix lists<a name="sharing-prereqs"></a>
+ To share a prefix list, you must own it\. You cannot share a prefix list that has been shared with you\. You cannot share an AWS\-managed prefix list\.
+ To share a prefix list with your organization or an organizational unit in AWS Organizations, you must enable sharing with AWS Organizations\. For more information, see [ Enable sharing with AWS Organizations](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html#getting-started-sharing-orgs) in the *AWS RAM User Guide*\.

## Share a prefix list<a name="sharing-share"></a>

To share a prefix list, you must add it to a resource share\. If you do not have a resource share, you must first create one using the [AWS RAM console](https://console.aws.amazon.com/ram)\.

If you are part of an organization in AWS Organizations, and sharing within your organization is enabled, consumers in your organization are automatically granted access to the shared prefix list\. Otherwise, consumers receive an invitation to join the resource share and are granted access to the shared prefix list after accepting the invitation\.

You can create a resource share and share a prefix list that you own using the AWS RAM console, or the AWS CLI\.

**To create a resource share and share a prefix list using the AWS RAM console**  
Follow the steps in [Create a resource share](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html#getting-started-sharing-create) in the *AWS RAM User Guide*\. For **Select resource type**, choose **Prefix Lists**, and then select the check box for your prefix list\.

**To add a prefix list to an existing resource share using the AWS RAM console**  
To add a managed prefix that you own to an existing resource share, follow the steps in [Updating a resource share](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html#working-with-sharing-update) in the *AWS RAM User Guide*\. For **Select resource type**, choose **Prefix Lists**, and then select the check box for your prefix list\.

**To share a prefix list that you own using the AWS CLI**  
Use the following commands to create and update a resource share:
+ [create\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html) 
+ [associate\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/associate-resource-share.html) 
+ [update\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/update-resource-share.html) 

## Identify a shared prefix list<a name="sharing-identify"></a>

Owners and consumers can identify shared prefix lists using the Amazon VPC console and AWS CLI\.

**To identify a shared prefix list using the Amazon VPC console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. The page displays the prefix lists that you own and the prefix lists that are shared with you\. The **Owner ID** column shows the AWS account ID of the prefix list owner\.

1. To view the resource share information for a prefix list, select the prefix list and choose **Sharing** in the lower pane\.

**To identify a shared prefix list using the AWS CLI**  
Use the [describe\-managed\-prefix\-lists](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-managed-prefix-lists.html) command\. The command returns the prefix lists that you own and the prefix lists that are shared with you\. `OwnerId` shows the AWS account ID of the prefix list owner\.

## Identify references to a shared prefix list<a name="sharing-identify-references"></a>

Owners can identify the consumer\-owned resources that are referencing a shared prefix list\.

**To identify references to a shared prefix list using the Amazon VPC console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. Select the prefix list and choose **Associations** in the lower pane\.

1. The IDs of the resources that are referencing the prefix list are listed in the **Resource ID** column\. The owners of the resources are listed in the **Resource Owner** column\.

**To identify references to a shared prefix list using the AWS CLI**  
Use the [get\-managed\-prefix\-list\-associations](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-managed-prefix-list-associations.html) command\.

## Unshare a shared prefix list<a name="sharing-unshare"></a>

When you unshare a prefix list, consumers can no longer view the prefix list or its entries in their account, and they cannot reference the prefix list in their resources\. If the prefix list is already referenced in the consumer's resources, those references continue to function as normal, and you can continue to [view those references](#sharing-identify-references)\. If you update the prefix list to a new version, the references use the latest version\.

To unshare a shared prefix list that you own, you must remove it from the resource share using AWS RAM\.

**To unshare a shared prefix list that you own using the AWS RAM console**  
See [Updating a resource share](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html#working-with-sharing-update) in the *AWS RAM User Guide*\.

**To unshare a shared prefix list that you own using the AWS CLI**  
Use the [disassociate\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/disassociate-resource-share.html) command\.

## Shared prefix list permissions<a name="sharing-perms"></a>

### Permissions for owners<a name="perms-owner"></a>

Owners are responsible for managing a shared prefix list and its entries\. Owners can view the IDs of the AWS resources that reference the prefix list\. However, they cannot add or remove references to a prefix list in AWS resources that are owned by consumers\. 

Owners cannot delete a prefix list if the prefix list is referenced in a resource that's owned by a consumer\.

### Permissions for consumers<a name="perms-consumer"></a>

Consumers can view the entries in a shared prefix list, and they can reference a shared prefix list in their AWS resources\. However, consumers can't modify, restore, or delete a shared prefix list\.

## Billing and metering<a name="sharing-billing"></a>

There are no additional charges for sharing prefix lists\. 

## Quotas for AWS RAM<a name="sharing-limits"></a>

For more information, see [Service quotas](https://docs.aws.amazon.com/general/latest/gr/ram.html#limits_ram)\.