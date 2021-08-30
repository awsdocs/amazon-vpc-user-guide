# DHCP options sets for your VPC<a name="VPC_DHCP_Options"></a>

The Dynamic Host Configuration Protocol \(DHCP\) provides a standard for passing configuration information to hosts on a TCP/IP network\. The `options` field of a DHCP message contains configuration parameters, including the domain name, domain name server, and the netbios\-node\-type\.

When you create a VPC, we automatically create a set of DHCP options and associate them with the VPC\. You can configure your own DHCP options set for your VPC\.

**Topics**
+ [Overview of DHCP options sets](#DHCPOptionSets)
+ [Amazon DNS server](#AmazonDNS)
+ [Change DHCP options](#DHCPOptions)
+ [Work with DHCP options sets](#DHCPOptionSet)
+ [API and command overview](#APIOverview)

## Overview of DHCP options sets<a name="DHCPOptionSets"></a>

By default, all instances in a nondefault VPC receive an unresolvable host name that AWS assigns \(for example, ip\-10\-0\-0\-202\)\. You can assign your own domain name to your instances, and use up to four of your own DNS servers\. To do that, you must create a custom set of DHCP options to use with the VPC\. 

The following are the supported options for a DHCP options set, and the value that is provided in the default DHCP options set for your VPC\. You can specify only the options that you need in your DHCP options set\. For more information about the options, see [RFC 2132](https://tools.ietf.org/html/rfc2132)\.

**domain\-name\-servers**  
The IP addresses of up to four domain name servers, or [AmazonProvidedDNS](#AmazonDNS)\. To specify more than one domain name server, separate them with commas\. Although you can specify up to four domain name servers, some operating systems might impose lower limits\.  
To use this option, set it to either AmazonProvidedDNS or custom domain name servers\. Using both might cause unexpected behavior\.  
Default DHCP options set: AmazonProvidedDNS

**domain\-name**  
The custom domain name for your instances\. If you are not using AmazonProvidedDNS, your custom domain name servers must resolve the hostname as appropriate\. If you use a Amazon Route 53 private hosted zone, you can use AmazonProvidedDNS\. For more information, see [DNS support for your VPC](vpc-dns.md)\.  
Some Linux operating systems accept multiple domain names separated by spaces\. However, other Linux operating systems and Windows treat the value as a single domain, which results in unexpected behavior\. If your DHCP options set is associated with a VPC that contains instances that are not all running the same operating systems, specify only one domain name\.  
Default DHCP options set: For `us-east-1`, the value is `ec2.internal`\. For other Regions, the value is *region*\.compute\.internal \(for example, `ap-northeast-1.compute.internal`\)\. To use the default values, set `domain-name-servers` to AmazonProvidedDNS\.

**ntp\-servers**  
The IP addresses of up to four Network Time Protocol \(NTP\) servers\. For more information, see section 8\.3 of [RFC 2132](https://tools.ietf.org/html/rfc2132)\. You can specify the Amazon Time Sync Service at IPv4 address `169.254.169.123` or IPv6 address `fd00:ec2::123`\. The IPv6 address is only accessible on [EC2 instances built on the Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances)\. For more information, see [Set the time for your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-time.html) in the *Amazon EC2 User Guide for Linux Instances*\.  
Default DHCP options set: None

**netbios\-name\-servers**  
The IP addresses of up to four NetBIOS name servers\.  
Default DHCP options set: None

**netbios\-node\-type**  
The NetBIOS node type \(1, 2, 4, or 8\)\. We recommend that you specify 2 \(point\-to\-point, or P\-node\)\. Broadcast and multicast are not currently supported\. For more information about these node types, see section 8\.7 of [RFC 2132](https://tools.ietf.org/html/rfc2132) and section 10 of [RFC1001](https://tools.ietf.org/html/rfc1001)\.  
Default DHCP options set: None

## Amazon DNS server<a name="AmazonDNS"></a>

The default DHCP options set for your VPC includes two options: `domain-name-servers=AmazonProvidedDNS`, and `domain-name=`*domain\-name\-for\-your\-region*\. AmazonProvidedDNS is an Amazon Route 53 Resolver server, and this option enables DNS for instances that need to communicate over the VPC's internet gateway\. The string `AmazonProvidedDNS` maps to a DNS server running on a reserved IP address at the base of the VPC IPv4 network range, plus two\. For example, the DNS Server on a 10\.0\.0\.0/16 network is located at 10\.0\.0\.2\. For VPCs with multiple IPv4 CIDR blocks, the DNS server IP address is located in the primary CIDR block\. The DNS server does not reside within a specific subnet or Availability Zone in a VPC\. 

When you launch an instance into a VPC, we provide the instance with a private DNS hostname, and a public DNS hostname if the instance receives a public IPv4 address\. If `domain-name-servers` in your DHCP options is set to AmazonProvidedDNS, the public DNS hostname takes the form `ec2-public-ipv4-address.compute-1.amazonaws.com` for the us\-east\-1 Region, and `ec2-public-ipv4-address.region.compute.amazonaws.com` for other Regions\. The private hostname takes the form `ip-private-ipv4-address.ec2.internal` for the us\-east\-1 Region, and `ip-private-ipv4-address.region.compute.internal` for other Regions\. To change these to custom DNS hostnames, you must set `domain-name-servers` to a custom DNS server\.

The Amazon DNS server in your VPC is used to resolve the DNS domain names that you specify in a private hosted zone in Route 53\. For more information about private hosted zones, see [Working with private hosted zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html) in the *Amazon Route 53 Developer Guide*\.

### Rules and considerations<a name="amazon-dns-rules"></a>

When using the Amazon DNS server, the following rules and considerations apply\.
+ You cannot filter traffic to or from the Amazon DNS server using network ACLs or security groups\.
+ Services that use the Hadoop framework, such as Amazon EMR, require instances to resolve their own fully qualified domain names \(FQDN\)\. In such cases, DNS resolution can fail if the `domain-name-servers` option is set to a custom value\. To ensure proper DNS resolution, consider adding a conditional forwarder on your DNS server to forward queries for the domain `region-name.compute.internal` to the Amazon DNS server\. For more information, see [Setting up a VPC to host clusters](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-vpc-host-job-flows.html) in the *Amazon EMR Management Guide*\.
+ Windows Server 2008 disallows the use of a DNS server located in the link\-local address range \(169\.254\.0\.0/16\)\.
+ The Amazon Route 53 Resolver only supports recursive DNS queries\.

## Change DHCP options<a name="DHCPOptions"></a>

After you create a set of DHCP options, you can't modify them\. If you need your VPC to use a different set of DHCP options, you must create it and then associate it with your VPC\. Alternatively, you can specify that your VPC should use no DHCP options\.

You can have multiple sets of DHCP options, but you can associate only one set of DHCP options with a VPC at a time\. If you delete a VPC, the DHCP options set that is associated with the VPC is disassociated from the VPC\.

After you associate a new set of DHCP options with a VPC, any existing instances and all new instances that you launch in the VPC use the new options\. You don't need to restart or relaunch your instances\. Instances automatically pick up the changes within a few hours, depending on how frequently they renew their DHCP leases\. If you prefer, you can explicitly renew the lease using the operating system on the instance\. 

## Work with DHCP options sets<a name="DHCPOptionSet"></a>

This section shows you how to work with DHCP options sets\.

**Topics**
+ [Create a DHCP options set](#CreatingaDHCPOptionSet)
+ [Change the set of DHCP options that a VPC uses](#ChangingDHCPOptionsofaVPC)
+ [Change a VPC to use no DHCP options](#DHCP_Use_No_Options)
+ [Modify the tags of a DHCP options set](#TaggingaDHCPOptionSet)
+ [Delete a DHCP options set](#DeletingaDHCPOptionSet)

### Create a DHCP options set<a name="CreatingaDHCPOptionSet"></a>

You can create as many additional DHCP options sets as you want\. However, you can only associate a VPC with one set of DHCP options at a time\. After you create a set of DHCP options, you must configure your VPC to use it\. For more information, see [Change the set of DHCP options that a VPC uses](#ChangingDHCPOptionsofaVPC)\.

**To create a DHCP options set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Options Sets**\.

1. Choose **Create DHCP options set**\.

1. For **Tag settings**, optionally enter a name for the DHCP options set\. This creates a Name tag for the DHCP options set\.

1. For **DHCP options**, provide the configuration parameters that you need\.
**Important**  
If your VPC has an internet gateway, make sure to specify your own DNS server or Amazon's DNS server \(AmazonProvidedDNS\) for the **Domain name servers** value\. Otherwise, the instances that need to communicate with the internet won't have access to DNS\.

1. For **Tags**, optionally add or remove a tag\.
   + \[Add a tag\] Choose **Add new tag** and enter the key name and key value\.
   + \[Remove a tag\] Choose **Remove** next to the tag\.

1. Choose **Create DHCP options set**\.

1. Make a note of the ID of the new set of DHCP options \(dopt\-*xxxxxxxx*\)\. You will need this ID to associate the new set of options with your VPC\.

Now that you've created a set of DHCP options, you must associate it with your VPC for the options to take effect\. You can create multiple sets of DHCP options, but you can associate only one set of DHCP options with your VPC at a time\.

### Change the set of DHCP options that a VPC uses<a name="ChangingDHCPOptionsofaVPC"></a>

You can change which set of DHCP options your VPC uses\. After you associate a new set of DHCP options with the VPC, any existing instances and all new instances that you launch in that VPC use the new options\. You don't need to restart or relaunch your instances\. Instances automatically pick up the changes within a few hours, depending on how frequently they renew their DHCP leases\. If you prefer, you can explicitly renew the lease using the operating system on the instance\. 

If you do not want your VPC to use DHCP options, see [Change a VPC to use no DHCP options](#DHCP_Use_No_Options)\.

**Note**  
The following procedure assumes that you've already created the DHCP options set\. Otherwise, create the options set as described in the previous section\.

**To change the DHCP options set associated with a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the checkbox for the VPC, and then choose **Actions**, **Edit DHCP options set**\.

1. For **DHCP options set**, choose the DHCP options set\.

1. Choose **Save changes**\.

### Change a VPC to use no DHCP options<a name="DHCP_Use_No_Options"></a>

You can set up your VPC so that it does not use a set of DHCP options\. You don't need to restart or relaunch your instances\. Instances automatically pick up the changes within a few hours, depending on how frequently they renew their DHCP leases\. If you prefer, you can explicitly renew the lease using the operating system on the instance\. 

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the checkbox for the VPC, and then choose **Actions**, **Edit DHCP options set**\.

1. For **DHCP options set**, choose **No DHCP options set**\.

1. Choose **Save changes**\.

### Modify the tags of a DHCP options set<a name="TaggingaDHCPOptionSet"></a>

You can use tags to easily identify your options set\.

**To modify the tags of a DHCP options set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP options sets**\.

1. Select the radio button for the DHCP options set, and then choose **Actions**, **Manage tags**\.

1. For **Tags**, add or remove tags as needed\.
   + \[Add a tag\] Choose **Add new tag** and enter the key name and key value\.
   + \[Remove a tag\] Choose **Remove** next to the tag\.

1. Choose **Save**\.

### Delete a DHCP options set<a name="DeletingaDHCPOptionSet"></a>

When you no longer need a DHCP options set, use the following procedure to delete it\. Make sure that you change the VPCs that use these options to another option set, or no options, For more information, see [Change the set of DHCP options that a VPC uses](#ChangingDHCPOptionsofaVPC) and [Change a VPC to use no DHCP options](#DHCP_Use_No_Options) \.

**To delete a DHCP options set**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Options Sets**\.

1. Select the radio button for the DHCP options set, and then choose **Actions**, **Delete DHCP options set**\.

1. When prompted for confirmation, enter **delete**, and then choose **Delete DHCP options set**\.

## API and command overview<a name="APIOverview"></a>

You can perform the tasks described in this topic using the command line or an API\. For more information about the command line interfaces and a list of available APIs, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create a set of DHCP options for your VPC**
+ [create\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-dhcp-options.html) \(AWS CLI\)
+ [New\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

**Associate a set of DHCP options with the specified VPC, or no DHCP options**
+ [associate\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-dhcp-options.html) \(AWS CLI\)
+ [Register\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

**Describe one or more sets of DHCP options**
+ [describe\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-dhcp-options.html) \(AWS CLI\)
+ [Get\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)

**Delete a set of DHCP options**
+ [delete\-dhcp\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-dhcp-options.html) \(AWS CLI\)
+ [Remove\-EC2DhcpOption](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2DhcpOption.html) \(AWS Tools for Windows PowerShell\)