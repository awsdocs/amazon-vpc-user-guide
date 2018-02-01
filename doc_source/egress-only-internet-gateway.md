# Egress\-Only Internet Gateways<a name="egress-only-internet-gateway"></a>

An egress\-only Internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows outbound communication over IPv6 from instances in your VPC to the Internet, and prevents the Internet from initiating an IPv6 connection with your instances\.

**Note**  
An egress\-only Internet gateway is for use with IPv6 traffic only\. To enable outbound\-only Internet communication over IPv4, use a NAT gateway instead\. For more information, see [NAT Gateways](vpc-nat-gateway.md)\.


+ [Egress\-Only Internet Gateway Basics](#egress-only-internet-gateway-basics)
+ [Working with Egress\-Only Internet Gateways](#egress-only-internet-gateway-working-with)
+ [API and CLI Overview](#egress-only-internet-gateway-api-cli)

## Egress\-Only Internet Gateway Basics<a name="egress-only-internet-gateway-basics"></a>

An instance in your public subnet can connect to the Internet through the Internet gateway if it has a public IPv4 address or an IPv6 address\. Similarly, resources on the Internet can initiate a connection to your instance using its public IPv4 address or its IPv6 address; for example, when you connect to your instance using your local computer\.

IPv6 addresses are globally unique, and are therefore public by default\. If you want your instance to be able to access the Internet, but you want to prevent resources on the Internet from initiating communication with your instance, you can use an egress\-only Internet gateway\. To do this, create an egress\-only Internet gateway in your VPC, and then add a route to your route table that points all IPv6 traffic \(`::/0`\) or a specific range of IPv6 address to the egress\-only Internet gateway\. IPv6 traffic in the subnet that's associated with the route table is routed to the egress\-only Internet gateway\.

An egress\-only Internet gateway is stateful: it forwards traffic from the instances in the subnet to the Internet or other AWS services, and then sends the response back to the instances\.

An egress\-only Internet gateway has the following characteristics:

+ You cannot associate a security group with an egress\-only Internet gateway\. You can use security groups for your instances in the private subnet to control the traffic to and from those instances\.

+ You can use a network ACL to control the traffic to and from the subnet for which the egress\-only Internet gateway routes traffic\. 

In the following diagram, a VPC has an IPv6 CIDR block, and a subnet in the VPC has an IPv6 CIDR block\. A custom route table is associated with Subnet 1 and points all Internet\-bound IPv6 traffic \(`::/0`\) to an egress\-only Internet gateway in the VPC\.

![\[Using an egress-only Internet gateway\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/egress-only-igw-diagram.png)

## Working with Egress\-Only Internet Gateways<a name="egress-only-internet-gateway-working-with"></a>

The following sections describe how to create an egress\-only Internet gateway for your private subnet, and configure routing for the subnet\. 

### Creating an Egress\-Only Internet Gateway<a name="egress-only-internet-gateway-create"></a>

You can create an egress\-only Internet gateway for your VPC using the Amazon VPC console\. 

**To create an egress\-only Internet Gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Egress Only Internet Gateways**\.

1. Choose **Create Egress Only Internet Gateway**\.

1. Select the VPC in which to create the egress\-only Internet gateway\. Choose **Create**\.

### Viewing Your Egress\-Only Internet Gateway<a name="egress-only-internet-gateway-describe"></a>

You can view information about your egress\-only Internet gateway in the Amazon VPC console\.

**To view information about an egress\-only Internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Egress Only Internet Gateways**\.

1. Select the egress\-only Internet gateway to view its information in the details pane\.

### Creating a Custom Route Table<a name="egress-only-internet-gateway-routing"></a>

To send traffic destined outside the VPC to the egress\-only Internet gateway, you must create a custom route table, add a route that sends traffic to the gateway, and then associate it with your subnet\. 

**To create a custom route table and add a route to the egress\-only Internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, **Create Route Table**\.

1. In the **Create Route Table** dialog box, optionally name your route table, then select your VPC, and then choose **Yes, Create**\.

1. Select the custom route table that you just created\. The details pane displays tabs for working with its routes, associations, and route propagation\.

1. On the **Routes** tab, choose **Edit**, specify `::/0` in the **Destination** box, select the egress\-only Internet gateway ID in the **Target** list, and then choose **Save**\. 

1. On the **Subnet Associations** tab, choose **Edit** and select the **Associate** check box for the subnet\. Choose **Save**\.

Alternatively, you can add a route to an existing route table that's associated with your subnet\. Select your existing route table, and follow steps 5 and 6 above to add a route for the egress\-only Internet gateway\.

For more information about route tables, see [Route Tables](VPC_Route_Tables.md)\.

### Deleting an Egress\-Only Internet Gateway<a name="egress-only-internet-gateway-delete"></a>

If you no longer need an egress\-only Internet gateway, you can delete it\. Any route in a route table that points to the deleted egress\-only Internet gateway remains in a `blackhole` status until you manually delete or update the route\.

**To delete an egress\-only Internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Egress Only Internet Gateways** and select the egress\-only Internet gateway\.

1. Choose **Delete**\.

1. Choose **Delete Egress Only Internet Gateway** in the confirmation dialog box\.

## API and CLI Overview<a name="egress-only-internet-gateway-api-cli"></a>

You can perform the tasks described on this page using the command line or an API\. For more information about the command line interfaces and a list of available API actions, see [Accessing Amazon VPC](VPC_Introduction.md#VPCInterfaces)\.

**Create an egress\-only Internet gateway**

+ [create\-egress\-only\-internet\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-egress-only-internet-gateway.html) \(AWS CLI\)

+ [New\-EC2EgressOnlyInternetGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2EgressOnlyInternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Describe an egress\-only Internet gateway**

+ [describe\-egress\-only\-internet\-gateways](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-egress-only-internet-gateways.html) \(AWS CLI\)

+ [Get\-EC2EgressOnlyInternetGatewayList](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2EgressOnlyInternetGatewayList.html) \(AWS Tools for Windows PowerShell\)

**Delete an egress\-only Internet gateway**

+ [delete\-egress\-only\-internet\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-egress-only-internet-gateway.html) \(AWS CLI\)

+ [Remove\-EC2EgressOnlyInternetGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2EgressOnlyInternetGateway.html) \(AWS Tools for Windows PowerShell\)