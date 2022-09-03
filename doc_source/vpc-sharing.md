# Share your VPC with other accounts<a name="vpc-sharing"></a>

VPC sharing allows multiple AWS accounts to create their application resources, such as Amazon EC2 instances, Amazon Relational Database Service \(RDS\) databases, Amazon Redshift clusters, and AWS Lambda functions, into shared, centrally\-managed virtual private clouds \(VPCs\)\. In this model, the account that owns the VPC \(owner\) shares one or more subnets with other accounts \(participants\) that belong to the same organization from AWS Organizations\. After a subnet is shared, the participants can view, create, modify, and delete their application resources in the subnets shared with them\. Participants cannot view, modify, or delete resources that belong to other participants or the VPC owner\.

You can share your VPCs to leverage the implicit routing within a VPC for applications that require a high degree of interconnectivity and are within the same trust boundaries\. This reduces the number of VPCs that you create and manage, while using separate accounts for billing and access control\. You can simplify network topologies by interconnecting shared Amazon VPCs using connectivity features, such as AWS PrivateLink, transit gateways, and VPC peering\. For more information about the benefits of VPC sharing, see [VPC sharing: A new approach to multiple accounts and VPC management](http://aws.amazon.com/blogs/networking-and-content-delivery/vpc-sharing-a-new-approach-to-multiple-accounts-and-vpc-management/)\.

**Topics**
+ [Shared VPCs prerequisites](#vpc-share-prerequisites)
+ [Share a subnet](#vpc-sharing-share-subnet)
+ [Unshare a shared subnet](#vpc-sharing-stop-share-subnet)
+ [Identify the owner of a shared subnet](#vpc-sharing-view-owner)
+ [Manage VPC resources](#vpc-sharing-permissions)
+ [Billing and metering for the owner and participants](#vpc-share-billing)
+ [Limitations](#vpc-share-limitations)
+ [Example of sharing public subnets and private subnets](example-vpc-share.md)

## Shared VPCs prerequisites<a name="vpc-share-prerequisites"></a>

You must enable resource sharing from the management account for your organization\. For information about enabling resource sharing, see [Enable sharing with AWS Organizations](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html#getting-started-sharing-orgs) in the *AWS RAM User Guide*\.

## Share a subnet<a name="vpc-sharing-share-subnet"></a>

You can share non\-default subnets with other accounts within your organization\. To share subnets, you must first create a Resource Share with the subnets to be shared and the AWS accounts, organizational units, or an entire organization that you want to share the subnets with\. For information about creating a Resource Share, see [Creating a resource share](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html#getting-started-sharing-create) in the *AWS RAM User Guide*\. 

**To share a subnet using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Share subnet**\. 

1. Select your resource share and choose **Share subnet**\. 

**To share a subnet using the AWS CLI**  
Use the [create\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html) and [associate\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/associate-resource-share.html) commands\.

### Map subnets across Availability Zones<a name="vpc-share-subnets-map-availability-zone"></a>

To ensure that resources are distributed across the Availability Zones for a Region, we independently map Availability Zones to names for each account\. For example, the Availability Zone `us-east-1a` for your AWS account might not have the same location as `us-east-1a` for another AWS account\.

To coordinate Availability Zones across accounts for VPC sharing, you must use an *AZ ID*, which is a unique and consistent identifier for an Availability Zone\. For example, `use1-az1` is the AZ ID for one of the Availability Zones in the `us-east-1` Region\. Use AZ IDs to determine the location of resources in one account relative to another account\. You can view the AZ ID for each subnet in the Amazon VPC console\.

The following diagram illustrates two accounts with different mappings of Availability Zone code to AZ ID\.

![\[Two accounts with different mappings of Availability Zone code to AZ ID.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/availability-zone-mapping.png)



## Unshare a shared subnet<a name="vpc-sharing-stop-share-subnet"></a>

The owner can unshare a shared subnet with participants at any time\. After the owner unshares a shared subnet, the following rules apply:
+ Existing participant resources continue to run in the unshared subnet\. AWS managed services \(for example, Elastic Load Balancing\) that have automated/managed workflows \(such as auto scaling or node replacement\) may require continuous access to the shared subnet for some resources\.
+ Participants can no longer create new resources in the unshared subnet\.
+ Participants can modify, describe, and delete their resources that are in the subnet\.
+ If participants still have resources in the unshared subnet, the owner cannot delete the shared subnet or the shared\-subnet VPC\. The owner can only delete the subnet or shared\-subnet VPC after the participants delete all the resources in the unshared subnet\.

**To unshare a subnet using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Share subnet**\. 

1. Choose **Actions**, **Stop sharing**\. 

**To unshare a subnet using the AWS CLI**  
Use the [disassociate\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/disassociate-resource-share.html) command\.

## Identify the owner of a shared subnet<a name="vpc-sharing-view-owner"></a>

Participants can view the subnets that have been shared with them by using the Amazon VPC console, or the command line tool\.

**To identify a subnet owner using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\. The **Owner** column displays the subnet owner\.

**To identify a subnet owner using the AWS CLI**  
Use the [describe\-subnets](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-subnets.html) and [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) commands, which include the ID of the owner in their output\.

## Manage VPC resources<a name="vpc-sharing-permissions"></a>

Owners and participants are responsible for the VPC resources that they own\.

### Owner resources<a name="vpc-owner-permissions"></a>

VPC owners are responsible for creating, managing, and deleting the resources associated with a shared VPC\. These include subnets, route tables, network ACLs, peering connections, gateway endpoints, interface endpoints, Amazon Route 53 Resolver endpoints, internet gateways, NAT gateways, virtual private gateways, and transit gateway attachments\. 

VPC owners cannot modify or delete resources created by participants, such as EC2 instances and security groups\. VPC owners can view the details for the network interfaces and security groups that are attached to participant resources, to facilitate troubleshooting and auditing\. VPC owners can create flow log subscriptions for the VPC, its subnets, and its network interfaces\.

### Participant resources<a name="vpc-participant-permissions"></a>

Participants can create, manage, and delete resources in a shared VPC\. Participants own the resources that they create in the VPC, such as EC2 instances, Amazon RDS databases, and Elastic Load Balancing load balancers\.

Participants can view the details of resources that the VPC owners are responsible for, such as route tables and network ACLs, but they can't modify them\. Participants can't view or modify resources that belong to other participant accounts\. Participants can create flow log subscriptions only for the network interfaces that they own\.

Participants can reference security groups that belong to other participants or the owner as follows:

```
account-number/security-group-id
```

Participants cannot directly associate one of their private hosted zones with the shared VPC\. A participant that needs to control the behavior of a private hosted zone associated with the VPC can use of the following solutions:
+ The participant can create and share a private hosted zone with the VPC owner\. For information about sharing a private hosted zone, see [Associating a VPC and a private hosted zone that you created with different AWS accounts](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zone-private-associate-vpcs-different-accounts.html) in the *Amazon Route 53 Developer Guide*\.
+ The VPC owner can create a cross\-account IAM role that provides control over a private hosted zone the owner has already associated with the VPC\. The owner can then grant the participant account the necessary permissions to assume the role\. For more information, see [IAM Tutorial: Delegate access across AWS accounts using IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_cross-account-with-roles.html) in the *IAM User Guide*\. The participate account can then assume the role, and exercise whatever control over the private hosted zone that the owner has delegated through the role’s permission 

## Billing and metering for the owner and participants<a name="vpc-share-billing"></a>

In a shared VPC, each participant pays for their application resources including Amazon EC2 instances, Amazon Relational Database Service databases, Amazon Redshift clusters, and AWS Lambda functions\. Participants also pay for data transfer charges associated with inter\-Availability Zone data transfer, data transfer over VPC peering connections, and data transfer through an AWS Direct Connect gateway\. VPC owners pay hourly charges \(where applicable\), data processing and data transfer charges across NAT gateways, virtual private gateways, transit gateways, AWS PrivateLink, and VPC endpoints\. Data transfer within the same Availability Zone \(uniquely identified using the AZ\-ID\) is free irrespective of account ownership of the communicating resources\.

## Limitations<a name="vpc-share-limitations"></a>

The following limitations apply to working with VPC sharing:
+ Owners can share subnets only with other accounts or organizational units that are in the same organization from AWS Organizations\.
+ Owners cannot share subnets that are in a default VPC\.
+ Participants cannot launch resources using security groups that are owned by other participants that share the VPC, or the VPC owner\.
+ Participants cannot launch resources using the default security group for the VPC because it belongs to the owner\.
+ Owners cannot launch resources using security groups that are owned by other participants\.
+ When participants launch resources in a shared subnet, they should make sure they attach their security group to the resource, and not rely on the default security group\. Participants cannot use the default security group because it belongs to the VPC owner\.
+ Participants cannot create Route 53 Resolver endpoints in a VPC that they do not own\. Only the VPC owner can create VPC\-level resources such as inbound endpoints\.
+ VPC tags, and tags for the resources within the shared VPC are not shared with the participants\.
+ Only a subnet owner can attach a transit gateway to the shared subnet\. Participants cannot\.
+ Participants can create Application Load Balancers and Network Load Balancers in a shared VPC, but they cannot register targets running in subnets that were not shared with them\.
+ Only a subnet owner can select a shared subnet when creating a Gateway Load Balancer\. Participants cannot\.
+ Service quotas apply per individual account\.