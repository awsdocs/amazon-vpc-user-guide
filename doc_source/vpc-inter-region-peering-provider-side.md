# Example: Service provider configures a service to span Regions<a name="vpc-inter-region-peering-provider-side"></a>

Consider the following example, where a service runs on instances in Provider VPC 1 in Region A, for example the us\-east\-1 Region\. Resources that are in Consumer VPC 1 in the same Region can directly access the service through the AWS PrivateLink VPC endpoint in Consumer VPC 1\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/inter-region-peering-provider-side.png)

To allow resources that are in Consumer VPC 2 in Region B, for example the eu\-west\-1 Region to privately access the service, the service provider must complete the following steps:

1. Create Provider VPC 2 in Region B\.

1. Configure VPC inter\-region peering between Provider VPC 1 and Provider VPC 2 so that traffic can route between the two VPCs\.

1. Create Network Load Balancer 2 in Provider VPC 2\.

1. Configure target groups on Network Load Balancer 2 that point to the IP addresses of the service instances that are in Provider VPC 1\.

1. Adjust the security groups that are associated with the service instances in Provider VPC 1 so that they allow traffic from Network Load Balancer 2\.

1. Create a VPC endpoint service configuration in Provider VPC 2 and associate it with Network Load Balancer 2\. The service consumer can then create an interface endpoint in Consumer VPC 2 to the service in Provider VPC 2\.

 The Provider 2 account incurs the inter\-region peering data transfer charges, Network Load Balancer charges\. The Provider 1 account incurs the service instances charges\.