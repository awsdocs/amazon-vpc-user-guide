# Scenario 2: VPC with Public and Private Subnets \(NAT\)<a name="VPC_Scenario2"></a>

The configuration for this scenario includes a virtual private cloud \(VPC\) with a public subnet and a private subnet\. We recommend this scenario if you want to run a public\-facing web application, while maintaining back\-end servers that aren't publicly accessible\. A common example is a multi\-tier website, with the web servers in a public subnet and the database servers in a private subnet\. You can set up security and routing so that the web servers can communicate with the database servers\.

The instances in the public subnet can send outbound traffic directly to the Internet, whereas the instances in the private subnet can't\. Instead, the instances in the private subnet can access the Internet by using a network address translation \(NAT\) gateway that resides in the public subnet\. The database servers can connect to the Internet for software updates using the NAT gateway, but the Internet cannot establish connections to the database servers\.

**Note**  
You can also use the VPC wizard to configure a VPC with a NAT instance; however, we recommend that you use a NAT gateway\. For more information, see [NAT Gateways](vpc-nat-gateway.md)\.

This topic assumes that you'll use the VPC wizard in the Amazon VPC console to create the VPC and NAT gateway\.

This scenario can also be optionally configured for IPv6—you can use the VPC wizard to create a VPC and subnets with associated IPv6 CIDR blocks\. Instances launched into the subnets can receive IPv6 addresses, and communicate using IPv6\. Instances in the private subnet can use an egress\-only Internet gateway to connect to the Internet over IPv6, but the Internet cannot establish connections to the private instances over IPv6\. For more information about IPv4 and IPv6 addressing, see [IP Addressing in Your VPC](vpc-ip-addressing.md)\.


