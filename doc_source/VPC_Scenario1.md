# Scenario 1: VPC with a Single Public Subnet<a name="VPC_Scenario1"></a>

The configuration for this scenario includes a virtual private cloud \(VPC\) with a single public subnet, and an Internet gateway to enable communication over the Internet\. We recommend this configuration if you need to run a single\-tier, public\-facing web application, such as a blog or a simple website\.

This scenario can also be optionally configured for IPv6—you can use the VPC wizard to create a VPC and subnet with associated IPv6 CIDR blocks\. Instances launched into the public subnet can receive IPv6 addresses, and communicate using IPv6\. For more information about IPv4 and IPv6 addressing, see [IP Addressing in Your VPC](vpc-ip-addressing.md)\.

**Topics**
+ [Overview](#Configuration)
+ [Routing](#VPC_Scenario1_Routing)
+ [Security](#VPC_Scenario1_Security)
+ [Implementing Scenario 1](#VPC_Scenario1_Implementation)

## Overview<a name="Configuration"></a>

The following diagram shows the key components of the configuration for this scenario\.

![\[Diagram for scenario 1: VPC with a public subnet\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/Case1_Diagram.png)

**Note**  
If you completed [Getting Started with Amazon VPC](vpc-getting-started.md), then you've already implemented this scenario using the VPC wizard in the Amazon VPC console\.

The configuration for this scenario includes the following:
+ A virtual private cloud \(VPC\) with a size /16 IPv4 CIDR block \(example: 10\.0\.0\.0/16\)\. This provides 65,536 private IPv4 addresses\.
+ A subnet with a size /24 IPv4 CIDR block \(example: 10\.0\.0\.0/24\)\. This provides 256 private IPv4 addresses\.
+ An Internet gateway\. This connects the VPC to the Internet and to other AWS services\.
+ An instance with a private IPv4 address in the subnet range \(example: 10\.0\.0\.6\), which enables the instance to communicate with other instances in the VPC, and an Elastic IPv4 address \(example: 198\.51\.100\.2\), which is a public IPv4 address that enables the instance to be reached from the Internet\.
+ A custom route table associated with the subnet\. The route table entries enable instances in the subnet to use IPv4 to communicate with other instances in the VPC, and to communicate directly over the Internet\. A subnet that's associated with a route table that has a route to an Internet gateway is known as a *public subnet*\.

For more information about subnets, see [VPCs and Subnets](VPC_Subnets.md)\. For more information about Internet gateways, see [Internet Gateways](VPC_Internet_Gateway.md)\.

### Overview for IPv6<a name="vpc-scenario-1-overview-ipv6"></a>

You can optionally enable IPv6 for this scenario\. In addition to the components listed above, the configuration includes the following:
+ A size /56 IPv6 CIDR block associated with the VPC \(example: 2001:db8:1234:1a00::/56\)\. Amazon automatically assigns the CIDR; you cannot choose the range yourself\.
+ A size /64 IPv6 CIDR block associated with the public subnet \(example: 2001:db8:1234:1a00::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the subnet IPv6 CIDR block\.
+ An IPv6 address assigned to the instance from the subnet range \(example: 2001:db8:1234:1a00::123\)\.
+ Route table entries in the custom route table that enable instances in the VPC to use IPv6 to communicate with each other, and directly over the Internet\.

![\[IPv6-enabled VPC with a public subnet\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/scenario-1-ipv6-diagram.png)

## Routing<a name="VPC_Scenario1_Routing"></a>

Your VPC has an implied router \(shown in the configuration diagram above\)\. In this scenario, the VPC wizard creates a custom route table that routes all traffic destined for an address outside the VPC to the Internet gateway, and associates this route table with the subnet\. 

The following table shows the route table for the example in the configuration diagram above\. The first entry is the default entry for local IPv4 routing in the VPC; this entry enables the instances in this VPC to communicate with each other\. The second entry routes all other IPv4 subnet traffic to the Internet gateway \(for example, `igw-1a2b3c4d`\)\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  0\.0\.0\.0/0  |  *igw\-id*  | 

### Routing for IPv6<a name="vpc-scenario-1-routing-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnet, your route table must include separate routes for IPv6 traffic\. The following table shows the custom route table for this scenario if you choose to enable IPv6 communication in your VPC\. The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. The fourth entry routes all other IPv6 subnet traffic to the Internet gateway\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *igw\-id*  | 
|  ::/0  | igw\-id | 

## Security<a name="VPC_Scenario1_Security"></a>

AWS provides two features that you can use to increase security in your VPC: *security groups* and *network ACLs*\. Security groups control inbound and outbound traffic for your instances, and network ACLs control inbound and outbound traffic for your subnets\. In most cases, security groups can meet your needs; however, you can also use network ACLs if you want an additional layer of security for your VPC\. For more information, see [Security](VPC_Security.md)\. 

For this scenario, you use a security group but not a network ACL\. If you'd like to use a network ACL, see [Recommended Rules for Scenario 1](vpc-recommended-nacl-rules.md#nacl-rules-scenario-1)\.

Your VPC comes with a [default security group](VPC_SecurityGroups.md#DefaultSecurityGroup)\. An instance that's launched into the VPC is automatically associated with the default security group if you don't specify a different security group during launch\. You can add rules to the default security group, but the rules may not be suitable for other instances that you launch into the VPC\. Instead, we recommend that you create a custom security group for your web server\.

For this scenario, create a security group named `WebServerSG`\. When you create a security group, it has a single outbound rule that allows all traffic to leave the instances\. You must modify the rules to enable inbound traffic and restrict the outbound traffic as needed\. You specify this security group when you launch instances into the VPC\. 

The following are the inbound and outbound rules for IPv4 traffic for the WebServerSG security group\. 


|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow inbound HTTP access to the web servers from any IPv4 address\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow inbound HTTPS access to the web servers from any IPv4 address  | 
|  Public IPv4 address range of your network   |  TCP  |  22  |  \(Linux instances\) Allow inbound SSH access from your network over IPv4\. You can get the public IPv4 address of your local computer using a service such as [http://checkip\.amazonaws\.com](http://checkip.amazonaws.com) or [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com)\. If you are connecting through an ISP or from behind your firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.   | 
|  Public IPv4 address range of your network   |  TCP  |  3389  |  \(Windows instances\) Allow inbound RDP access from your network over IPv4\.  | 
| The security group ID \(sg\-xxxxxxxx\) | All | All | \(Optional\) Allow inbound traffic from other instances associated with this security group\. This rule is automatically added to the default security group for the VPC; for any custom security group you create, you must manually add the rule to allow this type of communication\. | 
| Outbound \(Optional\) | 
| Destination | Protocol | Port Range | Comments | 
| 0\.0\.0\.0/0 | All | All | Default rule to allow all outbound access to any IPv4 address\. If you want your web server to initiate outbound traffic, for example, to get software updates, you can keep the default outbound rule\. Otherwise, you can remove this rule\. | 

### Security for IPv6<a name="vpc-scenario-1-security-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnet, you must add separate rules to your security group to control inbound and outbound IPv6 traffic for your web server instance\. In this scenario, the web server will be able to receive all Internet traffic over IPv6, and SSH or RDP traffic from your local network over IPv6\. 

The following are the IPv6\-specific rules for the WebServerSG security group \(which are in addition to the rules listed above\)\.


|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  ::/0  |  TCP  |  80  |  Allow inbound HTTP access to the web servers from any IPv6 address\.  | 
|  ::/0  |  TCP  |  443  |  Allow inbound HTTPS access to the web servers from any IPv6 address\.  | 
|  IPv6 address range of your network   |  TCP  |  22  |  \(Linux instances\) Allow inbound SSH access over IPv6 from your network\.   | 
|  IPv6 address range of your network   |  TCP  |  3389  |  \(Windows instances\) Allow inbound RDP access over IPv6 from your network  | 
| Outbound \(Optional\) | 
| Destination | Protocol | Port Range | Comments | 
| ::/0 | All | All | Default rule to allow all outbound access to any IPv6 address\. If you want your web server to initiate outbound traffic, for example, to get software updates, you can keep the default outbound rule\. Otherwise, you can remove this rule\. | 

## Implementing Scenario 1<a name="VPC_Scenario1_Implementation"></a>

To implement scenario 1, create a VPC using the VPC wizard, create and configure the WebServerSG security group, and then launch an instance into your VPC\.

These procedures include optional steps for enabling and configuring IPv6 communication for your VPC\. You do not have to perform these steps if you do not want to use IPv6 in your VPC\.

**To create a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the dashboard, choose **Launch VPC Wizard**\.  
![\[The Amazon VPC dashboard\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/VPC-dashboard.png)

1. Choose the first option, **VPC with a Single Public Subnet**, and then choose **Select**\.

1. \(Optional\) You can name your VPC and subnet to help you to identify them later in the console\. You can specify your own IPv4 CIDR block ranges for the VPC and subnet, or you can keep the default values \(10\.0\.0\.0/16 and 10\.0\.0\.0/24 respectively\)\.

1. \(Optional, IPv6\-only\) For **IPv6 CIDR block**, choose **Amazon\-provided IPv6 CIDR block**\. For **Public subnet's IPv6 CIDR**, choose **Specify a custom IPv6 CIDR** and specify the hexadecimal pair value for your subnet, or keep the default value \(`00`\)\.

1. Keep the rest of the default settings, and choose **Create VPC**\.<a name="vpc-scenario-1-create-web-server-group"></a>

**To create the WebServerSG security group**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Provide a name and description for the security group\. In this topic, the name `WebServerSG` is used as an example\. Select the ID of your VPC from the **VPC** menu, and then choose **Yes, Create**\.

1. Select the WebServerSG security group that you just created\. The details pane include a tab for information about the security group, plus tabs for working with its inbound rules and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit**, and then do the following:
   + Select **HTTP** from the **Type** list, and enter `0.0.0.0/0` in the **Source** field\.
   + Choose **Add another rule**, then select **HTTPS** from the **Type** list, and enter `0.0.0.0/0` in the **Source** field\.
   + Choose **Add another rule**, then select **SSH** \(for Linux\) or **RDP** \(for Windows\) from the **Type** list\. Enter your network's public IP address range in the **Source** field\. \(If you don't know this address range, you can use `0.0.0.0/0` for testing purposes; in production, you authorize only a specific IP address or range of addresses to access your instance\.\)
   + \(Optional\) Choose **Add another rule**, then select **ALL traffic** from the **Type** list\. In the **Source** field, enter the ID of the WebServerSG security group\.
   + \(Optional, IPv6\-only\) Choose **Add another rule**, select **HTTP** from the **Type** list, and enter `::/0` in the **Source** field\.
   + \(Optional, IPv6\-only\) Choose **Add another rule**, select **HTTPS** from the **Type** list, and enter `::/0` in the **Source** field\.
   + \(Optional, IPv6\-only\) Choose **Add another rule**, select **SSH** \(for Linux\) or **RDP** \(for Windows\) from the **Type** list\. Enter your network's IPv6 address range in the **Source** field\. \(If you don't know this address range, you can use `::/0` for testing purposes; in production, you authorize only a specific IPv6 address or range of addresses to access your instance\.\)

1. Choose **Save**\.

1. \(Optional\) On the **Outbound Rules** tab, choose **Edit**\. Locate the default rule that enables all outbound traffic, choose **Remove**, and then choose **Save**\.<a name="vpc-scenario-1-launch-instance"></a>

**To launch an instance into the VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the dashboard, choose **Launch Instance**\.

1. Follow the directions in the wizard\. Choose an AMI, choose an instance type, and then choose **Next: Configure Instance Details**\.
**Note**  
If you intend to use your instance for IPv6 communication, you must choose a supported instance type; for example, T2\. For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

1. On the **Configure Instance Details** page, select the VPC that you created in step 1 from the **Network** list, and then specify a subnet\.

1. \(Optional\) By default, instances launched into a nondefault VPC are not assigned a public IPv4 address\. To be able to connect to your instance, you can assign a public IPv4 address now, or allocate an Elastic IP address and assign it to your instance after it's launched\. To assign a public IPv4 address now, ensure that you select **Enable** from the **Auto\-assign Public IP** list\. 
**Note**  
You can only use the auto\-assign public IP feature for a single, new network interface with the device index of eth0\. For more information, see [Assigning a Public IPv4 Address During Instance Launch](vpc-ip-addressing.md#vpc-public-ip)\.

1. \(Optional, IPv6\-only\) You can auto\-assign an IPv6 address to your instance from the subnet range\. For **Auto\-assign IPv6 IP**, choose **Enable**\.

1. On the next two pages of the wizard, you can configure storage for your instance, and add tags\. On the **Configure Security Group** page, select the **Select an existing security group** option, and select the **WebServerSG** security group that you created in step 2\. Choose **Review and Launch**\.

1. Review the settings that you've chosen\. Make any changes that you need, and then choose **Launch** to choose a key pair and launch your instance\.

1. If you did not assign a public IPv4 address to your instance in step 5, you will not be able to connect to it over IPv4\. Assign an Elastic IP address to the instance:

   1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

   1. In the navigation pane, choose **Elastic IPs**\.

   1. Choose **Allocate new address**\.

   1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC**\.

   1. Select the Elastic IP address from the list, choose **Actions**, and then choose **Associate address**\.

   1. Select the instance to associate the address with, and then choose **Associate**\.

You can now connect to your instances in the VPC\. For information about how to connect to a Linux instance, see [Connect to Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#EC2_ConnectToInstance_Linux) in the *Amazon EC2 User Guide for Linux Instances*\. For information about how to connect to a Windows instance, see [Connect to Your Windows Instance](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2Win_GetStarted.html#EC2Win_ConnectToInstanceWindows) in the *Amazon EC2 User Guide for Windows Instances*\.