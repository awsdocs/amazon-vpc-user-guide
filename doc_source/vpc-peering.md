# Connect VPCs using VPC peering<a name="vpc-peering"></a>

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them privately\. Instances in either VPC can communicate with each other as if they are within the same network\. You can create a VPC peering connection between your own VPCs, with a VPC in another AWS account, or with a VPC in a different AWS Region\.

AWS uses the existing infrastructure of a VPC to create a VPC peering connection; it is neither a gateway nor an AWS Site\-to\-Site VPN connection, and does not rely on a separate piece of physical hardware\. There is no single point of failure for communication or a bandwidth bottleneck\. 

For more information about working with VPC peering connections, and examples of scenarios in which you can use a VPC peering connection, see the [Amazon VPC Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/)\.

## Examples: Services using VPC peering and AWS PrivateLink<a name="vpc-peer-region-example"></a>

While VPC peering enables you to privately connect VPCs, AWS PrivateLink enables you to configure applications or services in VPCs as endpoints that your VPC peering connections can connect to\.

An AWS PrivateLink service provider configures instances running services in their VPC with a Network Load Balancer as the front end\. Use intra\-region VPC peering \(VPCs are in the same Region\) and inter\-region VPC peering \(VPCs are in different Regions\) with AWS PrivateLink to allow private access to consumers across VPC peering connections\.

Consumers in remote VPCs cannot use Private DNS names across peering connections\. They can however create their own private hosted zone on Route 53, and attach it to their VPCs to use the same Private DNS name\. For information about using transit gateway with Amazon Route 53 Resolver, to share PrivateLink interface endpoints between multiple connected VPCs and an on\-premises environment, see [Integrating AWS Transit Gateway with AWS PrivateLink and Amazon Route 53 Resolver](http://aws.amazon.com/blogs/networking-and-content-delivery/integrating-aws-transit-gateway-with-aws-privatelink-and-amazon-route-53-resolver/)\.

For information about the following use\-cases, see [Securely Access Services Over AWS PrivateLink](https://d1.awsstatic.com/whitepapers/aws-privatelink.pdf):
+ Private Access to SaaS Applications
+ Shared Services
+ Hybrid Services
+ Inter\-Region Endpoint Services
+ Inter\-Region Access to Endpoint Services

**Additional resources**  
The following topics can help you configure the components needed for the use\-cases:
+ [VPC endpoint services](https://docs.aws.amazon.com/vpc/latest/privatelink/endpoint-service.html)
+ [Getting Started with Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html)
+ [Working with VPC peering connections](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html)
+ [Create an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#create-interface-endpoint)

For more VPC peering examples, see the following topics in the *Amazon VPC Peering Guide*:
+ [VPC peering configurations](https://docs.aws.amazon.com/vpc/latest/peering/peering-configurations.html)
+ [Unsupported VPC peering configurations](https://docs.aws.amazon.com/vpc/latest/peering/invalid-peering-configurations.html)