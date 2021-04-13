# Example: Service consumer configures access across Regions<a name="vpc-inter-region-peering-consumer-side"></a>

In the following example, a service runs on instances in Provider VPC in Region B, for example the us\-east\-1 Region\. Resources that are in Consumer VPC 3 can directly access the service through an interface endpoint in Consumer VPC 3\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/inter-region-peering-customer-side.png)

To allow resources that are in Consumer VPC 1 to privately access the service, the service consumer must complete the following steps:

1. Create Consumer VPC 2 in Region B\.

1. Create an interface endpoint that spans one or more subnets in Consumer VPC 2\. For more information, see [Create an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#create-interface-endpoint)\.

1. Ensure that the security groups that are associated with the interface endpoint in Consumer VPC 2 allow traffic from the instances in Consumer VPC 1\. Ensure that the security groups that are associated with the instances in Consumer VPC 1 allow traffic to the interface endpoint in Consumer VPC 2\.

1. Create an inter\-region VPC peering connection between Consumer VPC 1 and Consumer VPC 2 so that traffic is routed between the two VPCs\. For more information, see [Creating and accepting a VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/create-vpc-peering-connection.html)\.

The consumer account incurs the inter\-region peering data transfer charges, VPC endpoint data processing charges, and the VPC endpoint hourly charges\. The provider incurs the Network Load Balancer charges, and the service instances charges\.