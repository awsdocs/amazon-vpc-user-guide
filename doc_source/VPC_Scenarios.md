# Scenarios and Examples<a name="VPC_Scenarios"></a>

This section has examples for creating and configuring a VPC, including scenarios for how to use the VPC wizard in the Amazon VPC console\.


| Scenario | Usage | 
| --- | --- | 
|  [Scenario 1: VPC with a Single Public Subnet](VPC_Scenario1.md)  |  Use the VPC wizard to create a VPC for running a single\-tier, public\-facing web application such as a blog or simple web site\.  | 
|  [Scenario 2: VPC with Public and Private Subnets \(NAT\)](VPC_Scenario2.md)  |  Use the VPC wizard to create a VPC for running a public\-facing web application, while still maintaining non\-publicly accessible back\-end servers in a second subnet\.  | 
|  [Scenario 3: VPC with Public and Private Subnets and AWS Site\-to\-Site VPN Access](VPC_Scenario3.md)  |  Use the VPC wizard to create a VPC for extending your data center into the cloud, and also directly access the Internet from your VPC\.  | 
|  [Scenario 4: VPC with a Private Subnet Only and AWS Site\-to\-Site VPN Access](VPC_Scenario4.md)  |  Use the VPC wizard to create a VPC for extending your data center into the cloud, and leverage Amazon's infrastructure without exposing your network to the Internet\.  | 
| [Example: Create an IPv4 VPC and Subnets Using the AWS CLI](vpc-subnets-commands-example.md) | Use the AWS CLI to create a VPC with a public subnet and a private subnet\. | 
| [Example: Create an IPv6 VPC and Subnets Using the AWS CLI](vpc-subnets-commands-example-ipv6.md) | Use the AWS CLI to create a VPC with an associated IPv6 CIDR block and a public subnet and a private subnet, each with an associated IPv6 CIDR block\. | 
| [Example: Sharing Public Subnets and Private Subnets](example-vpc-share.md) | Share private and public subnets with accounts\. | 
| [Example: Services Using AWS PrivateLink and VPC Peering](vpc-peer-region-example.md) | Learn how to use a combination of VPC peering and AWS PrivateLink to extend access to private services to consumers\. | 
