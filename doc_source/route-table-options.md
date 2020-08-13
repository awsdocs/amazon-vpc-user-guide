# Example routing options<a name="route-table-options"></a>

The following topics describe routing for specific gateways or connections in your VPC\. 

**Topics**
+ [Routing to an internet gateway](#route-tables-internet-gateway)
+ [Routing to a NAT device](#route-tables-nat)
+ [Routing to a virtual private gateway](#route-tables-vgw)
+ [Routing to an AWS Outposts local gateway](#route-tables-lgw)
+ [Routing to carrier gateway](#route-tables-cgw)
+ [Routing to a VPC peering connection](#route-tables-vpc-peering)
+ [Routing for ClassicLink](#route-tables-classiclink)
+ [Routing to a gateway VPC endpoint](#route-tables-vpce)
+ [Routing to an egress\-only internet gateway](#route-tables-eigw)
+ [Routing for a transit gateway](#route-tables-tgw)
+ [Routing for a middlebox appliance in your VPC](#route-tables-appliance-routing)
+ [Routing using a prefix list](#route-tables-managed-prefix-list)

## Routing to an internet gateway<a name="route-tables-internet-gateway"></a>

You can make a subnet a public subnet by adding a route in your subnet route table to an internet gateway\. To do this, create and attach an internet gateway to your VPC, and then add a route with a destination of `0.0.0.0/0` for IPv4 traffic or `::/0` for IPv6 traffic, and a target of the internet gateway ID \(`igw-xxxxxxxxxxxxxxxxx`\)\. 


| Destination | Target | 
| --- | --- | 
| 0\.0\.0\.0/0 | igw\-id | 
| ::/0 | igw\-id | 

For more information, see [Internet gateways](VPC_Internet_Gateway.md)\.

## Routing to a NAT device<a name="route-tables-nat"></a>

To enable instances in a private subnet to connect to the internet, you can create a NAT gateway or launch a NAT instance in a public subnet\. Then add a route for the private subnet's route table that routes IPv4 internet traffic \(`0.0.0.0/0`\) to the NAT device\. 


| Destination | Target | 
| --- | --- | 
| 0\.0\.0\.0/0 | nat\-gateway\-id | 

You can also create more specific routes to other targets to avoid unnecessary data processing charges for using a NAT gateway, or to route certain traffic privately\. In the following example, Amazon S3 traffic \(pl\-xxxxxxxx; a specific IP address range for Amazon S3\) is routed to a gateway VPC endpoint, and 10\.25\.0\.0/16 traffic is routed to a VPC peering connection\. The pl\-xxxxxxxx and 10\.25\.0\.0/16 IP address ranges are more specific than 0\.0\.0\.0/0\. When instances send traffic to Amazon S3 or the peer VPC, the traffic is sent to the gateway VPC endpoint or the VPC peering connection\. All other traffic is sent to the NAT gateway\. 


| Destination | Target | 
| --- | --- | 
| 0\.0\.0\.0/0 | nat\-gateway\-id | 
| pl\-xxxxxxxx | vpce\-id | 
| 10\.25\.0\.0/16 | pcx\-id | 

For more information, see [NAT gateways](vpc-nat-gateway.md) and [NAT instances](VPC_NAT_Instance.md)\. NAT devices cannot be used for IPv6 traffic\.

## Routing to a virtual private gateway<a name="route-tables-vgw"></a>

You can use an AWS Site\-to\-Site VPN connection to enable instances in your VPC to communicate with your own network\. To do this, create and attach a virtual private gateway to your VPC\. Then add a route in your subnet route table with the destination of your network and a target of the virtual private gateway \(`vgw-xxxxxxxxxxxxxxxxx`\)\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | vgw\-id | 

You can then create and configure your Site\-to\-Site VPN connection\. For more information, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html) and [Route tables and VPN route priority](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html#vpn-route-priority) in the *AWS Site\-to\-Site VPN User Guide*\.

A Site\-to\-Site VPN connection on a virtual private gateway does not support IPv6 traffic\. However, we support IPv6 traffic routed through a virtual private gateway to an AWS Direct Connect connection\. For more information, see the [AWS Direct Connect User Guide](https://docs.aws.amazon.com/directconnect/latest/UserGuide/)\.

## Routing to an AWS Outposts local gateway<a name="route-tables-lgw"></a>

Subnets that are in VPCs associated with AWS Outposts can have an additional target type of a local gateway\. Consider the case where you want to have the local gateway route traffic with a destination address of 192\.168\.10\.0/24 to the customer network\. To do this, add the following route with the destination network and a target of the local gateway \(`lgw-xxxx`\)\.


| Destination | Target | 
| --- | --- | 
| 192\.168\.10\.0/24 | lgw\-id | 
| 2002:bc9:1234:1a00::/56 | igw\-id | 

## Routing to carrier gateway<a name="route-tables-cgw"></a>

Subnets that are in Wavelength Zones can have an additional target type of a carrier gateway\. Consider the case where you want to have the carrier gateway route traffic to route all non\-VPC traffic to the carrier network\. To do this, create and attach a carrier gateway to your VPC, and then add a route with a destination of `0.0.0.0/0` for IPv4 traffic or `::/0` for IPv6 traffic, and a target of the carrier gateway ID \(`cgw-xxxxxxxxxxxxxxxxx`\)\. 


| Destination | Target | 
| --- | --- | 
| 192\.168\.10\.0/24 | lgw\-id | 
| 2002:bc9:1234:1a00::/56 | igw\-id | 

## Routing to a VPC peering connection<a name="route-tables-vpc-peering"></a>

A VPC peering connection is a networking connection between two VPCs that allows you to route traffic between them using private IPv4 addresses\. Instances in either VPC can communicate with each other as if they are part of the same network\. 

To enable the routing of traffic between VPCs in a VPC peering connection, you must add a route to one or more of your subnet route tables that points to the VPC peering connection\. This allows you to access all or part of the CIDR block of the other VPC in the peering connection\. Similarly, the owner of the other VPC must add a route to their subnet route table to route traffic back to your VPC\.

For example, you have a VPC peering connection \(`pcx-11223344556677889`\) between two VPCs, with the following information:
+ VPC A: CIDR block is 10\.0\.0\.0/16
+ VPC B: CIDR block is 172\.31\.0\.0/16

To enable traffic between the VPCs and allow access to the entire IPv4 CIDR block of either VPC, the VPC A route table is configured as follows\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 172\.31\.0\.0/16 | pcx\-11223344556677889 | 

The VPC B route table is configured as follows\.


| Destination | Target | 
| --- | --- | 
| 172\.31\.0\.0/16 | Local | 
| 10\.0\.0\.0/16 | pcx\-11223344556677889 | 

Your VPC peering connection can also support IPv6 communication between instances in the VPCs, if the VPCs and instances are enabled for IPv6 communication\. For more information, see [VPCs and subnets](VPC_Subnets.md)\. To enable the routing of IPv6 traffic between VPCs, you must add a route to your route table that points to the VPC peering connection to access all or part of the IPv6 CIDR block of the peer VPC\.

For example, using the same VPC peering connection \(`pcx-11223344556677889`\) above, assume the VPCs have the following information:
+ VPC A: IPv6 CIDR block is `2001:db8:1234:1a00::/56`
+ VPC B: IPv6 CIDR block is `2001:db8:5678:2b00::/56`

To enable IPv6 communication over the VPC peering connection, add the following route to the subnet route table for VPC A\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 172\.31\.0\.0/16 | pcx\-11223344556677889 | 
| 2001:db8:5678:2b00::/56 | pcx\-11223344556677889 | 

Add the following route to the route table for VPC B\.


| Destination | Target | 
| --- | --- | 
| 172\.31\.0\.0/16 | Local | 
| 10\.0\.0\.0/16 | pcx\-11223344556677889 | 
| 2001:db8:1234:1a00::/56 | pcx\-11223344556677889 | 

For more information about VPC peering connections, see the [Amazon VPC Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/)\.

## Routing for ClassicLink<a name="route-tables-classiclink"></a>

ClassicLink is a feature that enables you to link an EC2\-Classic instance to a VPC, allowing communication between the EC2\-Classic instance and instances in the VPC using private IPv4 addresses\. For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\.

When you enable a VPC for ClassicLink, a route is added to all of the subnet route tables with a destination of `10.0.0.0/8` and a target of `local`\. This allows communication between instances in the VPC and any EC2\-Classic instances that are then linked to the VPC\. If you add another route table to a ClassicLink\-enabled VPC, it automatically receives a route with a destination of `10.0.0.0/8` and a target of `local`\. If you disable ClassicLink for a VPC, this route is automatically deleted in all the subnet route tables\.

If any of your subnet route tables have existing routes for address ranges within the `10.0.0.0/8` CIDR, you cannot enable your VPC for ClassicLink\. This does not include local routes for VPCs with `10.0.0.0/16` and `10.1.0.0/16` IP address ranges\. 

If you've already enabled a VPC for ClassicLink, you may not be able to add any more specific routes to your route tables for the `10.0.0.0/8` IP address range\.

If you modify a VPC peering connection to enable communication between instances in your VPC and an EC2\-Classic instance that's linked to the peer VPC, a static route is automatically added to your route tables with a destination of `10.0.0.0/8` and a target of `local`\. If you modify a VPC peering connection to enable communication between a local EC2\-Classic instance linked to your VPC and instances in a peer VPC, you must manually add a route to your main route table with a destination of the peer VPC CIDR block, and a target of the VPC peering connection\. The EC2\-Classic instance relies on the main route table for routing to the peer VPC\. For more information, see [Configurations With ClassicLink](https://docs.aws.amazon.com/vpc/latest/peering/peering-configurations-classiclink.html) in the *Amazon VPC Peering Guide*\.

## Routing to a gateway VPC endpoint<a name="route-tables-vpce"></a>

A gateway VPC endpoint enables you to create a private connection between your VPC and another AWS service\. When you create a gateway endpoint, you specify the subnet route tables in your VPC that are used by the gateway endpoint\. A route is automatically added to each of the route tables with a destination that specifies the prefix list ID of the service \(`pl-xxxxxxxx`\), and a target with the endpoint ID \(`vpce-xxxxxxxxxxxxxxxxx`\)\. You cannot explicitly delete or modify the endpoint route, but you can change the route tables that are used by the endpoint\.

For more information about routing for endpoints, and the implications for routes to AWS services, see [Routing for gateway endpoints](vpce-gateway.md#vpc-endpoints-routing)\.

## Routing to an egress\-only internet gateway<a name="route-tables-eigw"></a>

You can create an egress\-only internet gateway for your VPC to enable instances in a private subnet to initiate outbound communication to the internet, but prevent the internet from initiating connections with the instances\. An egress\-only internet gateway is used for IPv6 traffic only\. To configure routing for an egress\-only internet gateway, add a route in the private subnet's route table that routes IPv6 internet traffic \(`::/0`\) to the egress\-only internet gateway\. 


| Destination | Target | 
| --- | --- | 
| ::/0 | eigw\-id | 

For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.

## Routing for a transit gateway<a name="route-tables-tgw"></a>

When you attach a VPC to a transit gateway, you need to add a route to your subnet route table for traffic to route through the transit gateway\. 

Consider the following scenario where you have three VPCs that are attached to a transit gateway\. In this scenario, all attachments are associated with the transit gateway route table and propagate to the transit gateway route table\. Therefore, all attachments can route packets to each other, with the transit gateway serving as a simple layer 3 IP hub\. 

For example, you have two VPCs, with the following information:
+ VPC A: 10\.1\.0\.0/16, attachment ID tgw\-attach\-11111111111111111
+ VPC B: 10\.2\.0\.0/16, attachment ID tgw\-attach\-22222222222222222

To enable traffic between the VPCs and allow access to the transit gateway, the VPC A route table is configured as follows\.


| Destination | Target | 
| --- | --- | 
|  10\.1\.0\.0/16  |  local  | 
|  10\.0\.0\.0/8  |  *tgw\-id*  | 

The following is an example of the transit gateway route table entries for the VPC attachments\.


| Destination | Target | 
| --- | --- | 
|  10\.1\.0\.0/16  | tgw\-attach\-11111111111111111 | 
|  10\.2\.0\.0/16  |  tgw\-attach\-22222222222222222  | 

For more information about transit gateway route tables, see [Routing](https://docs.aws.amazon.com/vpc/latest/tgw/how-transit-gateways-work.html#tgw-routing-overview) in *Amazon VPC Transit Gateways*\.

## Routing for a middlebox appliance in your VPC<a name="route-tables-appliance-routing"></a>

You can intercept traffic that enters your VPC through an internet gateway or a virtual private gateway by directing it to a middlebox appliance in your VPC\. You can configure the appliance to suit your needs\. For example, you can configure a security appliance that screens all traffic, or a WAN acceleration appliance\. The appliance is deployed as an Amazon EC2 instance in a subnet in your VPC, and is represented by an elastic network interface \(network interface\) in your subnet\.

To route inbound VPC traffic to an appliance, you associate a route table with the internet gateway or virtual private gateway, and specify the network interface of your appliance as the target for VPC traffic\. For more information, see [Gateway route tables](VPC_Route_Tables.md#gateway-route-table)\. You can also route outbound traffic from your subnet to a middlebox appliance in another subnet\.

**Note**  
If you've enabled route propagation for the destination subnet route table, be aware of route priority\. We prioritize the most specific route, and if the routes match, we prioritize static routes over propagated routes\. Review your routes to ensure that traffic is routed correctly and that there are no unintended consequences if you enable or disable route propagation \(for example, route propagation is required for an AWS Direct Connect connection that supports jumbo frames\)\.

### Appliance considerations<a name="appliance-considerations"></a>

You can choose a third\-party appliance from [AWS Marketplace](https://aws.amazon.com/marketplace), or you can configure your own appliance\. When you create or configure an appliance, take note of the following:
+ The appliance must be configured in a separate subnet to the source or destination traffic\.
+ You must disable source/destination checking on the appliance\. For more information, see [Changing the Source or Destination Checking](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#change_source_dest_check) in the *Amazon EC2 User Guide for Linux Instances*\.
+ Service chaining is not supported\.
+ You cannot route traffic between hosts in the same subnet through an appliance\.
+ You cannot route traffic between subnets through an appliance\.
+ The appliance does not have to perform network address translation \(NAT\)\.
+ To intercept IPv6 traffic, ensure that you configure your VPC, subnet, and appliance for IPv6\. For more information, see [Working with VPCs and subnets](working-with-vpcs.md)\. Virtual private gateways do not support IPv6 traffic\.

### Appliance routing configuration<a name="appliance-routing-configuration"></a>

To route inbound traffic to an appliance, create a route table and add a route that points the traffic destined for a subnet to the appliance's network interface\. This route is more specific than the local route for the route table\. Associate this route table with your internet gateway or virtual private gateway\. The following route table routes IPv4 traffic destined for a subnet to the appliance's network interface\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 10\.0\.1\.0/24 | eni\-id | 

Alternatively, you can replace the target for the local route with the appliance's network interface\. You might do this to ensure that all traffic is automatically routed to the appliance, including traffic destined for subnets that you add to your VPC later\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | eni\-id | 

To route traffic from your subnet to an appliance in another subnet, add a route to your subnet route table that routes traffic to the appliance's network interface\. The destination must be less specific than the destination for the local route\. For example, for traffic destined for the internet, specify `0.0.0.0/0` \(all IPv4 addresses\) for the destination\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 0\.0\.0\.0/0 | eni\-id | 

Then, in the route table associated with the appliance's subnet, add a route that routes the traffic back to the internet gateway or virtual private gateway\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 0\.0\.0\.0/0 | igw\-id | 

You can apply the same routing configuration for IPv6 traffic\. For example, in your gateway route table, you can replace the target for both the IPv4 and IPv6 local routes with the appliance's network interface\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | eni\-id | 
| 2001:db8:1234:1a00::/56 | eni\-id | 

In the following diagram, a firewall appliance is installed and configured on an Amazon EC2 instance in subnet A in your VPC\. The appliance inspects all traffic that enters and leaves the VPC through the internet gateway\. Route table A is associated with the internet gateway\. Traffic destined for subnet B that enters the VPC through the internet gateway is routed to the appliance's network interface \(`eni-11223344556677889`\)\. All traffic that leaves subnet B is also routed to the appliance's network interface\.

![\[Inbound IPv4 routing to a VPC\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/ingress-routing-firewall.png)

The following example has the same setup as the preceding example, but includes IPv6 traffic\. IPv6 traffic that's destined for subnet B that enters the VPC through the internet gateway is routed to the appliance's network interface \(`eni-11223344556677889`\)\. All traffic \(IPv4 and IPv6\) that leaves subnet B is also routed to the appliance's network interface\.

![\[Inbound IPv4 and IPv6 routing to a VPC\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/ingress-routing-firewall-ipv6.png)

## Routing using a prefix list<a name="route-tables-managed-prefix-list"></a>

If you frequently reference the same set of CIDR blocks across your AWS resources, you can create a [customer\-managed prefix list](managed-prefix-lists.md) to group them together\. You can then specify the prefix list as the destination in your route table entry\. You can later add or remove entries for the prefix list without needing to update your route tables\.

For example, you have a transit gateway with multiple VPC attachments\. The VPCs must be able to communicate with two specific VPC attachments that have the following CIDR blocks:
+ 10\.0\.0\.0/16
+ 10\.2\.0\.0/16

You create a prefix list with both entries\. In your subnet route tables, you create a route and specify the prefix list as the destination, and the transit gateway as the target\.


| Destination | Target | 
| --- | --- | 
| 172\.31\.0\.0/16 | Local | 
| pl\-123abc123abc123ab | tgw\-id | 

The maximum number of entries for the prefix lists equals the same number of entries in the route table\.