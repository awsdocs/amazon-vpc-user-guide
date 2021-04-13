# VPC endpoint services \(AWS PrivateLink\)<a name="endpoint-service"></a>

You can create your own application in your VPC and configure it as an AWS PrivateLink\-powered service \(referred to as an *endpoint service*\)\. Other AWS principals can create a connection from their VPC to your endpoint service using an [interface VPC endpoint](vpce-interface.md) or a [Gateway Load Balancer endpoint](vpce-gateway-load-balancer.md), depending on the type of service\. You are the *service provider*, and the AWS principals that create connections to your service are *service consumers*\.

**Topics**
+ [VPC endpoint services for interface endpoints](endpoint-service-overview.md)
+ [VPC endpoint services for Gateway Load Balancer endpoints](vpc-endpoint-services-gwlbe.md)
+ [Create a VPC endpoint service configuration for interface endpoints](create-endpoint-service.md)
+ [Create a VPC endpoint service configuration for Gateway Load Balancer endpoints](create-endpoint-service-gwlbe.md)
+ [Add and remove permissions for your endpoint service](add-endpoint-service-permissions.md)
+ [Change the load balancers and acceptance settings](modify-endpoint-service.md)
+ [Accept and reject endpoint connection requests](accept-reject-endpoint-requests.md)
+ [Create and manage a notification for an endpoint service](create-notification-endpoint-service.md)
+ [Add or remove VPC endpoint service tags](modify-tags-vpc-endpoint-service-tags.md)
+ [Delete an endpoint service configuration](delete-endpoint-service.md)