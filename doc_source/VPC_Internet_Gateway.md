# Internet Gateways<a name="VPC_Internet_Gateway"></a>

An Internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the Internet\. It therefore imposes no availability risks or bandwidth constraints on your network traffic\. 

An Internet gateway serves two purposes: to provide a target in your VPC route tables for Internet\-routable traffic, and to perform network address translation \(NAT\) for instances that have been assigned public IPv4 addresses\. 

An Internet gateway supports IPv4 and IPv6 traffic\.

## Enabling Internet Access<a name="vpc-igw-internet-access"></a>

To enable access to or from the Internet for instances in a VPC subnet, you must do the following:

+ Attach an Internet gateway to your VPC\.

+ Ensure that your subnet's route table points to the Internet gateway\.

+ Ensure that instances in your subnet have a globally unique IP address \(public IPv4 address, Elastic IP address, or IPv6 address\)\.

+ Ensure that your network access control and security group rules allow the relevant traffic to flow to and from your instance\.

To use an Internet gateway, your subnet's route table must contain a route that directs Internet\-bound traffic to the Internet gateway\. You can scope the route to all destinations not explicitly known to the route table \(`0.0.0.0/0` for IPv4 or `::/0` for IPv6\), or you can scope the route to a narrower range of IP addresses; for example, the public IPv4 addresses of your company’s public endpoints outside of AWS, or the Elastic IP addresses of other Amazon EC2 instances outside your VPC\. If your subnet is associated with a route table that has a route to an Internet gateway, it's known as a *public subnet*\. 

To enable communication over the Internet for IPv4, your instance must have a public IPv4 address or an Elastic IP address that's associated with a private IPv4 address on your instance\. Your instance is only aware of the private \(internal\) IP address space defined within the VPC and subnet\. The Internet gateway logically provides the one\-to\-one NAT on behalf of your instance, so that when traffic leaves your VPC subnet and goes to the Internet, the reply address field is set to the public IPv4 address or Elastic IP address of your instance, and not its private IP address\. Conversely, traffic that's destined for the public IPv4 address or Elastic IP address of your instance has its destination address translated into the instance's private IPv4 address before the traffic is delivered to the VPC\.

To enable communication over the Internet for IPv6, your VPC and subnet must have an associated IPv6 CIDR block, and your instance must be assigned an IPv6 address from the range of the subnet\. IPv6 addresses are globally unique, and therefore public by default\. 

In the following diagram, Subnet 1 in the VPC is associated with a custom route table that points all Internet\-bound IPv4 traffic to an Internet gateway\. The instance has an Elastic IP address, which enables communication with the Internet\.

