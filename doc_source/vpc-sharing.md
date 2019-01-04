# Working with Shared VPCs<a name="vpc-sharing"></a>

VPC sharing allows multiple AWS accounts to create their application resources, such as Amazon EC2 instances, Amazon Relational Database Service \(RDS\) databases, Amazon Redshift clusters, and AWS Lambda functions, into shared, centrally\-managed Amazon Virtual Private Clouds \(VPCs\)\. In this model, the account that owns the VPC \(owner\) shares one or more subnets with other accounts \(participants\) that belong to the same organization from AWS Organizations\. After a subnet is shared, the participants can view, create, modify, and delete their application resources in the subnets shared with them\. Participants cannot view, modify, or delete resources that belong to other participants or the VPC owner\.

**Topics**
+ [Shared VPCs Prerequisites](#vpc-share-prerequisites)
+ [Sharing a Subnet](#vpc-sharing-share-subnet)
+ [Unsharing a Shared Subnet](#vpc-sharing-stop-share-subnet)
+ [Identifying the Owner of a Shared Subnet](#vpc-sharing-view-owner)
+ [Shared Subnets Permissions](#vpc-sharing-permissions)
+ [Billing and Metering for the Owner and Participants](#vpc-share-billing)
+ [Unsupported Services for Shared Subnets](#vpc-share-unsupported-services)
+ [Limitations](#vpc-share-limitations)

## Shared VPCs Prerequisites<a name="vpc-share-prerequisites"></a>

You must enable resource sharing from the Master account for your organization\. For information about enabling resource sharing, see [Enable Sharing with AWS Organizations](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html#getting-started-sharing-orgs) in the *AWS RAM User Guide*\.

## Sharing a Subnet<a name="vpc-sharing-share-subnet"></a>

You can share non\-default subnets with other accounts within your organization\. To share subnets, you must first create a Resource Share with the subnets to be shared and the AWS accounts, organizational units, or an entire organization that you want to share the subnets with\. For information about creating a Resource Share, see [Creating a Resource Share](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html#getting-started-sharing-create) in the *AWS RAM User Guide*\. 

**To share a subnet using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Share subnet**\. 

1. Select your resource share and choose **Share subnet**\. 

**To share a subnet using the AWS CLI**  
Use the [create\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html) and [associate\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/associate-resource-share.html) commands\.

### Mapping Subnets Across Availability Zones<a name="vpc-share-subnets-map-availability-zone"></a>

To ensure that resources are distributed across the Availability Zones for a Region, we independently map Availability Zones to names for each account\. For example, the Availability Zone `us-east-1a` for your AWS account might not have the same location as `us-east-1a` for another AWS account\.

To coordinate Availability Zones across accounts for VPC sharing, you must use the *AZ ID*, which is a unique and consistent identifier for an Availability Zone\. For example, `use1-az1` is one of the Availability Zones in the `us-east-1` Region\. Availability Zone IDs enable you to determine the location of resources in one account relative to the resources in another account\. For more information, see [AZ IDs for Your Resources](https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html) in the *AWS RAM User Guide*\.

## Unsharing a Shared Subnet<a name="vpc-sharing-stop-share-subnet"></a>

The owner can unshare a shared subnet with participants at any time\. After the owner unshares a shared subnet, the following rules apply:
+ Existing participant resources continue to run in the unshared subnet\.
+ Participants can no longer create new resources in the unshared subnet\.
+ Participants can modify, describe, and delete their resources that are in the subnet\.
+ The VPC flow log subscription excludes all data that belongs to participant ENIs\. The participant can decide whether to create, or delete the ENI flow logs\.
+ If participants still have resources in the unshared subnet, the owner cannot delete the shared subnet or the shared\-subnet VPC\. The owner can only delete the subnet or shared\-subnet VPC after the participants delete all the resources in the unshared subnet\.

**To unshare a subnet using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Share subnet**\. 

1. Choose **Actions**, **Stop sharing**\. 

**To unshare a subnet using the AWS CLI**  
Use the [disassociate\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/disassociate-resource-share.html) command\.

## Identifying the Owner of a Shared Subnet<a name="vpc-sharing-view-owner"></a>

Participants can view the subnets that have been shared with them by using the Amazon VPC console, or the command line tool\.

**To identify a subnet owner \(console\)**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\. The **Owner** column displays the subnet owner\.

**To identify a subnet owner using the AWS CLI**  
Use the [describe\-subnets](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-subnets.html) and [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) commands, which include the ID of the owner in their output\.

## Shared Subnets Permissions<a name="vpc-sharing-permissions"></a>

### Owner Permissions<a name="vpc-owner-permissions"></a>

VPC owners are responsible for creating, managing and deleting all VPC\-level resources including subnets, route tables, network ACLs, peering connections, VPC endpoints, PrivateLink endpoints, internet gateways, NAT gateways, virtual private gateways, and transit gateway attachments\.

VPC owners cannot modify or delete participant resources including security groups that participants created\. VPC owners can view the details for all the network interfaces, and the security groups that are attached to the participant resources in order to facilitate troubleshooting, and auditing\. VPC owners can create flow log subscriptions at the VPC, subnet, or ENI level for traffic monitoring or troubleshooting\.

### Participant Permissions<a name="vpc-participant-permissions"></a>

Participants that are in a shared VPC are responsible for the creation, management and deletion of their resources including Amazon EC2 instances, Amazon RDS databases, and load balancers\. Participants cannot view, or modify resources that belong to other participant accounts\. Participants can view the details of the route tables, and network ACLs that are attached to the subnets shared with them\. However, they cannot modify VPC\-level resources including route tables, network ACLs, or subnets\. Participants can reference security groups that belong to other participants or the owner using the security group ID\. Participants can only create flow log subscriptions for the interfaces that they own\.

## Billing and Metering for the Owner and Participants<a name="vpc-share-billing"></a>

In a shared VPC, each participant pays for their application resources including Amazon EC2 instances, Amazon Relational Database Service databases, Amazon Redshift clusters, and AWS Lambda functions\. Participants also pay for data transfer charges associated with inter\-Availability Zone data transfer, data transfer over VPC peering connections, and data transfer through a AWS Direct Connect gateway\. VPC owners pay hourly charges \(where applicable\), data processing and data transfer charges across NAT gateways, virtual private gateways, transit gateways, PrivateLink, and VPC endpoints\. Data transfer within the same availability zone \(uniquely identified using the AZ\-ID\) is free irrespective of account ownership of the communicating resources\.

## Unsupported Services for Shared Subnets<a name="vpc-share-unsupported-services"></a>

Participants cannot use a shared subnet with the following services:
+ Amazon Aurora Serverless 
+ AWS CloudHSM
+ AWS Glue 
+  Amazon EMR
+ Network Load Balancer 

## Limitations<a name="vpc-share-limitations"></a>

The following limitations apply to working with VPC sharing:
+ Owners can share subnets only with other accounts or organizational units that are in the same organization from AWS Organizations\.
+ Owners cannot share subnets that are in a default VPC\.
+ Participants cannot launch resources using security groups that are owned by other participants or the owner\.
+ Participants cannot launch resources using the default security group for the VPC because it belongs to the owner\.
+ Service limits apply per individual account\. For more information about service limits, see [AWS Service Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *Amazon Web Services General Reference*\.