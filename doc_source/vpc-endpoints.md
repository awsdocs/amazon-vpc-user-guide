# VPC endpoints<a name="vpc-endpoints"></a>

A VPC endpoint enables private connections between your VPC and supported AWS services and VPC endpoint services powered by AWS PrivateLink\. AWS PrivateLink is a technology that enables you to privately access services by using private IP addresses\. Traffic between your VPC and the other service does not leave the Amazon network\. A VPC endpoint does not require an internet gateway, virtual private gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. 

VPC endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components\. They allow communication between instances in your VPC and services without imposing availability risks\.

The following are the different types of VPC endpoints\. You create the type of VPC endpoint that's required by the supported service\.

** Interface endpoints**

An [interface endpoint](vpce-interface.md) is an elastic network interface with a private IP address from the IP address range of your subnet\. It serves as an entry point for traffic destined to a supported AWS service or a VPC endpoint service\. Interface endpoints are powered by AWS PrivateLink\.

For information about the AWS services that integrate with AWS PrivateLink, see [AWS services that you can use with AWS PrivateLink](integrated-services-vpce-list.md)\. You can also view all of the available AWS service names\. For more information, see [Viewing available AWS service names](vpce-interface.md#vpce-view-services)\.

 **Gateway Load Balancer endpoints**

A [Gateway Load Balancer endpoint](vpce-gateway-load-balancer.md) is an elastic network interface with a private IP address from the IP address range of your subnet\. Gateway Load Balancer endpoints are powered by AWS PrivateLink\. This type of endpoint serves as an entry point to intercept traffic and route it to a service that you've configured using [Gateway Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html), for example, for security inspection\. You specify a Gateway Load Balancer endpoint as a target for a route in a route table\. Gateway Load Balancer endpoints are supported for endpoint services that are configured for Gateway Load Balancers only\.

** Gateway endpoints**

A [gateway endpoint](vpce-gateway.md) is for supported AWS services only\. You specify a gateway endpoint as a route table target for traffic destined to the following AWS services:
+ Amazon S3
+ DynamoDB