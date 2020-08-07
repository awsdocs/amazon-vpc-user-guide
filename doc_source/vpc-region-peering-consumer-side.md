# Example: Service consumer configures access<a name="vpc-region-peering-consumer-side"></a>

Consider the following example, where a service runs on instances in Provider VPC\. Resources that are in Consumer VPC 3 can directly access the service through an AWS PrivateLink VPC interface endpoint service in Consumer VPC 3\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-inter-region-peering-customer-side.png)

To allow resources that are in Consumer VPC 1 to privately access the service \(without creating an interface endpoint directly in Consumer VPC 1\), the service consumer can do the following:

1. Create Consumer VPC 2\.

1. Create a VPC interface endpoint that spans one or more subnets in Consumer VPC 2\.

1. Adjust the security groups associated with the VPC endpoint service in Consumer VPC 2 to allow traffic from the instances in Consumer VPC 1\. Adjust the security groups associated with the instances in Consumer VPC 1 to allow traffic to the VPC endpoint service in Consumer VPC 2\.

1. Configure VPC peering between Consumer VPC 1 and Consumer VPC 2 so that traffic is routed between the two VPCs\.