# Associate Elastic IP addresses with resources in your VPC<a name="vpc-eips"></a>

An *Elastic IP address* is a static, public IPv4 address designed for dynamic cloud computing\. You can associate an Elastic IP address with any instance or network interface in any VPC in your account\. With an Elastic IP address, you can mask the failure of an instance by rapidly remapping the address to another instance in your VPC\. 

## Elastic IP address concepts and rules<a name="vpc-eip-overview"></a>

To use an Elastic IP address, you first allocate it for use in your account\. Then, you can associate it with an instance or network interface in your VPC\. Your Elastic IP address remains allocated to your AWS account until you explicitly release it\.

An Elastic IP address is a property of a network interface\. You can associate an Elastic IP address with an instance by updating the network interface attached to the instance\. The advantage of associating the Elastic IP address with the network interface instead of directly with the instance is that you can move all the attributes of the network interface from one instance to another in a single step\. For more information, see [Elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The following rules apply:
+ An Elastic IP address can be associated with a single instance or network interface at a time\. 
+ You can move an Elastic IP address from one instance or network interface to another\. 
+ If you associate an Elastic IP address with the eth0 network interface of your instance, its current public IPv4 address \(if it had one\) is released to the EC2\-VPC public IP address pool\. If you disassociate the Elastic IP address, the eth0 network interface is automatically assigned a new public IPv4 address within a few minutes\. This doesn't apply if you've attached a second network interface to your instance\. 
+ To ensure efficient use of Elastic IP addresses, we impose a small hourly charge when they aren't associated with a running instance, or when they are associated with a stopped instance or an unattached network interface\. While your instance is running, you aren't charged for one Elastic IP address associated with the instance, but you are charged for any additional Elastic IP addresses associated with the instance\. For more information, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/on-demand/#Elastic_IP_Addresses)\.
+ You're limited to five Elastic IP addresses\. To help conserve them, you can use a NAT device\. For more information, see [Connect to the internet or other networks using NAT devices](vpc-nat.md)\.
+ Elastic IP addresses for IPv6 are not supported\.
+ You can tag an Elastic IP address that's allocated for use in a VPC, however, cost allocation tags are not supported\. If you recover an Elastic IP address, tags are not recovered\. 
+ You can access an Elastic IP address from the internet when the security group and network ACL allow traffic from the source IP address\. The reply traffic from within the VPC back to the internet requires an internet gateway\. For more information, see [Control traffic to resources using security groups](VPC_SecurityGroups.md) and [Control traffic to subnets using Network ACLs](vpc-network-acls.md)\.
+ You can use any of the following options for the Elastic IP addresses:
  + Have Amazon provide the Elastic IP addresses\. When you select this option, you can associate the Elastic IP addresses with a network border group\. This is the location from which we advertise the CIDR block\. Setting the network border group limits the CIDR block to this group\. 
  + Use your own IP addresses\. For information about bringing your own IP addresses, see [Bring your own IP addresses \(BYOIP\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-byoip.html) in the* Amazon EC2 User Guide for Linux Instances*\.

There are differences between an Elastic IP address that you use in a VPC and one that you use in EC2\-Classic\. For more information, see [Differences between EC2\-Classic and VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-classic-platform.html#differences-ec2-classic-vpc) in the *Amazon EC2 User Guide for Linux Instances*\. You can move an Elastic IP address that you've allocated for use in the EC2\-Classic platform to the VPC platform\. For more information, see [Elastic IP addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-migrate.html#vpc-migrate-eip) in the *Amazon EC2 User Guide for Linux Instances*\.

Elastic IP addresses are regional\. For more information about using Global Accelerator to provision global IP addresses, see [Using global static IP addresses instead of regional static IP addresses](https://docs.aws.amazon.com/global-accelerator/latest/dg/about-accelerators.eip-accelerator.html) in the *AWS Global Accelerator Developer Guide*\.

## Work with Elastic IP addresses<a name="WorkWithEIPs"></a>

The following sections describe how you can work with Elastic IP addresses\.

**Topics**
+ [Allocate an Elastic IP address](#allocate-eip)
+ [Associate an Elastic IP address](#associate-eip)
+ [View your Elastic IP addresses](#view-eip)
+ [Tag an Elastic IP address](#tag-eip)
+ [Disassociate an Elastic IP address](#disassociate-eip)
+ [Transfer Elastic IP addresses](#transfer-EIPs-intro)
+ [Release an Elastic IP address](#release-eip)
+ [Recover an Elastic IP address](#recover-eip)
+ [API and command overview](#eip-api-cli)

### Allocate an Elastic IP address<a name="allocate-eip"></a>

Before you use an Elastic IP, you must allocate one for use in your VPC\.

**To allocate an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate Elastic IP address**\.

1. For **Public IPv4 address pool** choose one of the following:
   + **Amazon's pool of IP addresses**—If you want an IPv4 address to be allocated from Amazon's pool of IP addresses\.
   + **My pool of public IPv4 addresses**—If you want to allocate an IPv4 address from an IP address pool that you have brought to your AWS account\. This option is disabled if you do not have any IP address pools\.
   + **Customer owned pool of IPv4 addresses**—If you want to allocate an IPv4 address from a pool created from your on\-premises network for use with an Outpost\. This option is only available if you have an Outpost\.

1. \(Optional\) Add or remove a tag\.

   \[Add a tag\] Choose **Add new tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose **Remove** to the right of the tag’s Key and Value\.

1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC**\.

### Associate an Elastic IP address<a name="associate-eip"></a>

You can associate an Elastic IP with a running instance or network interface in your VPC\.

After you associate the Elastic IP address with your instance, the instance receives a public DNS hostname if DNS hostnames are enabled\. For more information, see [DNS attributes for your VPC](vpc-dns.md)\.

**To associate an Elastic IP address with an instance or network interface**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select an Elastic IP address that's allocated for use with a VPC \(the **Scope** column has a value of `vpc`\), and then choose **Actions**, **Associate Elastic IP address**\.

1. Choose **Instance** or **Network interface**, and then select either the instance or network interface ID\. Select the private IP address with which to associate the Elastic IP address\. Choose **Associate**\.

### View your Elastic IP addresses<a name="view-eip"></a>

You can view the Elastic IP addresses that are allocated to your account\.

**To view your Elastic IP addresses**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. To filter the displayed list, start typing part of the Elastic IP address or one of its attributes in the search box\.

### Tag an Elastic IP address<a name="tag-eip"></a>

You can apply tags to your Elastic IP address to help you identify it or categorize it according to your organization's needs\.

**To tag an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address and choose **Tags**\.

1. Choose **Manage tags**, enter the tag keys and values as required, and choose **Save**\.

### Disassociate an Elastic IP address<a name="disassociate-eip"></a>

To change the resource that the Elastic IP address is associated with, you must first disassociate it from the currently associated resource\.

**To disassociate an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, and then choose **Actions**, **Disassociate Elastic IP address**\.

1. When prompted, choose **Disassociate**\.

### Transfer Elastic IP addresses<a name="transfer-EIPs-intro"></a>

This section describes how to transfer Elastic IP addresses from one AWS account to another\. Transferring Elastic IP addresses can be helpful in the following situations:
+ **Organizational restructuring** – Use Elastic IP address transfers to quickly move workloads from one AWS account to another\. You don't have to wait for new Elastic IP addresses to be allowlisted in your security groups and NACLs\.
+ **Centralized security administration** – Use a centralized AWS security account to track and transfer Elastic IP addresses that have been vetted for security compliance\.
+ **Disaster recovery** – Use Elastic IP address transfers to quickly remap IPs for public\-facing internet workloads during emergency events\.

There is no charge for transferring Elastic IP addresses\.

**Topics**
+ [Enable Elastic IP address transfer](#using-instance-addressing-eips-transfer-enable)
+ [Disable Elastic IP address transfer](#using-instance-addressing-eips-transfer-disable)
+ [Accept a transferred Elastic IP address](#using-instance-addressing-eips-transfer-accept)

#### Enable Elastic IP address transfer<a name="using-instance-addressing-eips-transfer-enable"></a>

This section describes how to accept a transferred Elastic IP address\. Note the following limitations related to enabling Elastic IP addresses for transfer:
+ You can transfer Elastic IP addresses from any AWS account \(source account\) to any other AWS account in the same AWS Region \(transfer account\)\.
+ When you transfer an Elastic IP address, there is a two\-step handshake between the AWS accounts\. When the source account starts the transfer, the transfer accounts have seven hours to accept the Elastic IP address transfer, or the Elastic IP address is returned to its original owner\.
+ AWS does not notify transfer accounts about pending Elastic IP address transfer requests\. The owner of the source account must notify the owner of the transfer account that there is an Elastic IP address transfer request that they must accept\.
+ Any tags that are associated with an Elastic IP address being transferred are reset when the transfer is complete\.
+ You cannot transfer Elastic IP addresses allocated from public IPv4 address pools that you bring to your AWS account – commonly referred to as Bring Your Own IP \(BYOIP\) address pools\.
+ If you have enabled and configured AWS Outposts, you might have allocated Elastic IP addresses from a customer\-owned IP address pool \(CoIP\)\. You cannot transfer Elastic IP addresses allocated from a CoIP\. However, you can use AWS RAM to share a CoIP with another account\. For more information, see [Customer\-owned IP addresses](https://docs.aws.amazon.com/outposts/latest/userguide/routing.html#ip-addressing) in the *AWS Outposts User Guide*\.
+ You can use Amazon VPC IPAM to track the transfer of Elastic IP addresses to accounts in an organization from AWS Organizations\. For more information, see [View IP address history](https://docs.aws.amazon.com/vpc/latest/ipam/view-history-cidr-ipam.html)\. If an Elastic IP address is transferred to an AWS account outside of the organization, the IPAM audit history of the Elastic IP address is lost\.

These steps must be completed by the source account\.

**To enable Elastic IP address transfer**

1. Ensure that you're using the source AWS account\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select one or more Elastic IP address to enable for transfer and choose **Actions**, **Enable transfer**\.

1. If you are transferring multiple Elastic IP addresses, you’ll see the **Transfer type** option\. Choose one of the following options:
   + Choose **Single account** if you are transferring the Elastic IP addresses to a single AWS account\.
   + Choose **Multiple accounts** if you are transferring the Elastic IP addresses to multiple AWS accounts\.

1. Under **Transfer account ID**, enter the IDs of the AWS accounts that you want to transfer the Elastic IP addresses to\.

1. Confirm the transfer by entering **enable** in the text box\.

1. Choose **Submit**\.

1. To accept the transfer, see [Accept a transferred Elastic IP address](#using-instance-addressing-eips-transfer-accept)\. To disable the transfer, see [Disable Elastic IP address transfer](#using-instance-addressing-eips-transfer-disable)\.

#### Disable Elastic IP address transfer<a name="using-instance-addressing-eips-transfer-disable"></a>

This section describes how to disable an Elastic IP transfer after the transfer has been enabled\.

These steps must be completed by the source account that enabled the transfer\.

**To disable an Elastic IP address transfer**

1. Ensure that you're using the source AWS account\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. In the resource list of Elastic IPs, ensure that you have the property enabled that shows the column **Transfer status**\.

1. Select one or more Elastic IP address that have a **Transfer status** of **Pending**, and choose **Actions**, **Disable transfer**\.

1. Confirm by entering **disable** in the text box\.

1. Choose **Submit**\.

#### Accept a transferred Elastic IP address<a name="using-instance-addressing-eips-transfer-accept"></a>

This section describes how to accept a transferred Elastic IP address\.

When you transfer an Elastic IP address, there is a two\-step handshake between AWS accounts: the source account \(either a standard AWS account or an AWS Organizations account\) and the transfer accounts\. When the source account starts the transfer, the transfer accounts have seven hours to accept the Elastic IP address transfer, or the Elastic IP address will return to its original owner\. 

When accepting transfers, note the following exceptions that might occur and how to resolve them:
+ **AddressLimitExceeded**: If your transfer account has exceeded the Elastic IP address quota, the source account can enable Elastic IP address transfer, but this exception occurs when the transfer account tries to accept the transfer\. By default, all AWS accounts are limited to 5 Elastic IP addresses per Region\. See [Elastic IP address limit](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-limit) in the *Amazon EC2 User Guide for Linux Instances* for instructions on increasing the limit\.
+ **InvalidTransfer\.AddressCustomPtrSet**: If you or someone in your organization has configured the Elastic IP address that you are attempting to transfer to use reverse DNS lookup, the source account can enable transfer for the Elastic IP address, but this exception occurs when the transfer account tries to accept the transfer\. To resolve this issue, the source account must remove the DNS record for the Elastic IP address\. For more information, see [Remove a reverse DNS record](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#Using_Elastic_Addressing_Reverse_DNS) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **InvalidTransfer\.AddressAssociated**: If an Elastic IP address is associated with an ENI or EC2 instance, the source account can enable transfer for the Elastic IP address, but this exception occurs when the transfer account tries to accept the transfer\. To resolve this issue, the source account must disassociate the Elastic IP address\. For more information, see [Disassociate an Elastic IP address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-eips-associating-different) in the *Amazon EC2 User Guide for Linux Instances*\.

For any other exceptions, [contact AWS Support](https://aws.amazon.com/contact-us/)\.

These steps must be completed by the transfer account\.

**To accept an Elastic IP address transfer**

1. Ensure that you're using the transfer account\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Actions**, **Accept transfer**\.

1. No tags that are associated with the Elastic IP address being transferred are transferred with the Elastic IP address when you accept the transfer\. If you want to define a **Name** tag for the Elastic IP address that you are accepting, select **Create a tag with a key of 'Name' and a value that you specify**\.

1. Enter the Elastic IP address that you want to transfer\.

1. If you are accepting multiple transferred Elastic IP addresses, choose **Add address** to enter an additional Elastic IP address\.

1. Choose **Submit**\.

### Release an Elastic IP address<a name="release-eip"></a>

If you no longer need an Elastic IP address, we recommend that you release it\. You incur charges for any Elastic IP address that's allocated for use with a VPC but that's not associated with an instance\. The Elastic IP address must not be associated with an instance or network interface\.

**To release an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, and then choose **Actions**, **Release Elastic IP addresses**\.

1. When prompted, choose **Release**\.

### Recover an Elastic IP address<a name="recover-eip"></a>

If you release an Elastic IP address but change your mind, you might be able to recover it\. You cannot recover the Elastic IP address if it has been allocated to another AWS account, or if recovering it results in you exceeding your Elastic IP address quota\.

You can recover an Elastic IP address by using the Amazon EC2 API or a command line tool\.

**To recover an Elastic IP address using the AWS CLI**  
Use the [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) command and specify the IP address using the `--address` parameter\.

```
aws ec2 allocate-address --domain vpc --address 203.0.113.3
```

### API and command overview<a name="eip-api-cli"></a>

You can perform the tasks described in this section using the command line or an API\. For more information about the command line interfaces and a list of available API actions, see [Working with Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Accept Elastic IP address transfer**
+ [accept\-address\-transfer](https://docs.aws.amazon.com/cli/latest/reference/ec2/accept-address-transfer.html) \(AWS CLI\)
+ [Approve\-EC2AddressTransfer](https://docs.aws.amazon.com/powershell/latest/reference/items/Approve-EC2AddressTransfer.html) \(AWS Tools for Windows PowerShell\)

**Allocate an Elastic IP address**
+ [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) \(AWS CLI\)
+ [New\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**Associate an Elastic IP address with an instance or network interface**
+ [associate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)
+ [Register\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**Describe Elastic IP address transfers**
+ [describe\-address\-transfers](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-address-transfers.html) \(AWS CLI\)
+ [Get\-EC2AddressTransfer](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2AddressTransfer.html) \(AWS Tools for Windows PowerShell\)

**Disable Elastic IP address transfer**
+ [disable\-address\-transfer](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-address-transfer.html) \(AWS CLI\)
+ [Disable\-EC2AddressTransfer](https://docs.aws.amazon.com/powershell/latest/reference/items/Disable-EC2AddressTransfer.html) \(AWS Tools for Windows PowerShell\)

**Disassociate an Elastic IP address**
+ [disassociate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-address.html) \(AWS CLI\)
+ [Unregister\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**Enable Elastic IP address transfer**
+ [enable\-address\-transfer](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-address-transfer.html) \(AWS CLI\)
+ [Enable\-EC2AddressTransfer](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2AddressTransfer.html) \(AWS Tools for Windows PowerShell\)

**Release an Elastic IP address**
+ [release\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/release-address.html) \(AWS CLI\)
+ [Remove\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**Tag an Elastic IP address**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

**View your Elastic IP addresses**
+ [describe\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-addresses.html) \(AWS CLI\)
+ [Get\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) \(AWS Tools for Windows PowerShell\)