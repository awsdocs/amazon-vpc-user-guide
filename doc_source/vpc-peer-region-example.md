# Example: Services Using AWS PrivateLink and VPC Peering<a name="vpc-peer-region-example"></a>

An AWS PrivateLink service provider configures instances running services in their VPC, with a Network Load Balancer as the front end\. Service consumers that are in the same Region as the service provider can privately access those services through an AWS PrivateLink VPC endpoint\. Intra\-region VPC Peering \(VPCs are in the same Region\) and Inter\-region VPC Peering \(VPCs are in different Regions\) enable resources that run in different Regions to communicate privately\. You can use intra\-region or inter\-region peering and AWS PrivateLink to extend access to private services across Regions\.

The service consumer or the service provider can complete the configuration\. For more information, see the following examples\.

**Topics**
+ [Example: Service Provider Configures the Service](vpc--region-peering-provider-side.md)
+ [Example: Service Consumer Configures Access](vpc-region-peering-consumer-side.md)