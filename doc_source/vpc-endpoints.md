# VPC endpoints<a name="vpc-endpoints"></a>

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by AWS PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\. 

Endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components\. They allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic\.

There are two types of VPC endpoints: *interface endpoints* and *gateway endpoints*\. Create the type of VPC endpoint required by the supported service\.

An [interface endpoint](vpce-interface.md) is an elastic network interface with a private IP address from the IP address range of your subnet that serves as an entry point for traffic destined to a supported service\. Interface endpoints are powered by AWS PrivateLink, a technology that enables you to privately access services by using private IP addresses\. AWS PrivateLink restricts all network traffic between your VPC and services to the Amazon network\. You do not need an internet gateway, a NAT device, or a virtual private gateway\.

For information about the AWS services that integrate with AWS PrivateLink, see [AWS services that you can use with AWS PrivateLink](integrated-services-vpce-list.md)\.

**Gateway endpoints**

A [gateway endpoint](vpce-gateway.md) is a gateway that you specify as a target for a route in your route table for traffic destined to a supported AWS service\. The following AWS services are supported:
+ Amazon S3
+ DynamoDB

To view all of the available AWS service names, see [Viewing available AWS service names](vpce-interface.md#vpce-view-services)\.