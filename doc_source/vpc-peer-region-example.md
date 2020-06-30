# Examples: Services using AWS PrivateLink and VPC peering<a name="vpc-peer-region-example"></a>

An AWS PrivateLink service provider configures instances running services in their VPC, with a Network Load Balancer as the front end\. Use intra\-region VPC peering \(VPCs are in the same Region\) and inter\-region VPC peering \(VPCs are in different Regions\) with AWS PrivateLink to allow private access to consumers across VPC peering connections\.

Consumers in remote VPCs cannot use [Private DNS](verify-domains.md) names across peering connections\. They can however create their own private hosted zones on Route 53, and attach it to their VPCs to use the same Private DNS name\. For information about using transit gateway with Amazon Route 53 Resolver, to share PrivateLink interface endpoints between multiple connected VPCs and an on\-premises environment, see [Integrating AWS Transit Gateway with AWS PrivateLink and Amazon Route 53 Resolver](https://aws.amazon.com/blogs/networking-and-content-delivery/integrating-aws-transit-gateway-with-aws-privatelink-and-amazon-route-53-resolver/)\.

The following are example configurations using AWS PrivateLink and VPC peering\.

**Topics**
+ [Example: Service provider configures the service](vpc--region-peering-provider-side.md)
+ [Example: Service consumer configures access](vpc-region-peering-consumer-side.md)
+ [Example: Service provider configures a service to span Regions](vpc-inter-region-peering-provider-side.md)
+ [Example: Service consumer configures access across Regions](vpc-inter-region-peering-consumer-side.md)

**Additional resources**  
The following topics can help you configure the components needed for the examples:
+ [VPC endpoint services \(AWS PrivateLink\)](endpoint-service.md)
+ [Getting Started with Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html)
+ [Working with VPC peering connections](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html)
+ [Creating an interface endpoint](vpce-interface.md#create-interface-endpoint)

For more VPC peering examples, see the following topics in the *Amazon VPC Peering Guide*:
+ [VPC peering configurations](https://docs.aws.amazon.com/vpc/latest/peering/peering-configurations.html)
+ [Unsupported VPC peering configurations](https://docs.aws.amazon.com/vpc/latest/peering/invalid-peering-configurations.html)