# Examples for VPC<a name="VPC_Scenarios"></a>

This section has examples for creating and configuring a VPC\.


| Example | Usage | 
| --- | --- | 
| [Example: Create an IPv4 VPC and subnets using the AWS CLI](vpc-subnets-commands-example.md) |  Use the AWS CLI to create a VPC with a public subnet and a private subnet\.  | 
| [Example: Create an IPv6 VPC and subnets using the AWS CLI](vpc-subnets-commands-example-ipv6.md) | Use the AWS CLI to create a VPC with an associated IPv6 CIDR block and a public subnet and a private subnet, each with an associated IPv6 CIDR block\. | 
| [Example: Sharing public subnets and private subnets](example-vpc-share.md) | Share private and public subnets with accounts\. | 
| [Examples: Services using AWS PrivateLink and VPC peering](vpc-peer-region-example.md) | Learn how to use a combination of VPC peering and AWS PrivateLink to extend access to private services to consumers\. | 

You can also use a transit gateway to connect your VPCs\.


| Example | Usage | 
| --- | --- | 
|  Centralized router |  You can configure your transit gateway as a centralized router that connects all of your VPCs, AWS Direct Connect, and AWS Site\-to\-Site VPN connections\.  For more information about configuring your transit gateway as a centralized router, see [Transit Gateway Example: Centralized Router](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-centralized-router.html) in *Amazon VPC Transit Gateways*\. | 
| Isolated VPCs | You can configure your transit gateway as multiple isolated routers\. This is similar to using multiple transit gateways, but provides more flexibility in cases where the routes and attachments might change\.For more information about configuring your transit gateway to isolate your VPCs, see [Transit Gateway Example: Isolated VPCs](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-isolated.html) in *Amazon VPC Transit Gateways*\. | 
| Isolated VPCs with Shared Services | You can configure your transit gateway as multiple isolated routers that use a shared service This is similar to using multiple transit gateways, but provides more flexibility in cases where the routes and attachments might change\. For more information about configuring your transit gateway to isolate your VPCs, see [Transit Gateway Example: Isolated VPCs with Shared Services](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-isolated-shared.html) in *Amazon VPC Transit Gateways*\. | 