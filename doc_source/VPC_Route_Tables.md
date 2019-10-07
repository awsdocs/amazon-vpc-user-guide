# Route Tables<a name="VPC_Route_Tables"></a>

A *route table* contains a set of rules, called *routes*, that are used to determine where network traffic from your subnet is directed\.

**Topics**
+ [Route Table Concepts](#RouteTables)
+ [How Route Tables Work](#how-route-tables-work)
+ [Route Priority](#route-tables-priority)
+ [Routing Options](route-table-options.md)
+ [Working with Route Tables](WorkWithRouteTables.md)

## Route Table Concepts<a name="RouteTables"></a>

The following are the key concepts for route tables\.
+ **Main route table**—The route table that automatically comes with your VPC\. It controls the routing for all subnets that are not explicitly associated with any other route table\.
+ **Custom route table**—A route table that you create for your VPC\.
+ **Route table association**—The association between a route table and subnet\. The route table that's associated with a subnet controls the routing for that subnet\.
+ **Destination**—The destination CIDR where you want traffic from your subnet to go\. For example, an external corporate network with a `172.16.0.0/12` CIDR\.
+ **Target**—The target through which to send the destination traffic; for example, an internet gateway\.
+ **Local route**—A default route for communication within the VPC\.

## How Route Tables Work<a name="how-route-tables-work"></a>

Your VPC has an implicit router, and you use route tables to control where network traffic is directed\. Each subnet in your VPC must be associated with a route table, which controls the routing for the subnet\. You can explicitly associate a subnet with a particular route table\. Otherwise, the subnet is implicitly associated with the main route table\. A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same route table\.

There is a limit on the number of route tables you can create per VPC\. There is also a limit on the number of routes you can add per route table\. For more information, see [Amazon VPC Limits](amazon-vpc-limits.md)\.

**Topics**
+ [Routes](#route-table-routes)
+ [Main Route Table](#RouteTableDetails)
+ [Custom Route Tables](#CustomRouteTables)
+ [Route Table Association](#route-table-assocation)

### Routes<a name="route-table-routes"></a>

Each route in a table specifies a destination and a target\. For example, to enable your subnet to access the internet through an internet gateway, add the following route to your route table\.


| Destination | Target | 
| --- | --- | 
| 0\.0\.0\.0/0 | igw\-12345678901234567 | 

The destination for the route is `0.0.0.0/0`, which represents all IPv4 addresses\. The target is the internet gateway that's attached to your VPC\.

CIDR blocks for IPv4 and IPv6 are treated separately\. For example, a route with a destination CIDR of `0.0.0.0/0` does not automatically include all IPv6 addresses\. You must create a route with a destination CIDR of `::/0` for all IPv6 addresses\.

Every route table contains a local route for communication within the VPC\. This route is added by default to all route tables\. If your VPC has more than one IPv4 CIDR block, your route tables contain a local route for each IPv4 CIDR block\. If you've associated an IPv6 CIDR block with your VPC, your route tables contain a local route for the IPv6 CIDR block\. You cannot modify or delete these routes\.

If your route table has multiple routes, we use the most specific route that matches the traffic \(longest prefix match\) to determine how to route the traffic\.

In the following example, an IPv6 CIDR block is associated with your VPC\. In your route table:
+ IPv6 traffic destined to remain within the VPC \(`2001:db8:1234:1a00::/56`\) is covered by the `Local` route, and is routed within the VPC\.
+ IPv4 and IPv6 traffic are treated separately; therefore, all IPv6 traffic \(except for traffic within the VPC\) is routed to the egress\-only internet gateway\. 
+ There is a route for `172.31.0.0/16` IPv4 traffic that points to a peering connection\.
+ There is a route for all IPv4 traffic \(`0.0.0.0/0`\) that points to an internet gateway\.
+ There is a route for all IPv6 traffic \(`::/0`\) that points to an egress\-only internet gateway\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 2001:db8:1234:1a00::/56 | Local | 
| 172\.31\.0\.0/16 | pcx\-11223344556677889 | 
| 0\.0\.0\.0/0 | igw\-12345678901234567 | 
| ::/0 | eigw\-aabbccddee1122334 | 

### Main Route Table<a name="RouteTableDetails"></a>

When you create a VPC, it automatically has a main route table\. The main route table controls the routing for all subnets that are not explicitly associated with any other route table\. On the **Route Tables** page in the Amazon VPC console, you can view the main route table for a VPC by looking for **Yes** in the **Main** column\. 

By default, when you create a nondefault VPC, the main route table contains only a local route\. When you use the VPC wizard in the console to create a nondefault VPC with a NAT gateway or virtual private gateway, the wizard automatically adds routes to the main route table for those gateways\.

You can add, remove, and modify routes in the main route table\. You cannot delete the main route table, but you can replace the main route table with a custom route table that you've created\.

You can explicitly associate a subnet with the main route table, even if it's already implicitly associated\. You might want to do that if you change which table is the main route table\. When you change which table is the main route table, it also changes the default for additional new subnets, or for any subnets that are not explicitly associated with any other route table\. For more information, see [Replacing the Main Route Table](WorkWithRouteTables.md#Route_Replacing_Main_Table)\.

### Custom Route Tables<a name="CustomRouteTables"></a>

By default, a custom route table is empty and you add routes as needed\. When you use the VPC wizard in the console to create a VPC with an internet gateway, the wizard creates a custom route table and adds a route to the internet gateway\. One way to protect your VPC is to leave the main route table in its original default state\. Then, explicitly associate each new subnet that you create with one of the custom route tables you've created\. This ensures that you explicitly control how each subnet routes traffic\. 

You can add, remove, and modify routes in a custom route table\. You can delete a custom route table only if there are no subnets associated with it\.

### Route Table Association<a name="route-table-assocation"></a>

A subnet can be explicitly associated with custom route table, or implicitly or explicitly associated with the main route table\. For more information about viewing your subnet and route table associations, see [Determining Which Subnets Are Explicitly Associated with a Table](WorkWithRouteTables.md#Route_Which_Associations)\.

**Example 1: Implicit and Explicit Association**

The following diagram shows the routing for a VPC with an internet gateway, a virtual private gateway, a public subnet, and a VPN\-only subnet\. The main route table has a route to the virtual private gateway\. A custom route table is explicitly associated with the public subnet\. The custom route table has a route to the internet \(`0.0.0.0/0`\) through the internet gateway\.

![\[Main route table and custom table\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/custom-route-table-diagram.png)

If you create a new subnet in this VPC, it's automatically implicitly associated with the main route table, which routes traffic to the virtual private gateway\. If you set up the reverse configuration \(where the main route table has the route to the internet gateway, and the custom route table has the route to the virtual private gateway\), then a new subnet automatically has a route to the internet gateway\. 

**Example 2: Replacing the Main Route Table**

You might want to make changes to the main route table\. To avoid any disruption to your traffic, we recommend that you first test the route changes using a custom route table\. After you're satisfied with the testing, you can replace the main route table with the new custom table\.

The following diagram shows a VPC with two subnets that are implicitly associated with the main route table \(Route Table A\), and a custom route table \(Route Table B\) that isn't associated with any subnets\.

![\[Replace main table: Start\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/routing-Route_Replace_Main_Start-diagram.png)

You can create an explicit association between Subnet 2 and Route Table B\.

![\[Replace main table: New table\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/routing-Route_Replace_Main_New_Table-diagram.png)

After you've tested Route Table B, you can make it the main route table\. Note that Subnet 2 still has an explicit association with Route Table B, and Subnet 1 has an implicit association with Route Table B because it is the new main route table\. Route Table A is no longer in use\.

![\[Replace main table: Replace\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/routing-Route_Replace_Main_Replace-diagram.png)

If you disassociate Subnet 2 from Route Table B, there's still an implicit association between Subnet 2 and Route Table B\. If you no longer need Route Table A, you can delete it\.

![\[Replace main table: Disassociate\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/routing-Route_Replace_Main_Disassociate-diagram.png)

## Route Priority<a name="route-tables-priority"></a>

We use the most specific route in your route table that matches the traffic to determine how to route the traffic \(longest prefix match\)\. 

Routes to IPv4 and IPv6 addresses or CIDR blocks are independent of each other\. We use the most specific route that matches either IPv4 traffic or IPv6 traffic to determine how to route the traffic\. 

For example, the following route table has a route for IPv4 internet traffic \(`0.0.0.0/0`\) that points to an internet gateway, and a route for `172.31.0.0/16` IPv4 traffic that points to a peering connection \(`pcx-11223344556677889`\)\. Any traffic from the subnet that's destined for the `172.31.0.0/16` IP address range uses the peering connection, because this route is more specific than the route for internet gateway\. Any traffic destined for a target within the VPC \(`10.0.0.0/16`\) is covered by the `Local` route, and therefore is routed within the VPC\. All other traffic from the subnet uses the internet gateway\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 172\.31\.0\.0/16 | pcx\-11223344556677889 | 
| 0\.0\.0\.0/0 | igw\-12345678901234567 | 

If you've attached a virtual private gateway to your VPC and enabled route propagation on your route table, routes representing your Site\-to\-Site VPN connection automatically appear as propagated routes in your route table\. If the propagated routes overlap with static routes and longest prefix match cannot be applied, the static routes take priority over the propagated routes\. For more information, see [Route Tables and VPN Route Priority](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html#vpn-route-priority) in the *AWS Site\-to\-Site VPN User Guide*\.

In this example, your route table has a static route to an internet gateway \(which you added manually\), and a propagated route to a virtual private gateway\. Both routes have a destination of `172.31.0.0/24`\. In this case, all traffic destined for `172.31.0.0/24` is routed to the internet gateway — it is a static route and therefore takes priority over the propagated route\. 


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 172\.31\.0\.0/24 | vgw\-11223344556677889 \(propagated\) | 
| 172\.31\.0\.0/24 | igw\-12345678901234567 \(static\) | 

The same rule applies if your route table contains a static route to any of the following: 
+ NAT gateway
+ Network interface
+ Instance ID
+ Gateway VPC endpoint
+ Transit gateway
+ VPC peering connection

If the destinations for the static and propagated routes are the same, the static route takes priority\.