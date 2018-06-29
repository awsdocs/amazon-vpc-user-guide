# Getting Started with IPv6 for Amazon VPC<a name="get-started-ipv6"></a>

In this exercise, you create a VPC with an IPv6 CIDR block, a subnet with an IPv6 CIDR block, and launch a public\-facing instance into your subnet\. Your instance will be able to communicate with the Internet over IPv6, and you'll be able to access your instance over IPv6 from your local computer using SSH \(if it's a Linux instance\) or Remote Desktop \(if it's a Windows instance\)\. In your real world environment, you can use this scenario to create a public\-facing web server, for example, to host a blog\. 

To complete this exercise, do the following:
+ Create a nondefault VPC with an IPv6 CIDR block and a single public subnet\. Subnets enable you to group instances based on your security and operational needs\. A public subnet is a subnet that has access to the Internet through an Internet gateway\.
+ Create a security group for your instance that allows traffic only through specific ports\.
+ Launch an Amazon EC2 instance into your subnet, and associate an IPv6 address with your instance during launch\. An IPv6 address is globally unique, and allows your instance to communicate with the Internet\.

For more information about IPv4 and IPv6 addressing, see [IP Addressing in Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-ip-addressing.html)\.

Before you can use Amazon VPC for the first time, you must sign up for Amazon Web Services \(AWS\)\. When you sign up, your AWS account is automatically signed up for all services in AWS, including Amazon VPC\. If you haven't created an AWS account already, go to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choose **Create a Free Account**\.

**Topics**
+ [Step 1: Create the VPC](#get-started-ipv6-vpc)
+ [Step 2: Create a Security Group](#get-started-ipv6-sg)
+ [Step 3: Launch an Instance](#get-started-ipv6-instance)

## Step 1: Create the VPC<a name="get-started-ipv6-vpc"></a>

In this step, you use the Amazon VPC wizard in the Amazon VPC console to create a VPC\. The wizard performs the following steps for you:
+ Creates a VPC with a /16 IPv4 CIDR block and associates a /56 IPv6 CIDR block with the VPC\. For more information, see [Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html#YourVPC)\. The size of the IPv6 CIDR block is fixed \(/56\) and the range of IPv6 addresses is automatically allocated from Amazon's pool of IPv6 addresses \(you cannot select the range yourself\)\.
+ Attaches an Internet gateway to the VPC\. For more information about Internet gateways, see [Internet Gateways](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html)\.
+ Creates a subnet with an /24 IPv4 CIDR block and a /64 IPv6 CIDR block in the VPC\. The size of the IPv6 CIDR block is fixed \(/64\)\.
+ Creates a custom route table, and associates it with your subnet, so that traffic can flow between the subnet and the Internet gateway\. For more information about route tables, see [Route Tables](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Route_Tables.html)\.

The following diagram represents the architecture of your VPC after you've completed this step\.

![\[Getting started: VPC with IPv6 CIDR block and subnet\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/getting-started-ipv6-1-diagram.png)

**Note**  
This exercise covers the first scenario in the VPC wizard\. For more information about the other scenarios, see [Scenarios for Amazon VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Scenarios.html)\.

**To create a VPC using the Amazon VPC wizard**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation bar, on the top\-right, take note of the region in which you'll be creating the VPC\. Ensure that you continue working in the same region for the rest of this exercise, as you cannot launch an instance into your VPC from a different region\. For more information, see [Regions and Availability Zones](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. In the navigation pane, choose **VPC dashboard** and choose **Create VPC**\.  
![\[The Amazon VPC dashboard\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/VPC-dashboard.png)
**Note**  
Do not choose **Your VPCs** in the navigation pane; you cannot access the VPC wizard using the **Create VPC** button on that page\.

1. Choose the first option, **VPC with a Single Public Subnet**, and choose **Select**\.

1. On the configuration page, enter a name for your VPC for **VPC name**; for example, `my-vpc`, and enter a name for your subnet for **Subnet name**\. This helps you to identify the VPC and subnet in the Amazon VPC console after you've created them\. 

1. For **IPv4 CIDR block**, you can leave the default setting \(`10.0.0.0/16`\), or specify your own\. For more information, see [VPC Sizing](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html#VPC_Sizing)\. 

   For **IPv6 CIDR block**, choose **Amazon\-provided IPv6 CIDR block**\.

1. For **Public subnet's IPv4 CIDR**, leave the default setting, or specify your own\. For **Public subnet's IPv6 CIDR**, choose **Specify a custom IPv6 CIDR**\. You can leave the default hexadecimal pair value for the IPv6 subnet \(`00`\)\.

1. Leave the rest of the default configurations on the page, and choose **Create VPC**\.

1. A status window shows the work in progress\. When the work completes, choose **OK** to close the status window\.

1. The **Your VPCs** page displays your default VPC and the VPC that you just created\. 

### Viewing Information About Your VPC<a name="verify-vpc-components-ipv6"></a>

After you've created the VPC, you can view information about the subnet, Internet gateway, and route tables\. The VPC that you created has two route tables — a main route table that all VPCs have by default, and a custom route table that was created by the wizard\. The custom route table is associated with your subnet, which means that the routes in that table determine how the traffic for the subnet flows\. If you add a new subnet to your VPC, it uses the main route table by default\.

**To view information about your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\. Take note of the name and the ID of the VPC that you created \(look in the **Name** and **VPC ID** columns\)\. You use this information to identify the components that are associated with your VPC\. 

1. In the navigation pane, choose **Subnets**\. The console displays the subnet that was created when you created your VPC\. You can identify the subnet by its name in **Name** column, or you can use the VPC information that you obtained in the previous step and look in the **VPC** column\.

1. In the navigation pane, choose **Internet Gateways**\. You can find the Internet gateway that's attached to your VPC by looking at the **VPC** column, which displays the ID and the name \(if applicable\) of the VPC\.

1. In the navigation pane, choose **Route Tables**\. There are two route tables associated with the VPC\. Select the custom route table \(the **Main** column displays **No**\), and then choose the **Routes** tab to display the route information in the details pane:
   + The first two rows in the table are the local routes, which enable instances within the VPC to communicate over IPv4 and IPv6\. You can't remove these routes\.
   + The next row shows the route that the Amazon VPC wizard added to enable traffic destined for an IPv4 address outside the VPC \(`0.0.0.0/0`\) to flow from the subnet to the Internet gateway\. 
   + The next row shows the route that enables traffic destined for an IPv6 address outside the VPC \(`::/0`\) to flow from the subnet to the Internet gateway\. 

1. Select the main route table\. The main route table has a local route, but no other routes\. 

## Step 2: Create a Security Group<a name="get-started-ipv6-sg"></a>

A security group acts as a virtual firewall to control the traffic for its associated instances\. To use a security group, add the inbound rules to control incoming traffic to the instance, and outbound rules to control the outgoing traffic from your instance\. To associate a security group with an instance, specify the security group when you launch the instance\. 

Your VPC comes with a *default security group*\. Any instance not associated with another security group during launch is associated with the default security group\. In this exercise, you create a new security group, `WebServerSG`, and specify this security group when you launch an instance into your VPC\.

**Topics**
+ [Rules for the WebServerSG Security Group](#get-started-inbound-outbound-rules-ipv6)
+ [Creating Your WebServerSG Security Group](#getting-started-ipv6-create-group)

### Rules for the WebServerSG Security Group<a name="get-started-inbound-outbound-rules-ipv6"></a>

The following table describes the inbound and outbound rules for the `WebServerSG` security group\. You add the inbound rules yourself\. The outbound rule is a default rule that allows all outbound communication to anywhere — you do not need to add this rule yourself\.


|  | 
| --- |
| Inbound | 
|  Source IP  |  Protocol  |  Port Range  |  Comments  | 
| ::/0 | TCP | 80 | Allows inbound HTTP access from all IPv6 addresses\. | 
| ::/0 | TCP | 443 | Allows inbound HTTPS traffic from all IPv6 addresses\. | 
|  IPv6 address range of your home network   |  TCP  |  22 or 3389  |  Allows inbound SSH access \(port 22\) from the range of IPv6 addresses in your home network to a Linux/UNIX instance\. If your instance is a Windows instance, you need a rule that allows RDP access \(port 3389\)\.  | 
| Outbound | 
|  Destination IP  |  Protocol  |  Port Range  |  Comments  | 
| 0\.0\.0\.0/0 | All | All | The default outbound rule that allows all outbound IPv4 communication\. You do not need to modify this rule for this exercise\. | 
| ::/0 | All | All | The default outbound rule that allows all outbound IPv6 communication\. You do not need to modify this rule for this exercise\. | 

**Note**  
If you want to use your web server instance for IPv4 traffic too, you must add rules that enable access over IPv4; in this case, HTTP and HTTPS traffic from all IPv4 addresses \(0\.0\.0\.0/0\) and SSH/RDP access from the IPv4 address range of your home network\.

### Creating Your WebServerSG Security Group<a name="getting-started-ipv6-create-group"></a>

You can create your security group using the Amazon VPC console\.

**To create the WebServerSG security group and add rules**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**, **Create Security Group**\.

1. For **Group name**, enter `WebServerSG` as the name of the security group and provide a description\. You can optionally use the **Name tag** field to create a tag for the security group with a key of `Name` and a value that you specify\.

1. Select the ID of your VPC from the **VPC** menu and choose **Yes, Create**\.

1. Select the `WebServerSG` security group that you just created \(you can view its name in the **Group Name** column\)\.

1. On the **Inbound Rules** tab, choose **Edit** and add rules for inbound traffic as follows:

   1. For **Type**, choose **HTTP** and enter `::/0` in the **Source** field\.

   1. Choose **Add another rule**, For **Type**, choose **HTTPS**, and then enter `::/0` in the **Source** field\.

   1. Choose **Add another rule**\. If you're launching a Linux instance, choose **SSH** for **Type**, or if you're launching a Windows instance, choose **RDP**\. Enter your network's public IPv6 address range in the **Source** field\. If you don't know this address range, you can use `::/0` for this exercise\.
**Important**  
If you use `::/0`, you enable all IPv6 addresses to access your instance using SSH or RDP\. This is acceptable for the short exercise, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your instance\.

   1. Choose **Save**\.

## Step 3: Launch an Instance<a name="get-started-ipv6-instance"></a>

When you launch an EC2 instance into a VPC, you must specify the subnet in which to launch the instance\. In this case, you'll launch an instance into the public subnet of the VPC you created\. Use the Amazon EC2 launch wizard in the Amazon EC2 console to launch your instance\.

To ensure that your instance is accessible from the Internet, assign an IPv6 address from the subnet range to the instance during launch\. This ensures that your instance can communicate with the Internet over IPv6\.

The following diagram represents the architecture of your VPC after you've completed this step\.

![\[Getting started: Launch instance\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/getting-started-ipv6-2-diagram.png)

**To launch an EC2 instance into a VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar, on the top\-right, ensure that you select the same region in which you created your VPC and security group\. 

1. From the dashboard, choose **Launch Instance**\.

1. On the first page of the wizard, choose the AMI to use\. For this exercise, we recommend that you choose an Amazon Linux AMI or a Windows AMI\.

1. On the **Choose an Instance Type** page, you can select the hardware configuration and size of the instance to launch\. By default, the wizard selects the first available instance type based on the AMI that you selected\. You can leave the default selection and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select the VPC that you created from the **Network** list and the subnet from the **Subnet** list\. 

1. For **Auto\-assign IPv6 IP**, choose **Enable**\.

1. Leave the rest of the default settings, and go through the next pages of the wizard until you get to the **Add Tags** page\.

1. On the **Add Tags** page, you can tag your instance with a `Name` tag; for example `Name=MyWebServer`\. This helps you to identify your instance in the Amazon EC2 console after you've launched it\. Choose **Next: Configure Security Group** when you are done\. 

1. On the **Configure Security Group** page, the wizard automatically defines the launch\-wizard\-*x* security group to allow you to connect to your instance\. Instead, choose the **Select an existing security group** option, select the **WebServerSG** group that you created previously, and then choose **Review and Launch**\.

1. On the **Review Instance Launch** page, check the details of your instance and choose **Launch**\.

1. In the **Select an existing key pair or create a new key pair** dialog box, you can choose an existing key pair, or create a new one\. If you create a new key pair, ensure that you download the file and store it in a secure location\. You need the contents of the private key to connect to your instance after it's launched\. 

   To launch your instance, select the acknowledgment check box and choose **Launch Instances**\.

1. On the confirmation page, choose **View Instances** to view your instance on the **Instances** page\. Select your instance, and view its details in the **Description** tab\. The **Private IPs** field displays the private IPv4 address that's assigned to your instance from the range of IPv4 addresses in your subnet\. The **IPv6 IPs** field displays the IPv6 address that's assigned to your instance from the range of IPv6 addresses in your subnet\. 

For more information about the options available in the Amazon EC2 launch wizard, see [Launching an Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) in the *Amazon EC2 User Guide for Linux Instances*\.

You can connect to your instance through its IPv6 address using SSH or Remote Desktop from your home network\. For more information about how to connect to a Linux instance, see [Connecting to Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\. For more information about how to connect to a Windows instance, see [Connect to Your Windows Instance Using RDP](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\. 

**Note**  
If you also want your instance to be accessible via an IPv4 address over the Internet, SSH, or RDP, you must associate an Elastic IP address \(a static public IPv4 address\) to your instance, and you must adjust your security group rules to allow access over IPv4\. To do this, see the steps in [Getting Started with Amazon VPC](GetStarted.md)\.