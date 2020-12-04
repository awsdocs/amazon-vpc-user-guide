# Example: Service provider configures a service to span Regions<a name="vpc-inter-region-peering-provider-side"></a>

In the following example, a service runs on instances in Provider VPC 1 in Region A, for example the us\-east\-1 Region\. Resources that are in Consumer VPC 1 in the same Region can directly access the service through an interface endpoint in Consumer VPC 1\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/inter-region-peering-provider-side.png)

To allow resources that are in Consumer VPC 2 in Region B \(for example, eu\-west\-1\) to privately access the service, the service provider must complete the following steps:

1. Create Provider VPC 2 in Region B\.

1. Create an inter\-region VPC peering connection between Provider VPC 1 and Provider VPC 2 so that traffic can route between the two VPCs\. For more information, see [Creating and accepting a VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/create-vpc-peering-connection.html)\.

1. Create Network Load Balancer 2 in Provider VPC 2\. For more information, see [Getting Started with Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html)\.

1. Configure target groups on Network Load Balancer 2 that point to the IP addresses of the service instances that are in Provider VPC 1\. For more information, see [Register targets with your target group](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/target-group-register-targets.html)\.

1. Adjust the security groups that are associated with the service instances in Provider VPC 1 so that they allow traffic from Network Load Balancer 2\. For more information, see [Target security groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/target-group-register-targets.html#target-security-groups)\.

1. Create a VPC endpoint service configuration in Provider VPC 2 and associate it with Network Load Balancer 2\. For more information, see [Creating a VPC endpoint service configuration for interface endpoints](create-endpoint-service.md)\.

1. The service consumer can then create an interface endpoint in Consumer VPC 2 to the service in Provider VPC 2\. For more information, see [Creating an interface endpoint](vpce-interface.md#create-interface-endpoint)\.

The account that owns Provider VPC 2 incurs the inter\-region peering data transfer charges, and Network Load Balancer charges\. The account that owns Provider VPC 1 incurs the service instances charges\.