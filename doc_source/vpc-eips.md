# Elastic IP addresses<a name="vpc-eips"></a>

An *Elastic IP address* is a static, public IPv4 address designed for dynamic cloud computing\. You can associate an Elastic IP address with any instance or network interface for any VPC in your account\. With an Elastic IP address, you can mask the failure of an instance by rapidly remapping the address to another instance in your VPC\. Note that the advantage of associating the Elastic IP address with the network interface instead of directly with the instance is that you can move all the attributes of the network interface from one instance to another in a single step\.

We currently do not support Elastic IP addresses for IPv6\.

**Topics**
+ [Elastic IP address basics](#vpc-eip-overview)
+ [Working with Elastic IP addresses](#WorkWithEIPs)
+ [API and CLI overview](#eip-api-cli)

## Elastic IP address basics<a name="vpc-eip-overview"></a>

The following are the basic things that you need to know about Elastic IP addresses:
+ You first allocate an Elastic IP address for use in a VPC, and then associate it with an instance in your VPC \(it can be assigned to only one instance at a time\)\.
+ An Elastic IP address is a property of network interfaces\. You can associate an Elastic IP address with an instance by updating the network interface attached to the instance\.
+ If you associate an Elastic IP address with the eth0 network interface of your instance, its current public IPv4 address \(if it had one\) is released to the EC2\-VPC public IP address pool\. If you disassociate the Elastic IP address, the eth0 network interface is automatically assigned a new public IPv4 address within a few minutes\. This doesn't apply if you've attached a second network interface to your instance\. 
+ There are differences between an Elastic IP address that you use in a VPC and one that you use in EC2\-Classic\. For more information, see [Differences between EC2\-Classic and VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-classic-platform.html#differences-ec2-classic-vpc) in the *Amazon EC2 User Guide for Linux Instances*\)\.
+ You can move an Elastic IP address from one instance to another\. The instance can be in the same VPC or another VPC, but not in EC2\-Classic\.
+ Your Elastic IP addresses remain associated with your AWS account until you explicitly release them\.
+ To ensure efficient use of Elastic IP addresses, we impose a small hourly charge when they aren't associated with a running instance, or when they are associated with a stopped instance or an unattached network interface\. While your instance is running, you aren't charged for one Elastic IP address associated with the instance, but you are charged for any additional Elastic IP addresses associated with the instance\. For more information, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/on-demand/#Elastic_IP_Addresses)\.
+ You're limited to five Elastic IP addresses; to help conserve them, you can use a NAT device \(see [NAT](vpc-nat.md)\)\.
+ An Elastic IP address is accessed through the Internet gateway of a VPC\. If you have set up an AWS Site\-to\-Site VPN connection between your VPC and your network, the VPN traffic traverses a virtual private gateway, not an Internet gateway, and therefore cannot access the Elastic IP address\.
+ You can move an Elastic IP address that you've allocated for use in the EC2\-Classic platform to the VPC platform\. For more information, see [Migrating an Elastic IP address from EC2\-Classic](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-classic-platform.html#migrating-eip) in the *Amazon EC2 User Guide*\.
+ You can tag an Elastic IP address that's allocated for use in a VPC; however, cost allocation tags are not supported\. If you recover an Elastic IP address, tags are not recovered\. 
+ You can use any of the following options for the Elastic IP addresses:
  + Have Amazon provide the Elastic IP addresses\. When you select this option, you can associate the Elastic IP addresses with a network border group\. This is the location from which we advertise the CIDR block\. Setting the network border group limits the CIDR block to this group\. 
  + Use your own IP addresses\. For information about bringing your own IP addresses, see [Bring your own IP addresses \(BYOIP\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-byoip.html) in the* Amazon EC2 User Guide for Linux Instances*\.

## Working with Elastic IP addresses<a name="WorkWithEIPs"></a>

You can allocate an Elastic IP address and then associate it with an instance in a VPC\.

**To allocate an Elastic IP address for use in a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**\.

1. For **Public IPv4 address pool** choose one of the following:
   + **Amazon's pool of IP addresses**—If you want an IPv4 address to be allocated from Amazon's pool of IP addresses\.
   + **My pool of public IPv4 addresses**—If you want to allocate an IPv4 address from an IP address pool that you have brought to your AWS account\. This option is disabled if you do not have any IP address pools\.
   + **Customer owned pool of IPv4 addresses**—If you want to allocate an IPv4 address from a pool created from your on\-premises network for use with an AWS Outpost\. This option is disabled if you do not have an AWS Outpost\.

1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC**\.

**To view your Elastic IP addresses**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. To filter the displayed list, start typing part of the Elastic IP address or the ID of the instance to which it's assigned in the search box\. 

**To associate an Elastic IP address with a running instance in a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select an Elastic IP address that's allocated for use with a VPC \(the **Scope** column has a value of `vpc`\), choose **Actions**, and then choose **Associate address**\.

1. Choose **Instance** or **Network interface**, and then select either the instance or network interface ID\. Select the private IP address with which to associate the Elastic IP address\. Choose **Associate**\.
**Note**  
A network interface can have several attributes, including an Elastic IP address\. You can create a network interface and attach and detach it from instances in your VPC\. The advantage of making the Elastic IP address an attribute of the network interface instead of associating it directly with the instance is that you can move all the attributes of the network interface from one instance to another in a single step\. For more information, see [Elastic network interfaces\.](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html)

After you associate the Elastic IP address with your instance, it receives a DNS hostname if DNS hostnames are enabled\. For more information, see [Using DNS with your VPC](vpc-dns.md)\.

You can apply tags to your Elastic IP address to help you identify it or categorize it according to your organization's needs\.

**To tag an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address and choose **Tags**\.

1. Choose **Add/Edit Tags**, enter the tag keys and values as required, and choose **Save**\.

To change which instance an Elastic IP address is associated with, disassociate it from the currently associated instance, and then associate it with the new instance in the VPC\. 

**To disassociate an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, choose **Actions**, and then choose **Disassociate address**\.

1. When prompted, choose **Disassociate address**\.

If you no longer need an Elastic IP address, we recommend that you release it \(the address must not be associated with an instance\)\. You incur charges for any Elastic IP address that's allocated for use with a VPC but not associated with an instance\.

**To release an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, choose **Actions**, and then choose **Release addresses**\.

1. When prompted, choose **Release**\.

If you release your Elastic IP address, you might be able to recover it\. You cannot recover the Elastic IP address if it has been allocated to another AWS account, or if it results in you exceeding your Elastic IP address quota\.

Currently, you can recover an Elastic IP address using the Amazon EC2 API or a command line tool only\.

**To recover an Elastic IP address using the AWS CLI**
+ Use the [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) command and specify the IP address using the `--address` parameter\.

  ```
  aws ec2 allocate-address --domain vpc --address 203.0.113.3
  ```

## API and CLI overview<a name="eip-api-cli"></a>

You can perform the tasks described on this page using the command line or an API\. For more information about the command line interfaces and a list of available APIs, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Acquire an Elastic IP address**
+ [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) \(AWS CLI\)
+ [New\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**Associate an Elastic IP address with an instance or network interface**
+ [associate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)
+ [Register\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**Describe one or more Elastic IP addresses**
+ [describe\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-addresses.html) \(AWS CLI\)
+ [Get\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**Tag an Elastic IP address**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

**Disassociate an Elastic IP address**
+ [disassociate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-address.html) \(AWS CLI\)
+ [Unregister\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**Release an Elastic IP address**
+ [release\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/release-address.html) \(AWS CLI\)
+ [Remove\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Address.html) \(AWS Tools for Windows PowerShell\)