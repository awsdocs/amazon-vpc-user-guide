# Extend a VPC to a Local Zone, Wavelength Zone, or Outpost<a name="Extend_VPCs"></a>

You can host VPC resources, such as subnets, in multiple locations world\-wide\. These locations are composed of Regions, Availability Zones, Local Zones, and Wavelength Zones\. Each *Region* is a separate geographic area\. 
+ Availability Zones are multiple, isolated locations within each Region\.
+ Local Zones allow you to place resources, such as compute and storage, in multiple locations closer to your end users\.
+ AWS Outposts brings native AWS services, infrastructure, and operating models to virtually any data center, co\-location space, or on\-premises facility\.
+ Wavelength Zones allow developers to build applications that deliver ultra\-low latencies to 5G devices and end users\. Wavelength deploys standard AWS compute and storage services to the edge of telecommunication carriers' 5G networks\.

AWS operates state\-of\-the\-art, highly available data centers\. Although rare, failures can occur that affect the availability of instances that are in the same location\. If you host all of your instances in a single location that is affected by a failure, none of your instances would be available\.

To help you determine which deployment is best for you, see [AWS Wavelength FAQs](http://aws.amazon.com/wavelength/faqs/)\.

## Extend your VPC resources to Local Zones<a name="local-zone"></a>

AWS Local Zones allow you to place resources closer to your end users, and seamlessly connect to the full range of services in the AWS Region using familiar APIs and tool sets\. You can extend your VPC Region by creating a new subnet that has a Local Zone assignment\. When you create a subnet in a Local Zone, you extend the VPC to that Local Zone\. 

To use a Local Zone, you follow a three\-step process: 
+ First, opt in to the Local Zone\.
+ Next, create a subnet in the Local Zone\.
+ Finally, launch select resources in the Local Zone subnet, so that your applications are closer to your end users\.

When you create a VPC, you can choose to assign a set of Amazon\-provided public IP addresses to the VPC\. You can also set a network border group for the addresses that limits the addresses to the group\. When you set a network border group, the IP addresses can't move between network border groups\. Local Zone network traffic will go directly to the internet or to points\-of\-presence \(PoPs\) without traversing the Local Zone's parent Region, enabling access to low\-latency computing\. The complete list of Local Zones and their corresponding parent Regions can be found on the [AWS Local Zones](http://aws.amazon.com/about-aws/global-infrastructure/localzones/locations/) page\. 

The following rules apply to Local Zones:
+ The Local Zone subnets follow the same routing rules as Availability Zone subnets, including route tables, security groups, and network ACLs\.
+ You can assign subnets to Local Zones using the Amazon Virtual Private Cloud Console, AWS CLI, or API\.
+ You must provision public IP addresses for use in a Local Zone\. When you allocate addresses, you can specify the location from which the IP address is advertised\. We refer to this as a network border group, and you can set this parameter to limit the addresses to this location\. After you provision the IP addresses, you cannot move them between the Local Zone and the parent Region \(for example, from `us-west-2-lax-1a` to `us-west-2`\)\. 
+ You can request the IPv6 Amazon\-provided IP addresses and associate them with the network border group for a new or existing VPCs only for `us-west-2-lax-1a` and `use-west-2-lax-1b`\. All other Local Zones don't support IPv6\. 
+ Outbound internet traffic leaves a Local Zone from the Local Zone\.
+ You cannot create VPC endpoints inside Local Zone subnets\.

For more information about working with Local Zones, see the [AWS Local Zones User Guide](https://docs.aws.amazon.com/local-zones/latest/ug/)\.

### Considerations for internet gateways<a name="internet-gateway-local-zone-considerations"></a>

Take the following information into account when you use internet gateways \(in the parent Region\) in Local Zones:
+ You can use internet gateways in Local Zones with Elastic IP addresses or Amazon auto\-assigned public IP addresses\. The Elastic IP addresses that you associate must include the network border group of the Local Zone\. For more information, see [Associate Elastic IP addresses with resources in your VPC](vpc-eips.md)\.

  You cannot associate an Elastic IP address that is set for the Region\.
+ Elastic IP addresses that are used in Local Zones have the same quotas as Elastic IP addresses in a Region\. For more information, see [Elastic IP addresses](amazon-vpc-limits.md#vpc-limits-eips)\.
+ You can use internet gateways in route tables that are associated with Local Zone resources\. For more information, see [Routing to an internet gateway](route-table-options.md#route-tables-internet-gateway)\.

### Access Local Zones using a Direct Connect gateway<a name="access-local-zone"></a>

Consider the scenario where you want an on\-premises data center to access resources that are in a Local Zone\. You use a virtual private gateway for the VPC that's associated with the Local Zone to connect to a Direct Connect gateway\. The Direct Connect gateway connects to an AWS Direct Connect location in a Region\. The on\-premises data center has an AWS Direct Connect connection to the AWS Direct Connect location\.

**Note**  
Traffic within the US that is destined for a subnet in a Local Zone using Direct Connect does not travel through the parent Region of the Local Zone\. Instead, traffic takes the shortest path to the Local Zone\. This decreases latency and helps make your applications more responsive\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/dxgw-lz.png)

You configure the following resources for this configuration:
+ A virtual private gateway for the VPC that is associated with the Local Zone subnet\. You can view the VPC for the subnet on the subnet details page in the Amazon Virtual Private Cloud Console, or use [describe\-subnets](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-subnets.html)\.

  For information about creating a virtual private gateway, see [Create a target gateway](https://docs.aws.amazon.com/vpn/latest/s2svpn/SetUpVPNConnections.html#vpn-create-target-gateway) in the *AWS Site\-to\-Site VPN User Guide*\.
+ A Direct Connect connection\. For the best latency performance, AWS recommends that you use the [Direct Connect location](https://aws.amazon.com/about-aws/global-infrastructure/localzones/locations) closest to the Local Zone to which you'll be extending your subnet\.

  For information about ordering a connection, see [Cross connects](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Colocation.html#cross-connect-us-west-1) in the *AWS Direct Connect User Guide*\. 
+ A Direct Connect gateway\. For information about creating a Direct Connect gateway, see [Create a Direct Connect gateway](https://docs.aws.amazon.com/directconnect/latest/UserGuide/direct-connect-gateways-intro.html#create-direct-connect-gateway) in the *AWS Direct Connect User Guide*\.
+ A virtual private gateway association to connect the VPC to the Direct Connect gateway\. For information about creating a virtual private gateway association, see [Associating and disassociating virtual private gateways](https://docs.aws.amazon.com/directconnect/latest/UserGuide/virtualgateways.html#associate-vgw-with-direct-connect-gateway) in the *AWS Direct Connect User Guide*\.
+ A private virtual interface on the connection from the AWS Direct Connect location to the on\-premises data center\. For information about creating a Direct Connect gateway, see [Creating a private virtual interface to the Direct Connect gateway ](https://docs.aws.amazon.com/directconnect/latest/UserGuide/virtualgateways.html#create-private-vif-for-gateway) in the *AWS Direct Connect User Guide*\.

### Connect Local Zone subnets to a transit gateway<a name="connect-local-zone-tgw"></a>

You can't create a transit gateway attachment for a subnet in a Local Zone\. The following diagram shows how to configure your network so that subnets in the Local Zone connect to a transit gateway through the parent Availability Zone\. Create subnets in the Local Zones and subnets in the parent Availability Zones\. Connect the subnets in the parent Availability Zones to the transit gateway, and then create a route in the route table for each VPC that routes traffic destined for the other VPC CIDR to the network interface for the transit gateway attachment\.

**Note**  
Traffic destined for a subnet in a Local Zone that originates from a transit gateway will first traverse the parent Region\.

![\[Local Zone to transit gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/lz-tgw.png)

Create the following resources for this scenario:
+ A subnet in each parent Availability Zone\. For more information, see [Create a subnet](create-subnets.md)\.
+ A transit gateway\. For more information, see [Create a transit gateway](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-transit-gateways.html#create-tgw) in *Amazon VPC Transit Gateways*\.
+ A transit gateway attachment for each VPC using the parent Availability Zone\. For more information, see [Create a transit gateway attachment to a VPC](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-vpc-attachments.html#create-vpc-attachment) in *Amazon VPC Transit Gateways*\.
+ A transit gateway route table associated with the transit gateway attachment\. For more information, see [Transit gateway route tables](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-route-tables.html) in *Amazon VPC Transit Gateways*\.
+ For each VPC, an entry in the VPC route table that has the other VPC CIDR as the destination, and the ID of the network interface for the transit gateway attachment as the target\. To find the network interface for the transit gateway attachment, search the descriptions of your network interfaces for the ID of the transit gateway attachment\. For more information, see [Routing for a transit gateway](route-table-options.md#route-tables-tgw)\.

The following is an example route table for VPC 1\.


| Destination | Target | 
| --- | --- | 
|  *VPC 1 CIDR*  |  *local*  | 
|  *VPC 2 CIDR*  |  *vpc1\-attachment\-network\-interface\-id*  | 

The following is an example route table for VPC 2\.


| Destination | Target | 
| --- | --- | 
|  *VPC 2 CIDR*  |  *local*  | 
|  *VPC 1 CIDR*  | vpc2\-attachment\-network\-interface\-id | 

The following is an example of the transit gateway route table\. The CIDR blocks for each VPC propagate to the transit gateway route table\.


| CIDR | Attachment | Route type | 
| --- | --- | --- | 
|  *VPC 1 CIDR*  |  *Attachment for VPC 1*  |  propagated  | 
|  *VPC 2 CIDR*  |  *Attachment for VPC 2*  |  propagated  | 

## Extend your VPC resources to Wavelength Zones<a name="subnet-wavelength"></a>

*AWS Wavelength* allows developers to build applications that deliver ultra\-low latencies to mobile devices and end\-users\. Wavelength deploys standard AWS compute and storage services to the edge of telecommunication carriers' 5G networks\. Developers can extend an Amazon Virtual Private Cloud \(VPC\) to one or more Wavelength Zones, and then use AWS resources like Amazon Elastic Compute Cloud \(EC2\) instances to run applications that require ultra\-low latency and connect to AWS services in the Region\. 

To use a Wavelength Zones, you must first opt in to the Zone\. Next, create a subnet in the Wavelength Zone\. You can create Amazon EC2 instances, Amazon EBS volumes, and Amazon VPC subnets and carrier gateways in Wavelength Zones\. You can also use services that orchestrate or work with EC2, EBS, and VPC, such as Amazon EC2 Auto Scaling, Amazon EKS clusters, Amazon ECS clusters, Amazon EC2 Systems Manager, Amazon CloudWatch, AWS CloudTrail, and AWS CloudFormation\. The services in Wavelength are part of a VPC that is connected over a reliable, high bandwidth connection to an AWS Region for easy access to services including Amazon DynamoDB and Amazon RDS\.

The following rules apply to Wavelength Zones:
+ A VPC extends to a Wavelength Zone when you create a subnet in the VPC and associate it with the Wavelength Zone\.
+ By default, every subnet that you create in a VPC that spans a Wavelength Zone inherits the main VPC route table, including the local route\. 
+ When you launch an EC2 instance in a subnet in a Wavelength Zone, you assign a carrier IP address to it\. The carrier gateway uses the address for traffic from the interface to the internet, or mobile devices\. The carrier gateway uses NAT to translate the address, and then sends the traffic to the destination\. Traffic from the telecommunication carrier network routes through the carrier gateway\.
+ You can set the target of a VPC route table, or subnet route table in a Wavelength Zone to a carrier gateway, which allows inbound traffic from a carrier network in a specific location, and outbound traffic to the carrier network and internet\. For more information about routing options in a Wavelength Zone, see [Routing](https://docs.aws.amazon.com/wavelength/latest/developerguide/how-wavelengths-work.html#wavelength-routing-overview) in the *AWS Wavelength Developer Guide*\.
+ Subnets in Wavelength Zones have the same networking components as subnets in Availability Zones, including IPv4 addresses, DHCP option sets, and network ACLs\.
+ You can't create a transit gateway attachment to a subnet in a Wavelength Zone\. Instead, create the attachment through a subnet in the parent Availability Zone, and then route traffic to the desired destinations through the transit gateway\. For an example, see the next section\.

### Considerations for multiple Wavelength Zones<a name="multiple-wavelength-zones"></a>

EC2 instances that are in different Wavelength Zones in the same VPC are not allowed to communicate with each other\. If you need Wavelength Zone to Wavelength Zone communication, AWS recommends that you use multiple VPCs, one for each Wavelength Zone\. You can use a transit gateway to connect the VPCs\. This configuration enables communication between instances in the Wavelength Zones\.

Wavelength Zone to Wavelength Zone traffic routes through the AWS Region\. For more information, see [AWS Transit Gateway](http://aws.amazon.com/transit-gateway/)\.

The following diagram shows how to configure your network so that instances in two different Wavelength Zones can communicate\. You have two Wavelength Zones \(Wavelength Zone A and Wavelength Zone B\)\. You need to create the following resources to enable communication:
+ For each Wavelength Zone, a subnet in an Availability Zone that is the parent Availability Zone for the Wavelength Zone\. In the example, you create subnet 1 and subnet 2\. For information about creating subnets, see [Create a subnet](create-subnets.md)\. Use [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html) to find the parent zone\.
+ A transit gateway\. The transit gateway connects the VPCs\. For information about creating a transit gateway, see [Create a transit gateway](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-transit-gateways.html#create-tgw) in the *Amazon VPC Transit Gateways Guide*\.
+ For each VPC, a VPC attachment to the transit gateway in the parent Availability Zone of the Wavelength Zone\. For more information, see [Transit gateway attachments to a VPC](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-vpc-attachments.html) in the *Amazon VPC Transit Gateways Guide*\.
+ Entries for each VPC in the transit gateway route table\. For information about creating transit gateway routes, see [Transit gateway route tables](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-route-tables.html) in the *Amazon VPC Transit Gateways Guide*\.
+ For each VPC, an entry in the VPC route table that has the other VPC CIDR as the destination, and the transit gateway ID as the target\. For more information, see [Routing for a transit gateway](route-table-options.md#route-tables-tgw)\.

  In the example, the route table for VPC 1 has the following entry:     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/Extend_VPCs.html)

  The route table for VPC 2 has the following entry:     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/Extend_VPCs.html)

![\[Multiple Wavelength Zones\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/mult-wavelength-zones.png)

## Subnets in AWS Outposts<a name="outposts"></a>

AWS Outposts offers you the same AWS hardware infrastructure, services, APIs, and tools to build and run your applications on premises and in the cloud\. AWS Outposts is ideal for workloads that need low latency access to on\-premises applications or systems, and for workloads that need to store and process data locally\. For more information about AWS Outposts, see [AWS Outposts](http://aws.amazon.com/outposts)\.

Amazon VPC spans across all of the Availability Zones in an AWS Region\. When you connect Outposts to the parent Region, all existing and newly created VPCs in your account span across all Availability Zones and any associated Outpost locations in the Region\.

The following rules apply to AWS Outposts:
+ The subnets must reside in one Outpost location\.
+ A local gateway handles the network connectivity between your VPC and on\-premises networks\. For information about local gateways, see [Local Gateways](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-local-gateways.html) in the *AWS Outposts User Guide*\.
+ If your account is associated with AWS Outposts, you assign the subnet to an Outpost by specifying the Outpost ARN when you create the subnet\. 
+ By default, every subnet that you create in a VPC associated with an Outpost inherits the main VPC route table, including the local gateway route\. You can also explicitly associate a custom route table with the subnets in your VPC and have a local gateway as a next\-hop target for all traffic that needs to be routed to the on\-premises network\.