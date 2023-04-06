# Connect VPCs using VPC peering<a name="vpc-peering"></a>

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them privately\. Resources in peered VPCs can communicate with each other as if they are within the same network\. You can create a VPC peering connection between your own VPCs, with a VPC in another AWS account, or with a VPC in a different AWS Region\. Traffic between peered VPCs never traverses the public internet\.

![\[A VPC peering connection\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/peering-intro-diagram.png)

AWS uses the existing infrastructure of a VPC to create a VPC peering connection\. A VPC peering connection is not a gateway or an AWS Site\-to\-Site VPN connection, and it does not rely on a separate piece of physical hardware\. There is no single point of failure for communication or a bandwidth bottleneck\. 

For more information, see the [Amazon VPC Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/)\.