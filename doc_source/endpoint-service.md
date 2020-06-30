# VPC endpoint services \(AWS PrivateLink\)<a name="endpoint-service"></a>

You can create your own application in your VPC and configure it as an AWS PrivateLink\-powered service \(referred to as an *endpoint service*\)\. Other AWS principals can create a connection from their VPC to your endpoint service using an [interface VPC endpoint](vpce-interface.md)\. You are the *service provider*, and the AWS principals that create connections to your service are *service consumers*\.

**Topics**
+ [Overview](#endpoint-service-overview)
+ [Endpoint service Availability Zone considerations](#vpce-endpoint-service-availability-zones)
+ [Endpoint service DNS names](#vpc-service-private-dns)
+ [Connection to on\-premises data centers](#on-premises-connection)
+ [Accessing services through a VPC peering connection](#endpoints-peering-connections)
+ [Using proxy protocol for connection information](#endpoint-service-proxy-protocol)
+ [Endpoint service limitations](#endpoint-service-limits)
+ [Creating a VPC endpoint service configuration](create-endpoint-service.md)
+ [Adding and removing permissions for your endpoint service](add-endpoint-service-permissions.md)
+ [Changing the Network Load Balancers and acceptance settings](modify-endpoint-service.md)
+ [Accepting and rejecting interface endpoint connection requests](accept-reject-endpoint-requests.md)
+ [Creating and managing a notification for an endpoint service](create-notification-endpoint-service.md)
+ [Adding or removing VPC endpoint service tags](modify-tags-vpc-endpoint-service-tags.md)
+ [Deleting an endpoint service configuration](delete-endpoint-service.md)

## Overview<a name="endpoint-service-overview"></a>

The following are the general steps to create an endpoint service\.

1. Create a Network Load Balancer for your application in your VPC and configure it for each subnet \(Availability Zone\) in which the service should be available\. The load balancer receives requests from service consumers and routes it to your service\. For more information, see [Getting Started with Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html) in the *User Guide for Network Load Balancers*\. We recommend that you configure your service in all Availability Zones within the Region\.

1. Create a VPC endpoint service configuration and specify your Network Load Balancer\.

The following are the general steps to enable service consumers to connect to your service\.

1. Grant permissions to specific service consumers \(AWS accounts, IAM users, and IAM roles\) to create a connection to your endpoint service\.

1. A service consumer that has been granted permissions creates an interface endpoint to your service, optionally in each Availability Zone in which you configured your service\.

1. To activate the connection, accept the interface endpoint connection request\. By default, connection requests must be manually accepted\. However, you can configure the acceptance settings for your endpoint service so that any connection requests are automatically accepted\.

The combination of permissions and acceptance settings can help you control which service consumers \(AWS principals\) can access your service\. For example, you can grant permissions to selected principals that you trust and automatically accept all connection requests, or you can grant permissions to a wider group of principals and manually accept specific connection requests that you trust\.

In the following diagram, the account owner of VPC B is a service provider, and has a service running on instances in subnet B\. The owner of VPC B has a service endpoint \(vpce\-svc\-1234\) with an associated Network Load Balancer that points to the instances in subnet B as targets\. Instances in subnet A of VPC A use an interface endpoint to access the services in subnet B\.

![\[Using an interface endpoint to access an endpoint service\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-service.png)

For low latency and fault tolerance, we recommend using a Network Load Balancer with targets in every Availability Zone of the AWS Region\. To help achieve high availability for service consumers that use [zonal DNS hostnames](vpce-interface.md#access-service-though-endpoint) to access the service, you can enable cross\-zone load balancing\. Cross\-zone load balancing enables the load balancer to distribute traffic across the registered targets in all enabled Availability Zones\. For more information, see [Cross\-Zone Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancers.html#cross-zone-load-balancing) in the *User Guide for Network Load Balancers*\. Regional data transfer charges may apply to your account when you enable cross\-zone load balancing\.

In the following diagram, the owner of VPC B is the service provider, and it has configured a Network Load Balancer with targets in two different Availability Zones\. The service consumer \(VPC A\) has created interface endpoints in the same two Availability Zones in their VPC\. Requests to the service from instances in VPC A can use either interface endpoint\.

![\[Using interface endpoints to access an endpoint service\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-service-multi-az.png)

For examples of configuring a service and enabling service consumers to access it over a VPC peering connection, see [Examples: Services using AWS PrivateLink and VPC peering](vpc-peer-region-example.md)\.

## Endpoint service Availability Zone considerations<a name="vpce-endpoint-service-availability-zones"></a>

When you create an endpoint service, the service is created in the Availability Zone that is mapped to your account and is independent from other accounts\. When the service provider and the consumer are in different accounts, use the Availability Zone ID to uniquely and consistently identify the endpoint service Availability Zone\. For example, `use1-az1` is an AZ ID for the `us-east-1` Region and maps to the same location in every AWS account\. For information about Availability Zone IDs, see [AZ IDs for Your Resources](https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html) in the *AWS RAM User Guide* or use [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html)\. 

When the service provider and the consumer have different accounts and use multiple Availability Zones, and the consumer views the VPC endpoint service information, the response only includes the common Availability Zones\. For example, when the service provider account uses `us-east-1a` and `us-east-1c` and the consumer uses `us-east-1a` and `us-east-1b`, the response includes the VPC endpoint services in the common Availability Zone, `us-east-1a`\.

## Endpoint service DNS names<a name="vpc-service-private-dns"></a>

When you create a VPC endpoint service, AWS generates endpoint\-specific DNS hostnames that you can use to communicate with the service\. These names include the VPC endpoint ID, the Availability Zone name and Region Name, for example, vpce\-1234\-abcdev\-us\-east\-1\.vpce\-svc\-123345\.us\-east\-1\.vpce\.amazonaws\.com\. By default, your consumers access the service with that DNS name and usually need to modify the application configuration\. 

If the endpoint service is for an AWS service, or a service available in the AWS Marketplace, there is a default DNS name\. For other services, the service provider can configure a private DNS name so consumers can access the service using an existing DNS name without making changes to their applications\. For more information, see [Private DNS names for endpoint services](verify-domains.md)\.

Service providers can use the **ec2:VpceServicePrivateDnsName** condition context key in an IAM policy statement to control what private DNS names can be created\. For more information, see [Actions Defined by Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html) in the *IAM User Guide*\.

### Private DNS name requirements<a name="endpoint-dns-requirements"></a>

Service providers can specify a private DNS name for a new endpoint service, or an existing endpoint service\. To use a private DNS name, enable the feature, and then specify a private DNS name\. Before consumers can use the private DNS name, you must verify that you have control of the domain/subdomain\. You can initiate domain ownership verification using the Amazon VPC Console or API\. After the domain ownership verification completes, consumers access the endpoint by using the private DNS name\.

## Connection to on\-premises data centers<a name="on-premises-connection"></a>

You can use the following types of connections for a connection between an interface endpoint and your on\-premises data center:
+ AWS Direct Connect
+ AWS Site\-to\-Site VPN

## Accessing services through a VPC peering connection<a name="endpoints-peering-connections"></a>

You can use a VPC peering connection with a VPC endpoint to allow private access to consumers across the VPC peering connection\. For more information, see [Examples: Services using AWS PrivateLink and VPC peering](vpc-peer-region-example.md)\.

## Using proxy protocol for connection information<a name="endpoint-service-proxy-protocol"></a>

A Network Load Balancer provides source IP addresses to your application \(your service\)\. When service consumers send traffic to your service through an interface endpoint, the source IP addresses provided to your application are the private IP addresses of the Network Load Balancer nodes, and not the IP addresses of the service consumers\.

If you need the IP addresses of the service consumers and their corresponding interface endpoint IDs, enable Proxy Protocol on your load balancer and get the client IP addresses from the Proxy Protocol header\. For more information, see [Proxy Protocol](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html#proxy-protocol) in the *User Guide for Network Load Balancers*\.

## Endpoint service limitations<a name="endpoint-service-limits"></a>

To use endpoint services, you need to be aware of the current rules and limitations:
+ An endpoint service supports IPv4 traffic over TCP only\.
+ Service consumers can use the endpoint\-specific DNS hostnames to access the endpoint service, or the private DNS name\. 
+ If an endpoint service is associated with multiple Network Load Balancers, then for a specific Availability Zone, an interface endpoint establishes a connection with one load balancer only\.
+ For the endpoint service, the associated Network Load Balancer can support 55,000 simultaneous connections or about 55,000 connections per minute to each unique target \(IP address and port\)\. If you exceed these connections, there is an increased chance of port allocation errors\. To fix the port allocation errors, add more targets to the target group\. For information about Network Load Balancer target groups, see [Target Groups for Your Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html) and [Register Targets with Your Target Group](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/target-group-register-targets.html) in the *User Guide for Network Load Balancers*\.
+ Availability Zones in your account might not map to the same locations as Availability Zones in another account\. For example, your Availability Zone `us-east-1a` might not be the same location as `us-east-1a` for another account\. For more information, see [Region and Availability Zone Concepts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-regions-availability-zones)\. When you configure an endpoint service, it's configured in the Availability Zones as mapped to your account\.
+ Review the service\-specific limits for your endpoint service\.
+ Review the security best practices and examples for endpoint services\. For more information, see [Policy Best Practices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-policy-examples.html#security_iam_service-with-iam-policy-best-practices) and [Controlling access to services with VPC endpoints](vpc-endpoints-access.md)\.