![\[Using an Internet gateway\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/internet-gateway-overview-diagram.png)

**Internet Access for Default and Nondefault VPCs**

The following table provides an overview of whether your VPC automatically comes with the components required for Internet access over IPv4 or IPv6\. 


|  | Default VPC | Nondefault VPC | 
| --- | --- | --- | 
| Internet gateway | Yes | Yes, if you created the VPC using the first or second option in the VPC wizard\. Otherwise, you must manually create and attach the Internet gateway\. | 
| Route table with route to Internet gateway for IPv4 traffic \(0\.0\.0\.0/0\) | Yes | Yes, if you created the VPC using the first or second option in the VPC wizard\. Otherwise, you must manually create the route table and add the route\. | 
| Route table with route to Internet gateway for IPv6 traffic \(::/0\) | No | Yes, if you created the VPC using the first or second option in the VPC wizard, and if you specified the option to associate an IPv6 CIDR block with the VPC\. Otherwise, you must manually create the route table and add the route\. | 
| Public IPv4 address automatically assigned to instance launched into subnet | Yes \(default subnet\) | No \(nondefault subnet\) | 
| IPv6 address automatically assigned to instance launched into subnet | No \(default subnet\) | No \(nondefault subnet\) | 

For more information about default VPCs, see [Default VPC and Default Subnets](default-vpc.md)\. For more information about using the VPC wizard to create a VPC with an Internet gateway, see [Scenario 1: VPC with a Single Public Subnet](VPC_Scenario1.md) or [Scenario 2: VPC with Public and Private Subnets \(NAT\)](VPC_Scenario2.md)\. 

For more information about IP addressing in your VPC, and controlling how instances are assigned public IPv4 or IPv6 addresses, see [IP Addressing in Your VPC](vpc-ip-addressing.md)\.

When you add a new subnet to your VPC, you must set up the routing and security that you want for the subnet\. 

## Creating a VPC with an Internet Gateway<a name="working-with-igw"></a>

The following sections describe how to manually create a public subnet to support Internet access\.


+ [Creating a Subnet](#Add_IGW_Create_Subnet)
+ [Creating and Attaching an Internet Gateway](#Add_IGW_Attach_Gateway)
+ [Creating a Custom Route Table](#Add_IGW_Routing)
+ [Updating the Security Group Rules](#Add_IG_Security_Groups)
+ [Adding Elastic IP Addresses](#Add_IG_EIPs)
+ [Detaching an Internet Gateway from Your VPC](#detach-igw)
+ [Deleting an Internet Gateway](#delete-igw)
+ [API and Command Overview](#api_cli_overview)

### Creating a Subnet<a name="Add_IGW_Create_Subnet"></a>

**To add a subnet to your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**, and then choose **Create Subnet**\.

1. In the **Create Subnet** dialog box, select the VPC, select the Availability Zone, and specify the IPv4 CIDR block for the subnet\.

1. \(Optional, IPv6 only\) For **IPv6 CIDR block**, choose **Specify a custom IPv6 CIDR**\. 

1. Choose **Yes, Create**\.

For more information about subnets, see [VPCs and Subnets](VPC_Subnets.md)\.

### Creating and Attaching an Internet Gateway<a name="Add_IGW_Attach_Gateway"></a>

**To create an Internet gateway and attach it to your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Internet Gateways**, and then choose **Create Internet Gateway**\.

1. In the **Create Internet Gateway** dialog box, you can optionally name your Internet gateway, and then choose **Yes, Create**\.

1. Select the Internet gateway that you just created, and then choose **Attach to VPC**\.

1. In the **Attach to VPC** dialog box, select your VPC from the list, and then choose **Yes, Attach**\.

### Creating a Custom Route Table<a name="Add_IGW_Routing"></a>

When you create a subnet, we automatically associate it with the main route table for the VPC\. By default, the main route table doesn't contain a route to an Internet gateway\. The following procedure creates a custom route table with a route that sends traffic destined outside the VPC to the Internet gateway, and then associates it with your subnet\.

**To create a custom route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then choose **Create Route Table**\.

1. In the **Create Route Table** dialog box, optionally name your route table, then select your VPC, and then choose **Yes, Create**\.

1. Select the custom route table that you just created\. The details pane displays tabs for working with its routes, associations, and route propagation\.

1. On the **Routes** tab, choose **Edit**, **Add another route**, and add the following routes as necessary\. Choose **Save** when you're done\.

   + For IPv4 traffic specify `0.0.0.0/0` in the **Destination** box, and select the Internet gateway ID in the **Target** list\.

   + For IPv6 traffic, specify `::/0` in the **Destination** box, and select the Internet gateway ID in the **Target** list\.

1. On the **Subnet Associations** tab, choose **Edit**, select the **Associate** check box for the subnet, and then choose **Save**\.

For more information about route tables, see [Route Tables](VPC_Route_Tables.md)\.

### Updating the Security Group Rules<a name="Add_IG_Security_Groups"></a>

Your VPC comes with a default security group\. Each instance that you launch into a VPC is automatically associated with its default security group\. The default settings for a default security group allow no inbound traffic from the Internet and allow all outbound traffic to the Internet\. Therefore, to enable your instances to communicate with the Internet, create a new security group that allows public instances to access the Internet\.

**To create a new security group and associate it with your instances**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**, and then choose **Create Security Group**\.

1. In the **Create Security Group** dialog box, specify a name for the security group and a description\. Select the ID of your VPC from the **VPC** list, and then choose **Yes, Create**\.

1. Select the security group\. The details pane displays the details for the security group, plus tabs for working with its inbound rules and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit**\. Choose **Add Rule**, and complete the required information\. For example, select **HTTP** or **HTTPS** from the **Type** list, and enter the **Source** as `0.0.0.0/0` for IPv4 traffic, or `::/0` for IPv6 traffic\. Choose **Save** when you're done\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** \.

1. Select the instance, choose **Actions**, then **Networking**, and then select **Change Security Groups**\. 

1. In the **Change Security Groups** dialog box, clear the check box for the currently selected security group, and select the new one\. Choose **Assign Security Groups**\.

For more information about security groups, see [Security Groups for Your VPC](VPC_SecurityGroups.md)\.

### Adding Elastic IP Addresses<a name="Add_IG_EIPs"></a>

After you've launched an instance into the subnet, you must assign it an Elastic IP address if you want it to be reachable from the Internet over IPv4\.

**Note**  
If you assigned a public IPv4 address to your instance during launch, then your instance is reachable from the Internet, and you do not need to assign it an Elastic IP address\. For more information about IP addressing for your instance, see [IP Addressing in Your VPC](vpc-ip-addressing.md)\.

**To allocate an Elastic IP address and assign it to an instance using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**\.

1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC**\.

1. Select the Elastic IP address from the list, choose **Actions**, and then choose **Associate address**\.

1. Choose **Instance** or **Network interface**, and then select either the instance or network interface ID\. Select the private IP address with which to associate the Elastic IP address, and then choose **Associate**\.

For more information about Elastic IP addresses, see [Elastic IP Addresses](vpc-eips.md)\.

### Detaching an Internet Gateway from Your VPC<a name="detach-igw"></a>

If you no longer need Internet access for instances that you launch into a nondefault VPC, you can detach an Internet gateway from a VPC\. You can't detach an Internet gateway if the VPC has resources with associated public IP addresses or Elastic IP addresses\.

**To detach an Internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs** and select the Elastic IP address\.

1. Choose **Actions**, **Disassociate address**\. Choose **Disassociate address**\.

1. In the navigation pane, choose **Internet Gateways** \.

1. Select the Internet gateway and choose **Detach from VPC**\.

1. In the **Detach from VPC** dialog box, choose **Yes, Detach**\.

### Deleting an Internet Gateway<a name="delete-igw"></a>

If you no longer need an Internet gateway, you can delete it\. You can't delete an Internet gateway if it's still attached to a VPC\.

**To delete an Internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Internet Gateways**\.

1. Select the Internet gateway and choose **Delete**\.

1. In the **Delete Internet Gateway** dialog box, choose **Yes, Delete**\.

### API and Command Overview<a name="api_cli_overview"></a>

You can perform the tasks described on this page using the command line or an API\. For more information about the command line interfaces and a list of available API actions, see [Accessing Amazon VPC](VPC_Introduction.md#VPCInterfaces)\.

**Create an Internet gateway**

+ [create\-internet\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-internet-gateway.html) \(AWS CLI\)

+ [New\-EC2InternetGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Attach an Internet gateway to a VPC**

+ [attach\-internet\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/attach-internet-gateway.html) \(AWS CLI\)

+ [Add\-EC2InternetGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Add-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Describe an Internet gateway**

+ [describe\-internet\-gateways](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-internet-gateways.html) \(AWS CLI\)

+ [Get\-EC2InternetGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Detach an Internet gateway from a VPC**

+ [detach\-internet\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/detach-internet-gateway.html) \(AWS CLI\)

+ [Dismount\-EC2InternetGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Delete an Internet gateway**

+ [delete\-internet\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-internet-gateway.html) \(AWS CLI\)

+ [Remove\-EC2InternetGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)