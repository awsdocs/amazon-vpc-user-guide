# Scenario 4: VPC with a Private Subnet Only and AWS Managed VPN Access<a name="VPC_Scenario4"></a>

The configuration for this scenario includes a virtual private cloud \(VPC\) with a single private subnet, and a virtual private gateway to enable communication with your own network over an IPsec VPN tunnel\. There is no Internet gateway to enable communication over the Internet\. We recommend this scenario if you want to extend your network into [the cloud](https://aws.amazon.com/what-is-cloud-computing/) using Amazon's infrastructure without exposing your network to the Internet\.

This scenario can also be optionally configured for IPv6—you can use the VPC wizard to create a VPC and subnet with associated IPv6 CIDR blocks\. Instances launched into the subnet can receive IPv6 addresses\. Currently, we do not support IPv6 communication over a VPN connection; however, instances in the VPC can communicate with each other via IPv6\. For more information about IPv4 and IPv6 addressing, see [IP Addressing in Your VPC](vpc-ip-addressing.md)\.

**Topics**
+ [Overview](#Configuration-4)
+ [Routing](#VPC_Scenario4_Routing)
+ [Security](#VPC_Scenario4_Security)
+ [Implementing Scenario 4](#VPC_Scenario4_Implementation)

## Overview<a name="Configuration-4"></a>

The following diagram shows the key components of the configuration for this scenario\.

![\[Diagram for scenario 4: VPC with only a virtual private gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/Case4_Diagram.png)

**Important**  
For this scenario, the [Amazon VPC Network Administrator Guide](http://docs.aws.amazon.com/vpc/latest/adminguide/) describes what your network administrator needs to do to configure the Amazon VPC customer gateway on your side of the VPN connection\.

The configuration for this scenario includes the following:
+ A virtual private cloud \(VPC\) with a size /16 CIDR \(example: 10\.0\.0\.0/16\)\. This provides 65,536 private IP addresses\.
+ A VPN\-only subnet with a size /24 CIDR \(example: 10\.0\.0\.0/24\)\. This provides 256 private IP addresses\.
+ A VPN connection between your VPC and your network\. The VPN connection consists of a virtual private gateway located on the Amazon side of the VPN connection and a customer gateway located on your side of the VPN connection\.
+ Instances with private IP addresses in the subnet range \(examples: 10\.0\.0\.5, 10\.0\.0\.6, and 10\.0\.0\.7\), which enables the instances to communicate with each other and other instances in the VPC\.
+ The main route table contains a route that enables instances in the subnet to communicate with other instances in the VPC\. Route propagation is enabled, so a route that enables instances in the subnet to communicate directly with your network appears as a propagated route in the main route table\.

For more information about subnets, see [VPCs and Subnets](VPC_Subnets.md) and [IP Addressing in Your VPC](vpc-ip-addressing.md)\. For more information about your VPN connection, see [AWS Managed VPN Connections](VPC_VPN.md)\. For more information about configuring a customer gateway, see the * [Amazon VPC Network Administrator Guide](http://docs.aws.amazon.com/vpc/latest/adminguide/)*\.

### Overview for IPv6<a name="vpc-scenario-4-overview-ipv6"></a>

You can optionally enable IPv6 for this scenario\. In addition to the components listed above, the configuration includes the following:
+ A size /56 IPv6 CIDR block associated with the VPC \(example: 2001:db8:1234:1a00::/56\)\. AWS automatically assigns the CIDR; you cannot choose the range yourself\.
+ A size /64 IPv6 CIDR block associated with the VPN\-only subnet \(example: 2001:db8:1234:1a00::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the IPv6 CIDR\.
+ IPv6 addresses assigned to the instances from the subnet range \(example: 2001:db8:1234:1a00::1a\)\.
+ A route table entry in the main route table that enables instances in the private subnet to use IPv6 to communicate with each other\.

![\[IPv6-enabled VPC with a VPN-only subnet\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/scenario-4-ipv6-diagram.png)

## Routing<a name="VPC_Scenario4_Routing"></a>

Your VPC has an implied router \(shown in the configuration diagram for this scenario\)\. In this scenario, the VPC wizard creates a route table that routes all traffic destined for an address outside the VPC to the VPN connection, and associates the route table with the subnet\. 

The following describes the route table for this scenario\. The first entry is the default entry for local routing in the VPC; this entry enables the instances in this VPC to communicate with each other\. The second entry routes all other subnet traffic to the virtual private gateway \(for example, `vgw-1a2b3c4d`\)\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  0\.0\.0\.0/0  |  *vgw\-id*  | 

The VPN connection is configured either as a statically\-routed VPN connection or as a dynamically routed VPN connection \(using BGP\)\. If you select static routing, you'll be prompted to manually enter the IP prefix for your network when you create the VPN connection\. If you select dynamic routing, the IP prefix is advertised automatically to your VPC through BGP\.

The instances in your VPC can't reach the Internet directly; any Internet\-bound traffic must first traverse the virtual private gateway to your network, where the traffic is then subject to your firewall and corporate security policies\. If the instances send any AWS\-bound traffic \(for example, requests to Amazon S3 or Amazon EC2\), the requests must go over the virtual private gateway to your network and then to the Internet before reaching AWS\. Currently, we do not support IPv6 for VPN connections\.

### Routing for IPv6<a name="vpc-scenario-4-routing-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, your route table includes separate routes for IPv6 traffic\. The following describes the custom route table for this scenario\. The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. 


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *vgw\-id*  | 

## Security<a name="VPC_Scenario4_Security"></a>

AWS provides two features that you can use to increase security in your VPC: *security groups* and *network ACLs*\. Security groups control inbound and outbound traffic for your instances, and network ACLs control inbound and outbound traffic for your subnets\. In most cases, security groups can meet your needs; however, you can also use network ACLs if you want an additional layer of security for your VPC\. For more information, see [Security](VPC_Security.md)\. 

For scenario 4, you'll use the default security group for your VPC but not a network ACL\. If you'd like to use a network ACL, see [Recommended Rules for Scenario 4](vpc-recommended-nacl-rules.md#nacl-rules-scenario-4)\.

Your VPC comes with a default security group whose initial settings deny all inbound traffic, allow all outbound traffic, and allow all traffic between the instances assigned to the security group\. For this scenario, we recommend that you add inbound rules to the default security group to allow SSH traffic \(Linux\) and Remote Desktop traffic \(Windows\) from your network\.

**Important**  
The default security group automatically allows assigned instances to communicate with each other, so you don't have to add a rule to allow this\. If you use a different security group, you must add a rule to allow this\.

The following table describes the inbound rules that you should add to the default security group for your VPC\.


**Default Security Group: Recommended Rules**  

|  | 
| --- |
|  Inbound  | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  Private IPv4 address range of your network  |  TCP  |  22  |  \(Linux instances\) Allow inbound SSH traffic from your network\.  | 
|  Private IPv4 address range of your network  |  TCP  |  3389  |  \(Windows instances\) Allow inbound RDP traffic from your network\.  | 

### Security for IPv6<a name="vpc-scenario-4-security-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, you must add separate rules to your security group to control inbound and outbound IPv6 traffic for your instances\. In this scenario, the database servers cannot be reached over the VPN connection using IPv6; therefore, no additional security group rules are required\.

## Implementing Scenario 4<a name="VPC_Scenario4_Implementation"></a>

To implement scenario 4, get information about your customer gateway, and create the VPC using the VPC wizard, The VPC wizard creates a VPN connection for you with a customer gateway and virtual private gateway\.

**To prepare your customer gateway**

1. Determine the device you'll use as your customer gateway\. For information about the devices that we've tested, see [Amazon Virtual Private Cloud FAQs](http://aws.amazon.com/vpc/faqs/#C9)\. For more information about the requirements for your customer gateway, see the *[Amazon VPC Network Administrator Guide](http://docs.aws.amazon.com/vpc/latest/adminguide/)*\.

1. Obtain the Internet\-routable IP address for the customer gateway's external interface\. The address must be static and may be behind a device performing network address translation \(NAT\)\.

1. If you want to create a statically\-routed VPN connection, get the list of internal IP ranges \(in CIDR notation\) that should be advertised across the VPN connection to the virtual private gateway\. For more information, see [VPN Routing Options](VPC_VPN.md#VPNRoutingTypes)\.

Use the VPC wizard to create your VPC and a VPN connection\.

**To create a VPC using the VPC wizard**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the dashboard, choose **Launch VPC Wizard**\.  
![\[The Amazon VPC dashboard\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/VPC-dashboard.png)

1. Choose the fourth option, **VPC with a Private Subnet Only and Hardware VPN Access**, and then choose **Select**\.

1. On the **VPC with a Private Subnet Only and Hardware VPN Access** page, do the following:

   1. \(Optional\) Modify the IPv4 CIDR block ranges for the VPC and subnet as needed, or keep the default values\.

   1. \(Optional\) Name your VPC and subnet\. This helps you identify them later in the console\.

   1. \(Optional, IPv6\-only\) For **IPv6 CIDR block**, choose **Amazon\-provided IPv6 CIDR block**\. For **Private subnet's IPv6 CIDR**, choose **Specify a custom IPv6 CIDR**\. Specify the hexadecimal pair value for the IPv6 subnet or keep the default value \(`00`\)\.

   1. Choose **Next**\.

1. On the **Configure your VPN** page, do the following:

   1. For **Customer Gateway IP**, specify the public IP address of your VPN router\.

   1. \(Optional\) Name your customer gateway and VPN connection\.

   1. For **Routing Type**, select one of the [routing options](VPC_VPN.md#VPNRoutingTypes):
      + If your VPN router supports Border Gateway Protocol \(BGP\), select **Dynamic \(requires BGP\)**\.
      + If your VPN router does not support BGP, choose **Static**\. For **IP Prefix**, add each IP range for your network, in CIDR notation\.

   1. Choose **Create VPC**\.

1. When the wizard is done, choose **VPN Connections** in the navigation pane\. Select the VPN connection that the wizard created, and choose **Download Configuration**\. In the dialog box, select the vendor for the customer gateway, the platform, and the software version, and then choose **Yes, Download**\.

1. Save the text file containing the VPN configuration and give it to the network administrator along with this guide: [Amazon VPC Network Administrator Guide](http://docs.aws.amazon.com/vpc/latest/adminguide/)\. The VPN won't work until the network administrator configures the customer gateway\.

For this scenario, you need to update the default security group with new inbound rules that allow SSH and Remote Desktop \(RDP\) access from your network\. If you don't want instances to initiate outbound communication, you can also remove the default outbound rule\. 

**To update the rules for the default security group**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Security Groups** in the navigation pane, and then select the default security group for the VPC\. The details pane displays the details for the security group, plus tabs for working with its inbound and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit** and add rules for inbound traffic as follows:

   1. Select **SSH** from **Type**, and type your network's private IP address range in **Source** \(for example, `172.0.0.0/8`\)\.

   1. Choose **Add another rule**, then select **RDP** from **Type**, and type your network's private IP address range in **Source**\.

   1. Choose **Save**\.

1. \(Optional\) On the **Outbound Rules** tab, choose **Edit**, locate the default rule that enables all outbound traffic, choose **Remove**, and then choose **Save**\.

After your network administrator configures your customer gateway, you can launch instances into your VPC\. If you're already familiar with launching instances outside a VPC, then you already know most of what you need to know to launch an instance into a VPC\.

**To launch an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance** on the dashboard\.

1. Follow the directions in the wizard\. Choose an AMI, choose an instance type, and then choose **Next: Configure Instance Details**\.
**Note**  
If you intend to use your instance for IPv6 communication, you must choose a supported instance type; for example, T2\. For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

1. On the **Configure Instance Details** page, select the VPC that you created earlier from the **Network** list, and then select the subnet\. Choose **Next: Add Storage**\.

1. On the next two pages of the wizard, you can configure storage for your instance, and add tags\. On the **Configure Security Group** page, select the **Select an existing security group** option, and select the default security group\. Choose **Review and Launch**\.

1. Review the settings that you've chosen\. Make any changes that you need, and then choose **Launch** to choose a keypair and launch your instance\.

In scenario 4, you need a DNS server that enables your VPN\-only subnet to communicate with servers in your network\. You must create a new set of DHCP options that includes your DNS server and then configure the VPC to use that set of options\.

**Note**  
Your VPC automatically has a set of DHCP options with domain\-name\-servers=AmazonProvidedDNS\. This is a DNS server that Amazon provides to enable any public subnets in your VPC to communicate with the Internet over an Internet gateway\. Scenario 4 doesn't have any public subnets, so you don't need this set of DHCP options\.

**To update the DHCP options**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Options Sets**\.

1. Choose **Create DHCP Options Set**\.

1. In the **Create DHCP Options Set** dialog box, in the **Domain name servers** box, enter the address of your DNS server, and then choose **Yes, Create**\. In this example, your DNS server is 192\.0\.2\.1\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, and then choose **Edit** in the **Summary** tab\.

1. Select the ID of the new set of options from the **DHCP options set** list and then choose **Save**\.

1. \(Optional\) The VPC now uses this new set of DHCP options and therefore uses your DNS server\. If you want, you can delete the original set of options that the VPC used\.

You can now use SSH or RDP to connect to your instance in the VPC\. For information about how to connect to a Linux instance, see [Connect to Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#EC2_ConnectToInstance_Linux) in the *Amazon EC2 User Guide for Linux Instances*\. For information about how to connect to a Windows instance, see [Connect to Your Windows Instance](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2Win_GetStarted.html#EC2Win_ConnectToInstanceWindows) in the *Amazon EC2 User Guide for Windows Instances*\. 