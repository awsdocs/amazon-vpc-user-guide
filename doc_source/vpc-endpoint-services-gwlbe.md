# VPC endpoint services for Gateway Load Balancer endpoints<a name="vpc-endpoint-services-gwlbe"></a>

You can use a Gateway Load Balancer to distribute traffic to a fleet of network virtual appliances\. The appliances can be used for security inspection, compliance, policy controls, and other networking services\. You can then configure the Gateway Load Balancer as a VPC endpoint service, to enable other AWS principals to access the service through a Gateway Load Balancer endpoint\.

The following are the general steps to create an endpoint service for Gateway Load Balancer endpoints\.

1. Create a Gateway Load Balancer for your virtual appliances\. For more information, see [Getting started with Gateway Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/getting-started.html)\.

   We recommend that you configure your service in all Availability Zones within the Region\.

1. Create a VPC endpoint service configuration and specify your Gateway Load Balancer\.

The following are the general steps to enable service consumers to connect to your service\.

1. Grant permissions to specific service consumers \(AWS accounts, IAM users, and IAM roles\) to create a connection to your endpoint service\.

1. A service consumer that has been granted permissions creates a [Gateway Load Balancer endpoint](vpce-gateway-load-balancer.md) to your service\.

1. To activate the connection, accept the endpoint connection request\. By default, connection requests must be manually accepted\. However, you can configure the acceptance settings for your endpoint service so that any connection requests are automatically accepted\.

In the following example, a fleet of security appliances is configured behind a Gateway Load Balancer in the security VPC\. An endpoint service is configured for the Gateway Load Balancer\. The owner of the service consumer VPC creates a Gateway Load Balancer endpoint in subnet 2 in their VPC \(represented by an endpoint network interface\)\. All traffic entering the VPC through the internet gateway is first routed to the Gateway Load Balancer endpoint for inspection in the security VPC before it's routed to the destination subnet\. Similarly, all traffic leaving the EC2 instance in subnet 1 is first routed to Gateway Load Balancer endpoint for inspection in the security VPC before it's routed to the internet\.

![\[Using a Gateway Load Balancer endpoint to access an endpoint service\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-service-gwlbe.png)

For more information about the routing configuration for this scenario, see [Routing to a Gateway Load Balancer endpoint](route-table-options.md#route-tables-gwlbe)\.

## Availability Zone considerations<a name="vpce-endpoint-service-availability-zones-gwlbe"></a>

When you create an endpoint service, the service is created in the Availability Zone that is mapped to your account and is independent from other accounts\. When the service provider and the consumer are in different accounts, use the Availability Zone ID to uniquely and consistently identify the endpoint service Availability Zone\. For example, `use1-az1` is an AZ ID for the `us-east-1` Region and maps to the same location in every AWS account\. For information about Availability Zone IDs, see [AZ IDs for Your Resources](https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html) in the *AWS RAM User Guide* or use [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html)\. 

When the service provider and the consumer have different accounts and use multiple Availability Zones, and the consumer views the VPC endpoint service information, the response only includes the common Availability Zones\. For example, when the service provider account uses `us-east-1a` and `us-east-1c` and the consumer uses `us-east-1a` and `us-east-1b`, the response includes the VPC endpoint services in the common Availability Zone, `us-east-1a`\.

## Rules and limitations<a name="endpoint-service-limits-gwlbe"></a>

To use endpoint services for Gateway Load Balancer endpoints, be aware of the current rules and limitations: 
+ If an endpoint service is associated with multiple Gateway Load Balancers, then for a specific Availability Zone, a Gateway Load Balancer endpoint establishes a connection with one load balancer only\.
+ Private DNS names are not supported\.
+ Availability Zones in your account might not map to the same locations as Availability Zones in another account\. For example, your Availability Zone `us-east-1a` might not be the same location as `us-east-1a` for another account\. For more information, see [Regions and Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-regions-availability-zones)\. When you configure an endpoint service, it's configured in the Availability Zones as mapped to your account\.