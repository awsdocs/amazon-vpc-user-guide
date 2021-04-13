# Example: Service provider configures the service<a name="vpc--region-peering-provider-side"></a>

In the following example, a service runs on instances in Provider VPC 1\. Resources that are in Consumer VPC 1 can directly access the service through an interface endpoint in Consumer VPC 1\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-inter-region-peering-provider-side.png)

To allow resources that are in Consumer VPC 2 to privately access the service, the service provider must complete the following steps:

1. Create Provider VPC 2\.

1. Create a VPC peering connection between Provider VPC 1 and Provider VPC 2 so that traffic can route between the two VPCs\. For more information, see [Creating and accepting a VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/create-vpc-peering-connection.html)\.

1. Create Network Load Balancer 2 in Provider VPC 2\. For more information, see [Getting Started with Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html)\.

1. Create a target group\. For more information, see [Create a target group for your Network Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-target-group.html)\.

1. Configure target groups on Network Load Balancer 2 that point to the IP addresses of the service instances that are in Provider VPC 1\. For more information, see [Register targets with your target group](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/target-group-register-targets.html)\.

1. Adjust the security groups that are associated with the service instances in Provider VPC 1 so that they allow traffic from Network Load Balancer 2\. For more information, see [Target security groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/target-group-register-targets.html#target-security-groups)\.

1. Create a VPC endpoint service configuration in Provider VPC 2 and associate it with Network Load Balancer 2\. For more information, see [Create a VPC endpoint service configuration](https://docs.aws.amazon.com/vpc/latest/privatelink/create-endpoint-service.html)\.

1. The service consumer can then create an interface endpoint in Consumer VPC 2 to the service in Provider VPC 2\. For more information, see [Create an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#create-interface-endpoint)\.