# Internet gateways<a name="VPC_Internet_Gateway"></a>

An internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet\. 

An internet gateway serves two purposes: to provide a target in your VPC route tables for internet\-routable traffic, and to perform network address translation \(NAT\) for instances that have been assigned public IPv4 addresses\. 

An internet gateway supports IPv4 and IPv6 traffic\. It does not cause availability risks or bandwidth constraints on your network traffic\. There's no additional charge for having an internet gateway in your account\.

## Enabling internet access<a name="vpc-igw-internet-access"></a>

To enable access to or from the internet for instances in a subnet in a VPC, you must do the following:
+ Attach an internet gateway to your VPC\.
+ Add a route to your subnet's route table that directs internet\-bound traffic to the internet gateway\. If a subnet is associated with a route table that has a route to an internet gateway, it's known as a *public subnet*\. If a subnet is associated with a route table that does not have a route to an internet gateway, it's known as a *private subnet*\.
+ Ensure that instances in your subnet have a globally unique IP address \(public IPv4 address, Elastic IP address, or IPv6 address\)\.
+ Ensure that your network access control lists and security group rules allow the relevant traffic to flow to and from your instance\.

In your subnet route table, you can specify a route for the internet gateway to all destinations not explicitly known to the route table \(`0.0.0.0/0` for IPv4 or `::/0` for IPv6\)\. Alternatively, you can scope the route to a narrower range of IP addresses; for example, the public IPv4 addresses of your company’s public endpoints outside of AWS, or the Elastic IP addresses of other Amazon EC2 instances outside your VPC\.

To enable communication over the internet for IPv4, your instance must have a public IPv4 address or an Elastic IP address that's associated with a private IPv4 address on your instance\. Your instance is only aware of the private \(internal\) IP address space defined within the VPC and subnet\. The internet gateway logically provides the one\-to\-one NAT on behalf of your instance, so that when traffic leaves your VPC subnet and goes to the internet, the reply address field is set to the public IPv4 address or Elastic IP address of your instance, and not its private IP address\. Conversely, traffic that's destined for the public IPv4 address or Elastic IP address of your instance has its destination address translated into the instance's private IPv4 address before the traffic is delivered to the VPC\.

To enable communication over the internet for IPv6, your VPC and subnet must have an associated IPv6 CIDR block, and your instance must be assigned an IPv6 address from the range of the subnet\. IPv6 addresses are globally unique, and therefore public by default\. 

In the following diagram, Subnet 1 in the VPC is a public subnet\. It's associated with a custom route table that points all internet\-bound IPv4 traffic to an internet gateway\. The instance has an Elastic IP address, which enables communication with the internet\.

