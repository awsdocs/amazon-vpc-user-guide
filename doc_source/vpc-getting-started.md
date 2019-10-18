# Getting Started with Amazon VPC<a name="vpc-getting-started"></a>

You can use the Amazon VPC Console wizard to get started creating any of the following nondefault VPC configurations:
+ A single VPC with a public subnet: For more information, see [VPC with a Single Public Subnet](VPC_Scenario1.md)\.
+ A VPC with public and private subnets \(NAT\): For more information, see [VPC with Public and Private Subnets \(NAT\)](VPC_Scenario2.md)\.
+ A VPC with public and private subnets and AWS Site\-to\-Site VPN access: For more information, see [VPC with Public and Private Subnets and AWS Site\-to\-Site VPN Access](VPC_Scenario3.md)\.

  To implement this option, get information about your customer gateway, and create the VPC using the VPC wizard\. The VPC wizard creates a Site\-to\-Site VPN connection for you with a customer gateway and virtual private gateway\. For more information, see [Internet Gateways](VPC_Internet_Gateway.md)\.
+ A VPC with a private subnet only and AWS Site\-to\-Site VPN access: For more information, see [VPC with a Private Subnet Only and AWS Site\-to\-Site VPN Access](VPC_Scenario4.md)\.

  To implement this option, get information about your customer gateway, and create the VPC using the VPC wizard\. The VPC wizard creates a Site\-to\-Site VPN connection for you with a customer gateway and virtual private gateway\. For more information, see [Internet Gateways](VPC_Internet_Gateway.md)\.

If you need resources in your VPC to communicate over IPv6, you can set up a VPC with an associated IPv6 CIDR block\. Otherwise, set up a VPC with an associated IPv4 CIDR block\.

For information about how to configure IPv4 addressing, see [Getting Started with IPv4 for Amazon VPC](getting-started-ipv4.md)\.

For information about how to configure IPv4 addressing, see [Getting Started with IPv6 for Amazon VPC](get-started-ipv6.md)\.