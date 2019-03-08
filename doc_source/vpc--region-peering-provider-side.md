# Example: Service Provider Configures the Service<a name="vpc--region-peering-provider-side"></a>

Consider the following example, where a service runs on instances in Provider VPC 1\. Resources that are in Consumer VPC 1 through the AWS PrivateLink VPC endpoint in Consumer VPC 1\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-inter-region-peering-provider-side.png)

To allow resources that are in Consumer VPC 2 the service provider must complete the following steps:

1. Create Provider VPC 2\.

1. Configure VPC peering between Provider VPC 1 and Provider VPC 2 so that traffic can route between the two VPCs\.

1. Create Network Load Balancer 2 in Provider VPC 2\.

1. Configure target groups on Network Load Balancer 2 that point to the IP addresses of the service instances that are in VPC 1\.

1. Adjust the security groups that are associated with the service instances in Provider VPC 1 so that they allow traffic from Network Load Balancer 2\.

1. Create a VPC endpoint service configuration in Provider VPC 2 and associate it with Network Load Balancer 2\.