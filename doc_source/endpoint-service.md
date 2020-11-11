# VPC endpoint services \(AWS PrivateLink\)<a name="endpoint-service"></a>

You can create your own application in your VPC and configure it as an AWS PrivateLink\-powered service \(referred to as an *endpoint service*\)\. Other AWS principals can create a connection from their VPC to your endpoint service using an [interface VPC endpoint](vpce-interface.md) or a [Gateway Load Balancer endpoint](vpce-gateway-load-balancer.md), depending on the type of service\. You are the *service provider*, and the AWS principals that create connections to your service are *service consumers*\.

**Topics**
+ [VPC endpoint services for interface endpoints](endpoint-service-overview.md)
+ [VPC endpoint services for Gateway Load Balancer endpoints](vpc-endpoint-services-gwlbe.md)
+ [Working with VPC endpoint services](working-with-endpoint-services.md)