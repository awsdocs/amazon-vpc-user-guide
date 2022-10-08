# Work with subnets<a name="working-with-subnets"></a>

Use the following procedures to create and configure subnets for your virtual private cloud \(VPC\)\. Depending on the connectivity that you need, you might also need to add gateways and route tables\.

**Topics**
+ [Create a subnet in your VPC](#create-subnets)
+ [View your subnets](#view-subnet)
+ [Associate an IPv6 CIDR block with your subnet](#subnet-associate-ipv6-cidr)
+ [Disassociate an IPv6 CIDR block from your subnet](#subnet-disassociate-ipv6)
+ [Modify the public IPv4 addressing attribute for your subnet](#subnet-public-ip)
+ [Modify the IPv6 addressing attribute for your subnet](#subnet-ipv6)
+ [Delete a subnet](#subnet-deleting)
+ [API and command overview](#subnet-api-cli)

## Create a subnet in your VPC<a name="create-subnets"></a>

To add a new subnet to your VPC, you must specify an IPv4 CIDR block for the subnet from the range of your VPC\. You can specify the Availability Zone in which you want the subnet to reside\. You can have multiple subnets in the same Availability Zone\.

**Considerations**
+ You can optionally specify an IPv6 CIDR block for a subnet if there is an IPv6 CIDR block associated with the VPC\.
+ If you create an IPv6\-only subnet, be aware of the following\. An EC2 instance launched in an IPv6\-only subnet receives an IPv6 address but not an IPv4 address\. Any instances that you launch into an IPv6\-only subnet must be [instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances)\.
+ To create the subnet in a Local Zone or a Wavelength Zone, you must enable the Zone\. For more information, see [Regions and Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To add a subnet to your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Choose **Create subnet**\.

1. For **VPC ID**: Choose the VPC for the subnet\.

1. \(Optional\) For **Subnet name**, enter a name for your subnet\. Doing so creates a tag with a key of `Name` and the value that you specify\.

1. For **Availability Zone**, you can choose a Zone for your subnet, or leave the default **No Preference** to let AWS choose one for you\.

1. If the subnet should be an IPv6\-only subnet, choose **IPv6\-only**\. This option is only available if the VPC has an associated IPv6 CIDR block\. If you choose this option, you can't associate an IPv4 CIDR block with the subnet\.

1. For **IPv4 CIDR block**, enter an IPv4 CIDR block for your subnet\. For example, `10.0.1.0/24`\. For more information, see [IPv4 VPC CIDR blocks](configure-your-vpc.md#vpc-sizing-ipv4)\. If you chose **IPv6\-only**, this option is unavailable\.

1. For **IPv6 CIDR block**, choose **Custom IPv6 CIDR** and specify the hexadecimal pair value \(for example, **00**\)\. This option is available only if the VPC has an associated IPv6 CIDR block\.

1. Choose **Create subnet**\.

**Next steps**

After you create a subnet, you can configure it as follows:
+ Configure routing\. You can then create a custom route table and route that send traffic to a gateway that's associated with the VPC, such as an internet gateway\. For more information, see [Configure route tables](VPC_Route_Tables.md)\.
+ Modify the IP addressing behavior\. You can specify whether instances launched in the subnet receive a public IPv4 address, an IPv6 address, or both\. For more information, see [Subnet settings](configure-subnets.md#subnet-settings)\.
+ Modify the resource\-based name \(RBN\) settings\. For more information, see [Amazon EC2 instance hostname types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-naming.html#instance-naming-modify-instances)\.
+ Create or modify your network ACLs\. For more information, see [Control traffic to subnets using Network ACLs](vpc-network-acls.md)\.
+ Share the subnet with other accounts\. For more information, see [Share a subnet](vpc-sharing.md#vpc-sharing-share-subnet)\.

## View your subnets<a name="view-subnet"></a>

Use the following steps section to view the details about your subnet\.

**To view your subnets in the current Region**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the checkbox for the subnet or choose the subnet ID to open the detail page\.

**To view your subnets across Regions**  
Open the Amazon EC2 Global View console at [ https://console\.aws\.amazon\.com/ec2globalview/home](https://console.aws.amazon.com/ec2globalview/home)\. For more information, see [List and filter resources using the Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view) in the Amazon EC2 User Guide for Linux Instances\.

## Associate an IPv6 CIDR block with your subnet<a name="subnet-associate-ipv6-cidr"></a>

You can associate an IPv6 CIDR block with an existing subnet in your VPC\. The subnet must not have an existing IPv6 CIDR block associated with it\. 

**To associate an IPv6 CIDR block with a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Edit IPv6 CIDRs**\.

1. Choose **Add IPv6 CIDR**\. Specify the hexadecimal pair for the subnet \(for example, **00**\)\.

1. Choose **Save**\.

## Disassociate an IPv6 CIDR block from your subnet<a name="subnet-disassociate-ipv6"></a>

If you no longer want IPv6 support in your subnet, but you want to continue to use your subnet to create and communicate with IPv4 resources, you can disassociate the IPv6 CIDR block\.

To disassociate an IPv6 CIDR block, you must first unassign any IPv6 addresses that are assigned to any instances in your subnet\.

**To disassociate an IPv6 CIDR block from a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the subnet and choose **Actions**, **Edit IPv6 CIDRs**\.

1. Find the IPv6 CIDR block and choose **Remove**\.

1. Choose **Save**\.

## Modify the public IPv4 addressing attribute for your subnet<a name="subnet-public-ip"></a>

By default, nondefault subnets have the IPv4 public addressing attribute set to `false`, and default subnets have this attribute set to `true`\. An exception is a nondefault subnet created by the Amazon EC2 launch instance wizard — the wizard sets the attribute to `true`\. You can modify this attribute using the Amazon VPC console\.

**To modify your subnet's public IPv4 addressing behavior**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Edit subnet settings**\.

1. The **Enable auto\-assign public IPv4 address** check box, if selected, requests a public IPv4 address for all instances launched into the selected subnet\. Select or clear the check box as required, and then choose **Save**\.

## Modify the IPv6 addressing attribute for your subnet<a name="subnet-ipv6"></a>

By default, all subnets have the IPv6 addressing attribute set to `false`\. You can modify this attribute using the Amazon VPC console\. If you enable the IPv6 addressing attribute for your subnet, network interfaces created in the subnet receive an IPv6 address from the range of the subnet\. Instances launched into the subnet receive an IPv6 address on the primary network interface\. 

Your subnet must have an associated IPv6 CIDR block\.

**Note**  
If you enable the IPv6 addressing feature for your subnet, your network interface or instance only receives an IPv6 address if it's created using version `2016-11-15` or later of the Amazon EC2 API\. The Amazon EC2 console uses the latest API version\.

**To modify your subnet's IPv6 addressing behavior**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Edit subnet settings**\.

1. The **Enable auto\-assign IPv6 address** check box, if selected, requests an IPv6 address for all network interfaces created in the selected subnet\. Select or clear the check box as required, and then choose **Save**\.

## Delete a subnet<a name="subnet-deleting"></a>

If you no longer need a subnet, you can delete it\. You cannot delete a subnet if it contains any network interfaces\. For example, you must terminate any instances in a subnet before you can delete it\.

**To delete a subnet**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Terminate all instances in the subnet\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *EC2 User Guide*\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the subnet and choose **Actions**, **Delete subnet**\.

1. When prompted for confirmation, type **delete** and then choose **Delete**\.

## API and command overview<a name="subnet-api-cli"></a>

You can perform the tasks described on this page using the command line or an API\. For more information about the command line interfaces and a list of available API actions, see [Working with Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Add a subnet**
+ [create\-subnet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-subnet.html) \(AWS CLI\)
+ [New\-EC2Subnet](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Subnet.html) \(AWS Tools for Windows PowerShell\)

**Describe your subnets**
+ [describe\-subnets](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-subnets.html) \(AWS CLI\)
+ [Get\-EC2Subnet](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Subnet.html) \(AWS Tools for Windows PowerShell\)

**Associate an IPv6 CIDR block with a subnet**
+ [associate\-subnet\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-subnet-cidr-block.html) \(AWS CLI\)
+ [Register\-EC2SubnetCidrBlock](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2SubnetCidrBlock.html) \(AWS Tools for Windows PowerShell\)

**Disassociate an IPv6 CIDR block from a subnet**
+ [disassociate\-subnet\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-subnet-cidr-block.html) \(AWS CLI\)
+ [Unregister\-EC2SubnetCidrBlock](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2SubnetCidrBlock.html) \(AWS Tools for Windows PowerShell\)

**Delete a subnet**
+ [delete\-subnet](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-subnet.html) \(AWS CLI\)
+ [Remove\-EC2Subnet](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Subnet.html) \(AWS Tools for Windows PowerShell\)