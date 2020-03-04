# Example: Services Using AWS PrivateLink and VPC Peering<a name="vpc-peer-region-example"></a>

An AWS PrivateLink service provider configures instances running services in their VPC, with a Network Load Balancer as the front end\. Use intra\-region VPC Peering \(VPCs are in the same Region\) and inter\-region VPC Peering \(VPCs are in different Regions\) with AWS PrivateLink to allow private access to consumers across VPC peering connections\.

Consumers in remote VPCs cannot use [Private DNS Name](verify-domains.md) names across peering connections\. They can however create their own private hosted zones on Route 53, and attach it to their VPCs to use the same Private DNS name\. For information about using transit gateway with Amazon Route 53 Resolver, to share PrivateLink interface endpoints between multiple connected VPCs and an on\-premises environment, see [Integrating AWS Transit Gateway with AWS PrivateLink and Amazon Route 53 Resolver](https://aws.amazon.com/blogs/networking-and-content-delivery/integrating-aws-transit-gateway-with-aws-privatelink-and-amazon-route-53-resolver/)\.

For more information, see the following examples\.

**Topics**
+ [Example: Service Provider Configures the Service](vpc--region-peering-provider-side.md)
+ [Example: Service Consumer Configures Access](vpc-region-peering-consumer-side.md)
+ [Example: Service Provider Configures a Service to Span Regions](vpc-inter-region-peering-provider-side.md)
+ [Example: Service Consumer Configures Access Across Regions](vpc-inter-region-peering-consumer-side.md)