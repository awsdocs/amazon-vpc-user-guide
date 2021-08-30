# Example VPC configurations<a name="VPC_Scenarios"></a>

You can use the following examples to create and configure your VPCs\.


| Example | Usage | 
| --- | --- | 
| [Create an IPv4 VPC and subnets using the AWS CLI](vpc-subnets-commands-example.md) | Use the AWS CLI to create a VPC with a public subnet and a private subnet\. | 
| [Create an IPv6 VPC and subnets using the AWS CLI](vpc-subnets-commands-example-ipv6.md) | Use the AWS CLI to create a VPC with an associated IPv6 CIDR block and a public subnet and a private subnet, each with an associated IPv6 CIDR block\. | 
| [Share public subnets and private subnets](example-vpc-share.md) | Share private and public subnets with accounts\. | 
| [Services using AWS PrivateLink and VPC peering](vpc-peer-region-example.md) | Use a combination of VPC peering and AWS PrivateLink to extend access to private services to consumers\. | 
| [Middlebox routing](middlebox-routing-examples.md) | Configure fine\-grain control over the routing path of traffic entering or leaving your VPC\. | 

You can also use a transit gateway to connect your VPCs\.


| Example | Usage | 
| --- | --- | 
| Centralized router | Configure your transit gateway as a centralized router that connects all of your VPCs, AWS Direct Connect, and AWS Site\-to\-Site VPN connections\. For more information, see [Example: Centralized router](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-centralized-router.html) in Amazon VPC Transit Gateways\. | 
| Isolated VPCs | Configure your transit gateway as multiple isolated routers\. This is similar to using multiple transit gateways, but provides more flexibility in cases where the routes and attachments might change\. For more information, see [Example: Isolated VPCs](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-isolated.html) in Amazon VPC Transit Gateways\. | 
| Isolated VPCs with shared services | Configure your transit gateway as multiple isolated routers that use a shared service\. This is similar to using multiple transit gateways, but provides more flexibility in cases where the routes and attachments might change\. For more information, see [Example: Isolated VPCs with shared services](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-isolated-shared.html) in Amazon VPC Transit Gateways\. | 