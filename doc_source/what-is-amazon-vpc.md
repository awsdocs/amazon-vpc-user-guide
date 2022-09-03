# What is Amazon VPC?<a name="what-is-amazon-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources into a virtual network that you've defined\. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS\.

## Features<a name="amazon-vpc-features"></a>

The following features help you configure a VPC to provide the connectivity that your applications need:

**Virtual private clouds \(VPC\)**  
A [VPC](configure-your-vpc.md) is a virtual network that closely resembles a traditional network that you'd operate in your own data center\. After you create a VPC, you can add subnets\.

**Subnets**  
A [subnet](configure-subnets.md) is a range of IP addresses in your VPC\. A subnet must reside in a single Availability Zone\. After you add subnets, you can deploy AWS resources in your VPC\.

**IP addressing**  
You can assign IPv4 addresses and IPv6 addresses to your VPCs and subnets\. You can also bring your public IPv4 and IPv6 GUA addresses to AWS and allocate them to resources in your VPC, such as EC2 instances, NAT gateways, and Network Load Balancers\.

**Routing**  
Use [route tables](VPC_Route_Tables.md) to determine where network traffic from your subnet or gateway is directed\.

**Gateways and endpoints**  
A [gateway](extend-intro.md) connects your VPC to another network\. For example, use an [internet gateway](VPC_Internet_Gateway.md) to connect your VPC to the internet\. Use a [VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-access-aws-services.html) to connect to AWS services privately, without the use of an internet gateway or NAT device\.

**Peering connections**  
Use a [VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/) to route traffic between the resources in two VPCs\.

**Traffic Mirroring**  
[Copy network traffic](https://docs.aws.amazon.com/vpc/latest/mirroring/) from network interfaces and send it to security and monitoring appliances for deep packet inspection\.

**Transit gateways**  
Use a [transit gateway](extend-tgw.md), which acts as a central hub, to route traffic between your VPCs, VPN connections, and AWS Direct Connect connections\.

**VPC Flow Logs**  
A [flow log](flow-logs.md) captures information about the IP traffic going to and from network interfaces in your VPC\.

**VPN connections**  
Connect your VPCs to your on\-premises networks using [AWS Virtual Private Network \(AWS VPN\)](vpn-connections.md)\.

## Getting started with Amazon VPC<a name="getting-started"></a>

Your AWS account includes a [default VPC](default-vpc.md) in each AWS Region\. Your default VPCs are configured such that you can immediately start launching and connecting to EC2 instances\. For more information, see [Get started with Amazon VPC](vpc-getting-started.md)\.

You can choose to create additional VPCs with the subnets, IP addresses, gateways and routing that you need\. For more information, see [Create a VPC](working-with-vpcs.md#Create-VPC)\.

## Working with Amazon VPC<a name="VPCInterfaces"></a>

You can create and manage your VPCs using any of the following interfaces:
+ **AWS Management Console** — Provides a web interface that you can use to access your VPCs\.
+ **AWS Command Line Interface \(AWS CLI\)** — Provides commands for a broad set of AWS services, including Amazon VPC, and is supported on Windows, Mac, and Linux\. For more information, see [AWS Command Line Interface](https://aws.amazon.com/cli/)\.
+ **AWS SDKs** — Provides language\-specific APIs and takes care of many of the connection details, such as calculating signatures, handling request retries, and error handling\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.
+ **Query API** — Provides low\-level API actions that you call using HTTPS requests\. Using the Query API is the most direct way to access Amazon VPC, but it requires that your application handle low\-level details such as generating the hash to sign the request, and error handling\. For more information, see [Amazon VPC actions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/OperationList-query-vpc.html) in the *Amazon EC2 API Reference*\.

## Pricing for Amazon VPC<a name="pricing"></a>

There's no additional charge for using a VPC\. There are charges for some VPC components, such as NAT gateways, Reachability Analyzer, and traffic mirroring\. For more information, see [Amazon VPC Pricing](http://aws.amazon.com/vpc/pricing/)\.