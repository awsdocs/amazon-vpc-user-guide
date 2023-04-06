# Configure your VPC<a name="modify-vpcs"></a>

Use the following procedures to view and configure your virtual private clouds \(VPC\)\.

**Topics**
+ [View details about your VPC](#view-vpc)
+ [Visualize the resources in your VPC](#view-vpc-resource-map)
+ [Add an IPv4 CIDR block to your VPC](#add-ipv4-cidr)
+ [Add an IPv6 CIDR block to your VPC](#vpc-associate-ipv6-cidr)
+ [Remove an IPv4 CIDR block from your VPC](#remove-ipv4-cidr)
+ [Remove an IPv6 CIDR block from your VPC](#vpc-disassociate-ipv6)

## View details about your VPC<a name="view-vpc"></a>

Use the following steps to view the details about your VPCs\.

**To view VPC details using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **VPCs**\.

1. Select the VPC, and then choose **View Details** to see the configuration details of your VPC\.

**To describe a VPC using the AWS CLI**  
Use the [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) command\.

**To view all of your VPCs across all Regions**  
Open the Amazon EC2 Global View console at [https://console\.aws\.amazon\.com/ec2globalview/home](https://console.aws.amazon.com/ec2globalview/home)\. For more information, see [List and filter resources using the Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view) in the *Amazon EC2 User Guide for Linux Instances*\.

## Visualize the resources in your VPC<a name="view-vpc-resource-map"></a>

Use the following steps to see a visual representation of the resources in your VPC using the **Resource map** tab\. The resource map shows relationships between resources inside a VPC and how traffic flows from subnets to NAT gateways, internet gateway and gateway endpoints\.

You can use the resource map to understand the architecture of a VPC, see how many subnets it has in it, which subnets are associated with which route tables, and which route tables have routes to NAT gateways, internet gateways, and gateway endpoints\.

You can also use the resource map to spot undesirable or incorrect configurations, such as private subnets disconnected from NAT gateways or private subnets with a route directly to the internet gateway\. You can choose resources within the resource map, such as route tables, and edit the configurations for those resources\.

**To visualize the resources in your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **VPCs**\.

1. Select the VPC\.

1. Choose the **Resource map** tab to display a visualization of the subnets, route tables, and the resources that traffic can traverse to reach other networks \(internet gateways, NAT gateways, and gateway VPC endpoints\)\.

1. Choose **Show details** to view details in addition to the resource IDs and zones displayed by default\.
   + **VPC**: The IPv4 and IPv6 CIDR ranges assigned to the VPC\.
   + **Subnets**: The IPv4 and IPv6 CIDR ranges assigned to each subnet\.
   + **Route tables**: The subnet associations, and the number of routes\.
   + **Network connections**: The details related to each type of connection\. For example, the number of network interfaces for a NAT gateway or the name of the service that you can connect to using a gateway VPC endpoint\. 

1. Hover over a resource to see the relationship between the resources\. Solid lines represent relationships between resources\. Dotted lines represent network traffic to network connections\.

## Add an IPv4 CIDR block to your VPC<a name="add-ipv4-cidr"></a>

Your VPC can have up to five IPv4 CIDR blocks by default, but this limit is adjustable\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\. For information about restrictions on IPv4 CIDR blocks for a VPC, see [VPC CIDR blocks](vpc-cidr-blocks.md)\.

**To add an IPv4 CIDR block to a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, and then choose **Actions**, **Edit CIDRs**\.

1. Choose **Add new IPv4 CIDR**\.

1. For **IPv4 CIDR block**, do one of the following:
   + Choose **IPv4 CIDR manual input** and enter an IPv4 CIDR block\.
   + Choose **IPAM\-allocated IPv4 CIDR** and select a CIDR from an IPv4 IPAM pool\.

1. Choose **Save** and then choose **Close**\.

1. After you've added an IPv4 CIDR block to your VPC, you can create subnets that use the new CIDR block\. For more information, see [Create a subnet](create-subnets.md)\.

**To associate an IPv4 CIDR block with a VPC using the AWS CLI**  
Use the [associate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-vpc-cidr-block.html) command\.

## Add an IPv6 CIDR block to your VPC<a name="vpc-associate-ipv6-cidr"></a>

Your VPC can have up to five IPv6 CIDR blocks by default, but this limit is adjustable\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\. For information about restrictions on IPv6 CIDR blocks for a VPC, see [VPC CIDR blocks](vpc-cidr-blocks.md)\.

**To add an IPv6 CIDR block to a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, and then choose **Actions**, **Edit CIDRs**\.

1. Choose **Add new IPv6 CIDR**\.

1. For **IPv6 CIDR block**, do one of the following:
   + **IPAM\-allocated IPv6 CIDR block** – \([IPAM](https://docs.aws.amazon.com/vpc/latest/ipam/what-is-it-ipam.html)\) Allocates an IPv6 CIDR block from an IPAM pool\. For **IPv6 IPAM pool**, select an IPAM pool from which to allocate the IPv6 CIDR block\. The size of the CIDR is limited by the allocation rules on the IPAM pool \(allowed minimum, allowed maximum, and default\)\.
   + **Amazon\-provided IPv6 CIDR block** – Allocates an IPv6 CIDR block from an Amazon pool of IPv6 addresses\. For **Network border group**, choose the group from which AWS advertises IP addresses\. Amazon provides a fixed IPv6 CIDR block size of /56\.
   + **IPv6 CIDR owned by me** – \([BYOIP](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-byoip.html)\) Allocates an IPv6 CIDR block from your IPv6 address pool\. For **Pool**, choose the IPv6 address pool from which to allocate the IPv6 CIDR block\.

1. Choose **Select CIDR** and then choose **Close**\.

1. After you've added an IPv6 CIDR block to your VPC, you can create subnets that use the new CIDR block\. For more information, see [Create a subnet](create-subnets.md)\.

**To associate an IPv6 CIDR block with a VPC using the AWS CLI**  
Use the [associate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-vpc-cidr-block.html) command\.

## Remove an IPv4 CIDR block from your VPC<a name="remove-ipv4-cidr"></a>

If your VPC has more than one IPv4 CIDR block associated with it, you can remove an IPv4 CIDR block from the VPC\. You cannot remove the primary IPv4 CIDR block\. You must remove an entire CIDR block; you cannot remove a subset of a CIDR block or a merged range of CIDR blocks\. You must first delete all subnets in the CIDR block\.

**To remove a CIDR block from a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, and choose **Actions**, **Edit CIDRs**\.

1. Under **VPC IPv4 CIDRs**, remove the CIDR by choosing **Remove**\.

1. Choose **Close**\.

**To disassociate an IPv4 CIDR block from a VPC using the AWS CLI**  
Use the [disassociate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-vpc-cidr-block.html) command\.

## Remove an IPv6 CIDR block from your VPC<a name="vpc-disassociate-ipv6"></a>

If you no longer want IPv6 support in your VPC, but you want to continue using your VPC to create and communicate with IPv4 resources, you can remove the IPv6 CIDR block\.

To remove an IPv6 CIDR block, you must first unassign any IPv6 addresses that are assigned to any instances in your subnet\.

Removing an IPv6 CIDR block does not automatically delete any security group rules, network ACL rules, or route table routes that you've configured for IPv6 networking\. You must manually modify or delete these rules or routes\.

**To remove an IPv6 CIDR block from a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, choose **Actions**, **Edit CIDRs**\.

1. Under **IPv6 CIDRs**, remove the IPv6 CIDR block by choosing **Remove**\.

1. Choose **Close**\.

**To disassociate an IPv6 CIDR block from a VPC using the AWS CLI**  
Use the [disassociate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-vpc-cidr-block.html) command\.