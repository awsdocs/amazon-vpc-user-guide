# Enable outbound IPv6 traffic using an egress\-only internet gateway<a name="egress-only-internet-gateway"></a>

An egress\-only internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows outbound communication over IPv6 from instances in your VPC to the internet, and prevents the internet from initiating an IPv6 connection with your instances\.

**Note**  
An egress\-only internet gateway is for use with IPv6 traffic only\. To enable outbound\-only internet communication over IPv4, use a NAT gateway instead\. For more information, see [NAT gateways](vpc-nat-gateway.md)\.

**Topics**
+ [Egress\-only internet gateway basics](#egress-only-internet-gateway-basics)
+ [Work with egress\-only internet gateways](#egress-only-internet-gateway-working-with)
+ [API and CLI overview](#egress-only-internet-gateway-api-cli)

## Egress\-only internet gateway basics<a name="egress-only-internet-gateway-basics"></a>

IPv6 addresses are globally unique, and are therefore public by default\. If you want your instance to be able to access the internet, but you want to prevent resources on the internet from initiating communication with your instance, you can use an egress\-only internet gateway\. To do this, create an egress\-only internet gateway in your VPC, and then add a route to your route table that points all IPv6 traffic \(`::/0`\) or a specific range of IPv6 address to the egress\-only internet gateway\. IPv6 traffic in the subnet that's associated with the route table is routed to the egress\-only internet gateway\.

An egress\-only internet gateway is stateful: it forwards traffic from the instances in the subnet to the internet or other AWS services, and then sends the response back to the instances\.

An egress\-only internet gateway has the following characteristics:
+ You cannot associate a security group with an egress\-only internet gateway\. You can use security groups for your instances in the private subnet to control the traffic to and from those instances\.
+ You can use a network ACL to control the traffic to and from the subnet for which the egress\-only internet gateway routes traffic\. 

In the following diagram, the VPC has both IPv4 and IPv6 CIDR blocks, and the subnet both IPv4 and IPv6 CIDR blocks\. The VPC has an egress\-only internet gateway\.

![\[Using an egress-only internet gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/egress-only-igw.png)

The following is an example of the route table associated with the subnet\. There is a route that sends all internet\-bound IPv6 traffic \(::/0\) to the egress\-only internet gateway\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 2001:db8:1234:1a00:/56 | Local | 
| ::/0 | eigw\-id | 

## Work with egress\-only internet gateways<a name="egress-only-internet-gateway-working-with"></a>

The following tasks describe how to create an egress\-only \(outbound\) internet gateway for your private subnet, and to configure routing for the subnet\.

**Topics**
+ [Create an egress\-only internet gateway](#egress-only-internet-gateway-create)
+ [View your egress\-only internet gateway](#egress-only-internet-gateway-describe)
+ [Create a custom route table](#egress-only-internet-gateway-routing)
+ [Delete an egress\-only internet gateway](#egress-only-internet-gateway-delete)

### Create an egress\-only internet gateway<a name="egress-only-internet-gateway-create"></a>

You can create an egress\-only internet gateway for your VPC using the Amazon VPC console\.

**To create an egress\-only internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Egress Only Internet Gateways**\.

1. Choose **Create Egress Only Internet Gateway**\.

1. \(Optional\) Add or remove a tag\.

   \[Add a tag\] Choose **Add new tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose **Remove** to the right of the tag’s Key and Value\.

1. Select the VPC in which to create the egress\-only internet gateway\. 

1. Choose **Create**\.

### View your egress\-only internet gateway<a name="egress-only-internet-gateway-describe"></a>

You can view information about your egress\-only internet gateway in the Amazon VPC console\.

**To view information about an egress\-only internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Egress Only Internet Gateways**\.

1. Select the egress\-only internet gateway to view its information in the details pane\.

### Create a custom route table<a name="egress-only-internet-gateway-routing"></a>

To send traffic destined outside the VPC to the egress\-only internet gateway, you must create a custom route table, add a route that sends traffic to the gateway, and then associate it with your subnet\. 

**To create a custom route table and add a route to the egress\-only internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, **Create route table**\.

1. In the **Create route table** dialog box, optionally name your route table, then select your VPC and choose **Create route table**\.

1. Select the custom route table that you just created\. The details pane displays tabs for working with its routes, associations, and route propagation\.

1. On the **Routes** tab, choose **Edit routes**, specify `::/0` in the **Destination** box, select the egress\-only internet gateway ID in the **Target** list, and then choose **Save changes**\. 

1. On the **Subnet associations** tab, choose **Edit subnet associations**, and select the check box for the subnet\. Choose **Save**\.

Alternatively, you can add a route to an existing route table that's associated with your subnet\. Select your existing route table, and follow steps 5 and 6 above to add a route for the egress\-only internet gateway\.

For more information about route tables, see [Configure route tables](VPC_Route_Tables.md)\.

### Delete an egress\-only internet gateway<a name="egress-only-internet-gateway-delete"></a>

If you no longer need an egress\-only internet gateway, you can delete it\. Any route in a route table that points to the deleted egress\-only internet gateway remains in a `blackhole` status until you manually delete or update the route\.

**To delete an egress\-only internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Egress Only Internet Gateways**, and select the egress\-only internet gateway\.

1. Choose **Delete**\.

1. Choose **Delete Egress Only Internet Gateway** in the confirmation dialog box\.

## API and CLI overview<a name="egress-only-internet-gateway-api-cli"></a>

You can perform the tasks described on this page using the command line or an API\. For more information about the command line interfaces and a list of available API actions, see [Working with Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create an egress\-only internet gateway**
+ [create\-egress\-only\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-egress-only-internet-gateway.html) \(AWS CLI\)
+ [New\-EC2EgressOnlyInternetGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2EgressOnlyInternetGateway.html) \(AWS Tools for Windows PowerShell\)

**Describe an egress\-only internet gateway**
+ [describe\-egress\-only\-internet\-gateways](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-egress-only-internet-gateways.html) \(AWS CLI\)
+ [Get\-EC2EgressOnlyInternetGatewayList](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2EgressOnlyInternetGatewayList.html) \(AWS Tools for Windows PowerShell\)

**Delete an egress\-only internet gateway**
+ [delete\-egress\-only\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-egress-only-internet-gateway.html) \(AWS CLI\)
+ [Remove\-EC2EgressOnlyInternetGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2EgressOnlyInternetGateway.html) \(AWS Tools for Windows PowerShell\)
