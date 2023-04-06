# Configure route tables<a name="VPC_Route_Tables"></a>

A *route table* contains a set of rules, called *routes*, that determine where network traffic from your subnet or gateway is directed\.

**Topics**
+ [Route table concepts](#RouteTables)
+ [Subnet route tables](#subnet-route-tables)
+ [Gateway route tables](#gateway-route-tables)
+ [Route priority](#route-tables-priority)
+ [Route table quotas](#route-table-quotas)
+ [Example routing options](route-table-options.md)
+ [Work with route tables](WorkWithRouteTables.md)
+ [Middlebox routing wizard](middlebox-routing-console.md)

## Route table concepts<a name="RouteTables"></a>

The following are the key concepts for route tables\.
+ **Main route table**—The route table that automatically comes with your VPC\. It controls the routing for all subnets that are not explicitly associated with any other route table\.
+ **Custom route table**—A route table that you create for your VPC\.
+ **Destination**—The range of IP addresses where you want traffic to go \(destination CIDR\)\. For example, an external corporate network with the CIDR `172.16.0.0/12`\.
+ **Target**—The gateway, network interface, or connection through which to send the destination traffic; for example, an internet gateway\.
+ **Route table association**—The association between a route table and a subnet, internet gateway, or virtual private gateway\.
+ **Subnet route table**—A route table that's associated with a subnet\.
+ **Local route**—A default route for communication within the VPC\.
+ **Propagation**—If you've attached a virtual private gateway to your VPC and enable route propagation, we automatically add routes for your VPN connection to your subnet route tables\. This means that you don't need to manually add or remove VPN routes\. For more information, see [Site\-to\-Site VPN routing options](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html) in the *Site\-to\-Site VPN User Guide*\.
+ **Gateway route table**—A route table that's associated with an internet gateway or virtual private gateway\.
+ **Edge association**—A route table that you use to route inbound VPC traffic to an appliance\. You associate a route table with the internet gateway or virtual private gateway, and specify the network interface of your appliance as the target for VPC traffic\.
+ **Transit gateway route table**—A route table that's associated with a transit gateway\. For more information, see [Transit gateway route tables](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-route-tables.html) in *Amazon VPC Transit Gateways*\.
+ **Local gateway route table**—A route table that's associated with an Outposts local gateway\. For more information, see [Local gateways](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-local-gateways.html) in the *AWS Outposts User Guide*\.

## Subnet route tables<a name="subnet-route-tables"></a>

Your VPC has an implicit router, and you use route tables to control where network traffic is directed\. Each subnet in your VPC must be associated with a route table, which controls the routing for the subnet \(subnet route table\)\. You can explicitly associate a subnet with a particular route table\. Otherwise, the subnet is implicitly associated with the main route table\. A subnet can only be associated with one route table at a time, but you can associate multiple subnets with the same subnet route table\.

**Topics**
+ [Routes](#route-table-routes)
+ [Main route table](#main-route-table)
+ [Custom route tables](#custom-route-tables)
+ [Subnet route table association](#route-table-assocation)

### Routes<a name="route-table-routes"></a>

Each route in a table specifies a destination and a target\. For example, to enable your subnet to access the internet through an internet gateway, add the following route to your subnet route table\. The destination for the route is `0.0.0.0/0`, which represents all IPv4 addresses\. The target is the internet gateway that's attached to your VPC\.


| Destination | Target | 
| --- | --- | 
| 0\.0\.0\.0/0 | igw\-id | 

CIDR blocks for IPv4 and IPv6 are treated separately\. For example, a route with a destination CIDR of `0.0.0.0/0` does not automatically include all IPv6 addresses\. You must create a route with a destination CIDR of `::/0` for all IPv6 addresses\.

If you frequently reference the same set of CIDR blocks across your AWS resources, you can create a [customer\-managed prefix list](managed-prefix-lists.md) to group them together\. You can then specify the prefix list as the destination in your route table entry\. 

Every route table contains a local route for communication within the VPC\. This route is added by default to all route tables\. If your VPC has more than one IPv4 CIDR block, your route tables contain a local route for each IPv4 CIDR block\. If you've associated an IPv6 CIDR block with your VPC, your route tables contain a local route for the IPv6 CIDR block\. You can [replace or restore](WorkWithRouteTables.md#replace-local-route-target) the target of each local route as needed\.<a name="route-table-rule-considerations"></a>

**Rules and considerations**
+ You can add a route to your route tables that is more specific than the local route\. The destination must match the entire IPv4 or IPv6 CIDR block of a subnet in your VPC\. The target must be a NAT gateway, network interface, or Gateway Load Balancer endpoint\.
+ If your route table has multiple routes, we use the most specific route that matches the traffic \(longest prefix match\) to determine how to route the traffic\.
+ You can't add routes to IPv4 addresses that are an exact match or a subset of the following range: 169\.254\.168\.0/22\. This range is within the link\-local address space and is reserved for use by AWS services\. For example, Amazon EC2 uses addresses in this range for services that are accessible only from EC2 instances, such as the Instance Metadata Service \(IMDS\) and the Amazon DNS server\. You can use a CIDR block that is larger than but overlaps 169\.254\.168\.0/22, but packets destined for addresses in 169\.254\.168\.0/22 will not be forwarded\.
+ You can't add routes to IPv6 addresses that are an exact match or a subset of the following range: fd00:ec2::/32\. This range is within the unique local address \(ULA\) space and is reserved for use by AWS services\. For example, Amazon EC2 uses addresses in this range for services that are accessible only from EC2 instances, such as the Instance Metadata Service \(IMDS\) and the Amazon DNS server\. You can use a CIDR block that is larger than but overlaps fd00:ec2::/32, but packets destined for addresses in fd00:ec2::/32 will not be forwarded\.
+ You can add middlebox appliances to the routing paths for your VPC\. For more information, see [Routing for a middlebox appliance](route-table-options.md#route-tables-appliance-routing)\.

**Example**

In the following example, suppose that the VPC has both an IPv4 CIDR block and an IPv6 CIDR block\. In the route table:
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

### Main route table<a name="main-route-table"></a>

When you create a VPC, it automatically has a main route table\. When a subnet does not have an explicit routing table associated with it, the main routing table is used by default\. On the **Route tables** page in the Amazon VPC console, you can view the main route table for a VPC by looking for **Yes** in the **Main** column\. 

By default, when you create a nondefault VPC, the main route table contains only a local route\. If you [Create a VPC](create-vpc.md) and choose a NAT gateway, Amazon VPC automatically adds routes to the main route table for the gateways\.

The following rules apply to the main route table:
+ You cannot delete the main route table\.
+ You cannot set a gateway route table as the main route table\.
+ You can replace the main route table with a custom subnet route table\.
+ You can add, remove, and modify routes in the main route table\.
+ You can explicitly associate a subnet with the main route table, even if it's already implicitly associated\. 

  You might want to do that if you change which table is the main route table\. When you change which table is the main route table, it also changes the default for additional new subnets, or for any subnets that are not explicitly associated with any other route table\. For more information, see [Replace the main route table](WorkWithRouteTables.md#Route_Replacing_Main_Table)\.

### Custom route tables<a name="custom-route-tables"></a>

By default, a custom route table is empty and you add routes as needed\. If you [Create a VPC](create-vpc.md) and choose a public subnet, Amazon VPC creates a custom route table and adds a route that points to the internet gateway\. One way to protect your VPC is to leave the main route table in its original default state\. Then, explicitly associate each new subnet that you create with one of the custom route tables you've created\. This ensures that you explicitly control how each subnet routes traffic\.

You can add, remove, and modify routes in a custom route table\. You can delete a custom route table only if it has no associations\.

### Subnet route table association<a name="route-table-assocation"></a>

Each subnet in your VPC must be associated with a route table\. A subnet can be explicitly associated with custom route table, or implicitly or explicitly associated with the main route table\. For more information about viewing your subnet and route table associations, see [Determine which subnets and or gateways are explicitly associated](WorkWithRouteTables.md#Route_Which_Associations)\.

Subnets that are in VPCs associated with Outposts can have an additional target type of a local gateway\. This is the only routing difference from non\-Outposts subnets\.

**Example 1: Implicit and explicit subnet association**  
The following diagram shows the routing for a VPC with an internet gateway, a virtual private gateway, a public subnet, and a VPN\-only subnet\.

![\[Main route table and custom table\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/case-3_updated.png)

Route table A is a custom route table that is explicitly associated with the public subnet\. It has a route that sends all traffic to the internet gateway\.


| Destination | Target | 
| --- | --- | 
| VPC CIDR | Local | 
| 0\.0\.0\.0/0 | igw\-id | 

Route table B is the main route table\. It has a route that sends all traffic to the virtual private gateway\.


| Destination | Target | 
| --- | --- | 
| VPC CIDR | Local | 
| 0\.0\.0\.0/0 | vgw\-id | 

If you create a new subnet in this VPC, it's automatically implicitly associated with the main route table, which routes traffic to the virtual private gateway\. If you set up the reverse configuration \(where the main route table has the route to the internet gateway, and the custom route table has the route to the virtual private gateway\), then traffic to the new subnet is routed to the internet gateway\. 

**Example 2: Replacing the main route table**  
You might want to make changes to the main route table\. To avoid any disruption to your traffic, we recommend that you first test the route changes using a custom route table\. After you're satisfied with the testing, you can replace the main route table with the new custom table\.

The following diagram shows a VPC with two subnets that are implicitly associated with the main route table \(Route Table A\), and a custom route table \(Route Table B\) that isn't associated with any subnets\.

![\[Replace main table: Start\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/routing-Route_Replace_Main_Start-diagram_updated.png)

You can create an explicit association between Subnet 2 and Route Table B\.

![\[Replace main table: New table\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/routing-Route_Replace_Main_New_Table-diagram_updated.png)

After you've tested Route Table B, you can make it the main route table\. Note that Subnet 2 still has an explicit association with Route Table B, and Subnet 1 has an implicit association with Route Table B because it is the new main route table\. Route Table A is no longer in use\.

![\[Replace main table: Replace\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/routing-Route_Replace_Main_Replace-diagram_updated.png)

If you disassociate Subnet 2 from Route Table B, there's still an implicit association between Subnet 2 and Route Table B\. If you no longer need Route Table A, you can delete it\.

![\[Replace main table: Disassociate\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/routing-Route_Replace_Main_Disassociate-diagram_updated.png)

## Gateway route tables<a name="gateway-route-tables"></a>

You can associate a route table with an internet gateway or a virtual private gateway\. When a route table is associated with a gateway, it's referred to as a *gateway route table*\. You can create a gateway route table for fine\-grain control over the routing path of traffic entering your VPC\. For example, you can intercept the traffic that enters your VPC through an internet gateway by redirecting that traffic to a middlebox appliance \(such as a security appliance\) in your VPC\.

**Topics**
+ [Gateway route table routes](#gateway-route-table-routes)
+ [Rules and considerations](#gateway-route-table-rules)

### Gateway route table routes<a name="gateway-route-table-routes"></a>

A gateway route table associated with an internet gateway supports routes with the following targets:
+ The default local route
+ A [Gateway Load Balancer endpoint](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/)
+ A network interface for a middlebox appliance

A gateway route table associated with a virtual private gateway supports routes with the following targets:
+ The default local route
+ A network interface for a middlebox appliance

When the target is a Gateway Load Balancer endpoint or a network interface, the following destinations are allowed:
+ The entire IPv4 or IPv6 CIDR block of your VPC\. In this case, you replace the target of the default local route\.
+ The entire IPv4 or IPv6 CIDR block of a subnet in your VPC\. This is a more specific route than the default local route\.

If you change the target of the local route in a gateway route table to a network interface in your VPC, you can later restore it to the default `local` target\. For more information, see [Replace or restore the target for a local route](WorkWithRouteTables.md#replace-local-route-target)\. 

**Example**  
In the following gateway route table, traffic destined for a subnet with the `172.31.0.0/20` CIDR block is routed to a specific network interface\. Traffic destined for all other subnets in the VPC uses the local route\.


| Destination | Target | 
| --- | --- | 
| 172\.31\.0\.0/16 | Local | 
| 172\.31\.0\.0/20 | eni\-id | 

**Example**  
In the following gateway route table, the target for the local route is replaced with a network interface ID\. Traffic destined for all subnets within the VPC is routed to the network interface\.


| Destination | Target | 
| --- | --- | 
| 172\.31\.0\.0/16 | eni\-id | 

### Rules and considerations<a name="gateway-route-table-rules"></a>

You cannot associate a route table with a gateway if any of the following applies:
+ The route table contains existing routes with targets other than a network interface, Gateway Load Balancer endpoint, or the default local route\.
+ The route table contains existing routes to CIDR blocks outside of the ranges in your VPC\.
+ Route propagation is enabled for the route table\.

In addition, the following rules and considerations apply:
+ You cannot add routes to any CIDR blocks outside of the ranges in your VPC, including ranges larger than the individual VPC CIDR blocks\. 
+ You can only specify `local`, a Gateway Load Balancer endpoint, or a network interface as a target\. You cannot specify any other types of targets, including individual host IP addresses\. For more information, see [Example routing options](route-table-options.md)\.
+ You cannot route traffic from a virtual private gateway to a Gateway Load Balancer endpoint\. If you associate your route table with a virtual private gateway and you add a route with a Gateway Load Balancer endpoint as the target, traffic that's destined for the endpoint is dropped\.
+ You cannot specify a prefix list as a destination\.
+ You cannot use a gateway route table to control or intercept traffic outside of your VPC, for example, traffic through an attached transit gateway\. You can intercept traffic that enters your VPC and redirect it to another target in the same VPC only\.
+ To ensure that traffic reaches your middlebox appliance, the target network interface must be attached to a running instance\. For traffic that flows through an internet gateway, the target network interface must also have a public IP address\.
+ When configuring your middlebox appliance, take note of the [appliance considerations](route-table-options.md#appliance-considerations)\.
+ When you route traffic through a middlebox appliance, the return traffic from the destination subnet must be routed through the same appliance\. Asymmetric routing is not supported\.
+ Route table rules apply to all traffic that leaves a subnet\. Traffic that leaves a subnet is defined as traffic destined to that subnet's gateway router's MAC address\. Traffic that is destined for the MAC address of another network interface in the subnet makes use of data link \(layer 2\) routing instead of network \(layer 3\) so the rules do not apply to this traffic\.

## Route priority<a name="route-tables-priority"></a>

In general, we direct traffic using the most specific route that matches the traffic\. This is known as the longest prefix match\. If your route table has overlapping or matching routes, additional rules apply\.

**Topics**
+ [Longest prefix match](#route-table-longest-prefix-match)
+ [Route priority and propagated routes](#route-table-priority-propagated-routes)
+ [Route priority and prefix lists](#route-priority-managed-prefix-list)

### Longest prefix match<a name="route-table-longest-prefix-match"></a>

Routes to IPv4 and IPv6 addresses or CIDR blocks are independent of each other\. We use the most specific route that matches either IPv4 traffic or IPv6 traffic to determine how to route the traffic\. 

The following example subnet route table has a route for IPv4 internet traffic \(`0.0.0.0/0`\) that points to an internet gateway, and a route for `172.31.0.0/16` IPv4 traffic that points to a peering connection \(`pcx-11223344556677889`\)\. Any traffic from the subnet that's destined for the `172.31.0.0/16` IP address range uses the peering connection, because this route is more specific than the route for internet gateway\. Any traffic destined for a target within the VPC \(`10.0.0.0/16`\) is covered by the `local` route, and therefore is routed within the VPC\. All other traffic from the subnet uses the internet gateway\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | local | 
| 172\.31\.0\.0/16 | pcx\-11223344556677889 | 
| 0\.0\.0\.0/0 | igw\-12345678901234567 | 

### Route priority and propagated routes<a name="route-table-priority-propagated-routes"></a>

If you've attached a virtual private gateway to your VPC and enabled route propagation on your subnet route table, routes representing your Site\-to\-Site VPN connection automatically appear as propagated routes in your route table\.

If the destination of a propagated route overlaps a static route, the static route takes priority\.

If the destination of a propagated route is identical to the destination of a static route, the static route takes priority if the target is one of the following:
+ internet gateway
+ NAT gateway
+ Network interface
+ Instance ID
+ Gateway VPC endpoint
+ Transit gateway
+ VPC peering connection
+ Gateway Load Balancer endpoint

For more information, see [Route tables and VPN route priority](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html#vpn-route-priority) in the *AWS Site\-to\-Site VPN User Guide*\.

The following example route table has a static route to an internet gateway and a propagated route to a virtual private gateway\. Both routes have a destination of `172.31.0.0/24`\. Because a static route to an internet gateway takes priority, all traffic destined for `172.31.0.0/24` is routed to the internet gateway\. 


| Destination | Target | Propagated | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | local | No | 
| 172\.31\.0\.0/24 | vgw\-11223344556677889 | Yes | 
| 172\.31\.0\.0/24 | igw\-12345678901234567 | No | 

### Route priority and prefix lists<a name="route-priority-managed-prefix-list"></a>

If your route table references a prefix list, the following rules apply: 
+ If your route table contains a static route with a destination CIDR block that overlaps a static route with a prefix list, the static route with the CIDR block takes priority\.
+ If your route table contains a propagated route that matches a route that references a prefix list, the route that references the prefix list takes priority\. Please note that for routes that overlap, more specific routes always take priority irrespective of whether they are propagated routes, static routes, or routes that reference prefix lists\.
+ If your route table references multiple prefix lists that have overlapping CIDR blocks to different targets, we randomly choose which route takes priority\. Thereafter, the same route always takes priority\.

## Route table quotas<a name="route-table-quotas"></a>

There is a quota on the number of route tables that you can create per VPC\. There is also a quota on the number of routes that you can add per route table\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.