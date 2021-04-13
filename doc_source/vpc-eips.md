# Elastic IP addresses<a name="vpc-eips"></a>

An *Elastic IP address* is a static, public IPv4 address designed for dynamic cloud computing\. You can associate an Elastic IP address with any instance or network interface in any VPC in your account\. With an Elastic IP address, you can mask the failure of an instance by rapidly remapping the address to another instance in your VPC\. 

## Elastic IP address concepts and rules<a name="vpc-eip-overview"></a>

To use an Elastic IP address, you first allocate it for use in your account\. Then, you can associate it with an instance or network interface in your VPC\. Your Elastic IP address remains allocated to your AWS account until you explicitly release it\.

An Elastic IP address is a property of a network interface\. You can associate an Elastic IP address with an instance by updating the network interface attached to the instance\. The advantage of associating the Elastic IP address with the network interface instead of directly with the instance is that you can move all the attributes of the network interface from one instance to another in a single step\. For more information, see [Elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The following rules apply:
+ An Elastic IP address can be associated with a single instance or network interface at a time\. 
+ You can move an Elastic IP address from one instance or network interface to another\. 
+ If you associate an Elastic IP address with the eth0 network interface of your instance, its current public IPv4 address \(if it had one\) is released to the EC2\-VPC public IP address pool\. If you disassociate the Elastic IP address, the eth0 network interface is automatically assigned a new public IPv4 address within a few minutes\. This doesn't apply if you've attached a second network interface to your instance\. 
+ To ensure efficient use of Elastic IP addresses, we impose a small hourly charge when they aren't associated with a running instance, or when they are associated with a stopped instance or an unattached network interface\. While your instance is running, you aren't charged for one Elastic IP address associated with the instance, but you are charged for any additional Elastic IP addresses associated with the instance\. For more information, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/on-demand/#Elastic_IP_Addresses)\.
+ You're limited to five Elastic IP addresses\. To help conserve them, you can use a NAT device\. For more information, see [NAT devices for your VPC](vpc-nat.md)\.
+ Elastic IP addresses for IPv6 are not supported\.
+ You can tag an Elastic IP address that's allocated for use in a VPC, however, cost allocation tags are not supported\. If you recover an Elastic IP address, tags are not recovered\. 
+ You can access an Elastic IP address from the internet when the security group and network ACL allow traffic from the source IP address\. The reply traffic from within the VPC back to the internet requires an internet gateway\. For more information, see [Security groups for your VPC](VPC_SecurityGroups.md) and [Network ACLs](vpc-network-acls.md)\.
+ You can use any of the following options for the Elastic IP addresses:
  + Have Amazon provide the Elastic IP addresses\. When you select this option, you can associate the Elastic IP addresses with a network border group\. This is the location from which we advertise the CIDR block\. Setting the network border group limits the CIDR block to this group\. 
  + Use your own IP addresses\. For information about bringing your own IP addresses, see [Bring your own IP addresses \(BYOIP\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-byoip.html) in the* Amazon EC2 User Guide for Linux Instances*\.

There are differences between an Elastic IP address that you use in a VPC and one that you use in EC2\-Classic\. For more information, see [Differences between EC2\-Classic and VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-classic-platform.html#differences-ec2-classic-vpc) in the *Amazon EC2 User Guide for Linux Instances*\. You can move an Elastic IP address that you've allocated for use in the EC2\-Classic platform to the VPC platform\. For more information, see [Migrating an Elastic IP address from EC2\-Classic](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-classic-platform.html#migrating-eip)\.

Elastic IP addresses are regional\. For more information about using Global Accelerator to provision global IP addresses, see [Using global static IP addresses instead of regional static IP addresses](https://docs.aws.amazon.com/global-accelerator/latest/dg/about-accelerators.eip-accelerator.html) in the *AWS Global Accelerator Developer Guide*\.

## Working with Elastic IP addresses<a name="WorkWithEIPs"></a>

The following sections describe how you can work with Elastic IP addresses\.

**Topics**
+ [Allocating an Elastic IP address](#allocate-eip)
+ [Associating an Elastic IP address](#associate-eip)
+ [Viewing your Elastic IP addresses](#view-eip)
+ [Tagging an Elastic IP address](#tag-eip)
+ [Disassociating an Elastic IP address](#disassociate-eip)
+ [Releasing an Elastic IP address](#release-eip)
+ [Recovering an Elastic IP address](#recover-eip)

### Allocating an Elastic IP address<a name="allocate-eip"></a>

Before you use an Elastic IP, you must allocate one for use in your VPC\.

------
#### [ Console ]

**To allocate an Elastic IP address for use in a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate Elastic IP address**\.

1. For **Public IPv4 address pool** choose one of the following:
   + **Amazon's pool of IP addresses**—If you want an IPv4 address to be allocated from Amazon's pool of IP addresses\.
   + **My pool of public IPv4 addresses**—If you want to allocate an IPv4 address from an IP address pool that you have brought to your AWS account\. This option is disabled if you do not have any IP address pools\.
   + **Customer owned pool of IPv4 addresses**—If you want to allocate an IPv4 address from a pool created from your on\-premises network for use with an AWS Outpost\. This option is disabled if you do not have an AWS Outpost\.

1. \(Optional\) Add or remove a tag\.

   \[Add a tag\] Choose **Add new tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose **Remove** to the right of the tag’s Key and Value\.

1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC**\.

------
#### [ CLI and API ]

**To allocate an Elastic IP address**
+ [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) \(AWS CLI\)
+ [New\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) \(AWS Tools for Windows PowerShell\)

------

### Associating an Elastic IP address<a name="associate-eip"></a>

You can associate an Elastic IP with a running instance or network interface in your VPC\.

After you associate the Elastic IP address with your instance, the instance receives a public DNS hostname if DNS hostnames are enabled\. For more information, see [Using DNS with your VPC](vpc-dns.md)\.

------
#### [ Console ]

**To associate an Elastic IP address with an instance or network interface in a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select an Elastic IP address that's allocated for use with a VPC \(the **Scope** column has a value of `vpc`\), and then choose **Actions**, **Associate Elastic IP address**\.

1. Choose **Instance** or **Network interface**, and then select either the instance or network interface ID\. Select the private IP address with which to associate the Elastic IP address\. Choose **Associate**\.

------
#### [ CLI and API ]

**To associate an Elastic IP address with an instance or network interface**
+ [associate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)
+ [Register\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

------

### Viewing your Elastic IP addresses<a name="view-eip"></a>

You can view the Elastic IP addresses that are allocated to your account\.

------
#### [ Console ]

**To view your Elastic IP addresses**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. To filter the displayed list, start typing part of the Elastic IP address or one of its attributes in the search box\.

------
#### [ CLI and API ]

**To view one or more Elastic IP addresses**
+ [describe\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-addresses.html) \(AWS CLI\)
+ [Get\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) \(AWS Tools for Windows PowerShell\)

------

### Tagging an Elastic IP address<a name="tag-eip"></a>

You can apply tags to your Elastic IP address to help you identify it or categorize it according to your organization's needs\.

------
#### [ Console ]

**To tag an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address and choose **Tags**\.

1. Choose **Manage tags**, enter the tag keys and values as required, and choose **Save**\.

------
#### [ CLI and API ]

**To tag an Elastic IP address**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

------

### Disassociating an Elastic IP address<a name="disassociate-eip"></a>

To change the resource that the Elastic IP address is associated with, you must first disassociate it from the currently associated resource\.

------
#### [ Console ]

**To disassociate an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, and then choose **Actions**, **Disassociate Elastic IP address**\.

1. When prompted, choose **Disassociate**\.

------
#### [ CLI and API ]

**To disassociate an Elastic IP address**
+ [disassociate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-address.html) \(AWS CLI\)
+ [Unregister\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Address.html) \(AWS Tools for Windows PowerShell\)

------

### Releasing an Elastic IP address<a name="release-eip"></a>

If you no longer need an Elastic IP address, we recommend that you release it\. You incur charges for any Elastic IP address that's allocated for use with a VPC but not associated with an instance\. The Elastic IP address must not be associated with an instance or network interface\.

------
#### [ Console ]

**To release an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, and then choose **Actions**, **Release Elastic IP addresses**\.

1. When prompted, choose **Release**\.

------
#### [ CLI and API ]

**To release an Elastic IP address**
+ [release\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/release-address.html) \(AWS CLI\)
+ [Remove\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Address.html) \(AWS Tools for Windows PowerShell\)

------

### Recovering an Elastic IP address<a name="recover-eip"></a>

If you release your Elastic IP address, you might be able to recover it\. You cannot recover the Elastic IP address if it has been allocated to another AWS account, or if it results in you exceeding your Elastic IP address quota\.

You can recover an Elastic IP address using the Amazon EC2 API or a command line tool\.

**To recover an Elastic IP address using the AWS CLI**  
Use the [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) command and specify the IP address using the `--address` parameter\.

```
aws ec2 allocate-address --domain vpc --address 203.0.113.3
```