# Example: Services that Span Regions Using AWS PrivateLink and Inter\-Region VPC Peering<a name="vpc-inter-region-example"></a>

An AWS PrivateLink service provider configures instances running services in their VPC, with a Network Load Balancer as the front end\. Service consumers that are in the same Region as the service provider can privately access those services through an AWS PrivateLink VPC endpoint\. Inter\-region VPC Peering enables resources that run in different Regions to communicate privately\. AWS PrivateLink and inter\-region VPC peering transport traffic over Amazon's network\. You can use a combination of inter\-region peering and AWS PrivateLink to extend access to private services across Regions\.

The service consumer or the service provider can complete the configuration\. For more information, see the following examples\.

**Topics**
+ [Example: Service Provider Configures a Service to Span Regions](vpc-inter-region-peering-provider-side.md)
+ [Example: Service Consumer Configures Access Across Regions](vpc-inter-region-peering-consumer-side.md)