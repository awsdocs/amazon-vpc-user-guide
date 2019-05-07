# Example: Services Using AWS PrivateLink and VPC Peering<a name="vpc-peer-region-example"></a>

An AWS PrivateLink service provider configures instances running services in their VPC, with a Network Load Balancer as the front end\. Use intra\-region VPC Peering \(VPCs are in the same Region\) and inter\-region VPC Peering \(VPCs are in different Regions\) with AWS PrivateLink to allow private access to consumers\.

The service consumer or the service provider can complete the configuration\. For more information, see the following examples\.

**Topics**
+ [Example: Service Provider Configures the Service](vpc--region-peering-provider-side.md)
+ [Example: Service Consumer Configures Access](vpc-region-peering-consumer-side.md)
+ [Example: Service Provider Configures a Service to Span Regions](vpc-inter-region-peering-provider-side.md)
+ [Example: Service Consumer Configures Access Across Regions](vpc-inter-region-peering-consumer-side.md)