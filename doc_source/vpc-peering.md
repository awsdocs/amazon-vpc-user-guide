# VPC peering<a name="vpc-peering"></a>

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them privately\. Instances in either VPC can communicate with each other as if they are within the same network\. You can create a VPC peering connection between your own VPCs, with a VPC in another AWS account, or with a VPC in a different AWS Region\.

AWS uses the existing infrastructure of a VPC to create a VPC peering connection; it is neither a gateway nor an AWS Site\-to\-Site VPN connection, and does not rely on a separate piece of physical hardware\. There is no single point of failure for communication or a bandwidth bottleneck\. 

For more information about working with VPC peering connections, and examples of scenarios in which you can use a VPC peering connection, see the [Amazon VPC Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/)\.