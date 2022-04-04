# Multiple middleboxes in the same VPC<a name="multiple-middlebox-configurations"></a>

## Same middlebox inspecting traffic for multiple subnets in the same VPC<a name="multiple-middleboxes"></a>

Consider the scenario where you have traffic coming into the VPC through an internet gateway and you want to inspect all traffic that is destined for subnet 1, using middlebox 1\. Within the same VPC, you want to use middlebox 2 and middlebox 1 to inspect traffic that is destined for subnet 2\. The following configuration is not supported, because for the route tables for the subnets associated with the middleboxes each need a route for `0.0.0.0/0 ` that routes traffic to the internet gateway\.

![\[Unsupported middlebox configuration\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/multiple-middlebox-not-supported.png)

If you want to have the same middlebox in this configuration, then the middlebox must be in the same hop position \(for example the hop after the internet gateway\) for both subnets\. This means that the route table for the subnet associated with middlebox 2 has a route for `0.0.0.0/0 ` that routes traffic to the the subnet for middlebox 1\. There is a route in the route table associated with the middlebox 1 that has a route for `0.0.0.0/0 ` that routes traffic to the internet gateway\.

![\[Supported middlebox configuration\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/multiple-middlebox-supported.png)
