# NAT gateways<a name="vpc-nat-gateway"></a>

A NAT gateway is a Network Address Translation \(NAT\) service\. You can use a NAT gateway so that instances in a private subnet can connect to services outside your VPC but external services cannot initiate a connection with those instances\.

When you create a NAT gateway, you specify one of the following connectivity types:
+ **Public** – \(Default\) Instances in private subnets can connect to the internet through a public NAT gateway, but cannot receive unsolicited inbound connections from the internet\. You create a public NAT gateway in a public subnet and must associate an elastic IP address with the NAT gateway at creation\. You route traffic from the NAT gateway to the internet gateway for the VPC\. Alternatively, you can use a public NAT gateway to connect to other VPCs or your on\-premises network\. In this case, you route traffic from the NAT gateway through a transit gateway or a virtual private gateway\.
+ **Private** – Instances in private subnets can connect to other VPCs or your on\-premises network through a private NAT gateway\. You can route traffic from the NAT gateway through a transit gateway or a virtual private gateway\. You cannot associate an elastic IP address with a private NAT gateway\. You can attach an internet gateway to a VPC with a private NAT gateway, but if you route traffic from the private NAT gateway to the internet gateway, the internet gateway drops the traffic\.

The NAT gateway replaces the source IP address of the instances with the IP address of the NAT gateway\. For a public NAT gateway, this is the elastic IP address of the NAT gateway\. For a private NAT gateway, this is the private IP address of the NAT gateway\. When sending response traffic to the instances, the NAT device translates the addresses back to the original source IP address\.

