# Get started with Amazon VPC<a name="vpc-getting-started"></a>

To get started using Amazon VPC, you can create a VPC with two subnets\. Once you create the VPC and subnets, you'll launch an EC2 instance into the subnet and connect to it\. After you successfully connect to the instance, you'll terminate the instance and delete the VPC and subnets\. For the standard create VPC process and for complete information about each of the options, see [Create a VPC](working-with-vpcs.md#Create-VPC)\.

**Note**  
In this exercise, you'll create a VPC and two subnets\. Within one of the subnets you'll launch an on\-demand EC2 instance\. There is no charge for creating a VPC and subnets, but there are data usage charges associated with EC2 instances\. For more information, see [Amazon EC2 On\-Demand Pricing](http://aws.amazon.com/ec2/pricing/on-demand/)\.

**Topics**
+ [Prerequisites](#create-vpc-prereq)
+ [Step 1: Create a VPC](#create-vpc)
+ [Step 2: View information about your VPC](#verify-vpc-components)
+ [Step 3: Launch an instance into your VPC](#getting-started-launch-instance)
+ [Step 4: Connect to the E2 instance in your public subnet](#getting-started-assign-eip)
+ [Step 5: Clean up](#getting-started-delete-vpc)
+ [Next steps](#getting-started-next-steps)

## Prerequisites<a name="create-vpc-prereq"></a>

If this is your first time using AWS, you must sign up for Amazon Web Services \(AWS\) before you can use Amazon VPC\. When you sign up, your AWS account is automatically signed up for all services in AWS, including Amazon VPC\.

If you haven't created an AWS account already, go to [https://aws\.amazon\.com/](https://aws.amazon.com/), and create a free account\.

For more information about IAM permissions required to work with Amazon VPC, see [Identity and access management for Amazon VPC](security-iam.md) and [Amazon VPC policy examples](vpc-policy-examples.md)\.

## Step 1: Create a VPC<a name="create-vpc"></a>

In this step, you create a VPC, subnets, Availability Zones, NAT gateways, and VPC endpoints\.

**To create a VPC, subnets, and other VPC resources**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**, **Create VPC**\.

1. Under **Resources to create**, choose **VPC, subnets, etc\.**\.

1. Modify the options as needed:
   + **Name tag auto\-generation**: Choose a Name tag that will be applied to the resources you create\. The tag can either be automatically generated for you, or you can define the value\. The defined value will be used to generate the Name tag in all resources as "name\-resource"\. For example if you enter "Preproduction", each subnet will be tagged with a "Preproduction\-subnet" Name tag\. A tag is a label that you assign to an AWS resource\. Each tag consists of a key and an optional value\. You can use tags to search and filter your resources or track your AWS costs\.
   + **IPv4 CIDR block**: Choose an IPv4 CIDR for the VPC\. This option is required\.
   + **IPv6 CIDR block**: Leave **No IPv6 CIDR block** selected for this exercise\.
   + **Tenancy**: Choose **Default** for this exercise to ensure that EC2 instances launched in this VPC use the EC2 instance tenancy attribute specified when the EC2 instance is launched\. For more information, see [Create a VPC](working-with-vpcs.md#Create-VPC)\.
   + **Availability Zones \(AZs\)**: Choose **1** for this exercise\. An AZ is one or more discrete data centers with redundant power, networking, and connectivity in an AWS Region\. AZs give you the ability to operate production applications and databases that are more highly available, fault tolerant, and scalable than would be possible from a single data center\. If you partition your applications running in subnets across AZs, you are better isolated and protected from issues such as power outages, lightning strikes, tornadoes, earthquakes, and more\.
   + **Customize AZs**: Leave the default options selected for this exercise\.
   + **Number of public subnets**: Choose **1** for this exercise\. A "public" subnet is a subnet that as a route table entry that points to an internet gateway\. This enables EC2 instances running in the subnet to be publicly accessible over the internet\.
   + **Customize public subnets CIDR blocks**: Leave the default options selected for this exercise\. For more information, see [Create a VPC](working-with-vpcs.md#Create-VPC)\.
   + **Number of private subnets**: Choose **1** for this exercise \. A "private" subnet is a subnet that does not have a route table entry that points to an internet gateway\. Use private subnets to secure backend resources that do not need to be publicly accessible over the internet\.
   + **Customize private subnets CIDR blocks**: Leave the default options selected for this exercise\. For more information, see [Create a VPC](working-with-vpcs.md#Create-VPC)\.
   + **NAT gateways**: Choose **None** for this exercise\. A NAT gateway is an AWS\-managed service that enables EC2 instances in private subnets to send outbound traffic to the internet\. Resources on the internet, however, cannot establish a connection with the instances\. Note that there is cost associated with NAT gateways\. For more information, see [NAT gateways](vpc-nat-gateway.md)\.
   + **VPC endpoints**: Choose **None** for this exercise\. A VPC endpoint enables you to privately connect your VPC to supported AWS services like Amazon S3\. VPC endpoints enable you to create an isolated VPC that is closed from the public internet\. There is no additional charge for using gateway endpoints\. This can help avoid the costs associated with NAT gateways\.
   + **DNS options**: Select both domain name resolution options for the EC2 instances launched into this VPC\.

     
     + **Enable DNS hostnames**: Enables hostnames to be provisioned for EC2 instance public IPv4 addresses\.
     + **Enable DNS resolution**: Enables hostnames to be provisioned for EC2 instance public IPv4 addresses and enables domain name resolution of the hostnames\.

1. In the **Preview** pane, you can see the VPC, subnets, route tables, and network connections that will be created\. An *\-igw* network connection represents an internet gateway that will be created\. A routing entry that points to the internet gateway will also be added to the route table associated with the public subnet\. 

1. Choose **Create VPC**\.

## Step 2: View information about your VPC<a name="verify-vpc-components"></a>

After you've created the VPC, you can view information about the subnet, the internet gateway, and the route tables\.

**To view information about your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\. Take note of the name and the ID of the VPC that you created \(look in the **Name** and **VPC ID** columns\)\. You will use this information to identify the components that are associated with your VPC\. 

1. In the navigation pane, choose **Subnets**\. The console displays the public and private subnets that you created\. You can identify the subnet by its name in **Name** column, or you can use the VPC information that you obtained in the previous step and look in the **VPC** column\.

1. In the navigation pane, choose **Internet gateways**\. You can find the internet gateway that's attached to your VPC by looking at the **VPC** column, which displays the ID and the name \(if applicable\) of the VPC\.

1. In the navigation pane, choose **Route tables**\. There are two new custom route tables associated with the VPC\.
   + Select the route table with *private* in the name, and then choose the **Routes** tab to display the route information in the details pane\. The first row in the table is the local route, which enables instances within the VPC to communicate\. This route is present in every route table by default, and you can't remove it\.
   + Select the route table with *public* in the name, and then choose the **Routes** tab to display the route information in the details pane\. The second row shows the entry for the internet gateway to enable traffic destined for the internet \(`0.0.0.0/0`\) to flow from the subnet to the internet gateway\.

1. Select the main route table\. The main route table has a local route and no other routes\. 

**Note**  
Each VPC you create gets a default route table \(called the main route table\) in addition to a custom route table for each subnet\. The main route table controls the routing for all subnets that are not explicitly associated with any other route tables\. You can identify the main route table by the value it has in the **Main** column when you view your **Route tables**\. If the value in that column is **Yes**, the route table is a main route table\. If the value is **No**, the route table is a custom route table\.

## Step 3: Launch an instance into your VPC<a name="getting-started-launch-instance"></a>

Complete the steps in [Launching an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Important**  
When you create the EC2 instance, ensure the following:   
When you open the EC2 console, ensure you use the same AWS Region in which you created your VPC\.
When you configure your EC2 instance to launch into a VPC, choose the VPC and public subnet that you created in the previous step\.
When you configure your EC2 instance to launch into your public subnet, ensure **Auto\-assign Public IP** is set to **Enable**\. By default, an instance in a nondefault VPC is not assigned a public IPv4 address, and we want to assign a public IPv4 address so that we can connect to our instance in the subnet over the internet\.
The EC2 launch wizard creates a security group rule that allows all IP addresses \(`0.0.0.0/0`\) to access your instance using SSH or RDP\. This is acceptable for this exercise, but it's unsafe for production environments\. In production, you'll authorize only a specific IP address or range of addresses to access your instance\.

## Step 4: Connect to the E2 instance in your public subnet<a name="getting-started-assign-eip"></a>

The EC2 instance in your public subnet is accessible from the internet\. You can connect to your instance using SSH or Remote Desktop from your home network\.
+ For more information about how to connect to a Linux instance in your public subnet, see [Connecting to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ For more information about how to connect to a Windows instance in your public subnet, see [Connect to your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Step 5: Clean up<a name="getting-started-delete-vpc"></a>

You can choose to continue using your instance in your VPC, or if you do not need the instance, you can terminate it and release its Elastic IP address to avoid incurring charges for them\. You can also delete your VPC — note that you are not charged for the VPC and VPC components created in this exercise \(such as the subnets and route tables\)\.

Before you can delete a VPC, you must terminate any instances that are running in the VPC\. You can then delete the VPC and its components using the VPC console\.

**To terminate your instance and delete your VPC**

1. Follow the steps in [Terminate an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#terminating-instances-console) in the *Amazon EC2 User Guide for Linux Instances* to terminate the EC2 instance\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC, choose **Actions**, and then choose **Delete VPC**\.

1. Confirm deletion and choose **Delete**\.

## Next steps<a name="getting-started-next-steps"></a>

After you create a nondefault VPC, you might want to do the following:
+ Add subnets to your VPC\. For more information, see [Create a subnet in your VPC](working-with-subnets.md#create-subnets)\.
+ Enable IPv6 support for your VPC and subnets\. For more information, see [Associate an IPv6 CIDR block with your subnet](working-with-subnets.md#subnet-associate-ipv6-cidr)\.
+ Enable instances in a private subnet to access the internet\. For more information, see [Connect to the internet or other networks using NAT devices](vpc-nat.md)\.