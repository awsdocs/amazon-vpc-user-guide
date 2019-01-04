# Scenario 3: VPC with Public and Private Subnets and AWS Site\-to\-Site VPN Access<a name="VPC_Scenario3"></a>

The configuration for this scenario includes a virtual private cloud \(VPC\) with a public subnet and a private subnet, and a virtual private gateway to enable communication with your own network over an IPsec VPN tunnel\. We recommend this scenario if you want to extend your network into the cloud and also directly access the Internet from your VPC\. This scenario enables you to run a multi\-tiered application with a scalable web front end in a public subnet, and to house your data in a private subnet that is connected to your network by an IPsec AWS Site\-to\-Site VPN connection\.

This scenario can also be optionally configured for IPv6—you can use the VPC wizard to create a VPC and subnets with associated IPv6 CIDR blocks\. Instances launched into the subnets can receive IPv6 addresses\. Currently, we do not support IPv6 communication over a Site\-to\-Site VPN connection; however, instances in the VPC can communicate with each other via IPv6, and instances in the public subnet can communicate over the Internet via IPv6\. For more information about IPv4 and IPv6 addressing, see [IP Addressing in Your VPC](vpc-ip-addressing.md)\.

**Topics**
+ [Overview](#Configuration-3)
+ [Routing](#VPC_Scenario3_Routing)
+ [Security](#VPC_Scenario3_Security)
+ [Implementing Scenario 3](#VPC_Scenario3_Implementation)

## Overview<a name="Configuration-3"></a>

The following diagram shows the key components of the configuration for this scenario\.

![\[Diagram for scenario 3: VPC with public and private subnets and VPN access\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/Case3_Diagram.png)

**Important**  
For this scenario, the* [Amazon VPC Network Administrator Guide](https://docs.aws.amazon.com/vpc/latest/adminguide/)* describes what your network administrator needs to do to configure the Amazon VPC customer gateway on your side of the Site\-to\-Site VPN connection\.

The configuration for this scenario includes the following:
+ A virtual private cloud \(VPC\) with a size /16 IPv4 CIDR \(example: 10\.0\.0\.0/16\)\. This provides 65,536 private IPv4 addresses\.
+ A public subnet with a size /24 IPv4 CIDR \(example: 10\.0\.0\.0/24\)\. This provides 256 private IPv4 addresses\. A public subnet is a subnet that's associated with a route table that has a route to an Internet gateway\.
+ A VPN\-only subnet with a size /24 IPv4 CIDR \(example: 10\.0\.1\.0/24\)\. This provides 256 private IPv4 addresses\.
+ An Internet gateway\. This connects the VPC to the Internet and to other AWS products\.
+ A Site\-to\-Site VPN connection between your VPC and your network\. The Site\-to\-Site VPN connection consists of a virtual private gateway located on the Amazon side of the Site\-to\-Site VPN connection and a customer gateway located on your side of the Site\-to\-Site VPN connection\.
+ Instances with private IPv4 addresses in the subnet range \(examples: 10\.0\.0\.5 and 10\.0\.1\.5\), which enables the instances to communicate with each other and other instances in the VPC\. 
+ Instances in the public subnet with Elastic IP addresses \(example: 198\.51\.100\.1\), which are public IPv4 addresses that enable them to be reached from the Internet\. The instances can have public IPv4 addresses assigned at launch instead of Elastic IP addresses\. Instances in the VPN\-only subnet are back\-end servers that don't need to accept incoming traffic from the Internet, but can send and receive traffic from your network\.
+ A custom route table associated with the public subnet\. This route table contains an entry that enables instances in the subnet to communicate with other instances in the VPC, and an entry that enables instances in the subnet to communicate directly with the Internet\.
+ The main route table associated with the VPN\-only subnet\. The route table contains an entry that enables instances in the subnet to communicate with other instances in the VPC, and an entry that enables instances in the subnet to communicate directly with your network\.

For more information about subnets, see [VPCs and Subnets](VPC_Subnets.md) and [IP Addressing in Your VPC](vpc-ip-addressing.md)\. For more information about Internet gateways, see [Internet Gateways](VPC_Internet_Gateway.md)\. For more information about your AWS Site\-to\-Site VPN connection, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html) in the *AWS Site\-to\-Site VPN User Guide*\. For more information about configuring a customer gateway, see the *[Amazon VPC Network Administrator Guide](https://docs.aws.amazon.com/vpc/latest/adminguide/)*\.

### Overview for IPv6<a name="vpc-scenario-3-overview-ipv6"></a>

You can optionally enable IPv6 for this scenario\. In addition to the components listed above, the configuration includes the following:
+ A size /56 IPv6 CIDR block associated with the VPC \(example: 2001:db8:1234:1a00::/56\)\. AWS automatically assigns the CIDR; you cannot choose the range yourself\.
+ A size /64 IPv6 CIDR block associated with the public subnet \(example: 2001:db8:1234:1a00::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the IPv6 CIDR\.
+ A size /64 IPv6 CIDR block associated with the VPN\-only subnet \(example: 2001:db8:1234:1a01::/64\)\. You can choose the range for your subnet from the range allocated to the VPC\. You cannot choose the size of the IPv6 CIDR\.
+ IPv6 addresses assigned to the instances from the subnet range \(example: 2001:db8:1234:1a00::1a\)\.
+ Route table entries in the custom route table that enable instances in the public subnet to use IPv6 to communicate with each other, and directly over the Internet\.
+ A route table entry in the main route table that enable instances in the VPN\-only subnet to use IPv6 to communicate with each other\.

![\[IPv6-enabled VPC with a public and VPN-only subnet\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/scenario-3-ipv6-diagram.png)

## Routing<a name="VPC_Scenario3_Routing"></a>

Your VPC has an implied router \(shown in the configuration diagram for this scenario\)\. In this scenario, the VPC wizard updates the main route table used with the VPN\-only subnet, and creates a custom route table and associates it with the public subnet\. 

The instances in the VPN\-only subnet can't reach the Internet directly; any Internet\-bound traffic must first traverse the virtual private gateway to your network, where the traffic is then subject to your firewall and corporate security policies\. If the instances send any AWS\-bound traffic \(for example, requests to the Amazon S3 or Amazon EC2 APIs\), the requests must go over the virtual private gateway to your network and then egress to the Internet before reaching AWS\. Currently, we do not support IPv6 for Site\-to\-Site VPN connections\.

**Tip**  
Any traffic from your network going to an Elastic IP address for an instance in the public subnet goes over the Internet, and not over the virtual private gateway\. You could instead set up a route and security group rules that enable the traffic to come from your network over the virtual private gateway to the public subnet\. 

The Site\-to\-Site VPN connection is configured either as a statically\-routed Site\-to\-Site VPN connection or as a dynamically\-routed Site\-to\-Site VPN connection \(using BGP\)\. If you select static routing, you'll be prompted to manually enter the IP prefix for your network when you create the Site\-to\-Site VPN connection\. If you select dynamic routing, the IP prefix is advertised automatically to the virtual private gateway for your VPC using BGP\.

The following tables describe the route tables for this scenario\.

### Main Route Table<a name="scenario-3-main-route-table"></a>

The first entry is the default entry for local routing in the VPC; this entry enables the instances in the VPC to communicate with each other over IPv4\. The second entry routes all other IPv4 subnet traffic from the private subnet to your network over the virtual private gateway \(for example, `vgw-1a2b3c4d`\)\.


| Destination | Target | 
| --- | --- | 
|  `10.0.0.0/16`  |  local  | 
|  `0.0.0.0/0`  |  *vgw\-id*  | 

### Custom Route Table<a name="scenario-3-custom-route-table"></a>

The first entry is the default entry for local routing in the VPC; this entry enables the instances in the VPC to communicate with each other\. The second entry routes all other IPv4 subnet traffic from the public subnet to the Internet over the Internet gateway \(for example, `igw-1a2b3c4d`\)\.


| Destination | Target | 
| --- | --- | 
|  `10.0.0.0/16`  |  local  | 
|  `0.0.0.0/0`  |  *igw\-id*  | 

### Alternate Routing<a name="Case3_Alternate_Routing"></a>

Alternatively, if you want instances in the private subnet to access the Internet, you can create a network address translation \(NAT\) gateway or instance in the public subnet, and set up the routing so that the Internet\-bound traffic for the subnet goes to the NAT device\. This enables the instances in the VPN\-only subnet to send requests over the Internet gateway \(for example, for software updates\)\. 

For more information about setting up a NAT device manually, see [NAT](vpc-nat.md)\. For information about using the VPC wizard to set up a NAT device, see [Scenario 2: VPC with Public and Private Subnets \(NAT\)](VPC_Scenario2.md)\.

To enable the private subnet's Internet\-bound traffic to go to the NAT device, you must update the main route table as follows\. 

The first entry is the default entry for local routing in the VPC\. The second row entry for routes the subnet traffic bound for your customer network \(in this case, assume your local network's IP address is `172.16.0.0/12`\) to the virtual private gateway\. The third entry sends all other subnet traffic to a NAT gateway\.


| Destination | Target | 
| --- | --- | 
|  `10.0.0.0/16`  |  local  | 
|  `172.16.0.0/12`  |  *vgw\-id*  | 
|  `0.0.0.0/0`  |  *nat\-gateway\-id*  | 

### Routing for IPv6<a name="vpc-scenario-3-routing-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, your route tables must include separate routes for IPv6 traffic\. The following tables show the route tables for this scenario if you choose to enable IPv6 communication in your VPC\. 

**Main Route Table**

The second entry is the default route that's automatically added for local routing in the VPC over IPv6\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *vgw\-id*  | 

**Custom Route Table**

The second entry is the default route that's automatically added for local routing in the VPC over IPv6\. The fourth entry routes all other IPv6 subnet traffic to the Internet gateway\.


| Destination | Target | 
| --- | --- | 
|  10\.0\.0\.0/16  |  local  | 
|  2001:db8:1234:1a00::/56  |  local  | 
|  0\.0\.0\.0/0  |  *igw\-id*  | 
|  ::/0  | igw\-id | 

## Security<a name="VPC_Scenario3_Security"></a>

AWS provides two features that you can use to increase security in your VPC: *security groups* and *network ACLs*\. Security groups control inbound and outbound traffic for your instances, and network ACLs control inbound and outbound traffic for your subnets\. In most cases, security groups can meet your needs; however, you can also use network ACLs if you want an additional layer of security for your VPC\. For more information, see [Security](VPC_Security.md)\. 

For scenario 3, you'll use security groups but not network ACLs\. If you'd like to use a network ACL, see [Recommended Rules for Scenario 3](vpc-recommended-nacl-rules.md#nacl-rules-scenario-3)\.

Your VPC comes with a [default security group](VPC_SecurityGroups.md#DefaultSecurityGroup)\. An instance that's launched into the VPC is automatically associated with the default security group if you don't specify a different security group during launch\. For this scenario, we recommend that you create the following security groups instead of using the default security group:
+ **WebServerSG**: Specify this security group when you launch web servers in the public subnet\.
+ **DBServerSG**: Specify this security group when you launch database servers in the VPN\-only subnet\.

The instances assigned to a security group can be in different subnets\. However, in this scenario, each security group corresponds to the type of role an instance plays, and each role requires the instance to be in a particular subnet\. Therefore, in this scenario, all instances assigned to a security group are in the same subnet\.

The following table describes the recommended rules for the WebServerSG security group, which allow the web servers to receive Internet traffic, as well as SSH and RDP traffic from your network\. The web servers can also initiate read and write requests to the database servers in the VPN\-only subnet, and send traffic to the Internet; for example, to get software updates\. Because the web server doesn't initiate any other outbound communication, the default outbound rule is removed\.

**Note**  
The group includes both SSH and RDP access, and both Microsoft SQL Server and MySQL access\. For your situation, you might only need rules for Linux \(SSH and MySQL\) or Windows \(RDP and Microsoft SQL Server\)\.


**WebServerSG: Recommended Rules**  

|  | 
| --- |
|  Inbound  | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow inbound HTTP access to the web servers from any IPv4 address\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow inbound HTTPS access to the web servers from any IPv4 address\.  | 
|  Your network's public IP address range  |  TCP  |  22  |  Allow inbound SSH access to Linux instances from your network \(over the Internet gateway\)\.  | 
|  Your network's public IP address range  |  TCP  |  3389  |  Allow inbound RDP access to Windows instances from your network \(over the Internet gateway\)\.  | 
|   **Outbound**   | 
|  The ID of your DBServerSG security group  |  TCP  |  1433  |  Allow outbound Microsoft SQL Server access to the database servers assigned to DBServerSG\.  | 
|  The ID of your DBServerSG security group  |  TCP  |  3306  |  Allow outbound MySQL access to the database servers assigned to DBServerSG\.  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow outbound HTTP access to the Internet\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow outbound HTTPS access to the Internet\.  | 

The following table describes the recommended rules for the DBServerSG security group, which allow Microsoft SQL Server and MySQL read and write requests from the web servers and SSH and RDP traffic from your network\. The database servers can also initiate traffic bound for the Internet \(your route table sends that traffic over the virtual private gateway\)\.


**DBServerSG: Recommended Rules**  

|  | 
| --- |
|  Inbound  | 
|  Source  |  Protocol  |  Port range  |  Comments  | 
|  The ID of your WebServerSG security group  |  TCP  |  1433  |  Allow inbound Microsoft SQL Server access from the web servers associated with the WebServerSG security group\.  | 
|  The ID of your WebServerSG security group  |  TCP  |  3306  |  Allow inbound MySQL Server access from the web servers associated with the WebServerSG security group\.  | 
|  Your network's IPv4 address range  |  TCP  |  22  |  Allow inbound SSH traffic to Linux instances from your network \(over the virtual private gateway\)\.  | 
|  Your network's IPv4 address range  |  TCP  |  3389  |  Allow inbound RDP traffic to Windows instances from your network \(over the virtual private gateway\)\.  | 
|   **Outbound**   | 
|  Destination  |  Protocol  |  Port range  |  Comments  | 
|  0\.0\.0\.0/0  |  TCP  |  80  |  Allow outbound IPv4 HTTP access to the Internet \(for example, for software updates\) over the virtual private gateway\.  | 
|  0\.0\.0\.0/0  |  TCP  |  443  |  Allow outbound IPv4 HTTPS access to the Internet \(for example, for software updates\) over the virtual private gateway\.  | 

\(Optional\) The default security group for a VPC has rules that automatically allow assigned instances to communicate with each other\. To allow that type of communication for a custom security group, you must add the following rules:


|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  The ID of the security group  |  All  |  All  |  Allow inbound traffic from other instances assigned to this security group\.  | 
| Outbound | 
| Destination  |  Protocol  |  Port Range  |  Comments  | 
| The ID of the security group | All | All | Allow outbound traffic to other instances assigned to this security group\. | 

### Security for IPv6<a name="vpc-scenario-3-security-ipv6"></a>

If you associate an IPv6 CIDR block with your VPC and subnets, you must add separate rules to your WebServerSG and DBServerSG security groups to control inbound and outbound IPv6 traffic for your instances\. In this scenario, the web servers will be able to receive all Internet traffic over IPv6, and SSH or RDP traffic from your local network over IPv6\. They can also initiate outbound IPv6 traffic to the Internet\. The database servers cannot initiate outbound IPv6 traffic to the Internet, so they do not require any additional security group rules\.

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

## Implementing Scenario 3<a name="VPC_Scenario3_Implementation"></a>

To implement scenario 3, get information about your customer gateway, and create the VPC using the VPC wizard\. The VPC wizard creates a Site\-to\-Site VPN connection for you with a customer gateway and virtual private gateway\.

These procedures include optional steps for enabling and configuring IPv6 communication for your VPC\. You do not have to perform these steps if you do not want to use IPv6 in your VPC\.

**To prepare your customer gateway**

1. Determine the device you'll use as your customer gateway\. For more information about the devices that we've tested, see [Amazon Virtual Private Cloud FAQs](http://aws.amazon.com/vpc/faqs/#C9)\. For more information about the requirements for your customer gateway, see the [Amazon VPC Network Administrator Guide](https://docs.aws.amazon.com/vpc/latest/adminguide/)\.

1. Obtain the Internet\-routable IP address for the customer gateway's external interface\. The address must be static and may be behind a device performing network address translation \(NAT\)\.

1. If you want to create a statically\-routed Site\-to\-Site VPN connection, get the list of internal IP ranges \(in CIDR notation\) that should be advertised across the Site\-to\-Site VPN connection to the virtual private gateway\. For more information, see [Route Tables and VPN Route Priority](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html#vpn-route-priority) in the *AWS Site\-to\-Site VPN User Guide*\.

**To create a VPC using the VPC wizard**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the dashboard, choose **Launch VPC Wizard**\.  
![\[The Amazon VPC dashboard\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/VPC-dashboard.png)

1. Choose the third option, **VPC with Public and Private Subnets and Hardware VPN Access**, and then choose **Select**\.

1. On the **VPC with Public and Private Subnets and Hardware VPN Access** page, do the following:

   1. \(Optional\) Modify the IPv4 CIDR block ranges for the VPC and subnets as needed, or keep the default values\.

   1. \(Optional\) Name your VPC and subnets\. This helps you identify them later in the console\.

   1. \(Optional, IPv6\-only\) For **IPv6 CIDR block**, choose **Amazon\-provided IPv6 CIDR block**\. For **Public subnet's IPv6 CIDR**, choose **Specify a custom IPv6 CIDR** and specify the hexadecimal pair value for your subnet, or keep the default value\. For **Private subnet's IPv6 CIDR**, choose **Specify a custom IPv6 CIDR**\. Specify the hexadecimal pair value for the IPv6 subnet or keep the default value\.

   1. Choose **Next**\.

1. On the **Configure your VPN** page, do the following:

   1. For **Customer Gateway IP**, specify the public IP address of your VPN router\.

   1. \(Optional\) Name your customer gateway and Site\-to\-Site VPN connection\.

   1. For **Routing Type**, select one of the routing options\. For more information, see [Site\-to\-Site VPN Routing Options](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html#VPNRoutingTypes) in the *AWS Site\-to\-Site VPN User Guide*\.
      + If your VPN router supports Border Gateway Protocol \(BGP\), select **Dynamic \(requires BGP\)**\.
      + If your VPN router does not support BGP, choose **Static**\. For **IP Prefix**, add each IP range for your network in CIDR notation\.

   1. Choose **Create VPC**

1. When the wizard is done, choose **Site\-to\-Site VPN Connections** in the navigation pane\. Select the Site\-to\-Site VPN connection that the wizard created, and choose **Download Configuration**\. In the dialog box, select the vendor for your customer gateway, the platform, and the software version, and then choose **Yes, Download**\.

1. Save the text file containing the VPN configuration and give it to the network administrator along with this guide: [Amazon VPC Network Administrator Guide](https://docs.aws.amazon.com/vpc/latest/adminguide/)\. The VPN won't work until the network administrator configures the customer gateway\.

Create the WebServerSG and DBServerSG security groups\. These security groups will reference each other, therefore you must create them before you add rules to them\.

**To create the WebServerSG and DBServerSG security groups**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Provide a name and description for the security group\. In this topic, the name `WebServerSG` is used as an example\. Select the ID of your VPC from **VPC**, and then choose **Yes, Create**\.

1. Choose **Create Security Group** again\.

1. Provide a name and description for the security group\. In this topic, the name `DBServerSG` is used as an example\. Select the ID of your VPC from **VPC**, and then choose **Yes, Create**\.

**To add rules to the WebServerSG security group**

1. Select the WebServerSG security group that you created\. The details pane displays the details for the security group, plus tabs for working with its inbound and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit** and add rules for inbound traffic as follows:

   1. Select **HTTP** from **Type**, and type `0.0.0.0/0` in **Source**\.

   1. Choose **Add another rule**, then select **HTTPS** from **Type**, and type `0.0.0.0/0` for **Source**\.

   1. Choose **Add another rule**, then select **SSH** from **Type**\. Type your network's public IP address range in **Source**\.

   1. Choose **Add another rule**, then select **RDP** from **Type**\. Type your network's public IP address range in **Source**\.

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **HTTP**\. For **Source**, type `::/0`\. 

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **HTTPS**\. For **Source**, type `::/0`\. 

   1. \(Optional, IPv6\-only\) Choose **Add another rule**, **Type**, **SSH** \(for Linux\) or **RDP** \(for Windows\)\. For **Source**, type your network's IPv6 address range\.

   1. Choose **Save**\.

1. On the **Outbound Rules** tab, choose **Edit** and add rules for outbound traffic as follows:

   1. Locate the default rule that enables all outbound traffic, and then choose **Remove**\. 

   1. Select **MS SQL** from **Type**\. For **Destination**, type the ID of the DBServerSG security group\.

   1. Choose **Add another rule**, then select **MySQL** from **Type**\. For **Destination**, specify the ID of the DBServerSG security group\.

   1. Choose **Add another rule**, then select **HTTPS** from **Type**\. For **Destination**, type `0.0.0.0/0`\.

   1. Choose **Add another rule**, then select **HTTP** from **Type**\. For **Destination**, type `0.0.0.0/0`\.

   1. Choose **Save**\.

**To add the recommended rules to the DBServerSG security group**

1. Select the DBServerSG security group that you created\. The details pane displays the details for the security group, plus tabs for working with its inbound and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit** and add rules for inbound traffic as follows:

   1. Select **SSH** from **Type**, and type the IP address range of your network in **Source**\.

   1. Choose **Add another rule**, then select **RDP** from **Type**, and type the IP address range of your network in **Source**\.

   1. Choose **Add another rule**, then select **MS SQL** from **Type**\. Type the ID of your WebServerSG security group in **Source**\.

   1. Choose **Add another rule**, then select **MYSQL** from **Type**\. Type the ID of your WebServerSG security group in **Source**\.

   1. Choose **Save**\.

1. On the **Outbound Rules** tab, choose **Edit** and add rules for outbound traffic as follows:

   1. Locate the default rule that enables all outbound traffic, and then choose **Remove**\.

   1. Select **HTTP** from **Type**\. For **Destination**, type `0.0.0.0/0`\.

   1. Choose **Add another rule**, then select **HTTPS** from **Type**\. For **Destination**, type `0.0.0.0/0`\.

   1. Choose **Save**\.

After your network administrator configures your customer gateway, you can launch instances into your VPC\.

**To launch an instance \(web server or database server\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance** on the dashboard\.

1. Follow the directions in the wizard\. Choose an AMI, choose an instance type, and then choose **Next: Configure Instance Details**\.
**Note**  
If you intend to use your instance for IPv6 communication, you must choose a supported instance type; for example, T2\. For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

1. On the **Configure Instance Details** page, select the VPC that you created earlier from **Network**, and then select a subnet\. For example, launch a web server into the public subnet and the database server into the private subnet\.

1. \(Optional\) By default, instances launched into a nondefault VPC are not assigned a public IPv4 address\. To be able to connect to your instance in the public subnet, you can assign a public IPv4 address now, or allocate an Elastic IP address and assign it to your instance after it's launched\. To assign a public IP address now, ensure that you select **Enable** from **Auto\-assign Public IP**\. You do not need to assign a public IP address to an instance in the private subnet\.
**Note**  
You can only use the auto\-assign public IP address feature with a single, new network interface with the device index of eth0\. For more information, see [Assigning a Public IPv4 Address During Instance Launch](vpc-ip-addressing.md#vpc-public-ip)\. 

1. \(Optional, IPv6\-only\) You can auto\-assign an IPv6 address to your instance from the subnet range\. For **Auto\-assign IPv6 IP**, choose **Enable**\.

1. On the next two pages of the wizard, you can configure storage for your instance, and add tags\. On the **Configure Security Group** page, select the **Select an existing security group** option, and select one of the security groups that you created \(**WebServerSG** for a web server instance or **DBServerSG** for a database server instance\)\. Choose **Review and Launch**\.

1. Review the settings that you've chosen\. Make any changes that you need, and then choose **Launch** to choose a key pair and launch your instance\.

For the instances running in the VPN\-only subnet, you can test their connectivity by pinging them from your network\. For more information, see [Testing the Site\-to\-Site VPN Connection](https://docs.aws.amazon.com/vpn/latest/s2svpn/HowToTestEndToEnd_Linux.html)\.

If you did not assign a public IPv4 address to your instance in the public subnet in step 5, you will not be able to connect to it\. Before you can access an instance in your public subnet, you must assign it an Elastic IP address\.

**To allocate an Elastic IP address and assign it to an instance using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**\.

1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC**\.

1. Select the Elastic IP address from the list, and choose **Actions**, **Associate address**\.

1. Select the network interface or instance\. Select the address to associate the Elastic IP address with from the corresponding **Private IP**, and then choose **Associate**\.

In scenario 3, you need a DNS server that enables your public subnet to communicate with servers on the Internet, and you need another DNS server that enables your VPN\-only subnet to communicate with servers in your network\.

Your VPC automatically has a set of DHCP options with domain\-name\-servers=AmazonProvidedDNS\. This is a DNS server that Amazon provides to enable any public subnets in your VPC to communicate with the Internet over an Internet gateway\. You must provide your own DNS server and add it to the list of DNS servers your VPC uses\. Sets of DHCP options aren't modifiable, so you must create a set of DHCP options that includes both your DNS server and the Amazon DNS server, and update the VPC to use the new set of DHCP options\.

**To update the DHCP options**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **DHCP Options Sets**\.

1. Choose **Create DHCP options set**\.

1. In the **Create DHCP options set** dialog box, for **Domain name servers**, specify the address of the Amazon DNS server \(AmazonProvidedDNS\) and the address of your DNS server \(for example,` 192.0.2.1`\), separated by a comma, and then choose **Yes, Create**\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, and then choose **Actions**, **Edit DHCP Options Set**\.

1. Select the ID of the new set of options from **DHCP options set** and then choose **Save**\.

1. \(Optional\) The VPC now uses this new set of DHCP options and therefore has access to both DNS servers\. If you want, you can delete the original set of options that the VPC used\.

You can now connect to your instances in the VPC\. For information about how to connect to a Linux instance, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#EC2_ConnectToInstance_Linux) in the *Amazon EC2 User Guide for Linux Instances*\. For information about how to connect to a Windows instance, see [Connect to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2Win_GetStarted.html#EC2Win_ConnectToInstanceWindows) in the *Amazon EC2 User Guide for Windows Instances*\.