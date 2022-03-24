# Connect your VPC to other VPCs and networks using a transit gateway<a name="extend-tgw"></a>

You can connect your virtual private clouds \(VPC\) and on\-premises networks using a transit gateway, which acts as a central hub, routing traffic between VPCs, VPN connections, and AWS Direct Connect connections\. For more information, see [AWS Transit Gateway](http://aws.amazon.com/transit-gateway/)\.

The following table describes some common use case for transit gateways and provides links to more information in the *Amazon VPC Transit Gateways*\.


| Example | Usage | 
| --- | --- | 
| Centralized router | Configure your transit gateway as a centralized router that connects all of your VPCs, AWS Direct Connect, and AWS Site\-to\-Site VPN connections\. For more information, see [Example: Centralized router](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-centralized-router.html)\. | 
| Isolated VPCs | Configure your transit gateway as multiple isolated routers\. This is similar to using multiple transit gateways, but provides more flexibility in cases where the routes and attachments might change\. For more information, see [Example: Isolated VPCs](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-isolated.html)\. | 
| Isolated VPCs with shared services | Configure your transit gateway as multiple isolated routers that use a shared service\. This is similar to using multiple transit gateways, but provides more flexibility in cases where the routes and attachments might change\. For more information, see [Example: Isolated VPCs with shared services](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-isolated-shared.html)\. | 