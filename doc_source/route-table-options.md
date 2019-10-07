# Routing Options<a name="route-table-options"></a>

The following topics describe routing for specific gateways or connections in your VPC\. 

**Topics**
+ [Route Tables for an Internet Gateway](#route-tables-internet-gateway)
+ [Route Tables for a NAT Device](#route-tables-nat)
+ [Route Tables for a Virtual Private Gateway](#route-tables-vgw)
+ [Route Tables for a VPC Peering Connection](#route-tables-vpc-peering)
+ [Route Tables for ClassicLink](#route-tables-classiclink)
+ [Route Tables for a Gateway VPC Endpoint](#route-tables-vpce)
+ [Route Tables for an Egress\-Only Internet Gateway](#route-tables-eigw)
+ [Route Tables for Transit Gateways](#route-tables-tgw)

## Route Tables for an Internet Gateway<a name="route-tables-internet-gateway"></a>

You can make a subnet a public subnet by adding a route to an internet gateway\. To do this, create and attach an internet gateway to your VPC, and then add a route with a destination of `0.0.0.0/0` for IPv4 traffic or `::/0` for IPv6 traffic, and a target of the internet gateway ID \(`igw-xxxxxxxxxxxxxxxxx`\)\. 


| Destination | Target | 
| --- | --- | 
| 0\.0\.0\.0/0 | igw\-id | 
| ::/0 | igw\-id | 

For more information, see [Internet Gateways](VPC_Internet_Gateway.md)\.

## Route Tables for a NAT Device<a name="route-tables-nat"></a>

To enable instances in a private subnet to connect to the internet, you can create a NAT gateway or launch a NAT instance in a public subnet\. Then add a route for the private subnet that routes IPv4 internet traffic \(`0.0.0.0/0`\) to the NAT device\. 


| Destination | Target | 
| --- | --- | 
| 0\.0\.0\.0/0 | nat\-gateway\-id | 

For more information, see [NAT Gateways](vpc-nat-gateway.md) and [NAT Instances](VPC_NAT_Instance.md)\. NAT devices cannot be used for IPv6 traffic\.

## Route Tables for a Virtual Private Gateway<a name="route-tables-vgw"></a>

You can use an AWS Site\-to\-Site VPN connection to enable instances in your VPC to communicate with your own network\. To do this, create and attach a virtual private gateway to your VPC\. Then add a route with the destination of your network and a target of the virtual private gateway \(`vgw-xxxxxxxxxxxxxxxxx`\)\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | vgw\-id | 

You can then create and configure your Site\-to\-Site VPN connection\. For more information, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html) and [Route Tables and VPN Route Priority](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html#vpn-route-priority) in the *AWS Site\-to\-Site VPN User Guide*\.

We currently do not support IPv6 traffic over an AWS Site\-to\-Site VPN connection\. However, we support IPv6 traffic routed through a virtual private gateway to an AWS Direct Connect connection\. For more information, see the [AWS Direct Connect User Guide](https://docs.aws.amazon.com/directconnect/latest/UserGuide/)\.

## Route Tables for a VPC Peering Connection<a name="route-tables-vpc-peering"></a>

A VPC peering connection is a networking connection between two VPCs that allows you to route traffic between them using private IPv4 addresses\. Instances in either VPC can communicate with each other as if they are part of the same network\. 

To enable the routing of traffic between VPCs in a VPC peering connection, you must add a route to one or more of your VPC route tables that points to the VPC peering connection\. This allows you to access all or part of the CIDR block of the other VPC in the peering connection\. Similarly, the owner of the other VPC must add a route to their VPC route table to route traffic back to your VPC\.

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

Your VPC peering connection can also support IPv6 communication between instances in the VPCs, if the VPCs and instances are enabled for IPv6 communication\. For more information, see [VPCs and Subnets](VPC_Subnets.md)\. To enable the routing of IPv6 traffic between VPCs, you must add a route to your route table that points to the VPC peering connection to access all or part of the IPv6 CIDR block of the peer VPC\.

For example, using the same VPC peering connection \(`pcx-11223344556677889`\) above, assume the VPCs have the following information:
+ VPC A: IPv6 CIDR block is `2001:db8:1234:1a00::/56`
+ VPC B: IPv6 CIDR block is `2001:db8:5678:2b00::/56`

To enable IPv6 communication over the VPC peering connection, add the following route to the route table for VPC A\.


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

## Route Tables for ClassicLink<a name="route-tables-classiclink"></a>

ClassicLink is a feature that enables you to link an EC2\-Classic instance to a VPC, allowing communication between the EC2\-Classic instance and instances in the VPC using private IPv4 addresses\. For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\.

When you enable a VPC for ClassicLink, a route is added to all of the VPC route tables with a destination of `10.0.0.0/8` and a target of `local`\. This allows communication between instances in the VPC and any EC2\-Classic instances that are then linked to the VPC\. If you add another route table to a ClassicLink\-enabled VPC, it automatically receives a route with a destination of `10.0.0.0/8` and a target of `local`\. If you disable ClassicLink for a VPC, this route is automatically deleted in all the VPC route tables\.

If any of your VPC route tables have existing routes for address ranges within the `10.0.0.0/8` CIDR, you cannot enable your VPC for ClassicLink\. This does not include local routes for VPCs with `10.0.0.0/16` and `10.1.0.0/16` IP address ranges\. 

If you've already enabled a VPC for ClassicLink, you may not be able to add any more specific routes to your route tables for the `10.0.0.0/8` IP address range\.

If you modify a VPC peering connection to enable communication between instances in your VPC and an EC2\-Classic instance that's linked to the peer VPC, a static route is automatically added to your route tables with a destination of `10.0.0.0/8` and a target of `local`\. If you modify a VPC peering connection to enable communication between a local EC2\-Classic instance linked to your VPC and instances in a peer VPC, you must manually add a route to your main route table with a destination of the peer VPC CIDR block, and a target of the VPC peering connection\. The EC2\-Classic instance relies on the main route table for routing to the peer VPC\. For more information, see [Configurations With ClassicLink](https://docs.aws.amazon.com/vpc/latest/peering/peering-configurations-classiclink.html) in the *Amazon VPC Peering Guide*\.

## Route Tables for a Gateway VPC Endpoint<a name="route-tables-vpce"></a>

A gateway VPC endpoint enables you to create a private connection between your VPC and another AWS service\. When you create a gateway endpoint, you specify the route tables in your VPC that are used by the gateway endpoint\. A route is automatically added to each of the route tables with a destination that specifies the prefix list ID of the service \(`pl-xxxxxxxx`\), and a target with the endpoint ID \(`vpce-xxxxxxxxxxxxxxxxx`\)\. You cannot explicitly delete or modify the endpoint route, but you can change the route tables that are used by the endpoint\.

For more information about routing for endpoints, and the implications for routes to AWS services, see [Routing for Gateway Endpoints](vpce-gateway.md#vpc-endpoints-routing)\.

## Route Tables for an Egress\-Only Internet Gateway<a name="route-tables-eigw"></a>

You can create an egress\-only internet gateway for your VPC to enable instances in a private subnet to initiate outbound communication to the internet, but prevent the internet from initiating connections with the instances\. An egress\-only internet gateway is used for IPv6 traffic only\. To configure routing for an egress\-only internet gateway, add a route for the private subnet that routes IPv6 internet traffic \(`::/0`\) to the egress\-only internet gateway\. 


| Destination | Target | 
| --- | --- | 
| ::/0 | eigw\-id | 

For more information, see [Egress\-Only Internet Gateways](egress-only-internet-gateway.md)\.

## Route Tables for Transit Gateways<a name="route-tables-tgw"></a>

When you attach a VPC to a transit gateway, you need to add a route for traffic to route through the transit gateway\. 

Consider the following scenario where you have three VPCs that are attached to a transit gateway\. In this scenario, all attachments are associated with the transit gateway route table and propagate to the transit gateway route table\. Therefore, all attachments can route packets to each other, with the transit gateway serving as a simple layer 3 IP hub\. 

For example, you have two VPCs, with the following information:
+ VPC A: 10\.1\.0\.0/16, attachment ID tgw\-attach\-11111111111111111
+ VPC B: 10\.2\.0\.0/16, attachment ID tgw\-attach\-22222222222222222

To enable traffic between the VPCs and allow access to the transit gateway the VPC A route table is configured as follows\.


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