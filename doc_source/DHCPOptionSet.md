# Work with DHCP option sets<a name="DHCPOptionSet"></a>

This section shows you how to view and work with DHCP option sets\.

EC2 instances launched into VPC subnets automatically use the default DHCP options unless you create a new DHCP option set or you disassociate the default option set from your VPC\. A custom DHCP option set enables you to customize your VPC with your own DNS server, domain name, and more\. Disassociating the default option set from your VPC enables you to disable domain name resolution in your VPC\.

**Topics**
+ [View your default option set](#ViewDefaultDHCPOptionSet)
+ [Create a DHCP option set](#CreatingaDHCPOptionSet)
+ [Change the option set associated with a VPC](#ChangingDHCPOptionsofaVPC)
+ [Delete a DHCP option set](#DeletingaDHCPOptionSet)

## View your default option set<a name="ViewDefaultDHCPOptionSet"></a>

**To View the default DHCP option set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Option Sets**\.

1. Choose the option set that's available\.

1. View the configurations in the default option set:
   + **Domain name servers**: The DNS servers that will be used to resolve the IP address of the host\. In the default option set, the only value is **AmazonProvidedDNS**\. The string `AmazonProvidedDNS` maps to Amazon's DNS server\. For more information about this server, see [Amazon DNS server](vpc-dns.md#AmazonDNS)\.
   + **Domain name**: The domain name that a client should use when resolving hostnames via the Domain Name System\.

     In the default option set, for VPCs in AWS Region `us-east-1`, the value is `ec2.internal`\. For VPCs in other Regions, the value is *region*\.compute\.internal \(for example, `ap-northeast-1.compute.internal`\)\.
   + **NTP servers**: The NTP servers that provide the time to your network\. In the default option set, there is no value for NTP servers\. Your default DHCP option set does not include an NTP server because EC2 instances use the Amazon Time Sync Service by default to retrieve the time\.
   + **NetBIOS name servers**: For EC2 instances running a Windows OS, the NetBIOS computer name is a friendly name assigned to the instance to identify it on the network\. The NetBIOS name server maintains a list of mappings between NetBIOS computer names and network addresses for networks that use NetBIOS as their naming service\. In the default option set, there are no NetBIOS name servers defined\.
   + **NetBIOS node type**: For EC2 instances running a Windows OS, this is the method that the instances use to resolve NetBIOS names to IP addresses\. In the default option set, there are no NetBIOS node types set\.

**Describe one or more sets of DHCP options using the AWS CLI or API**

For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [describe\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-dhcp-options.html) \(AWS CLI\)
+ [Get\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

## Create a DHCP option set<a name="CreatingaDHCPOptionSet"></a>

A custom DHCP option set enables you to customize your VPC with your own DNS server, domain name, and more\. You can create as many additional DHCP option sets as you want\. However, you can only associate a VPC with one set of DHCP options at a time\.

**Note**  
After you create a DHCP option set, you can't modify it\. If you need your VPC to use a different set of DHCP options, you must create a new option set and then associate it with your VPC\. Alternatively, you can specify that your VPC should use the default option set or no DHCP options\.

**To create a DHCP options set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Option Sets**\.

1. Choose **Create DHCP options set**\.

1. For **Tag settings**, optionally enter a name for the DHCP option set\. If you enter a value, it automatically creates a Name tag for the DHCP option set\.

1. For **DHCP options**, provide the configuration parameters that you need\.
   + **Domain name** \(optional\): Enter the domain name that a client should use when resolving hostnames via the Domain Name System\. If you are not using AmazonProvidedDNS, your custom domain name servers must resolve the hostname as appropriate\. If you use an Amazon Route 53 private hosted zone, you can use AmazonProvidedDNS\. For more information, see [DNS attributes for your VPC](vpc-dns.md)\.

     Some Linux operating systems accept multiple domain names separated by spaces\. However, other Linux operating systems and Windows treat the value as a single domain, which results in unexpected behavior\. If your DHCP option set is associated with a VPC that contains instances that are not all running the same operating systems, specify only one domain name\.
   + **Domain name servers** \(optional\): Enter the DNS servers that will be used to resolve the IP address of a host from the host's name\.

     You can enter either **AmazonProvidedDNS** or custom domain name servers\. Using both might cause unexpected behavior\. You can enter the IP addresses of up to four IPv4 domain name servers \(or up to three IPv4 domain name servers and **AmazonProvidedDNS**\) and four IPv6 domain name servers separated by commas\. Although you can specify up to eight domain name servers, some operating systems might impose lower limits\. For more information about **AmazonProvidedDNS** and the Amazon DNS server, see [Amazon DNS server](vpc-dns.md#AmazonDNS)\.
**Important**  
If your VPC has an internet gateway, make sure to specify your own DNS server or an Amazon DNS server \(AmazonProvidedDNS\) for the **Domain name servers** value\. Otherwise, the instances that need to communicate with the internet won't have access to DNS\.
   + **NTP servers** \(optional\): Enter the IP addresses of up to eight Network Time Protocol \(NTP\) servers \(four IPv4 addresses and four IPv6 addresses\)\.

      NTP servers provide the time to your network\. You can specify the Amazon Time Sync Service at IPv4 address `169.254.169.123` or IPv6 address `fd00:ec2::123`\. Instances communicate with the Amazon Time Sync Service by default\. Note that the IPv6 address is only accessible on [EC2 instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances)\.

     For more information about the NTP servers option, see [RFC 2132](https://datatracker.ietf.org/doc/html/rfc2132#section-8.3)\. For more information about the Amazon Time Sync Service, see [Set the time for your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-time.html) in the *Amazon EC2 User Guide for Linux Instances*\.
   + **NetBIOS name servers** \(optional\): Enter the IP addresses of up to four NetBIOS name servers\.

     For EC2 instances running a Windows OS, the NetBIOS computer name is a friendly name assigned to the instance to identify it on the network\. The NetBIOS name server maintains a list of mappings between NetBIOS computer names and network addresses for networks that use NetBIOS as their naming service\.
   + **NetBIOS node type** \(optional\): Enter **1**, **2**, **4**, or **8**\. We recommend that you specify **2** \(point\-to\-point or P\-node\)\. Broadcast and multicast are not currently supported\. For more information about these node types, see section 8\.7 of [RFC 2132](https://tools.ietf.org/html/rfc2132) and section 10 of [RFC1001](https://tools.ietf.org/html/rfc1001)\.

     For EC2 instances running a Windows OS, this is the method that the instances use to resolve NetBIOS names to IP addresses\. In the default options set, there is no value for NetBIOS node type\.

     

1. Add **Tags**\.

1. Choose **Create DHCP options set**\.

1. Note the ID of the new set of DHCP options \(dopt\-*xxxxxxxx*\)\. You will need this ID in the next step\.

1. Configure your VPC to use the new option set\. For more information, see [Change the option set associated with a VPC](#ChangingDHCPOptionsofaVPC)\.

**Note**  
After you create a DHCP option set, you can't modify it\. If you need your VPC to use a different set of DHCP options, you must create a new option set and then associate it with your VPC\. Alternatively, you can specify that your VPC should use the default option set or no DHCP options\.

**Create a set of DHCP options for your VPC using the AWS CLI or API**

For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [create\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-dhcp-options.html) \(AWS CLI\)
+ [New\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

## Change the option set associated with a VPC<a name="ChangingDHCPOptionsofaVPC"></a>

Follow the steps in this section to change which set of DHCP options your VPC uses\. You can assign a new DHCP option set to the VPC, or you can disassociate a DHCP option set from your VPC\. You might want to disassociate any option sets to disable domain name resolution in your VPC\.

**Note**  
The following procedure assumes that you've already created the DHCP option set\. Otherwise, create the option set as described in the previous section\.
If you associate a new set of DHCP options with the VPC, any existing instances and all new instances that you launch in that VPC use the new options\. You don't need to restart or relaunch your instances\. Instances automatically pick up the changes within a few hours, depending on how frequently they renew their DHCP leases\. If you prefer, you can explicitly renew the lease using the operating system on the instance\. 
You can have multiple sets of DHCP options, but you can associate only one set of DHCP options with a VPC at a time\. If you delete a VPC, the DHCP option set that is associated with the VPC is disassociated from the VPC\.

**To change the DHCP option set associated with a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the check box for the VPC, and then choose **Actions**, **Edit DHCP options set**\.

1. For **DHCP options set**, choose a new DHCP option set\. Alternatively, choose **No DHCP options set** to not use a DHCP option set in your VPC\.

1. Choose **Save changes**\.

**Associate a set of DHCP options with the specified VPC or no DHCP options using the AWS CLI or API**

For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [associate\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-dhcp-options.html) \(AWS CLI\)
+ [Register\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

## Delete a DHCP option set<a name="DeletingaDHCPOptionSet"></a>

When you no longer need a DHCP option set, use the following procedure to delete it\. Make sure that you change the VPCs that use these options to another option set or to no option set before you delete the option set\. You cannot delete an option set if it's associated with a VPC\. For more information, see [Change the option set associated with a VPC](#ChangingDHCPOptionsofaVPC)\.

**To delete a DHCP option set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Option Sets**\.

1. Select the radio button for the DHCP option set, and then choose **Actions**, **Delete DHCP options set**\.

1. When prompted for confirmation, enter **delete**, and then choose **Delete DHCP options set**\.

**Delete a DHCP option set using the AWS CLI or API**

For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [delete\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-dhcp-options.html) \(AWS CLI\)
+ [Remove\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)