**Pricing**  
When you provision a NAT gateway, you are charged for each hour that your NAT gateway is available and each Gigabyte of data that it processes\. For more information, see [Amazon VPC Pricing](http://aws.amazon.com/vpc/pricing/)\.

The following strategies can help you reduce the data transfer charges for your NAT gateway:
+ If your AWS resources send or receive a significant volume of traffic across Availability Zones, ensure that the resources are in the same Availability Zone as the NAT gateway, or create a NAT gateway in the same Availability Zone as the resources\.
+ If most traffic through your NAT gateway is to AWS services that support interface endpoints or gateway endpoints, consider creating an interface endpoint or gateway endpoint for these services\. For more information about the potential cost savings, see [AWS PrivateLink pricing](http://aws.amazon.com/privatelink/pricing/)\.

**Topics**
+ [NAT gateway basics](#nat-gateway-basics)
+ [Control the use of NAT gateways](#nat-gateway-iam)
+ [Work with NAT gateways](#nat-gateway-working-with)
+ [API and CLI overview](#nat-gateway-api-cli)
+ [Use cases](nat-gateway-scenarios.md)
+ [DNS64 and NAT64](nat-gateway-nat64-dns64.md)
+ [CloudWatch metrics](vpc-nat-gateway-cloudwatch.md)
+ [Troubleshooting](nat-gateway-troubleshooting.md)

## NAT gateway basics<a name="nat-gateway-basics"></a>

Each NAT gateway is created in a specific Availability Zone and implemented with redundancy in that zone\. There is a quota on the number of NAT gateways that you can create in each Availability Zone\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

If you have resources in multiple Availability Zones and they share one NAT gateway, and if the NAT gateway’s Availability Zone is down, resources in the other Availability Zones lose internet access\. To create an Availability Zone\-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT gateway in the same Availability Zone\.

The following characteristics and rules apply to NAT gateways:
+ A NAT gateway supports the following protocols: TCP, UDP, and ICMP\.
+ NAT gateways are supported for IPv4 or IPv6 traffic\. For IPv6 traffic, NAT gateway performs NAT64\. By using this in conjunction with DNS64 \(available on Route 53 resolver\), your IPv6 workloads in a subnet in Amazon VPC can communicate with IPv4 resources\. These IPv4 services may be present in the same VPC \(in a separate subnet\) or a different VPC, on your on\-premises environment or on the internet\.
+ A NAT gateway supports 5 Gbps of bandwidth and automatically scales up to 100 Gbps\. If you require more bandwidth, you can split your resources into multiple subnets and create a NAT gateway in each subnet\.
+ A NAT gateway can process one million packets per second and automatically scales up to ten million packets per second\. Beyond this limit, a NAT gateway will drop packets\. To prevent packet loss, split your resources into multiple subnets and create a separate NAT gateway for each subnet\.
+ A NAT gateway can support up to 55,000 simultaneous connections to each unique destination\. This limit also applies if you create approximately 900 connections per second to a single destination \(about 55,000 connections per minute\)\. If the destination IP address, the destination port, or the protocol \(TCP/UDP/ICMP\) changes, you can create an additional 55,000 connections\. For more than 55,000 connections, there is an increased chance of connection errors due to port allocation errors\. These errors can be monitored by viewing the `ErrorPortAllocation` CloudWatch metric for your NAT gateway\. For more information, see [Monitor NAT gateways with Amazon CloudWatch](vpc-nat-gateway-cloudwatch.md)\.
+ You can associate exactly one Elastic IP address with a public NAT gateway\. You cannot disassociate an Elastic IP address from a NAT gateway after it's created\. To use a different Elastic IP address for your NAT gateway, you must create a new NAT gateway with the required address, update your route tables, and then delete the existing NAT gateway if it's no longer required\.
+ A private NAT gateway receives an available private IP address from the subnet in which it is configured\. The assigned private IP address persists until you delete the private NAT gateway\. You cannot detach the private IP address and you cannot attach additional private IP addresses\.
+ You cannot associate a security group with a NAT gateway\. You can associate security groups with your instances to control inbound and outbound traffic\.
+ You can use a network ACL to control the traffic to and from the subnet for your NAT gateway\. NAT gateways use ports 1024–65535\. For more information, see [Control traffic to subnets using Network ACLs](vpc-network-acls.md)\.
+ A NAT gateway receives a network interface that's automatically assigned a private IP address from the IP address range of the subnet\. You can view the network interface for the NAT gateway using the Amazon EC2 console\. For more information, see [Viewing details about a network interface](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#view_eni_details)\. You cannot modify the attributes of this network interface\.
+ A NAT gateway cannot be accessed through a ClassicLink connection that is associated with your VPC\.
+ You cannot route traffic to a NAT gateway through a VPC peering connection, a Site\-to\-Site VPN connection, or AWS Direct Connect\. A NAT gateway cannot be used by resources on the other side of these connections\.

## Control the use of NAT gateways<a name="nat-gateway-iam"></a>

By default, IAM users do not have permission to work with NAT gateways\. You can create an IAM user policy that grants users permissions to create, describe, and delete NAT gateways\. For more information, see [Identity and access management for Amazon VPC](security-iam.md)\.

## Work with NAT gateways<a name="nat-gateway-working-with"></a>

You can use the Amazon VPC console to create and manage your NAT gateways\.

**Topics**
+ [Create a NAT gateway](#nat-gateway-creating)
+ [Tag a NAT gateway](#nat-gateway-tagging)
+ [Delete a NAT gateway](#nat-gateway-deleting)

### Create a NAT gateway<a name="nat-gateway-creating"></a>

To create a NAT gateway, enter an optional name, a subnet, and an optional connectivity type\. With a public NAT gateway, you must specify an available elastic IP address\. A private NAT gateway receives a primary private IP address selected at random from its subnet\. You cannot detach the primary private IP address or add secondary private IP addresses\.

**To create a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **NAT Gateways**\.

1. Choose **Create NAT Gateway** and do the following:

   1. \(Optional\) Specify a name for the NAT gateway\. This creates a tag where the key is **Name** and the value is the name that you specify\.

   1. Select the subnet in which to create the NAT gateway\.

   1. For **Connectivity type**, select **Private** to create a private NAT gateway or **Public** \(the default\) to create a public NAT gateway\.

   1. \(Public NAT gateway only\) For **Elastic IP allocation ID**, select an Elastic IP address to associate with the NAT gateway\.

   1. \(Optional\) For each tag, choose **Add new tag** and enter the key name and value\.

   1. Choose **Create a NAT Gateway**\.

1. The initial status of the NAT gateway is `Pending`\. After the status changes to `Available`, the NAT gateway is ready for you to use\. Be sure to update your route tables as needed\. For examples, see [NAT gateway use cases](nat-gateway-scenarios.md)\.

   If the status of the NAT gateway changes to `Failed`, there was an error during creation\. For more information, see [NAT gateway creation fails](nat-gateway-troubleshooting.md#nat-gateway-troubleshooting-failed)\.

### Tag a NAT gateway<a name="nat-gateway-tagging"></a>

You can tag your NAT gateway to help you identify it or categorize it according to your organization's needs\. For information about working with tags, see [Tagging your Amazon EC2 resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Cost allocation tags are supported for NAT gateways\. Therefore, you can also use tags to organize your AWS bill and reflect your own cost structure\. For more information, see [Using cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing User Guide*\. For more information about setting up a cost allocation report with tags, see [Monthly cost allocation report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html) in *About AWS Account Billing*\. 

### Delete a NAT gateway<a name="nat-gateway-deleting"></a>

If you no longer need a NAT gateway, you can delete it\. After you delete a NAT gateway, its entry remains visible in the Amazon VPC console for about an hour, after which it's automatically removed\. You cannot remove this entry yourself\.

Deleting a NAT gateway disassociates its Elastic IP address, but does not release the address from your account\. If you delete a NAT gateway, the NAT gateway routes remain in a `blackhole` status until you delete or update the routes\.

**To delete a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **NAT Gateways**\.

1. Select the radio button for the NAT gateway, and then choose **Actions**, **Delete NAT gateway**\.

1. When prompted for confirmation, enter **delete** and then choose **Delete**\.

1. If you no longer need the Elastic IP address that was associated with a public NAT gateway, we recommend that you release it\. For more information, see [Release an Elastic IP address](vpc-eips.md#release-eip)\.

## API and CLI overview<a name="nat-gateway-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interfaces and a list of available API operations, see [Working with Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create a NAT gateway**
+ [create\-nat\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-nat-gateway.html) \(AWS CLI\)
+ [New\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [CreateNatGateway](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNatGateway.html) \(Amazon EC2 Query API\)

**Describe a NAT gateway**
+ [describe\-nat\-gateways](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-nat-gateways.html) \(AWS CLI\)
+ [Get\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeNatGateways](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNatGateways.html) \(Amazon EC2 Query API\)

**Tag a NAT gateway**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)
+ [CreateTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateTags.html) \(Amazon EC2 Query API\)

**Delete a NAT gateway**
+ [delete\-nat\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-nat-gateway.html) \(AWS CLI\)
+ [Remove\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteNatGateway](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNatGateway.html) \(Amazon EC2 Query API\)