# Work with DHCP options sets<a name="DHCPOptionSet"></a>

This section shows you how to view and work with DHCP options sets\.

EC2 instances launched into VPC subnets automatically use the default DHCP options unless you create a new DHCP options set or disassociate the default options set from your VPC\. A custom DHCP options set enables you to customize your VPC with your own DNS server, domain name, and more\. Disassociating the default options set from your VPC enables you to disable domain name resolution in your VPC\.

**Topics**
+ [View your default options set](#ViewDefaultDHCPOptionSet)
+ [Create a DHCP options set](#CreatingaDHCPOptionSet)
+ [Change the options set associated with a VPC](#ChangingDHCPOptionsofaVPC)
+ [Delete a DHCP options set](#DeletingaDHCPOptionSet)

## View your default options set<a name="ViewDefaultDHCPOptionSet"></a>

**To View the default DHCP options set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Options Sets**\.

1. Choose the options set that's available\.

1. View the configurations in the default options set:
   + **Domain name servers**: The DNS servers that will be used to resolve the IP address of the host\. In the default options set, the only value is **AmazonProvidedDNS**\. For more information about this server, see [Amazon DNS server](vpc-dns.md#AmazonDNS)\.
   + **Domain name**: The domain name that will be appended to the public IPv4 addresses of EC2 instances launched into the VPC\.

     In the default options set, for VPCs in AWS Region `us-east-1`, the value is `ec2.internal`\. For VPCs in other Regions, the value is *region*\.compute\.internal \(for example, `ap-northeast-1.compute.internal`\)\.
   + **NTP servers**: The NTP servers that provide the time to your network\. In the default options set, there is no value for NTP servers\. Your default DHCP options set does not include an NTP server because EC2 instances use the Amazon Time Sync Service by default to retrieve the time\.
   + **NetBIOS name servers**: For Windows EC2 instances, the NetBIOS computer name is a friendly name assigned to the instance to identify it on the network\. The NetBIOS name server maintains a list of mappings between NetBIOS computer names and network addresses for networks that use NetBIOS as their naming service\. In the default options set, there is no value for NetBIOS name servers\.
   + **NetBIOS node type**: For Windows EC2 instances, this is the method that the instances use to resolve NetBIOS names to IP addresses\. In the default options set, there is no value for NetBIOS node type\.

**Describe one or more sets of DHCP options using the AWS CLI or API**

For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [describe\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-dhcp-options.html) \(AWS CLI\)
+ [Get\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

## Create a DHCP options set<a name="CreatingaDHCPOptionSet"></a>

A custom DHCP options set enables you to customize your VPC with your own DNS server, domain name, and more\.

By default, all instances in a nondefault VPC receive an unresolvable host name that AWS assigns \(for example, ip\-10\-0\-0\-202\)\. You can assign your own domain name to your instances and your own DNS servers\. To do that, you must create a custom set of DHCP options to use with the VPC\. 

You can create as many additional DHCP options sets as you want\. However, you can only associate a VPC with one set of DHCP options at a time\.

**To create a DHCP options set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Options Sets**\.

1. Choose **Create DHCP options set**\.

1. For **Tag settings**, optionally enter a name for the DHCP options set\. This creates a Name tag for the DHCP options set\.

1. For **DHCP options**, provide the configuration parameters that you need\.
**Important**  
If your VPC has an internet gateway, make sure to specify your own DNS server or Amazon's DNS server \(AmazonProvidedDNS\) for the **Domain name servers** value\. Otherwise, the instances that need to communicate with the internet won't have access to DNS\.
**Note**  
While all settings are optional you cannot create an options set without defining at least one setting\.
   + **Domain name servers** \(optional\): The DNS servers that will be used to resolve the IP address of a host from the host's name\.

     Enter either AmazonProvidedDNS or custom domain name servers\. Using both might cause unexpected behavior\. You can enter the IP addresses of up to four IPv4 domain name servers \(or up to three IPv4 domain name servers and **AmazonProvidedDNS**\) and four IPv6 domain name servers separated by commas\. Although you can specify up to eight domain name servers, some operating systems might impose lower limits\. For more information about the Amazon DNS server, see [Amazon DNS server](vpc-dns.md#AmazonDNS)\.
   + **Domain name** \(optional\): The domain name that will be appended to the public IPv4 addresses of EC2 instances launched into the VPC\.

     If you are not using AmazonProvidedDNS, your custom domain name servers must resolve the hostname as appropriate\. If you use a Amazon Route 53 private hosted zone, you can use AmazonProvidedDNS\. For more information, see [DNS attributes for your VPC](vpc-dns.md)\.

     Some Linux operating systems accept multiple domain names separated by spaces\. However, other Linux operating systems and Windows treat the value as a single domain, which results in unexpected behavior\. If your DHCP options set is associated with a VPC that contains instances that are not all running the same operating systems, specify only one domain name\.
   + **NTP servers** \(optional\): The NTP servers that provide the time to your network\.

     Enter the IP addresses of up to eight Network Time Protocol \(NTP\) servers \(four IPv4 addresses and four IPv6 addresses\)\.

     You can specify the Amazon Time Sync Service at IPv4 address `169.254.169.123` or IPv6 address `fd00:ec2::123`\. The IPv6 address is only accessible on [EC2 instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances)\.

     For more information about the NTP servers option, see [RFC 2132](https://datatracker.ietf.org/doc/html/rfc2132#section-8.3)\. For more information about the Amazon Time Sync Service, see [Set the time for your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-time.html) in the *Amazon EC2 User Guide for Linux Instances*\.
   + **NetBIOS name servers** \(optional\): Enter the IP addresses of up to four NetBIOS name servers\.

     For Windows EC2 instances, the NetBIOS computer name is a friendly name assigned to the instance to identify it on the network\. The NetBIOS name server maintains a list of mappings between NetBIOS computer names and network addresses for networks that use NetBIOS as their naming service\.
   + **NetBIOS node type**: For Windows EC2 instances, this is the method that the instances use to resolve NetBIOS names to IP addresses\. In the default options set, there is no value for NetBIOS node type\.

     The NetBIOS node type options are 1, 2, 4, or 8\. We recommend that you specify 2 \(point\-to\-point or P\-node\)\. Broadcast and multicast are not currently supported\. For more information about these node types, see section 8\.7 of [RFC 2132](https://tools.ietf.org/html/rfc2132) and section 10 of [RFC1001](https://tools.ietf.org/html/rfc1001)\.

1. Add **Tags**\.

1. Choose **Create DHCP options set**\.

1. Note the ID of the new set of DHCP options \(dopt\-*xxxxxxxx*\)\. You will need this ID in the next step\.

1. Configure your VPC to use the new options set\. For more information, see [Change the options set associated with a VPC](#ChangingDHCPOptionsofaVPC)\.

After you create a set of DHCP options, you can't modify them\. If you need your VPC to use a different set of DHCP options, you must create a new options set and then associate it with your VPC\. Alternatively, you can specify that your VPC should use the default options set or no DHCP options\.

**Create a set of DHCP options for your VPC using the AWS CLI or API**

For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [create\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-dhcp-options.html) \(AWS CLI\)
+ [New\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

## Change the options set associated with a VPC<a name="ChangingDHCPOptionsofaVPC"></a>

Follow the steps in this section to change which set of DHCP options your VPC uses\. You can assign a new DHCP options set to the VPC or you can disassociate a DHCP options set from your VPC\. You may want to disassociate any options sets to disable domain name resolution in your VPC\.

**Note**  
The following procedure assumes that you've already created the DHCP options set\. Otherwise, create the options set as described in the previous section\.
If you associate a new set of DHCP options with the VPC, any existing instances and all new instances that you launch in that VPC use the new options\. You don't need to restart or relaunch your instances\. Instances automatically pick up the changes within a few hours, depending on how frequently they renew their DHCP leases\. If you prefer, you can explicitly renew the lease using the operating system on the instance\. 
You can have multiple sets of DHCP options, but you can associate only one set of DHCP options with a VPC at a time\. If you delete a VPC, the DHCP options set that is associated with the VPC is disassociated from the VPC\.

**To change the DHCP options set associated with a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the checkbox for the VPC, and then choose **Actions**, **Edit DHCP options set**\.

1. For **DHCP options set**, choose a new DHCP options set or choose **No DHCP options set** to not use a DHCP options set in your VPC\.

1. Choose **Save changes**\.

**Associate a set of DHCP options with the specified VPC or no DHCP options using the AWS CLI or API**

For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [associate\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-dhcp-options.html) \(AWS CLI\)
+ [Register\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

## Delete a DHCP options set<a name="DeletingaDHCPOptionSet"></a>

When you no longer need a DHCP options set, use the following procedure to delete it\. Make sure that you change the VPCs that use these options to another option set or no options set before you delete the options set\. For more information, see [Change the options set associated with a VPC](#ChangingDHCPOptionsofaVPC)\.

**To delete a DHCP options set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Options Sets**\.

1. Select the radio button for the DHCP options set, and then choose **Actions**, **Delete DHCP options set**\.

1. When prompted for confirmation, enter **delete**, and then choose **Delete DHCP options set**\.

**Delete a DHCP options set using the AWS CLI or API**

For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [delete\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-dhcp-options.html) \(AWS CLI\)
+ [Remove\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)