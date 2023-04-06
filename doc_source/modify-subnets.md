# Configure your subnets<a name="modify-subnets"></a>

Use the following procedures to configure subnets for your virtual private cloud \(VPC\)\.

**Topics**
+ [View your subnets](#view-subnet)
+ [Add an IPv6 CIDR block to your subnet](#subnet-associate-ipv6-cidr)
+ [Remove an IPv6 CIDR block from your subnet](#subnet-disassociate-ipv6)
+ [Modify the public IPv4 addressing attribute for your subnet](#subnet-public-ip)
+ [Modify the IPv6 addressing attribute for your subnet](#subnet-ipv6)

## View your subnets<a name="view-subnet"></a>

Use the following steps section to view the details about your subnet\.

**To view subnet details using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the checkbox for the subnet or choose the subnet ID to open the detail page\.

**To describe a subnet using the AWS CLI**  
Use the [describe\-subnets](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-subnets.html) command\.

**To view your subnets across all Regions**  
Open the Amazon EC2 Global View console at [ https://console\.aws\.amazon\.com/ec2globalview/home](https://console.aws.amazon.com/ec2globalview/home)\. For more information, see [List and filter resources using the Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view) in the *Amazon EC2 User Guide for Linux Instances*\.

## Add an IPv6 CIDR block to your subnet<a name="subnet-associate-ipv6-cidr"></a>

You can associate an IPv6 CIDR block with an existing subnet in your VPC\. The subnet must not have an existing IPv6 CIDR block associated with it\. 

**To add an IPv6 CIDR block to a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Edit IPv6 CIDRs**\.

1. Choose **Add IPv6 CIDR**\. Specify the hexadecimal pair for the subnet \(for example, **00**\)\.

1. Choose **Save**\.

**To associate an IPv6 CIDR block with a subnet using the AWS CLI**  
Use the [associate\-subnet\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-subnet-cidr-block.html) command\.

## Remove an IPv6 CIDR block from your subnet<a name="subnet-disassociate-ipv6"></a>

If you no longer want IPv6 support in your subnet, but you want to continue to use your subnet to create and communicate with IPv4 resources, you can remove the IPv6 CIDR block\.

Before you can remove an IPv6 CIDR block, you must first unassign any IPv6 addresses that are assigned to any instances in your subnet\.

**To remove an IPv6 CIDR block from a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the subnet and choose **Actions**, **Edit IPv6 CIDRs**\.

1. Find the IPv6 CIDR block and choose **Remove**\.

1. Choose **Save**\.

**To disassociate an IPv6 CIDR block from a subnet using the AWS CLI**  
Use the [disassociate\-subnet\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-subnet-cidr-block.html) command\.

## Modify the public IPv4 addressing attribute for your subnet<a name="subnet-public-ip"></a>

By default, nondefault subnets have the IPv4 public addressing attribute set to `false`, and default subnets have this attribute set to `true`\. An exception is a nondefault subnet created by the Amazon EC2 launch instance wizard — the wizard sets the attribute to `true`\. You can modify this attribute using the Amazon VPC console\.

**To modify your subnet's public IPv4 addressing behavior**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Actions**, **Edit subnet settings**\.

1. The **Enable auto\-assign public IPv4 address** check box, if selected, requests a public IPv4 address for all instances launched into the selected subnet\. Select or clear the check box as required, and then choose **Save**\.

**To modify a subnet attribute using the AWS CLI**  
Use the [modify\-subnet\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-subnet-attribute.html) command\.

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

**To modify a subnet attribute using the AWS CLI**  
Use the [modify\-subnet\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-subnet-attribute.html) command\.