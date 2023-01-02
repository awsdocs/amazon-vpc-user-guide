# Get started with Amazon VPC<a name="vpc-getting-started"></a>

You can create AWS resources in the subnets of your virtual private cloud \(VPC\)\. For example, to get started with Amazon EC2 quickly, you can launch an EC2 instance into the default subnets of a default VPC\. For more information, see [Default VPCs](default-vpc.md)\.

Alternatively, you can create subnets in a custom VPC for your AWS resources\. For more information, see [Create a VPC](working-with-vpcs.md#Create-VPC)\.

**Note**  
In this tutorial, you'll launch an EC2 instance into a default subnet, connect to the instance, and then terminate the instance\. If you are within the AWS Free Tier, there is no charge for launching an On\-Demand instance\. For more information, see [Amazon EC2 Pricing](http://aws.amazon.com/ec2/pricing/)\.

**Topics**
+ [Prerequisites](#create-vpc-prereq)
+ [Step 1: Get to know your default VPC](#verify-vpc-components)
+ [Step 2: Launch an instance into your VPC](#getting-started-launch-instance)
+ [Step 3: Connect to an EC2 instance in your public subnet](#getting-started-assign-eip)
+ [Step 4: Clean up](#getting-started-delete-vpc)
+ [Next steps](#getting-started-next-steps)

## Prerequisites<a name="create-vpc-prereq"></a>

If this is your first time using AWS, you must sign up for an account\. When you sign up, your AWS account is automatically signed up for all services in AWS, including Amazon VPC\. If you haven't created an AWS account already, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/accounts/latest/reference/root-user-tasks.html)\.

Before you can use Amazon VPC, you must have the required permissions\. For more information, see [Identity and access management for Amazon VPC](security-iam.md) and [Amazon VPC policy examples](vpc-policy-examples.md)\.

## Step 1: Get to know your default VPC<a name="verify-vpc-components"></a>

If you are new to Amazon VPC, use the following procedure to view the configuration of your default VPC, including its default subnets, main route table, and internet gateway\. All default subnets use the main route table, which has a route to the internet gateway\. This means that the resources that you launch into a default subnet have access to the internet\.

**To view the configuration of your default VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\. For the default VPC, the **Default VPC** column is **Yes**\. If you've created other VPCs, **Default VPC** is **No**\.

1. Each VPC has a main route table\. The default subnets use the main route table, as they are not associated with another route table\. To view the main route table, select the checkbox for the default VPC and choose the ID under **Route table**\. Alternatively, choose **Route tables** from the navigation pane and find the route table where the **Main** column is **Yes** and the **VPC** column displays the ID of the VPC followed by the name, **default**\.

   On the **Routes** tab, there is a local route that allows the resources in the VPC to communicate with each other and another route that allows all other traffic to reach the internet through the internet gateway\.

1. In the navigation pane, choose **Subnets**\. For the default VPC, there is one subnet per Availability Zone\. For these default subnets, the **Default subnet** column is **Yes**\. If you select each subnet, you can view information such as its CIDR block, the routes for the route table, and the rules for the default network ACL\.

1. In the navigation pane, choose **Internet gateways**\. For the internet gateway attached to the default VPC, the **VPC ID** column displays the ID of the VPC followed by the name, **default**\.

## Step 2: Launch an instance into your VPC<a name="getting-started-launch-instance"></a>

The Amazon EC2 console provides default values for your instance configuration, which makes it easy to get started quickly\. For example, after you choose an AWS Region, we automatically choose the default VPC for this Region\.

**To launch an instance in a default subnet**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the dashboard, choose **Launch instance**\.

1. From the navigation bar at the top of the screen, select a Region in which to launch the instance\.

1. \(Optional\) Under **Name and tags**, enter a descriptive name for your instance\.

1. Under **Application and OS Images \(Amazon Machine Image\)**, choose **Quick Start**, and then choose an operating system \(OS\) for your instance\.

1. Under **Instance type**, keep the default value, **t2\.micro**, which is free tier eligible\.

1. Under **Key pair \(login\)**, choose an existing key pair, create a new key pair, or choose **Proceed without a key pair** if you do not intend to connect to the new instance that you create as part of this exercise\.

1. Under **Network settings**, notice that we've selected the default VPC for the Region that you selected, we will select a default subnet for you, and we will assign your instance a public IP address\. You can keep these settings\. We also create a default security group with a rule that allows SSH traffic \(Linux instances\) or RDP traffic \(Windows instances\) to your instance from anywhere\.
**Important**  
Rules that allow SSH or RDP traffic to your instance from anywhere are acceptable if you are launching a test instance and plan to stop or terminate it after completing this exercise\. In a production environment, you should authorize only specific address ranges for SSH or RDP traffic\.

1. In the **Summary** panel, choose **Launch instance**\.

For more information, see [Launch an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Step 3: Connect to an EC2 instance in your public subnet<a name="getting-started-assign-eip"></a>

The EC2 instance in your default public subnet is accessible from the internet\. You can connect to your instance using SSH or Remote Desktop from your home network\.
+ For more information about how to connect to a Linux instance in your public subnet, see [Connecting to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ For more information about how to connect to a Windows instance in your public subnet, see [Connect to your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Step 4: Clean up<a name="getting-started-delete-vpc"></a>

When you are finished, you can terminate an instance\. As soon as the instance state changes, you stop incurring any charges for that instance\. After the instance is terminated, it remains visible in the console for a short while\.

Do not delete your default VPC\.

**To terminate an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, and choose **Instance state**, **Terminate instance**\.

1. When prompted for confirmation, choose **Terminate**\.

For more information, see [Terminate an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#terminating-instances-console) in the *Amazon EC2 User Guide for Linux Instances*\.

## Next steps<a name="getting-started-next-steps"></a>

Now that you've worked with your default VPC and the default public subnets, you might want to do the following:
+ Learn more about VPCs: [Virtual private clouds \(VPC\)](configure-your-vpc.md)\.
+ Add a private subnet to your VPC: [Create a subnet in your VPC](working-with-subnets.md#create-subnets)\.
+ Enable IPv6 support for your VPC and subnets: [Associate an IPv6 CIDR block with your subnet](working-with-subnets.md#subnet-associate-ipv6-cidr)\.
+ Enable instances in a private subnet to access the internet: [Connect to the internet or other networks using NAT devices](vpc-nat.md)\.