+ [Overview](#Configuration-2)
+ [Routing](#VPC_Scenario2_Routing)
+ [Security](#VPC_Scenario2_Security)
+ [Implementing Scenario 2](#VPC_Scenario2_Implementation)
+ [Implementing Scenario 2 with a NAT Instance](#vpc-scenario-2-nat-instance)

## Overview<a name="Configuration-2"></a>

The following diagram shows the key components of the configuration for this scenario\.

![\[Diagram for scenario 2: VPC with public and private subnets\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/nat-gateway-diagram.png)

The configuration for this scenario includes the following:

+ A VPC with a size /16 IPv4 CIDR block \(example: 10\.0\.0\.0/16\)\. This provides 65,536 private IPv4 addresses\.

+ A public subnet with a size /24 IPv4 CIDR block \(example: 10\.0\.0\.0/24\)\. This provides 256 private IPv4 addresses\. A public subnet is a subnet that's associated with a route table that has a route to an Internet gateway\.

+ A private subnet with a size /24 IPv4 CIDR block \(example: 10\.0\.1\.0/24\)\. This provides 256 private IPv4 addresses\.

+ An Internet gateway\. This connects the VPC to the Internet and to other AWS services\.

+ Instances with private IPv4 addresses in the subnet range \(examples: 10\.0\.0\.5, 10\.0\.1\.5\)\. This enables them to communicate with each other and other instances in the VPC\. 

+ Instances in the public subnet with Elastic IPv4 addresses \(example: 198\.51\.100\.1\), which are public IPv4 addresses that enable them to be reached from the Internet\. The instances can have public IP addresses assigned at launch instead of Elastic IP addresses\. Instances in the private subnet are back\-end servers that don't need to accept incoming traffic from the Internet and therefore do not have public IP addresses; however, they can send requests to the Internet using the NAT gateway \(see the next bullet\)\. 

+ A NAT gateway with its own Elastic IPv4 address\. Instances in the private subnet can send requests to the Internet through the NAT gateway over IPv4 \(for example, for software updates\)\.

+ A custom route table associated with the public subnet\. This route table contains an entry that enables instances in the subnet to communicate with other instances in the VPC over IPv4, and an entry that enables instances in the subnet to communicate directly with the Internet over IPv4\.

+ The main route table associated with the private subnet\. The route table contains an entry that enables instances in the subnet to communicate with other instances in the VPC over IPv4, and an entry that enables instances in the subnet to communicate with the Internet through the NAT gateway over IPv4\.

For more information about subnets, see [VPCs and Subnets](VPC_Subnets.md)\. For more information about Internet gateways, see [Internet Gateways](VPC_Internet_Gateway.md)\. For more information about NAT gateways, see [NAT Gateways](vpc-nat-gateway.md)\.

### Overview for IPv6<a name="vpc-scenario-2-overview-ipv6"></a>

You can optionally enable IPv6 for this scenario\. In addition to the components listed above, the configuration includes the following:

+ A size /56 IPv6 CIDR block associated with the VPC \(example: 2001:db8:1234:1a00::/56\)\. Amazon automatically assigns the CIDR; you cannot choose the range yourself\.

+ A size /64 IPv6 CIDR block associated with the public subnet \(example: 2001:db8:1234:1a00::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the VPC IPv6 CIDR block\.

+ A size /64 IPv6 CIDR block associated with the private subnet \(example: 2001:db8:1234:1a01::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the subnet IPv6 CIDR block\.

+ IPv6 addresses assigned to the instances from the subnet range \(example: 2001:db8:1234:1a00::1a\)\.

+ An egress\-only Internet gateway\. This enables instances in the private subnet to send requests to the Internet over IPv6 \(for example, for software updates\)\. An egress\-only Internet gateway is necessary if you want instances in the private subnet to be able to initiate communication with the Internet over IPv6\. For more information, see [Egress\-Only Internet Gateways](egress-only-internet-gateway.md)\.

+ Route table entries in the custom route table that enable instances in the public subnet to use IPv6 to communicate with each other, and directly over the Internet\.

+ Route table entries in the main route table that enable instances in the private subnet to use IPv6 to communicate with each other, and to communicate with the Internet through an egress\-only Internet gateway\.

![\[IPv6-enabled VPC with a public and private subnet\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/scenario-2-ipv6-diagram.png)

## Routing<a name="VPC_Scenario2_Routing"></a>

In this scenario, the VPC wizard updates the main route table used with the private subnet, and creates a custom route table and associates it with the public subnet\.

In this scenario, all traffic from each subnet that is bound for AWS \(for example, to the Amazon EC2 or Amazon S3 endpoints\) goes over the Internet gateway\. The database servers in the private subnet can't receive traffic from the Internet directly because they don't have Elastic IP addresses\. However, the database servers can send and receive Internet traffic through the NAT device in the public subnet\. 

Any additional subnets that you create use the main route table by default, which means that they are private subnets by default\. If you want to make a subnet public, you can always change the route table that it's associated with\.

The following tables describe the route tables for this scenario\.

### Main Route Table<a name="scenario-2-main-route-table"></a>

The first entry is the default entry for local routing in the VPC; this entry enables the instances in the VPC to communicate with each other\. The second entry sends all other subnet traffic to the NAT gateway \(for example, `nat-12345678901234567`\)\.


| Destination | Target | 
| --- | --- | 
|  `10.0.0.0/16`  |  local  | 
|  `0.0.0.0/0`  |  *nat\-gateway\-id*  | 

### Custom Route Table<a name="scenario-2-custom-route-table"></a>

The first entry is the default entry for local routing in the VPC; this entry enables the instances in this VPC to communicate with each other\. The second entry routes all other subnet traffic to the Internet over the Internet gateway \(for example, `igw-1a2b3d4d`\)\.


| Destination | Target | 
| --- | --- | 
|  `10.0.0.0/16`  |  local  | 
|  `0.0.0.0/0`  |  *igw\-id*  | 

### Routing for IPv6<a name="vpc-scenario-2-routing-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, your route tables must include separate routes for IPv6 traffic\. The following tables show the route tables for this scenario if you choose to enable IPv6 communication in your VPC\. 

**Main Route Table**

The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. The fourth entry routes all other IPv6 subnet traffic to the egress\-only Internet gateway\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *nat\-gateway\-id*  | 
|  ::/0  | egress\-only\-igw\-id | 

**Custom Route Table**

The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. The fourth entry routes all other IPv6 subnet traffic to the Internet gateway\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *igw\-id*  | 
|  ::/0  | igw\-id | 

## Security<a name="VPC_Scenario2_Security"></a>

AWS provides two features that you can use to increase security in your VPC: *security groups* and *network ACLs*\. Security groups control inbound and outbound traffic for your instances, and network ACLs control inbound and outbound traffic for your subnets\. In most cases, security groups can meet your needs; however, you can also use network ACLs if you want an additional layer of security for your VPC\. For more information, see [Security](VPC_Security.md)\. 

For scenario 2, you'll use security groups but not network ACLs\. If you'd like to use a network ACL, see [Recommended Rules for Scenario 2](VPC_Appendix_NACLs.md#VPC_Appendix_NACLs_Scenario_2)\.

Your VPC comes with a [default security group](VPC_SecurityGroups.md#DefaultSecurityGroup)\. An instance that's launched into the VPC is automatically associated with the default security group if you don't specify a different security group during launch\. For this scenario, we recommend that you create the following security groups instead of using the default security group:

+ **WebServerSG**: Specify this security group when you launch the web servers in the public subnet\.

+ **DBServerSG**: Specify this security group when you launch the database servers in the private subnet\.

The instances assigned to a security group can be in different subnets\. However, in this scenario, each security group corresponds to the type of role an instance plays, and each role requires the instance to be in a particular subnet\. Therefore, in this scenario, all instances assigned to a security group are in the same subnet\.

The following table describes the recommended rules for the WebServerSG security group, which allow the web servers to receive Internet traffic, as well as SSH and RDP traffic from your network\. The web servers can also initiate read and write requests to the database servers in the private subnet, and send traffic to the Internet; for example, to get software updates\. Because the web server doesn't initiate any other outbound communication, the default outbound rule is removed\.

**Note**  
These recommendations include both SSH and RDP access, and both Microsoft SQL Server and MySQL access\. For your situation, you might only need rules for Linux \(SSH and MySQL\) or Windows \(RDP and Microsoft SQL Server\)\.


**WebServerSG: Recommended Rules**  

|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow inbound HTTP access to the web servers from any IPv4 address\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow inbound HTTPS access to the web servers from any IPv4 address\.  | 
|  Your home network's public IPv4 address range  |  TCP  |  22  |  Allow inbound SSH access to Linux instances from your home network \(over the Internet gateway\)\. You can get the public IPv4 address of your local computer using a service such as [http://checkip\.amazonaws\.com](http://checkip.amazonaws.com) or [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com)\. If you are connecting through an ISP or from behind your firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.   | 
|  Your home network's public IPv4 address range  |  TCP  |  3389  |  Allow inbound RDP access to Windows instances from your home network \(over the Internet gateway\)\.  | 
|   **Outbound**   | 
|  Destination  |  Protocol  |  Port Range  |  Comments  | 
|  The ID of your DBServerSG security group  |  TCP  |  1433  |  Allow outbound Microsoft SQL Server access to the database servers assigned to the DBServerSG security group\.  | 
|  The ID of your DBServerSG security group  |  TCP  |  3306  |  Allow outbound MySQL access to the database servers assigned to the DBServerSG security group\.  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow outbound HTTP access to any IPv4 address\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow outbound HTTPS access to any IPv4 address\.  | 

The following table describes the recommended rules for the DBServerSG security group, which allow read or write database requests from the web servers\. The database servers can also initiate traffic bound for the Internet \(the route table sends that traffic to the NAT gateway, which then forwards it to the Internet over the Internet gateway\)\.


**DBServerSG: Recommended Rules**  

|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  The ID of your WebServerSG security group  |  TCP  |  1433  |  Allow inbound Microsoft SQL Server access from the web servers associated with the WebServerSG security group\.  | 
|  The ID of your WebServerSG security group  |  TCP  |  3306  |  Allow inbound MySQL Server access from the web servers associated with the WebServerSG security group\.  | 
|   **Outbound**   | 
|  Destination  |  Protocol  |  Port Range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow outbound HTTP access to the Internet over IPv4 \(for example, for software updates\)\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow outbound HTTPS access to the Internet over IPv4 \(for example, for software updates\)\.  | 

\(Optional\) The default security group for a VPC has rules that automatically allow assigned instances to communicate with each other\. To allow that type of communication for a custom security group, you must add the following rules:


|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  The ID of the security group  |  All  |  All  |  Allow inbound traffic from other instances assigned to this security group\.  | 
| Outbound | 
| Destination  |  Protocol  |  Port Range  |  Comments  | 
| The ID of the security group | All | All | Allow outbound traffic to other instances assigned to this security group\. | 

### Security for IPv6<a name="vpc-scenario-2-security-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, you must add separate rules to your WebServerSG and DBServerSG security groups to control inbound and outbound IPv6 traffic for your instances\. In this scenario, the web servers will be able to receive all Internet traffic over IPv6, and SSH or RDP traffic from your local network over IPv6\. They can also initiate outbound IPv6 traffic to the Internet\. The database servers can initiate outbound IPv6 traffic to the Internet\.

The following are the IPv6\-specific rules for the WebServerSG security group \(which are in addition to the rules listed above\)\.


|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  ::/0  |  TCP  |  80  |  Allow inbound HTTP access to the web servers from any IPv6 address\.  | 
|  ::/0  |  TCP  |  443  |  Allow inbound HTTPS access to the web servers from any IPv6 address\.  | 
|  IPv6 address range of your network   |  TCP  |  22  |  \(Linux instances\) Allow inbound SSH access over IPv6 from your network\.   | 
|  IPv6 address range of your network   |  TCP  |  3389  |  \(Windows instances\) Allow inbound RDP access over IPv6 from your network  | 
| Outbound | 
| Destination | Protocol | Port Range | Comments | 
| ::/0 | TCP | HTTP | Allow outbound HTTP access to any IPv6 address\. | 
| ::/0 | TCP | HTTPS | Allow outbound HTTPS access to any IPv6 address\. | 

The following are the IPv6\-specific rules for the DBServerSG security group \(which are in addition to the rules listed above\)\.


|  | 
| --- |
|   **Outbound**   | 
|  Destination  |  Protocol  |  Port Range  |  Comments  | 
|  ::/0  |  TCP  |  80  |  Allow outbound HTTP access to any IPv6 address\.  | 
|  ::/0  |  TCP  |  443  |  Allow outbound HTTPS access to any IPv6 address\.  | 

## Implementing Scenario 2<a name="VPC_Scenario2_Implementation"></a>

You can use the VPC wizard to create the VPC, subnets, NAT gateway, and optionally, an egress\-only Internet gateway\. You must specify an Elastic IP address for your NAT gateway; if you don't have one, you must first allocate one to your account\. If you want to use an existing Elastic IP address, ensure that it's not currently associated with another instance or network interface\. The NAT gateway is automatically created in the public subnet of your VPC\.

These procedures include optional steps for enabling and configuring IPv6 communication for your VPC\. You do not have to perform these steps if you do not want to use IPv6 in your VPC\.

**\(Optional\) To allocate an Elastic IP address for the NAT gateway \(IPv4\)**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate New Address**\.

1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC** from the **Network platform** list\.

**To create a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the VPC dashboard, choose **Start VPC Wizard**\.

1. Choose the second option, **VPC with Public and Private Subnets**, and **Select**\.

1. For **VPC name**, **Public subnet name** and **Private subnet name**, you can name your VPC and subnets to help you identify them later in the console\. You can specify your own IPv4 CIDR block range for the VPC and subnets, or you can leave the default values\. 

1. \(Optional, IPv6\-only\) For **IPv6 CIDR block**, choose **Amazon\-provided IPv6 CIDR block**\. For **Public subnet's IPv6 CIDR**, choose **Specify a custom IPv6 CIDR** and specify the hexadecimal pair value for your subnet, or leave the default value\. For **Private subnet's IPv6 CIDR**, choose **Specify a custom IPv6 CIDR**\. Specify the hexadecimal pair value for the IPv6 subnet or leave the default value\.

1. In the **Specify the details of your NAT gateway** section, specify the allocation ID for an Elastic IP address in your account\.

1. You can leave the rest of the default values on the page, and choose **Create VPC**\.

Because the WebServerSG and DBServerSG security groups reference each other, create all the security groups required for this scenario before you add rules to them\.

**To create the WebServerSG and DBServerSG security groups**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**, **Create Security Group**\.

1. Provide a name and description for the security group\. In this topic, the name `WebServerSG` is used as an example\. For **VPC**, select the ID of the VPC you created and choose **Yes, Create**\.

1. Choose **Create Security Group** again\.

1. Provide a name and description for the security group\. In this topic, the name `DBServerSG` is used as an example\. For **VPC**, select the ID of your VPC and choose **Yes, Create**\.

**To add rules to the WebServerSG security group**

1. Select the WebServerSG security group that you created\. The details pane displays the details for the security group, plus tabs for working with its inbound and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit** and add rules for inbound traffic as follows:

   1. Choose **Type**, **HTTP**\. For **Source**, enter `0.0.0.0/0`\.

   1. Choose **Add another rule**, **Type**, **HTTPS**\. For **Source**, enter `0.0.0.0/0`\.

   1. Choose **Add another rule**, **Type**, **SSH**\. For **Source**, enter your network's public IPv4 address range\. 

   1. Choose **Add another rule**, **Type**, **RDP**\. For **Source**, enter your network's public IPv4 address range\. 

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **HTTP**\. For **Source**, enter `::/0`\. 

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **HTTPS**\. For **Source**, enter `::/0`\. 

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **SSH** \(for Linux\) or **RDP** \(for Windows\)\. For **Source**, enter your network's IPv6 address range\. 

   1. Choose **Save**\.

1. On the **Outbound Rules** tab, choose **Edit** and add rules for outbound traffic as follows:

   1. Locate the default rule that enables all outbound traffic and choose **Remove**\. 

   1. Choose **Type**, **MS SQL**\. For **Destination**, specify the ID of the DBServerSG security group\.

   1. Choose **Add another rule**, **Type**, **MySQL**\. For **Destination**, specify the ID of the DBServerSG security group\.

   1. Choose **Add another rule**, **Type**, **HTTPS**\. For **Destination**, enter `0.0.0.0/0`\.

   1. Choose **Add another rule**, **Type**, **HTTP**\. For **Destination**, enter `0.0.0.0/0`\.

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **HTTPS**\. For **Destination**, enter `::/0`\.

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **HTTP**\. For **Destination**, enter `::/0`\.

   1. Choose **Save**\.

**To add the recommended rules to the DBServerSG security group**

1. Select the DBServerSG security group that you created\. The details pane displays the details for the security group, plus tabs for working with its inbound and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit** and add rules for inbound traffic as follows:

   1. Choose **Type**, **MS SQL**\. For **Source**, specify the ID of your WebServerSG security group\.

   1. Choose **Add another rule**, **Type**, **MYSQL**\. For **Source**, specify the ID of your WebServerSG security group\.

   1. Choose **Save**\.

1. On the **Outbound Rules** tab, choose **Edit** and add rules for outbound traffic as follows:

   1. Locate the default rule that enables all outbound traffic and choose **Remove**\. 

   1. Choose **Type**, **HTTP**\. For **Destination**, enter `0.0.0.0/0`\.

   1. Choose **Add another rule**, **Type**, **HTTPS**\. For **Destination**, enter `0.0.0.0/0`\.

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **HTTP**\. For **Destination**, enter `::/0`\.

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **HTTPS**\. For **Destination**, enter `::/0`\.

   1. Choose **Save**\.

You can now launch instances into your VPC\. 

**To launch an instance \(web server or database server\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the dashboard, choose **Launch Instance**\.

1. Select an AMI and an instance type and choose **Next: Configure Instance Details**\.
**Note**  
If you intend to use your instance for IPv6 communication, you must choose a supported instance type; for example, T2\. For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

1. On the **Configure Instance Details** page, for **Network**, select the VPC that you created earlier and then select a subnet\. For example, launch a web server into the public subnet and the database server into the private subnet\. 

1. \(Optional\) By default, instances launched into a nondefault VPC are not assigned a public IPv4 address\. To be able to connect to your instance in the public subnet, you can assign a public IPv4 address now, or allocate an Elastic IP address and assign it to your instance after it's launched\. To assign a public IPv4 address now, ensure that you choose **Enable** from the **Auto\-assign Public IP** list\. You do not need to assign a public IP address to an instance in the private subnet\.
**Note**  
You can only use the auto\-assign public IPv4 feature for a single, new network interface with the device index of eth0\. For more information, see [Assigning a Public IPv4 Address During Instance Launch](vpc-ip-addressing.md#vpc-public-ip)\. 

1. \(Optional, IPv6\-only\) You can auto\-assign an IPv6 address to your instance from the subnet range\. For **Auto\-assign IPv6 IP**, choose **Enable**\.

1. On the next two pages of the wizard, you can configure storage for your instance, and add tags\. On the **Configure Security Group** page, choose the **Select an existing security group** option, and select one of the security groups you created earlier \(**WebServerSG** for a web server or **DBServerSG** for a database server\)\. Choose **Review and Launch**\.

1. Review the settings that you've chosen\. Make any changes that you need and choose **Launch** to choose a key pair and launch your instance\.

If you did not assign a public IPv4 address to your instance in the public subnet in step 5, you will not be able to connect to it\. Before you can access an instance in your public subnet, you must assign it an Elastic IP address\.

**To allocate an Elastic IP address and assign it to an instance \(IPv4\)**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**\.

1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC**\.

1. Select the Elastic IP address from the list and choose **Actions**, **Associate address**\.

1. Select the network interface or instance\. For **Private IP**, select the corresponding address to associate the Elastic IP address with and choose **Associate**\.

You can now connect to your instances in the VPC\. For information about how to connect to a Linux instance, see [Connect to Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#EC2_ConnectToInstance_Linux) in the *Amazon EC2 User Guide for Linux Instances*\. For information about how to connect to a Windows instance, see [Connect to Your Windows Instance](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2Win_GetStarted.html#EC2Win_ConnectToInstanceWindows) in the *Amazon EC2 User Guide for Windows Instances*\.

## Implementing Scenario 2 with a NAT Instance<a name="vpc-scenario-2-nat-instance"></a>

You can implement scenario 2 using a NAT instance instead of a NAT gateway\. For more information about NAT instances, see [NAT Instances](VPC_NAT_Instance.md)\. 

You can follow the same procedures as above; however, in the NAT section of the VPC wizard, choose **Use a NAT instance instead** and specify the details for your NAT instance\. You will also require a security group for your NAT instance \(`NATSG`\), which allows the NAT instance to receive Internet\-bound traffic from instances in the private subnet, as well as SSH traffic from your network\. The NAT instance can also send traffic to the Internet, so that instances in the private subnet can get software updates\. 

After you've created the VPC with the NAT instance, you must change the security group associated with the NAT instance to the new `NATSG` security group \(by default, the NAT instance is launched using the default security group\)\.


**NATSG: Recommended Rules**  

|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  10\.0\.1\.0/24  |  TCP  |  80  |  Allow inbound HTTP traffic from database servers that are in the private subnet  | 
|  10\.0\.1\.0/24  |  TCP  |  443  |  Allow inbound HTTPS traffic from database servers that are in the private subnet  | 
|  Your network's public IP address range  |  TCP  |  22  |  Allow inbound SSH access to the NAT instance from your network \(over the Internet gateway\)  | 
|   **Outbound**   | 
|  Destination  |  Protocol  |  Port Range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow outbound HTTP access to the Internet \(over the Internet gateway\)  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow outbound HTTPS access to the Internet \(over the Internet gateway\)  | 

**To create the NATSG security group**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**, and the choose **Create Security Group**\.

1. Specify a name and description for the security group\. In this topic, the name `NATSG` is used as an example\. For **VPC**, select the ID of your VPC and choose **Yes, Create**\.

1. Select the NATSG security group that you created\. The details pane displays the details for the security group, plus tabs for working with its inbound and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit** and add rules for inbound traffic as follows:

   1. Choose **Type**, **HTTP** \. For **Source**, enter the IP address range of your private subnet\.

   1. Choose **Add another rule**, **Type**, **HTTPS**\. For **Source**, enter the IP address range of your private subnet\.

   1. Choose **Add another rule**, **Type**, **SSH**\. For **Source**, enter your network's public IP address range\. 

   1. Choose **Save**\.

1. On the **Outbound Rules** tab, choose **Edit** and add rules for outbound traffic as follows:

   1. Locate the default rule that enables all outbound traffic and choose **Remove**\. 

   1. Choose **Type**, **HTTP**\. For **Destination**, enter `0.0.0.0/0`\.

   1. Choose **Add another rule**, **Type**, **HTTPS**\. For **Destination**, enter `0.0.0.0/0`\.

   1. Choose **Save**\.

When the VPC wizard launched the NAT instance, it used the default security group for the VPC\. You need to associate the NAT instance with the NATSG security group instead\.

**To change the security group of the NAT instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface for the NAT instance from the list and choose **Actions**, **Change Security Groups**\.

1. In the **Change Security Groups** dialog box, for **Security groups**, select the NATSG security group that you created \(see [Security](#VPC_Scenario2_Security)\) and choose **Save**\.