![\[Using an internet gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/internet-gateway-overview-diagram.png)

To provide your instances with internet access without assigning them public IP addresses, you can use a NAT device instead\. For more information, see [NAT](vpc-nat.md)\.

**Internet access for default and nondefault VPCs**

The following table provides an overview of whether your VPC automatically comes with the components required for internet access over IPv4 or IPv6\. 


| Component | Default VPC | Nondefault VPC | 
| --- | --- | --- | 
| Internet gateway | Yes | Yes, if you created the VPC using the first or second option in the VPC wizard\. Otherwise, you must manually create and attach the internet gateway\. | 
| Route table with route to internet gateway for IPv4 traffic \(0\.0\.0\.0/0\) | Yes | Yes, if you created the VPC using the first or second option in the VPC wizard\. Otherwise, you must manually create the route table and add the route\. | 
| Route table with route to internet gateway for IPv6 traffic \(::/0\) | No | Yes, if you created the VPC using the first or second option in the VPC wizard, and if you specified the option to associate an IPv6 CIDR block with the VPC\. Otherwise, you must manually create the route table and add the route\. | 
| Public IPv4 address automatically assigned to instance launched into subnet | Yes \(default subnet\) | No \(nondefault subnet\) | 
| IPv6 address automatically assigned to instance launched into subnet | No \(default subnet\) | No \(nondefault subnet\) | 

For more information about default VPCs, see [Default VPC and default subnets](default-vpc.md)\. For more information about using the VPC wizard to create a VPC with an internet gateway, see [VPC with a single public subnet](VPC_Scenario1.md) or [VPC with public and private subnets \(NAT\)](VPC_Scenario2.md)\. 

For more information about IP addressing in your VPC, and controlling how instances are assigned public IPv4 or IPv6 addresses, see [IP Addressing in your VPC](vpc-ip-addressing.md)\.

When you add a new subnet to your VPC, you must set up the routing and security that you want for the subnet\.

## Adding an internet gateway to your VPC<a name="working-with-igw"></a>

The following describes how to manually create a public subnet and attach an internet gateway to your VPC to support internet access\.

**Topics**
+ [Creating a subnet](#Add_IGW_Create_Subnet)
+ [Creating and attaching an internet gateway](#Add_IGW_Attach_Gateway)
+ [Creating a custom route table](#Add_IGW_Routing)
+ [Creating a security group for internet access](#Add_IG_Security_Groups)
+ [Adding Elastic IP addresses](#Add_IG_EIPs)
+ [Detaching an internet gateway from your VPC](#detach-igw)
+ [Deleting an internet gateway](#delete-igw)
+ [API and command overview](#api_cli_overview)

### Creating a subnet<a name="Add_IGW_Create_Subnet"></a>

**To add a subnet to your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**, **Create subnet**\.

1. Specify the subnet details as needed:\.
   + **Name tag**: Optionally provide a name for your subnet\. Doing so creates a tag with a key of `Name` and the value that you specify\.
   + **VPC**: Choose the VPC for which you're creating the subnet\.
   + **Availability Zone**: Optionally choose an Availability Zone or Local Zone in which your subnet will reside, or leave the default **No Preference** to let AWS choose an Availability Zone for you\.

     For information about the Regions that support Local Zones, see [Available Regions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) in the *Amazon EC2 User Guide for Linux Instances*\. 
   + **IPv4 CIDR block**: Specify an IPv4 CIDR block for your subnet, for example, `10.0.1.0/24`\. For more information, see [VPC and subnet sizing for IPv4](VPC_Subnets.md#vpc-sizing-ipv4)\.
   + **IPv6 CIDR block**: \(Optional\) If you've associated an IPv6 CIDR block with your VPC, choose **Specify a custom IPv6 CIDR**\. Specify the hexadecimal pair value for the subnet, or leave the default value\. 

1. Choose **Create**\.

For more information about subnets, see [VPCs and subnets](VPC_Subnets.md)\.

### Creating and attaching an internet gateway<a name="Add_IGW_Attach_Gateway"></a>

After you create an internet gateway, attach it to your VPC\.

**To create an internet gateway and attach it to your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Internet Gateways**, and then choose **Create internet gateway**\.

1. Optionally name your internet gateway\.

1. Optionally add or remove a tag\.

   \[Add a tag\] Choose **Add tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose **Remove** to the right of the tag’s Key and Value\.

1.  Choose **Create internet gateway**\.

1. Select the internet gateway that you just created, and then choose **Actions, Attach to VPC**\.

1. Select your VPC from the list, and then choose **Attach internet gateway**\.

### Creating a custom route table<a name="Add_IGW_Routing"></a>

When you create a subnet, we automatically associate it with the main route table for the VPC\. By default, the main route table doesn't contain a route to an internet gateway\. The following procedure creates a custom route table with a route that sends traffic destined outside the VPC to the internet gateway, and then associates it with your subnet\.

**To create a custom route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then choose **Create Route Table**\.

1. In the **Create Route Table** dialog box, optionally name your route table, then select your VPC, and then choose **Yes, Create**\.

1. Select the custom route table that you just created\. The details pane displays tabs for working with its routes, associations, and route propagation\.

1. On the **Routes** tab, choose **Edit**, **Add another route**, and add the following routes as necessary\. Choose **Save** when you're done\.
   + For IPv4 traffic, specify `0.0.0.0/0` in the **Destination** box, and select the internet gateway ID in the **Target** list\.
   + For IPv6 traffic, specify `::/0` in the **Destination** box, and select the internet gateway ID in the **Target** list\.

1. On the **Subnet Associations** tab, choose **Edit**, select the **Associate** check box for the subnet, and then choose **Save**\.

For more information, see [Route tables](VPC_Route_Tables.md)\.

### Creating a security group for internet access<a name="Add_IG_Security_Groups"></a>

By default, a VPC security group allows all outbound traffic\. You can create a new security group and add rules that allow inbound traffic from the internet\. You can then associate the security group with instances in the public subnet\.

**To create a new security group and associate it with your instances**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**, and then choose **Create Security Group**\.

1. In the **Create Security Group** dialog box, specify a name for the security group and a description\. Select the ID of your VPC from the **VPC** list, and then choose **Yes, Create**\.

1. Select the security group\. The details pane displays the details for the security group, plus tabs for working with its inbound rules and outbound rules\.

1. On the **Inbound Rules** tab, choose **Edit**\. Choose **Add Rule**, and complete the required information\. For example, select **HTTP** or **HTTPS** from the **Type** list, and enter the **Source** as `0.0.0.0/0` for IPv4 traffic, or `::/0` for IPv6 traffic\. Choose **Save** when you're done\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose **Actions**, then **Networking**, and then select **Change Security Groups**\. 

1. In the **Change Security Groups** dialog box, clear the check box for the currently selected security group, and select the new one\. Choose **Assign Security Groups**\.

For more information, see [Security groups for your VPC](VPC_SecurityGroups.md)\.

### Adding Elastic IP addresses<a name="Add_IG_EIPs"></a>

After you've launched an instance into the subnet, you must assign it an Elastic IP address if you want it to be reachable from the internet over IPv4\.

**Note**  
If you assigned a public IPv4 address to your instance during launch, then your instance is reachable from the internet, and you do not need to assign it an Elastic IP address\. For more information about IP addressing for your instance, see [IP Addressing in your VPC](vpc-ip-addressing.md)\.

**To allocate an Elastic IP address and assign it to an instance using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**\.

1. Choose **Allocate**\.
**Note**  
If your account supports EC2\-Classic, first choose **VPC**\.

1. Select the Elastic IP address from the list, choose **Actions**, and then choose **Associate address**\.

1. Choose **Instance** or **Network interface**, and then select either the instance or network interface ID\. Select the private IP address with which to associate the Elastic IP address, and then choose **Associate**\.

For more information, see [Elastic IP addresses](vpc-eips.md)\.

### Detaching an internet gateway from your VPC<a name="detach-igw"></a>

If you no longer need internet access for instances that you launch into a nondefault VPC, you can detach an internet gateway from a VPC\. You can't detach an internet gateway if the VPC has resources with associated public IP addresses or Elastic IP addresses\.

**To detach an internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Elastic IPs** and select the Elastic IP address\.

1. Choose **Actions**, **Disassociate address**\. Choose **Disassociate address**\.

1. In the navigation pane, choose **Internet Gateways**\.

1. Select the internet gateway and choose **Actions, Detach from VPC**\.

1. In the **Detach from VPC** dialog box, choose **Detach internet gateway**\.

### Deleting an internet gateway<a name="delete-igw"></a>

If you no longer need an internet gateway, you can delete it\. You can't delete an internet gateway if it's still attached to a VPC\.

**To delete an internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Internet Gateways**\.

1. Select the internet gateway and choose **Actions**, **Delete internet gateway**\.

1. In the **Delete internet gateway** dialog box, enter `delete`, and choose **Delete internet gateway**\.

### API and command overview<a name="api_cli_overview"></a>

You can perform the tasks described on this page using the command line or an API\. For more information about the command line interfaces and a list of available API actions, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create an internet gateway**
+ [create\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-internet-gateway.html) \(AWS CLI\)
+ [New\-EC2InternetGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Attach an internet gateway to a VPC**
+ [attach\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-internet-gateway.html) \(AWS CLI\)
+ [Add\-EC2InternetGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Add-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Describe an internet gateway**
+ [describe\-internet\-gateways](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-internet-gateways.html) \(AWS CLI\)
+ [Get\-EC2InternetGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Detach an internet gateway from a VPC**
+ [detach\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/detach-internet-gateway.html) \(AWS CLI\)
+ [Dismount\-EC2InternetGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Delete an internet gateway**
+ [delete\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-internet-gateway.html) \(AWS CLI\)
+ [Remove\-EC2InternetGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2InternetGateway.html) \(AWS Tools for Windows PowerShell\)