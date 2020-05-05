# Example: Service consumer configures access across Regions<a name="vpc-inter-region-peering-consumer-side"></a>

Consider the following example, where a service runs on instances in Provider VPC in Region B, for example the us\-east\-1 Region\. Resources that are in Consumer VPC 3 can directly access the service through an AWS PrivateLink VPC interface endpoint in Consumer VPC 3\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/inter-region-peering-customer-side.png)

To allow resources that are in Consumer VPC 1 to privately access the service, the service consumer must complete the following steps:

1. Create Consumer VPC 2 in Region B\.

1. Create a VPC interface endpoint that spans one or more subnets in Consumer VPC 2\.

1. Adjust the security groups associated with the VPC endpoint service in Consumer VPC 2 to allow traffic from the instances in Consumer VPC 1\. Adjust the security groups associated with the instances in Consumer VPC 1 to allow traffic to the VPC endpoint service in Consumer VPC 2\.

1. Configure VPC inter\-region peering between Consumer VPC 1 and Consumer VPC 2 so that traffic is routed between the two VPCs\.

After the configuration is complete, Consumer VPC 1 can privately access the service\.

The consumer account incurs the inter\-region peering data transfer charges, VPC endpoint data processing charges, and the VPC endpoint hourly charges\. The provider incurs the Network Load Balancer charges, and the service instances charges\.