# Default VPCs<a name="default-vpc"></a>

When you start using Amazon VPC, you have a default VPC in each AWS Region\. A default VPC comes with a public subnet in each Availability Zone, an internet gateway, and settings to enable DNS resolution\. Therefore, you can immediately start launching Amazon EC2 instances into a default VPC\. You can also use services such as Elastic Load Balancing, Amazon RDS, and Amazon EMR in your default VPC\.

A default VPC is suitable for getting started quickly and for launching public instances such as a blog or simple website\. You can modify the components of your default VPC as needed\.

You can add subnets to your default VPC\. For more information, see [Create a subnet](create-subnets.md)\.

**Topics**
+ [Default VPC components](#default-vpc-components)
+ [Default subnets](#default-subnet)
+ [View your default VPC and default subnets](#view-default-vpc)
+ [Create a default VPC](#create-default-vpc)
+ [Create a default subnet](#create-default-subnet)
+ [Delete your default subnets and default VPC](#deleting-default-vpc)

## Default VPC components<a name="default-vpc-components"></a>

When we create a default VPC, we do the following to set it up for you:
+ Create a VPC with a size `/16` IPv4 CIDR block \(`172.31.0.0/16`\)\. This provides up to 65,536 private IPv4 addresses\. 
+ Create a size `/20` default subnet in each Availability Zone\. This provides up to 4,096 addresses per subnet, a few of which are reserved for our use\.
+ Create an [internet gateway](VPC_Internet_Gateway.md) and connect it to your default VPC\.
+ Add a route to the main route table that points all traffic \(`0.0.0.0/0`\) to the internet gateway\.
+ Create a default security group and associate it with your default VPC\.
+ Create a default network access control list \(ACL\) and associate it with your default VPC\.
+ Associate the default DHCP options set for your AWS account with your default VPC\.

**Note**  
Amazon creates the above resources on your behalf\. IAM policies do not apply to these actions because you do not perform these actions\. For example, if you have an IAM policy that denies the ability to call CreateInternetGateway, and then you call CreateDefaultVpc, the internet gateway in the default VPC is still created\.

The following figure illustrates the key components that we set up for a default VPC\.

![\[We create a default VPC in each Region, with a default subnet in each Availability Zone.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/default-vpc.png)

The following table shows the routes in the main route table for the default VPC\.


| Destination | Target | 
| --- | --- | 
| 172\.31\.0\.0/16 | local | 
| 0\.0\.0\.0/0 | internet\_gateway\_id | 

You can use a default VPC as you would use any other VPC: 
+ Add additional nondefault subnets\.
+ Modify the main route table\.
+ Add additional route tables\.
+ Associate additional security groups\.
+ Update the rules of the default security group\.
+ Add AWS Site\-to\-Site VPN connections\.
+ Add more IPv4 CIDR blocks\.
+ Access VPCs in a remote Region by using a Direct Connect gateway\. For information about Direct Connect gateway options, see [Direct Connect gateways](https://docs.aws.amazon.com/directconnect/latest/UserGuide/direct-connect-gateways-intro.html) in the *AWS Direct Connect User Guide*\.

You can use a default subnet as you would use any other subnet; add custom route tables and set network ACLs\. You can also specify a specific default subnet when you launch an EC2 instance\.

You can optionally associate an IPv6 CIDR block with your default VPC\.

## Default subnets<a name="default-subnet"></a>

By default, a default subnet is a public subnet, because the main route table sends the subnet's traffic that is destined for the internet to the internet gateway\. You can make a default subnet into a private subnet by removing the route from the destination 0\.0\.0\.0/0 to the internet gateway\. However, if you do this, no EC2 instance running in that subnet can access the internet\. 

Instances that you launch into a default subnet receive both a public IPv4 address and a private IPv4 address, and both public and private DNS hostnames\. Instances that you launch into a nondefault subnet in a default VPC don't receive a public IPv4 address or a DNS hostname\. You can change your subnet's default public IP addressing behavior\. For more information, see [Modify the public IPv4 addressing attribute for your subnet](modify-subnets.md#subnet-public-ip)\.

From time to time, AWS may add a new Availability Zone to a Region\. In most cases, we automatically create a new default subnet in this Availability Zone for your default VPC within a few days\. However, if you made any modifications to your default VPC, we do not add a new default subnet\. If you want a default subnet for the new Availability Zone, you can create one yourself\. For more information, see [Create a default subnet](#create-default-subnet)\.

## View your default VPC and default subnets<a name="view-default-vpc"></a>

You can view your default VPC and subnets using the Amazon VPC console or the command line\.

**To view your default VPC and subnets using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. In the **Default VPC** column, look for a value of **Yes**\. Take note of the ID of the default VPC\.

1. In the navigation pane, choose **Subnets**\.

1. In the search bar, type the ID of the default VPC\. The returned subnets are subnets in your default VPC\.

1. To verify which subnets are default subnets, look for a value of **Yes** in the **Default Subnet **column\.

**To describe your default VPC using the command line**
+ Use the [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) \(AWS CLI\)
+ Use the [Get\-EC2Vpc](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Vpc.html) \(AWS Tools for Windows PowerShell\)

Use the commands with the `isDefault` filter and set the filter value to `true`\. 

**To describe your default subnets using the command line**
+ Use the [describe\-subnets](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-subnets.html) \(AWS CLI\)
+ Use the [Get\-EC2Subnet](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Subnet.html) \(AWS Tools for Windows PowerShell\)

Use the commands with the `vpc-id` filter and set the filter value to the ID of the default VPC\. In the output, the `DefaultForAz` field is set to `true` for default subnets\.

## Create a default VPC<a name="create-default-vpc"></a>

If you delete your default VPC, you can create a new one\. You cannot restore a previous default VPC that you deleted, and you cannot mark an existing nondefault VPC as a default VPC\. If your account supports EC2\-Classic, you cannot use these procedures to create a default VPC in a Region that supports EC2\-Classic\.

When you create a default VPC, it is created with the standard [components](#default-vpc-components) of a default VPC, including a default subnet in each Availability Zone\. You cannot specify your own components\. The subnet CIDR blocks of your new default VPC may not map to the same Availability Zones as your previous default VPC\. For example, if the subnet with CIDR block `172.31.0.0/20` was created in `us-east-2a` in your previous default VPC, it may be created in `us-east-2b` in your new default VPC\.

If you already have a default VPC in the Region, you cannot create another one\. 

**To create a default VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Choose **Actions**, **Create Default VPC**\.

1. Choose **Create**\. Close the confirmation screen\.

**To create a default VPC using the command line**  
You can use the [create\-default\-vpc](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-default-vpc.html) AWS CLI command\. This command does not have any input parameters\.

```
aws ec2 create-default-vpc
```

The following is example output\.

```
{
    "Vpc": {
        "VpcId": "vpc-3f139646", 
        "InstanceTenancy": "default", 
        "Tags": [], 
        "Ipv6CidrBlockAssociationSet": [], 
        "State": "pending", 
        "DhcpOptionsId": "dopt-61079b07", 
        "CidrBlock": "172.31.0.0/16", 
        "IsDefault": true
    }
}
```

Alternatively, you can use the [New\-EC2DefaultVpc](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2DefaultVpc.html) Tools for Windows PowerShell command or the [CreateDefaultVpc](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateDefaultVpc.html) Amazon EC2 API action\.

## Create a default subnet<a name="create-default-subnet"></a>

You can create a default subnet in an Availability Zone that does not have one\. For example, you might want to create a default subnet if you have deleted a default subnet, or if AWS has added a new Availability Zone and did not automatically create a default subnet for that zone in your default VPC\. 

When you create a default subnet, it is created with a size `/20` IPv4 CIDR block in the next available contiguous space in your default VPC\. The following rules apply:
+ You cannot specify the CIDR block yourself\.
+ You cannot restore a previous default subnet that you deleted\.
+ You can have only one default subnet per Availability Zone\.
+ You cannot create a default subnet in a nondefault VPC\.

If there is not enough address space in your default VPC to create a size `/20` CIDR block, the request fails\. If you need more address space, you can [add an IPv4 CIDR block to your VPC](vpc-cidr-blocks.md#vpc-resize)\.

If you've associated an IPv6 CIDR block with your default VPC, the new default subnet does not automatically receive an IPv6 CIDR block\. Instead, you can associate an IPv6 CIDR block with the default subnet after you create it\. For more information, see [Add an IPv6 CIDR block to your subnet](modify-subnets.md#subnet-associate-ipv6-cidr)\.

You cannot create a default subnet using the AWS Management Console\.

**To create a default subnet using the AWS CLI**  
Use the [create\-default\-subnet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-default-subnet.html) AWS CLI command and specify the Availability Zone in which to create the subnet\.

```
aws ec2 create-default-subnet --availability-zone us-east-2a
```

The following is example output\.

```
{
    "Subnet": {
        "AvailabilityZone": "us-east-2a", 
        "Tags": [], 
        "AvailableIpAddressCount": 4091, 
        "DefaultForAz": true, 
        "Ipv6CidrBlockAssociationSet": [], 
        "VpcId": "vpc-1a2b3c4d", 
        "State": "available", 
        "MapPublicIpOnLaunch": true, 
        "SubnetId": "subnet-1122aabb", 
        "CidrBlock": "172.31.32.0/20", 
        "AssignIpv6AddressOnCreation": false
    }
}
```

For more information about setting up the AWS CLI, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

Alternatively, you can use the [New\-EC2DefaultSubnet](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2DefaultSubnet.html) Tools for Windows PowerShell command or the [CreateDefaultSubnet](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateDefaultSubnet.html) Amazon EC2 API action\.

## Delete your default subnets and default VPC<a name="deleting-default-vpc"></a>

You can delete a default subnet or default VPC just as you can delete any other subnet or VPC\. However, if you delete your default subnets or default VPC, you must explicitly specify a subnet in one of your VPCs when you launch instances\. If you do not have another VPC, you must create a VPC with a subnet in at least one Availability Zone\. For more information, see [Create a VPC](create-vpc.md)\. 

If you delete your default VPC, you can create a new one\. For more information, see [Create a default VPC](#create-default-vpc)\. 

If you delete a default subnet, you can create a new one\. For more information, see [Create a default subnet](#create-default-subnet)\. To ensure that your new default subnet behaves as expected, modify the subnet attribute to assign public IP addresses to instances that are launched in that subnet\. For more information, see [Modify the public IPv4 addressing attribute for your subnet](modify-subnets.md#subnet-public-ip)\. You can only have one default subnet per Availability Zone\. You cannot create a default subnet in a nondefault VPC\. 