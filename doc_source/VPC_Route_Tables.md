# Route Tables<a name="VPC_Route_Tables"></a>

A *route table* contains a set of rules, called *routes*, that are used to determine where network traffic is directed\.

Each subnet in your VPC must be associated with a route table; the table controls the routing for the subnet\. A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same route table\.

**Topics**
+ [Route Table Basics](#RouteTables)
+ [Route Priority](#route-tables-priority)
+ [Routing Options](#route-table-options)
+ [Working with Route Tables](#WorkWithRouteTables)
+ [API and Command Overview](#route-tables-api-cli)

## Route Table Basics<a name="RouteTables"></a>

The following are the basic things that you need to know about route tables:
+ Your VPC has an implicit router\.
+ Your VPC automatically comes with a main route table that you can modify\. 
+ You can create additional custom route tables for your VPC\.
+ Each subnet must be associated with a route table, which controls the routing for the subnet\. If you don't explicitly associate a subnet with a particular route table, the subnet is implicitly associated with the main route table\.
+ You cannot delete the main route table, but you can replace the main route table with a custom table that you've created \(so that this table is the default table each new subnet is associated with\)\.
+ Each route in a table specifies a destination CIDR and a target \(for example, traffic destined for the external corporate network 172\.16\.0\.0/12 is targeted for the virtual private gateway\)\. We use the most specific route that matches the traffic to determine how to route the traffic\.
  + CIDR blocks for IPv4 and IPv6 are treated separately\. For example, a route with a destination CIDR of 0\.0\.0\.0/0 \(all IPv4 addresses\) does not automatically include all IPv6 addresses\. You must create a route with a destination CIDR of ::/0 for all IPv6 addresses\.
+ Every route table contains a local route for communication within the VPC over IPv4\. If your VPC has more than one IPv4 CIDR block, your route tables contain a local route for each IPv4 CIDR block\. If you've associated an IPv6 CIDR block with your VPC, your route tables contain a local route for the IPv6 CIDR block\. You cannot modify or delete these routes\.
+ When you add an Internet gateway, an egress\-only Internet gateway, a virtual private gateway, a NAT device, a peering connection, or a VPC endpoint in your VPC, you must update the route table for any subnet that uses these gateways or connections\.
+ There is a limit on the number of route tables you can create per VPC, and the number of routes you can add per route table\. For more information, see [Amazon VPC Limits](VPC_Appendix_Limits.md)\.

### Main Route Tables<a name="RouteTableDetails"></a>

When you create a VPC, it automatically has a main route table\. On the **Route Tables** page in the Amazon VPC console, you can view the main route table for a VPC by looking for **Yes** in the **Main** column\. The main route table controls the routing for all subnets that are not explicitly associated with any other route table\. You can add, remove, and modify routes in the main route table\.

You can explicitly associate a subnet with the main route table, even if it's already implicitly associated\. You might do that if you change which table is the main route table, which changes the default for additional new subnets, or any subnets that are not explicitly associated with any other route table\. For more information, see [Replacing the Main Route Table](#Route_Replacing_Main_Table)\.

### Custom Route Tables<a name="CustomRouteTables"></a>

Your VPC can have route tables other than the default table\. One way to protect your VPC is to leave the main route table in its original default state \(with only the local route\), and explicitly associate each new subnet you create with one of the custom route tables you've created\. This ensures that you explicitly control how each subnet routes outbound traffic\. 

The following diagram shows the routing for a VPC with both an Internet gateway and a virtual private gateway, plus a public subnet and a VPN\-only subnet\. The main route table came with the VPC, and it also has a route for the VPN\-only subnet\. A custom route table is associated with the public subnet\. The custom route table has a route over the Internet gateway \(the destination is 0\.0\.0\.0/0, and the target is the Internet gateway\)\.

![\[Main route table and custom table\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/custom-route-table-diagram.png)

If you create a new subnet in this VPC, it's automatically associated with the main route table, which routes its traffic to the virtual private gateway\. If you were to set up the reverse configuration \(the main route table with the route to the Internet gateway, and the custom route table with the route to the virtual private gateway\), then a new subnet automatically has a route to the Internet gateway\. 

### Route Table Association<a name="route-table-assocation"></a>

The VPC console shows the number of subnets explicitly associated with each route table, and provides information about subnets that are implicitly associated with the main route table\. For more information, see [Determining Which Subnets Are Explicitly Associated with a Table](#Route_Which_Associations)\)\.

Subnets can be implicitly or explicitly associated with the main route table\. Subnets typically won't have an explicit association to the main route table, although it might happen temporarily if you're replacing the main route table\.

You might want to make changes to the main route table, but to avoid any disruption to your traffic, you can first test the route changes using a custom route table\. After you're satisfied with the testing, you then replace the main route table with the new custom table\.

The following diagram shows a VPC with two subnets that are implicitly associated with the main route table \(Route Table A\), and a custom route table \(Route Table B\) that isn't associated with any subnets\.

![\[Replace main table: Start\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/routing-Route_Replace_Main_Start-diagram.png)

You can create an explicit association between Subnet 2 and Route Table B\.

![\[Replace main table: New table\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/routing-Route_Replace_Main_New_Table-diagram.png)

After you've tested Route Table B, you can make it the main route table\. Note that Subnet 2 still has an explicit association with Route Table B, and Subnet 1 has an implicit association with Route Table B because it is the new main route table\. Route Table A is no longer in use\.

![\[Replace main table: Replace\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/routing-Route_Replace_Main_Replace-diagram.png)

If you disassociate Subnet 2 from Route Table B, there's still an implicit association between Subnet 2 and Route Table B\. If you no longer need Route Table A, you can delete it\.

![\[Replace main table: Disassociate\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/routing-Route_Replace_Main_Disassociate-diagram.png)

## Route Priority<a name="route-tables-priority"></a>

We use the most specific route in your route table that matches the traffic to determine how to route the traffic \(longest prefix match\)\. 

Routes to IPv4 and IPv6 addresses or CIDR blocks are independent of each other; we use the most specific route that matches either IPv4 traffic or IPv6 traffic to determine how to route the traffic\. 

For example, the following route table has a route for IPv4 Internet traffic \(`0.0.0.0/0`\) that points to an Internet gateway, and a route for `172.31.0.0/16` IPv4 traffic that points to a peering connection \(`pcx-1a2b3c4d`\)\. Any traffic from the subnet that's destined for the `172.31.0.0/16` IP address range uses the peering connection, because this route is more specific than the route for Internet gateway\. Any traffic destined for a target within the VPC \(`10.0.0.0/16`\) is covered by the `Local` route, and therefore routed within the VPC\. All other traffic from the subnet uses the Internet gateway\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 172\.31\.0\.0/16 | pcx\-1a2b3c4d | 
| 0\.0\.0\.0/0 | igw\-11aa22bb | 

If you've attached a virtual private gateway to your VPC and enabled route propagation on your route table, routes representing your VPN connection automatically appear as propagated routes in your route table\. For more information, see [Route Tables and VPN Route Priority](VPC_VPN.md#vpn-route-priority)\.

In this example, an IPv6 CIDR block is associated with your VPC\. In your route table, IPv6 traffic destined for within the VPC \(`2001:db8:1234:1a00::/56`\) is covered by the `Local` route, and is routed within the VPC\. The route table also has a route for `172.31.0.0/16` IPv4 traffic that points to a peering connection \(`pcx-1a2b3c4d`\), a route for all IPv4 traffic \(`0.0.0.0/0`\) that points to an Internet gateway, and a route for all IPv6 traffic \(`::/0`\) that points to an egress\-only Internet gateway\. IPv4 and IPv6 traffic are treated separately; therefore, all IPv6 traffic \(except for traffic within the VPC\) is routed to the egress\-only Internet gateway\. 


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 2001:db8:1234:1a00::/56 | Local | 
| 172\.31\.0\.0/16 | pcx\-1a2b3c4d | 
| 0\.0\.0\.0/0 | igw\-11aa22bb | 
| ::/0 | eigw\-aabb1122 | 

## Routing Options<a name="route-table-options"></a>

The following topics explain routing for specific gateways or connections in your VPC\. 

**Topics**
+ [Route Tables for an Internet Gateway](#route-tables-internet-gateway)
+ [Route Tables for a NAT Device](#route-tables-nat)
+ [Route Tables for a Virtual Private Gateway](#route-tables-vgw)
+ [Route Tables for a VPC Peering Connection](#route-tables-vpc-peering)
+ [Route Tables for ClassicLink](#route-tables-classiclink)
+ [Route Tables for a VPC Endpoint](#route-tables-vpce)
+ [Route Tables for an Egress\-Only Internet Gateway](#route-tables-eigw)

### Route Tables for an Internet Gateway<a name="route-tables-internet-gateway"></a>

You can make a subnet a public subnet by adding a route to an Internet gateway\. To do this, create and attach an Internet gateway to your VPC, and then add a route with a destination of `0.0.0.0/0` for IPv4 traffic or `::/0` for IPv6 traffic, and a target of the Internet gateway ID \(`igw-xxxxxxxx`\)\. For more information, see [Internet Gateways](VPC_Internet_Gateway.md)\.

### Route Tables for a NAT Device<a name="route-tables-nat"></a>

To enable instances in a private subnet to connect to the Internet, you can create a NAT gateway or launch a NAT instance in a public subnet, and then add a route for the private subnet that routes IPv4 Internet traffic \(`0.0.0.0/0`\) to the NAT device\. For more information, see [NAT Gateways](vpc-nat-gateway.md) and [NAT Instances](VPC_NAT_Instance.md)\. NAT devices cannot be used for IPv6 traffic\.

### Route Tables for a Virtual Private Gateway<a name="route-tables-vgw"></a>

You can use an AWS managed VPN connection to enable instances in your VPC to communicate with your own network\. To do this, create and attach a virtual private gateway to your VPC, and then add a route with the destination of your network and a target of the virtual private gateway \(`vgw-xxxxxxxx`\)\. You can then create and configure your VPN connection\. For more information, see [AWS Managed VPN Connections](VPC_VPN.md)\.

We currently do not support IPv6 traffic over a VPN connection\. However, we support IPv6 traffic routed through a virtual private gateway to an AWS Direct Connect connection\. For more information, see the [AWS Direct Connect User Guide](http://docs.aws.amazon.com/directconnect/latest/UserGuide/)\.

### Route Tables for a VPC Peering Connection<a name="route-tables-vpc-peering"></a>

A VPC peering connection is a networking connection between two VPCs that allows you to route traffic between them using private IPv4 addresses\. Instances in either VPC can communicate with each other as if they are part of the same network\. 

To enable the routing of traffic between VPCs in a VPC peering connection, you must add a route to one or more of your VPC route tables that points to the VPC peering connection to access all or part of the CIDR block of the other VPC in the peering connection\. Similarly, the owner of the other VPC must add a route to their VPC route table to route traffic back to your VPC\.

For example, you have a VPC peering connection \(`pcx-1a2b1a2b`\) between two VPCs, with the following information:
+ VPC A: `vpc-1111aaaa`, CIDR block is 10\.0\.0\.0/16
+ VPC B: `vpc-2222bbbb`, CIDR block is 172\.31\.0\.0/16

To enable traffic between the VPCs and allow access to the entire IPv4 CIDR block of either VPC, the VPC A route table is configured as follows\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 172\.31\.0\.0/16 | pcx\-1a2b1a2b | 

The VPC B route table is configured as follows\.


| Destination | Target | 
| --- | --- | 
| 172\.31\.0\.0/16 | Local | 
| 10\.0\.0\.0/16 | pcx\-1a2b1a2b | 

Your VPC peering connection can also support IPv6 communication between instances in the VPCs, provided the VPCs and instances are enabled for IPv6 communication\. For more information, see [VPCs and Subnets](VPC_Subnets.md)\. To enable the routing of IPv6 traffic between VPCs, you must add a route to your route table that points to the VPC peering connection to access all or part of the IPv6 CIDR block of the peer VPC\.

For example, using the same VPC peering connection \(`pcx-1a2b1a2b`\) above, assume the VPCs have the following information:
+ VPC A: IPv6 CIDR block is `2001:db8:1234:1a00::/56`
+ VPC B: IPv6 CIDR block is `2001:db8:5678:2b00::/56`

To enable IPv6 communication over the VPC peering connection, add the following route to the route table for VPC A:


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 172\.31\.0\.0/16 | pcx\-1a2b1a2b | 
| 2001:db8:5678:2b00::/56 | pcx\-1a2b1a2b | 

Add the following route to the route table for VPC B:


| Destination | Target | 
| --- | --- | 
| 172\.31\.0\.0/16 | Local | 
| 10\.0\.0\.0/16 | pcx\-1a2b1a2b | 
| 2001:db8:1234:1a00::/56 | pcx\-1a2b1a2b | 

For more information about VPC peering connections, see the [Amazon VPC Peering Guide](http://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/)\.

### Route Tables for ClassicLink<a name="route-tables-classiclink"></a>

ClassicLink is a feature that enables you to link an EC2\-Classic instance to a VPC, allowing communication between the EC2\-Classic instance and instances in the VPC using private IPv4 addresses\. For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\.

When you enable a VPC for ClassicLink, a route is added to all of the VPC route tables with a destination of `10.0.0.0/8` and a target of `local`\. This allows communication between instances in the VPC and any EC2\-Classic instances that are then linked to the VPC\. If you add another route table to a ClassicLink\-enabled VPC, it automatically receives a route with a destination of `10.0.0.0/8` and a target of `local`\. If you disable ClassicLink for a VPC, this route is automatically deleted in all the VPC route tables\.

If any of your VPC route tables have existing routes for address ranges within the `10.0.0.0/8` CIDR, then you cannot enable your VPC for ClassicLink\. This does not include local routes for VPCs with `10.0.0.0/16` and `10.1.0.0/16` IP address ranges\. 

If you've already enabled a VPC for ClassicLink, you may not be able to add any more specific routes to your route tables for the `10.0.0.0/8` IP address range\.

If you modify a VPC peering connection to enable communication between instances in your VPC and an EC2\-Classic instance that's linked to the peer VPC, a static route is automatically added to your route tables with a destination of `10.0.0.0/8` and a target of `local`\. If you modify a VPC peering connection to enable communication between a local EC2\-Classic instance linked to your VPC and instances in a peer VPC, you must manually add a route to your main route table with a destination of the peer VPC CIDR block, and a target of the VPC peering connection\. The EC2\-Classic instance relies on the main route table for routing to the peer VPC\. For more information, see [Configurations With ClassicLink](http://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/peering-configurations-classiclink.html) in the *Amazon VPC Peering Guide*\.

### Route Tables for a VPC Endpoint<a name="route-tables-vpce"></a>

A VPC endpoint enables you to create a private connection between your VPC and another AWS service\. When you create an endpoint, you specify the route tables in your VPC that are used by the endpoint\. A route is automatically added to each of the route tables with a destination that specifies the prefix list ID of the service \(`pl-xxxxxxxx`\), and a target with the endpoint ID \(`vpce-xxxxxxxx`\)\. You cannot explicitly delete or modify the endpoint route, but you can change the route tables that are used by the endpoint\.

For more information about routing for endpoints, and the implications for routes to AWS services, see [Routing for Gateway Endpoints](vpce-gateway.md#vpc-endpoints-routing)\.

### Route Tables for an Egress\-Only Internet Gateway<a name="route-tables-eigw"></a>

You can create an egress\-only Internet gateway for your VPC to enable instances in a private subnet to initiate outbound communication to the Internet, but prevent the Internet from initiating connections with the instances\. An egress\-only Internet gateway is used for IPv6 traffic only\. To configure routing for an egress\-only Internet gateway, add a route for the private subnet that routes IPv6 Internet traffic \(`::/0`\) to the egress\-only Internet gateway\. For more information, see [Egress\-Only Internet Gateways](egress-only-internet-gateway.md)\.

## Working with Route Tables<a name="WorkWithRouteTables"></a>

This section shows you how to work with route tables\.

**Note**  
When you use the wizard in the console to create a VPC with a gateway, the wizard automatically updates the route tables to use the gateway\. If you're using the command line tools or API to set up your VPC, you must update the route tables yourself\.

**Topics**
+ [Determining Which Route Table a Subnet Is Associated With](#SubnetRouteTables)
+ [Determining Which Subnets Are Explicitly Associated with a Table](#Route_Which_Associations)
+ [Creating a Custom Route Table](#CustomRouteTable)
+ [Adding and Removing Routes from a Route Table](#AddRemoveRoutes)
+ [Enabling and Disabling Route Propagation](#EnableDisableRouteProp)
+ [Associating a Subnet with a Route Table](#AssociateSubnet)
+ [Changing a Subnet Route Table](#ChangeRouteTable)
+ [Disassociating a Subnet from a Route Table](#DisassociateSubnetRouteTable)
+ [Replacing the Main Route Table](#Route_Replacing_Main_Table)
+ [Deleting a Route Table](#DeleteRouteTable)

### Determining Which Route Table a Subnet Is Associated With<a name="SubnetRouteTables"></a>

You can determine which route table a subnet is associated with by looking at the subnet details in the Amazon VPC console\.

**To determine which route table a subnet is associated with**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. The subnet details are displayed in the **Summary** tab\. Choose the **Route Table** tab to view the route table ID and its routes\. If it's the main route table, the console doesn't indicate whether the association is implicit or explicit\. To determine if the association to the main route table is explicit, see [Determining Which Subnets Are Explicitly Associated with a Table](#Route_Which_Associations)\.

### Determining Which Subnets Are Explicitly Associated with a Table<a name="Route_Which_Associations"></a>

You can determine how many and which subnets are explicitly associated with a route table\. 

The main route table can have explicit and implicit associations\. Custom route tables have only explicit associations\.

Subnets that aren't explicitly associated with any route table have an implicit association with the main route table\. You can explicitly associate a subnet with the main route table \(for an example of why you might do that, see [Replacing the Main Route Table](#Route_Replacing_Main_Table)\)\.

**To determine which subnets are explicitly associated**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. View the **Explicitly Associated With** column to determine the number of explicitly associated subnets\.

1. Select the required route table\.

1. Choose the **Subnet Associations** tab in the details pane\. The subnets explicitly associated with the table are listed on the tab\. Any subnets not associated with any route table \(and thus implicitly associated with the main route table\) are also listed\.

### Creating a Custom Route Table<a name="CustomRouteTable"></a>

You can create a custom route table for your VPC using the Amazon VPC console\. 

**To create a custom route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Choose **Create Route Table**\.

1. In the **Create Route Table** dialog box, you can optionally name your route table for **Name tag**\. Doing so creates a tag with a key of `Name` and a value that you specify\. Select your VPC for **VPC**, and then choose **Yes, Create**\.

### Adding and Removing Routes from a Route Table<a name="AddRemoveRoutes"></a>

You can add, delete, and modify routes in your route tables\. You can only modify routes that you've added\.

**To modify or add a route to a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. In the **Routes** tab, choose **Edit**\.

1. To modify an existing route, replace the destination CIDR block or a single IP address for **Destination**, and then select a target for **Target**\. Choose **Add another route**, **Save**\.

**To delete a route from a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. In the **Routes** tab, choose **Edit**, and then choose **Remove** for the route to delete\.

1. Choose **Save** when you're done\.

### Enabling and Disabling Route Propagation<a name="EnableDisableRouteProp"></a>

Route propagation allows a virtual private gateway to automatically propagate routes to the route tables so that you don't need to manually enter VPN routes to your route tables\. You can enable or disable route propagation\.

For more information about VPN routing options, see [VPN Routing Options](VPC_VPN.md#VPNRoutingTypes)\.

**To enable route propagation**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. On the **Route Propagation** tab, choose **Edit**\.

1. Select the **Propagate** check box next to the virtual private gateway, and then choose **Save**\.

**To disable route propagation**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. On the **Route Propagation** tab, choose **Edit**\. 

1. Clear the **Propagate** check box, and then choose **Save**\.

### Associating a Subnet with a Route Table<a name="AssociateSubnet"></a>

To apply route table routes to a particular subnet, you must associate the route table with the subnet\. A route table can be associated with multiple subnets; however, a subnet can only be associated with one route table at a time\. Any subnet not explicitly associated with a table is implicitly associated with the main route table by default\.

**To associate a route table with a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. On the **Subnet Associations** tab, choose **Edit**\.

1. Select the **Associate** check box for the subnet to associate with the route table, and then choose **Save**\.

### Changing a Subnet Route Table<a name="ChangeRouteTable"></a>

You can change which route table a subnet is associated with\. 

**To change a subnet route table association**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**, and then select the subnet\.

1. In the **Route Table** tab, choose **Edit**\.

1. Select the new route table with which to associate the subnet from the **Change to** list, and then choose **Save**\.

### Disassociating a Subnet from a Route Table<a name="DisassociateSubnetRouteTable"></a>

You can disassociate a subnet from a route table\. Until you associate the subnet with another route table, it's implicitly associated with the main route table\.

**To disassociate a subnet from a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. In the **Subnet Associations** tab, choose **Edit**\.

1. Clear the **Associate** check box for the subnet, and then choose **Save**\.

### Replacing the Main Route Table<a name="Route_Replacing_Main_Table"></a>

You can change which route table is the main route table in your VPC\. 

**To replace the main route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the route table that should be the new main route table, and then choose **Set as Main Table**\.

1. In the confirmation dialog box, choose **Yes, Set**\.

The following procedure describes how to remove an explicit association between a subnet and the main route table\. The result is an implicit association between the subnet and the main route table\. The process is the same as disassociating any subnet from any route table\.

**To remove an explicit association with the main route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. In the **Subnet Associations** tab, choose **Edit**\.

1. Clear the **Associate** check box for the subnet, and then choose **Save**\.

### Deleting a Route Table<a name="DeleteRouteTable"></a>

You can delete a route table only if there are no subnets associated with it\. You can't delete the main route table\.

**To delete a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the route table, and then choose **Delete Route Table**\.

1. In the confirmation dialog box, choose **Yes, Delete**\.

## API and Command Overview<a name="route-tables-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interface and a list of available API operations, see [Accessing Amazon VPC](VPC_Introduction.md#VPCInterfaces)\.

**Create a custom route table**
+ [create\-route\-table](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-route-table.html) \(AWS CLI\)
+ [New\-EC2RouteTable](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

**Add a route to a route table**
+ [create\-route](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) \(AWS CLI\)
+ [New\-EC2Route](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**Associate a subnet with a route table**
+ [associate\-route\-table](http://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) \(AWS CLI\)
+ [Register\-EC2RouteTable](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

**Describe one or more route tables**
+ [describe\-route\-tables](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-route-tables.html) \(AWS CLI\)
+ [Get\-EC2RouteTable](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

**Delete a route from a route table**
+ [delete\-route](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route.html) \(AWS CLI\)
+ [Remove\-EC2Route](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**Replace an existing route in a route table**
+ [replace\-route](http://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route.html) \(AWS CLI\)
+ [Set\-EC2Route](http://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**Disassociate a subnet from a route table**
+ [disassociate\-route\-table](http://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-route-table.html) \(AWS CLI\)
+ [Unregister\-EC2RouteTable](http://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

**Change the route table associated with a subnet**
+ [replace\-route\-table\-association](http://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route-table-association.html) \(AWS CLI\)
+ [Set\-EC2RouteTableAssociation](http://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2RouteTableAssociation.html) \(AWS Tools for Windows PowerShell\)

**Create a static route associated with a VPN connection**
+ [create\-vpn\-connection\-route](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpn-connection-route.html) \(AWS CLI\)
+ [New\-EC2VpnConnectionRoute](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpnConnectionRoute.html) \(AWS Tools for Windows PowerShell\)

**Delete a static route associated with a VPN connection**
+ [delete\-vpn\-connection\-route](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpn-connection-route.html) \(AWS CLI\)
+ [Remove\-EC2VpnConnectionRoute](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2VpnConnectionRoute.html) \(AWS Tools for Windows PowerShell\)

**Enable a virtual private gateway \(VGW\) to propagate routes to the routing tables of a VPC**
+ [enable\-vgw\-route\-propagation](http://docs.aws.amazon.com/cli/latest/reference/ec2/enable-vgw-route-propagation.html) \(AWS CLI\)
+ [Enable\-EC2VgwRoutePropagation](http://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VgwRoutePropagation.html) \(AWS Tools for Windows PowerShell\)

**Disable a VGW from propagating routes to the routing tables of a VPC**
+ [disable\-vgw\-route\-propagation](http://docs.aws.amazon.com/cli/latest/reference/ec2/disable-vgw-route-propagation.html) \(AWS CLI\)
+ [Disable\-EC2VgwRoutePropagation](http://docs.aws.amazon.com/powershell/latest/reference/items/Disable-EC2VgwRoutePropagation.html) \(AWS Tools for Windows PowerShell\)

**Delete a route table**
+ [delete\-route\-table](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route-table.html) \(AWS CLI\)
+ [Remove\-EC2RouteTable](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)