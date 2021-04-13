# Example: Service consumer configures access<a name="vpc-region-peering-consumer-side"></a>

In the following example, a service runs on instances in Provider VPC\. Resources that are in Consumer VPC 3 can directly access the service through an interface endpoint in Consumer VPC 3\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-inter-region-peering-customer-side.png)

To allow resources that are in Consumer VPC 1 to privately access the service \(without creating an interface endpoint directly in Consumer VPC 1\), the service consumer can do the following:

1. Create Consumer VPC 2\.

1. Create an interface endpoint that spans one or more subnets in Consumer VPC 2\. For more information, see [Create an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#create-interface-endpoint)\.

1. Verify that the security groups that are associated with the interface endpoint in Consumer VPC 2 allow traffic from the instances in Consumer VPC 1\. Verify that the security groups that are associated with the instances in Consumer VPC 1 allow traffic to the interface endpoint in Consumer VPC 2\.

1. Create a VPC peering connection between Consumer VPC 1 and Consumer VPC 2 so that traffic is routed between the two VPCs\. For more information, see [Creating and accepting a VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/create-vpc-peering-connection.html)\.