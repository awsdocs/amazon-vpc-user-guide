# Work with VPCs<a name="working-with-vpcs"></a>

Use the following procedures to create and configure virtual private clouds \(VPC\)\. Before you can launch resources into your VPC, you must subnets\.

Alternatively, you can create a VPC plus its subnets, gateways, and route tables in one step\. For more information, see [Create VPCs using the wizard](VPC_wizard.md)\.

**Topics**
+ [Create a VPC](#Create-VPC)
+ [View your VPCs](#view-vpc)
+ [Associate a secondary IP address CIDR block with your VPC](#add-ipv4-cidr)
+ [Associate an IPv6 CIDR block with your VPC](#vpc-associate-ipv6-cidr)
+ [Disassociate an IPv4 CIDR block from your VPC](#remove-ipv4-cidr)
+ [Disassociate an IPv6 CIDR block from your VPC](#vpc-disassociate-ipv6)
+ [Delete your VPC](#VPC_Deleting)

## Create a VPC<a name="Create-VPC"></a>

Follow the steps in this section to create a VPC\. When you create a VPC, you have two options:
+ **VPC only**: Creates only a VPC without any additional resources like subnets or NAT gateways within the VPC\.
+ **VPC, subnets, etc\.**: Creates a VPC, subnets, NAT gateways, and VPC endpoints\.

Follow the steps in either section below depending on the option that fits your needs\.

### Create a VPC only<a name="create-vpc-vpc-only"></a>

Follow the steps in this section to create only a VPC and no additional resources\.

**To create a VPC only**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**, **Create VPC**\.

1. Under **Resources to create**, choose **VPC only**\.

1. Specify the following VPC details as needed\. 
   + **Name tag**: Optionally provide a name for your VPC\. Doing so creates a tag with a key of `Name` and the value that you specify\.
   + **IPv4 CIDR block**: Specify an IPv4 CIDR block \(or IP address range\) for your VPC\. Choose one of the following options:
     + **IPv4 CIDR manual input**: Manually input an IPv4 CIDR\. The CIDR block size must have a size between /16 and /28\. We recommend that you specify a CIDR block from the private \(non\-publicly routable\) IP address ranges as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html); for example, `10.0.0.0/16`, or `192.168.0.0/16` \. 
**Note**  
You can specify a range of publicly routable IPv4 addresses\. However, we currently do not support direct access to the internet from publicly routable CIDR blocks in a VPC\. Windows instances cannot boot correctly if launched into a VPC with ranges from `224.0.0.0` to `255.255.255.255` \(Class D and Class E IP address ranges\)\.
     + **IPAM\-allocated IPv4 CIDR block**: If there is an Amazon VPC IP Address Manager \(IPAM\) IPv4 address pool available in this Region, you can get a CIDR from an IPAM pool\. If you select an IPAM pool, the size of the CIDR is limited by the allocation rules on the IPAM pool \(allowed minimum, allowed maximum, and default\)\. For more information about IPAM, see [What is IPAM?](https://docs.aws.amazon.com/vpc/latest/ipam/what-is-it-ipam.html) in the *Amazon VPC IPAM User Guide*\.
   + **IPv6 CIDR block**: Optionally associate an IPv6 CIDR block with your VPC\. Choose one of the following options, and then choose **Select CIDR**:
     + **No IPv6 CIDR block**: No IPv6 CIDR will be provisioned for this VPC\.
     + **IPAM\-allocated IPv6 CIDR block**: If there is an Amazon VPC IP Address Manager \(IPAM\) IPv6 address pool available in this Region, you can get a CIDR from an IPAM pool\. If you select an IPAM pool, the size of the CIDR is limited by the allocation rules on the IPAM pool \(allowed minimum, allowed maximum, and default\)\. For more information about IPAM, see [What is IPAM?](https://docs.aws.amazon.com/vpc/latest/ipam/what-is-it-ipam.html) in the *Amazon VPC IPAM User Guide*\.
     + **Amazon\-provided IPv6 CIDR block**: Requests an IPv6 CIDR block from an Amazon pool of IPv6 addresses\. For **Network Border Group**, select the group from which AWS advertises IP addresses\. Amazon provides a fixed IPv6 CIDR block size of /56\. You cannot configure the size of the IPv6 CIDR that Amazon provides
     + **IPv6 CIDR owned by me**: \([BYOIP](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-byoip.html)\) Allocates an IPv6 CIDR block from your IPv6 address pool\. For **Pool,** choose the IPv6 address pool from which to allocate the IPv6 CIDR block\.
   + **Tenancy**: Choose the tenancy option for this VPC\.
     + Select **Default** to ensure that EC2 instances launched in this VPC use the EC2 instance tenancy attribute specified when the EC2 instance is launched\.
     + Select **Dedicated** to ensure that EC2 instances launched in this VPC are run on dedicated tenancy instances regardless of the tenancy attribute specified at launch\.

     For more information about tenancy see [Configuring instance tenancy with a launch configuration](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-dedicated-instances.html) in the *Amazon EC2 Auto Scaling User Guide*\.
**Note**  
If your AWS Outposts require private connectivity, you must select **Default**\. For more information about AWS Outposts, see [What is AWS Outposts?](https://docs.aws.amazon.com/outposts/latest/userguide/what-is-outposts.html) in the *AWS Outposts User Guide*\. 
   + **Tags**: Add optional tags on the VPC\. A tag is a label that you assign to an AWS resource\. Each tag consists of a key and an optional value\. You can use tags to search and filter your resources or track your AWS costs\.

1. Choose **Create VPC**\.

Alternatively, you can use a command line tool\.

**To create a VPC using a command line tool**
+ [create\-vpc](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc.html) \(AWS CLI\)
+ [New\-EC2Vpc](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Vpc.html) \(AWS Tools for Windows PowerShell\)

**To describe a VPC using a command line tool**
+ [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) \(AWS CLI\)
+ [Get\-EC2Vpc](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Vpc.html) \(AWS Tools for Windows PowerShell\)

For more information about IP addresses, see [IP addressing](how-it-works.md#vpc-ip-addressing)\.

After you have created a VPC, you can create subnets\. For more information, see [Create a subnet in your VPC](working-with-subnets.md#create-subnets)\.

### Create a VPC, subnets, and other VPC resources<a name="create-vpc-and-other-resources"></a>

In this step, you create a VPC, subnets, Availability Zones, NAT gateways, and VPC endpoints\.

**To create a VPC, subnets, and other VPC resources**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**, **Create VPC**\.

1. Under **Resources to create**, choose **VPC, subnets, etc\.**\.

1. Modify the options as needed:
   + **Name tag auto\-generation**: Choose the Name tag that will be applied to the resources you create\. The tag can either be automatically generated for you, or you can define the value\. The defined value will be used to generate the Name tag in all resources as "name\-resource"\. For example if you enter "Preproduction", each subnet will be tagged with a "Preproduction\-subnet" Name tag\. For more information about tags, see \.
   + **IPv4 CIDR block**: Choose an IPv4 CIDR for the VPC\. This option is required\.
   + **IPv6 CIDR block**: Choose an IPv6 CIDR for the VPC\.
   + **Tenancy**: Choose the tenancy option for this VPC\.
     + Select **Default** to ensure that EC2 instances launched in this VPC use the EC2 instance tenancy attribute specified when the EC2 instance is launched\.
     + Select **Dedicated** to ensure that EC2 instances launched in this VPC are run on dedicated tenancy instances regardless of the tenancy attribute specified at launch\.

     For more information about tenancy see [Configuring instance tenancy with a launch configuration](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-dedicated-instances.html) in the *Amazon EC2 Auto Scaling User Guide*\.
**Note**  
If your AWS Outposts require private connectivity, you must select **Default**\. For more information about AWS Outposts, see [What is AWS Outposts?](https://docs.aws.amazon.com/outposts/latest/userguide/what-is-outposts.html) in the *AWS Outposts User Guide*\. 
   + **Availability Zones \(AZs\)**: Choose the number of Availability Zones \(AZ\) in which you want to create subnets\. An AZ is one or more discrete data centers with redundant power, networking, and connectivity in an AWS Region\. AZs give you the ability to operate production applications and databases that are more highly available, fault tolerant, and scalable than would be possible from a single data center\. If you partition your applications running in subnets across AZs, you are better isolated and protected from issues such as power outages, lightning strikes, tornadoes, earthquakes, and more\.
   + **Customize AZs**: Choose which AZs your subnets will be created in\.
   + **Number of public subnets**: Choose the number of subnets you would like to be considered "public" subnets\. A "public" subnet is a subnet that as a route table entry that points to an internet gateway\. This enables EC2 instances running in the subnet to be publicly accessible over the internet\.
   + **Customize public subnets CIDR blocks**: Choose the CIDR blocks for the "public" subnets\.
   + **Number of private subnets**: Choose the number of subnets you would like to be considered "private" subnets\. A "private" subnet is a subnet that does not have a route table entry that points to an internet gateway\. Use private subnets to secure backend resources that do not need to be publicly accessible over the internet\.
   + **Customize private subnets CIDR blocks**: Choose the CIDR blocks for the "private" subnets\.
   + **NAT gateways**: Choose the number of AZs in which to create Network Address Translation \(NAT\) gateways\. A NAT gateway is an AWS\-managed service that enables EC2 instances in private subnets to send outbound traffic to the internet\. Resources on the internet, however, cannot establish a connection with the instances\. Note that there is cost associated with NAT gateways\. For more information, see [NAT gateways](vpc-nat-gateway.md)\.
   + **VPC endpoints**: A VPC endpoint enables you to privately connect your VPC to supported AWS services like Amazon S3\. VPC endpoints enable you to create an isolated VPC that is closed from the public internet\. There is no additional charge for using gateway endpoints\. This can help avoid the costs associated with NAT gateways\.
   + **DNS options**: Choose the domain name resolution options for the EC2 instances launched into this VPC\. 

     
     + **Enable DNS hostnames**: Enables hostnames to be provisioned for EC2 instance public IPv4 addresses\.
     + **Enable DNS resolution**: Enables hostnames to be provisioned for EC2 instance public IPv4 addresses and enables domain name resolution of the hostnames\.
**Note**  
If you want to provision public IPv4 DNS hostnames to the EC2 instances launched into the subnets you are creating, you must enable both **Enable DNS hostnames** and **Enable DNS resolution** on the VPC\. If you only enable **Enable DNS hostnames**, the Public IPv4 DNS hostname does not get provisioned\.

1. In the **Preview** pane, you can see the planned VPC, subnet, route tables, and network interfaces that will be created\.

1. Choose **Create VPC**\.

## View your VPCs<a name="view-vpc"></a>

Use the following steps to view the details about your VPCs\.

**To view VPC details using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **VPCs**\.

1. Select the VPC, and then choose **View Details**\.

**To describe a VPC using a command line tool**
+ [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) \(AWS CLI\)
+ [Get\-EC2Vpc](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Vpc.html) \(AWS Tools for Windows PowerShell\)

**To view all of your VPCs across Regions**  
Open the Amazon EC2 Global View console at [ https://console\.aws\.amazon\.com/ec2globalview/home](https://console.aws.amazon.com/ec2globalview/home)\.

For more information about using Amazon EC2 Global View, see [List and filter resources using the Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view) in the Amazon EC2 User Guide for Linux Instances\.

## Associate a secondary IP address CIDR block with your VPC<a name="add-ipv4-cidr"></a>

You can add CIDR blocks to your VPC\. Ensure that you have read the applicable [restrictions](configure-your-vpc.md#vpc-resize)\.

After you've associated a CIDR block, the status goes to `associating`\. The CIDR block is ready to use when it's in the `associated` state\.

The Amazon Virtual Private Cloud Console provides the status of the request at the top of the page\.

**To add a CIDR block to your VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, and then choose **Actions**, **Edit CIDRs**\.

1. Choose **Add new IPv4 CIDR** or **Add new IPv6 CIDR**\.

1. For complete information about what your CIDR options are, see [Create a VPC](#Create-VPC)\.

1. Choose **Close**\.

**To add a CIDR block using a command line tool**
+ [associate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-vpc-cidr-block.html) \(AWS CLI\)
+ [Register\-EC2VpcCidrBlock](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2VpcCidrBlock.html) \(AWS Tools for Windows PowerShell\)

After you've added the CIDR blocks that you need, you can create subnets\. For more information, see [Create a subnet in your VPC](working-with-subnets.md#create-subnets)\.

## Associate an IPv6 CIDR block with your VPC<a name="vpc-associate-ipv6-cidr"></a>

You can associate an IPv6 CIDR block with any existing VPC\. The VPC must not have an existing IPv6 CIDR block associated with it\. 

**To associate an IPv6 CIDR block with a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, and then choose **Actions**, **Edit CIDRs**\.

1. Choose **Add new IPv6 CIDR**\.

1. For **IPv6 CIDR block**, do one of the following:
   + Choose **Amazon\-provided IPv6 CIDR block** to request an IPv6 CIDR block from Amazon's pool of IPv6 addresses\. For **Network border group**, select the group from where AWS advertises the IP addresses\.
   + Choose **IPv6 CIDR owned by me** to allocate an IPv6 CIDR block from your IPv6 address pool\. For **Pool**, choose the IPv6 address pool from which to allocate the IPv6 CIDR block\.

1. Choose **Select CIDR**\.

1. Choose **Close**\.

**To associate an IPv6 CIDR block with a VPC using a command line tool**
+ [associate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-vpc-cidr-block.html) \(AWS CLI\)
+ [Register\-EC2VpcCidrBlock](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2VpcCidrBlock.html) \(AWS Tools for Windows PowerShell\)

## Disassociate an IPv4 CIDR block from your VPC<a name="remove-ipv4-cidr"></a>

If your VPC has more than one IPv4 CIDR block associated with it, you can disassociate an IPv4 CIDR block from the VPC\. You cannot disassociate the primary IPv4 CIDR block\. You can only disassociate an entire CIDR block; you cannot disassociate a subset of a CIDR block or a merged range of CIDR blocks\. You must first delete all subnets in the CIDR block\.

**To remove a CIDR block from a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, and choose **Actions**, **Edit CIDRs**\.

1. Under **VPC IPv4 CIDRs**, choose the delete button \(a cross\) for the CIDR block to remove\.

1. Choose **Close**\.

Alternatively, you can use a command line tool\.

**To remove an IPv4 CIDR block from a VPC using a command line tool**
+ [disassociate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-vpc-cidr-block.html) \(AWS CLI\)
+ [Unregister\-EC2VpcCidrBlock](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2VpcCidrBlock.html) \(AWS Tools for Windows PowerShell\)

## Disassociate an IPv6 CIDR block from your VPC<a name="vpc-disassociate-ipv6"></a>

If you no longer want IPv6 support in your VPC, but you want to continue using your VPC to create and communicate with IPv4 resources, you can disassociate the IPv6 CIDR block\.

To disassociate an IPv6 CIDR block, you must first unassign any IPv6 addresses that are assigned to any instances in your subnet\.

**To disassociate an IPv6 CIDR block from a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, choose **Actions**, **Edit CIDRs**\.

1. Remove the IPv6 CIDR block by choosing the cross icon\.

1. Choose **Close**\.

**Note**  
Disassociating an IPv6 CIDR block does not automatically delete any security group rules, network ACL rules, or route table routes that you've configured for IPv6 networking\. You must manually modify or delete these rules or routes\. 

Alternatively, you can use a command line tool\.

**To disassociate an IPv6 CIDR block from a VPC using a command line tool**
+ [disassociate\-vpc\-cidr\-block](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-vpc-cidr-block.html) \(AWS CLI\)
+ [Unregister\-EC2VpcCidrBlock](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2VpcCidrBlock.html) \(AWS Tools for Windows PowerShell\)

## Delete your VPC<a name="VPC_Deleting"></a>

To delete a VPC using the VPC console, you must first terminate or delete the following components:
+ All instances in the VPC \- For information about how to terminate an instance, see [Terminate your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ VPC peering connections
+ Interface endpoints
+ NAT gateways

When you delete a VPC using the VPC console, we also delete the following VPC components for you:
+ Subnets
+ Security groups
+ Network ACLs
+ Route tables
+ Gateway endpoints
+ Internet gateways
+ Egress\-only internet gateways
+ DHCP options

If you have a AWS Site\-to\-Site VPN connection, you don't have to delete it or the other components related to the VPN \(such as the customer gateway and virtual private gateway\)\. If you plan to use the customer gateway with another VPC, we recommend that you keep the Site\-to\-Site VPN connection and the gateways\. Otherwise, you must configure your customer gateway device again after you create a new Site\-to\-Site VPN connection\. 

**To delete your VPC using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Terminate all instances in the VPC\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC to delete and choose **Actions**, **Delete VPC**\.

1. If you have a Site\-to\-Site VPN connection, select the option to delete it; otherwise, leave it unselected\. Choose **Delete VPC**\.

Alternatively, you can use a command line tool\. When you delete a VPC using the command line, you must first terminate all instances, and delete or detach all associated resources, including subnets, custom security groups, custom network ACLs, custom route tables, VPC peering connections, endpoints, the NAT gateway, the internet gateway, and the egress\-only internet gateway\.

**To delete a VPC using the command line**
+ [delete\-vpc](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc.html) \(AWS CLI\)
+ [Remove\-EC2Vpc](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Vpc.html) \(AWS Tools for Windows PowerShell\)