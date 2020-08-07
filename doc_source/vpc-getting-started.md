# Getting started with Amazon VPC<a name="vpc-getting-started"></a>

To get started using Amazon VPC, you can create a nondefault VPC\. The following steps describe how to use the Amazon VPC wizard to create a nondefault VPC with a public subnet, which is a subnet that has access to the internet through an internet gateway\. You can then launch an instance into the subnet and connect to it\.

Alternatively, to get started launching an instance into your existing default VPC, see [Launching an EC2 instance into your default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html#launching-into)\.

Before you can use Amazon VPC for the first time, you must sign up for Amazon Web Services \(AWS\)\. When you sign up, your AWS account is automatically signed up for all services in AWS, including Amazon VPC\. If you haven't created an AWS account already, go to [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create a Free Account**\.

If you want to use a Local Zone for your VPC, create a VPC, and then create a subnet in the Local Zone\. For more information, see [Creating a VPC](working-with-vpcs.md#Create-VPC) and [Creating a subnet in your VPC](working-with-vpcs.md#AddaSubnet)\.

**Topics**
+ [Overview](#getting-started-overview)
+ [Step 1: Create the VPC](#getting-started-create-vpc)
+ [Step 2: Launch an instance into your VPC](#getting-started-launch-instance)
+ [Step 3: Assign an Elastic IP address to your instance](#getting-started-assign-eip)
+ [Step 4: Clean up](#getting-started-delete-vpc)
+ [Next steps](#getting-started-next-steps)
+ [Getting started with IPv6 for Amazon VPC](get-started-ipv6.md)
+ [Amazon VPC console wizard configurations](VPC_wizard.md)

## Overview<a name="getting-started-overview"></a>

To complete this exercise, do the following:
+ Create a nondefault VPC with a single public subnet\.
+ Launch an Amazon EC2 instance into your subnet\.
+ Associate an Elastic IP address with your instance\. This allows your instance to access the internet\.

For more information about granting permissions to IAM users to work with Amazon VPC, see [Identity and access management for Amazon VPC](security-iam.md) and [Amazon VPC policy examples](vpc-policy-examples.md)\.

## Step 1: Create the VPC<a name="getting-started-create-vpc"></a>

In this step, you'll use the Amazon VPC wizard in the Amazon VPC console to create a VPC\. The wizard performs the following steps for you:
+ Creates a VPC with a /16 IPv4 CIDR block \(a network with 65,536 private IP addresses\)\.
+ Attaches an internet gateway to the VPC\.
+ Creates a size /24 IPv4 subnet \(a range of 256 private IP addresses\) in the VPC\. 
+ Creates a custom route table, and associates it with your subnet, so that traffic can flow between the subnet and the internet gateway\.

**To create a VPC using the Amazon VPC Wizard**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation bar, on the top\-right, take note of the [AWS Region](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in which you'll be creating the VPC\. Ensure that you continue working in the same Region for the rest of this exercise, as you cannot launch an instance into your VPC from a different Region\.

1. In the navigation pane, choose **VPC dashboard**\. From the dashboard, choose **Launch VPC Wizard**\.
**Note**  
Do not choose **Your VPCs** in the navigation pane; you cannot access the VPC wizard using the **Create VPC** button on that page\.

1. Choose **VPC with a Single Public Subnet**, and then choose **Select**\.

1. On the configuration page, enter a name for your VPC in the **VPC name** field; for example, `my-vpc`, and enter a name for your subnet in the **Subnet name** field\. This helps you to identify the VPC and subnet in the Amazon VPC console after you've created them\. For this exercise, leave the rest of the configuration settings on the page, and choose **Create VPC**\. 

1. A status window shows the work in progress\. When the work completes, choose **OK** to close the status window\.

1. The **Your VPCs** page displays your default VPC and the VPC that you just created\. The VPC that you created is a nondefault VPC, therefore the **Default VPC** column displays **No**\.

### View information about your VPC<a name="verify-vpc-components"></a>

After you've created the VPC, you can view information about the subnet, the internet gateway, and the route tables\. The VPC that you created has two route tables — a main route table that all VPCs have by default, and a custom route table that was created by the wizard\. The custom route table is associated with your subnet, which means that the routes in that table determine how the traffic for the subnet flows\. If you add a new subnet to your VPC, it uses the main route table by default\.

**To view information about your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\. Take note of the name and the ID of the VPC that you created \(look in the **Name** and **VPC ID** columns\)\. You will use this information to identify the components that are associated with your VPC\. 

1. In the navigation pane, choose **Subnets**\. The console displays the subnet that was created when you created your VPC\. You can identify the subnet by its name in **Name** column, or you can use the VPC information that you obtained in the previous step and look in the **VPC** column\.

1. In the navigation pane, choose **Internet Gateways**\. You can find the internet gateway that's attached to your VPC by looking at the **VPC** column, which displays the ID and the name \(if applicable\) of the VPC\.

1. In the navigation pane, choose **Route Tables**\. There are two route tables associated with the VPC\. Select the custom route table \(the **Main** column displays **No**\), and then choose the **Routes** tab to display the route information in the details pane:
   + The first row in the table is the local route, which enables instances within the VPC to communicate\. This route is present in every route table by default, and you can't remove it\.
   + The second row shows the route that the Amazon VPC wizard added to enable traffic destined for the internet \(`0.0.0.0/0`\) to flow from the subnet to the internet gateway\. 

1. Select the main route table\. The main route table has a local route, but no other routes\. 

## Step 2: Launch an instance into your VPC<a name="getting-started-launch-instance"></a>

When you launch an EC2 instance into a VPC, you must specify the subnet in which to launch the instance\. In this case, you'll launch an instance into the public subnet of the VPC you created\. You'll use the Amazon EC2 launch wizard in the Amazon EC2 console to launch your instance\. 

**To launch an EC2 instance into a VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar, on the top\-right, ensure that you select the same Region in which you created your VPC\. 

1. From the dashboard, choose **Launch Instance**\.

1. On the first page of the wizard, choose the AMI that you want to use\. For this exercise, choose an Amazon Linux AMI or a Windows AMI\.

1. On the **Choose an Instance Type** page, you can select the hardware configuration and size of the instance to launch\. By default, the wizard selects the first available instance type based on the AMI you selected\. You can leave the default selection, and then choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select the VPC that you created from the **Network** list, and the subnet from the **Subnet** list\. Leave the rest of the default settings, and go through the next pages of the wizard until you get to the **Add Tags** page\.

1. On the **Add Tags** page, you can tag your instance with a `Name` tag; for example `Name=MyWebServer`\. This helps you to identify your instance in the Amazon EC2 console after you've launched it\. Choose **Next: Configure Security Group** when you are done\. 

1. On the **Configure Security Group** page, the wizard automatically defines the launch\-wizard\-*x* security group to allow you to connect to your instance\. Choose **Review and Launch**\.
**Important**  
The wizard creates a security group rule that allows all IP addresses \(`0.0.0.0/0`\) to access your instance using SSH or RDP\. This is acceptable for the short exercise, but it's unsafe for production environments\. In production, you'll authorize only a specific IP address or range of addresses to access your instance\.

1. On the **Review Instance Launch** page, choose **Launch**\.

1. In the **Select an existing key pair or create a new key pair** dialog box, you can choose an existing key pair, or create a new one\. If you create a new key pair, ensure that you download the file and store it in a secure location\. You'll need the contents of the private key to connect to your instance after it's launched\. 

   To launch your instance, select the acknowledgment check box, and then choose **Launch Instances**\.

1. On the confirmation page, choose **View Instances** to view your instance on the **Instances** page\. Select your instance, and view its details in the **Description** tab\. The **Private IPs** field displays the private IP address that's assigned to your instance from the range of IP addresses in your subnet\.

For more information about the options available in the Amazon EC2 launch wizard, see [Launching an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Step 3: Assign an Elastic IP address to your instance<a name="getting-started-assign-eip"></a>

In the previous step, you launched your instance into a public subnet — a subnet that has a route to an internet gateway\. However, the instance in your subnet also needs a public IPv4 address to be able to communicate with the internet\. By default, an instance in a nondefault VPC is not assigned a public IPv4 address\. In this step, you'll allocate an Elastic IP address to your account, and then associate it with your instance\.

**To allocate and assign an Elastic IP address**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**, and then **Allocate**\.

1. Select the Elastic IP address from the list, choose **Actions**, and then choose **Associate Address**\.

1. For **Resource type**, ensure that **Instance** is selected\. Choose your instance from the **Instance** list\. Choose **Associate** when you're done\.

Your instance is now accessible from the internet\. You can connect to your instance through its Elastic IP address using SSH or Remote Desktop from your home network\. For more information about how to connect to a Linux instance, see [Connecting to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\. For more information about how to connect to a Windows instance, see [Connect to your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\. 

## Step 4: Clean up<a name="getting-started-delete-vpc"></a>

You can choose to continue using your instance in your VPC, or if you do not need the instance, you can terminate it and release its Elastic IP address to avoid incurring charges for them\. You can also delete your VPC — note that you are not charged for the VPC and VPC components created in this exercise \(such as the subnets and route tables\)\.

Before you can delete a VPC, you must terminate any instances that are running in the VPC\. You can then delete the VPC and it components using the VPC console\.

**To terminate your instance, release your Elastic IP address, and delete your VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, choose **Actions**, then **Instance State**, and then select **Terminate**\.

1. In the dialog box, expand the **Release attached Elastic IPs** section, and select the check box next to the Elastic IP address\. Choose **Yes, Terminate**\. 

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, choose **Actions**, and then choose **Delete VPC**\.

1. When prompted for confirmation, choose **Delete VPC**\.

## Next steps<a name="getting-started-next-steps"></a>

After you create a nondefault VPC, you might want to do the following:
+ Add more subnets to your VPC\. For more information, see [Creating a subnet in your VPC](working-with-vpcs.md#AddaSubnet)\.
+ Enable IPv6 support for your VPC and subnets\. For more information, see [Associating an IPv6 CIDR block with your VPC](working-with-vpcs.md#vpc-associate-ipv6-cidr) and [Associating an IPv6 CIDR block with your subnet](working-with-vpcs.md#subnet-associate-ipv6-cidr)\.
+ Enable instances in a private subnet to access the internet\. For more information, see [NAT](vpc-nat.md)\.