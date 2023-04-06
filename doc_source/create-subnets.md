# Create a subnet<a name="create-subnets"></a>

Use the following procedure to create subnets for your virtual private cloud \(VPC\)\. Depending on the connectivity that you need, you might also need to add gateways and route tables\.

**Considerations**
+ You must specify an IPv4 CIDR block for the subnet from the range of your VPC\. You can optionally specify an IPv6 CIDR block for a subnet if there is an IPv6 CIDR block associated with the VPC\. For more information, see [IP addressing for your VPCs and subnets](vpc-ip-addressing.md)\.
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

1. For **IPv4 CIDR block**, enter an IPv4 CIDR block for your subnet\. For example, `10.0.1.0/24`\. If you chose **IPv6\-only**, this option is unavailable\.

1. For **IPv6 CIDR block**, choose **Custom IPv6 CIDR** and specify the hexadecimal pair value \(for example, **00**\)\. This option is available only if the VPC has an associated IPv6 CIDR block\.

1. Choose **Create subnet**\.

**To add a subnet to your VPC using the AWS CLI**  
Use the [create\-subnet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-subnet.html) command\.

**Next steps**

After you create a subnet, you can configure it as follows:
+ Configure routing\. You can then create a custom route table and route that send traffic to a gateway that's associated with the VPC, such as an internet gateway\. For more information, see [Configure route tables](VPC_Route_Tables.md)\.
+ Modify the IP addressing behavior\. You can specify whether instances launched in the subnet receive a public IPv4 address, an IPv6 address, or both\. For more information, see [Subnet settings](configure-subnets.md#subnet-settings)\.
+ Modify the resource\-based name \(RBN\) settings\. For more information, see [Amazon EC2 instance hostname types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-naming.html#instance-naming-modify-instances)\.
+ Create or modify your network ACLs\. For more information, see [Control traffic to subnets using Network ACLs](vpc-network-acls.md)\.
+ Share the subnet with other accounts\. For more information, see [Share a subnet](vpc-sharing.md#vpc-sharing-share-subnet)\.