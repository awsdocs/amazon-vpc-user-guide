# What is Amazon VPC?<a name="what-is-amazon-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources into a virtual network that you've defined\. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS\.

## Amazon VPC concepts<a name="Overview"></a>

Amazon VPC is the networking layer for Amazon EC2\. If you're new to Amazon EC2, see [What is Amazon EC2?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) in the *Amazon EC2 User Guide for Linux Instances* to get a brief overview\.

The following are the key concepts for VPCs:
+ A *virtual private cloud* \(VPC\) is a virtual network dedicated to your AWS account\. 
+ A *subnet* is a range of IP addresses in your VPC\. 
+ A *route table* contains a set of rules, called routes, that are used to determine where network traffic is directed\. 
+ An *internet gateway* is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the internet\. It therefore imposes no availability risks or bandwidth constraints on your network traffic\. 
+ A *VPC endpoint* enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\. 

## Accessing Amazon VPC<a name="VPCInterfaces"></a>

You can create, access, and manage your VPCs using any of the following interfaces:
+ **AWS Management Console**— Provides a web interface that you can use to access your VPCs\.
+ **AWS Command Line Interface \(AWS CLI\)** — Provides commands for a broad set of AWS services, including Amazon VPC, and is supported on Windows, Mac, and Linux\. For more information, see [AWS Command Line Interface](https://aws.amazon.com/cli/)\.
+ **AWS SDKs** — Provides language\-specific APIs and takes care of many of the connection details, such as calculating signatures, handling request retries, and error handling\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.
+ **Query API**— Provides low\-level API actions that you call using HTTPS requests\. Using the Query API is the most direct way to access Amazon VPC, but it requires that your application handle low\-level details such as generating the hash to sign the request, and error handling\. For more information, see the [Amazon EC2 API Reference](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/)\.

## Pricing for Amazon VPC<a name="Paying"></a>

There's no additional charge for using Amazon VPC\. You pay the standard rates for the instances and other Amazon EC2 features that you use\. There are charges for using a Site\-to\-Site VPN connection and using a NAT gateway\. For more information, see [Amazon VPC Pricing](https://aws.amazon.com/vpc/pricing/) and [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/)\.

## Amazon VPC quotas<a name="CurrentCapabilities"></a>

There are quotas on the number of Amazon VPC components that you can provision\. You can request an increase for some of these quotas\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

## Amazon VPC resources<a name="howto"></a>

To get a hands\-on introduction to Amazon VPC, complete [Getting started with Amazon VPC](vpc-getting-started.md)\. This exercise guides you through the steps to create a nondefault VPC with a public subnet, and to launch an instance into your subnet\.

If you have a default VPC, and you want to get started launching instances into your VPC without performing any additional configuration on your VPC, see [Launching an EC2 instance into your default VPC](default-vpc.md#launching-into)\.

To learn about the basic scenarios for Amazon VPC, see [Examples for VPC](VPC_Scenarios.md)\. You can configure your VPC and subnets in other ways to suit your needs\.

The following table lists related resources that you might find useful as you work with this service\.


|  Resource  |  Description  | 
| --- | --- | 
| [Amazon Virtual Private Cloud Connectivity Options](https://docs.aws.amazon.com/aws-technical-content/latest/aws-vpc-connectivity-options/introduction.html) | Provides an overview of the options for network connectivity\. | 
|  [Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html)  | Describes VPC peering connection scenarios and supported peering configurations\. | 
|  [Traffic Mirroring](https://docs.aws.amazon.com/vpc/latest/mirroring/what-is-traffic-mirroring.html)  | Describes traffic mirroring targets, filters, and sessions, and helps administrators configure them\. | 
|  [Transit Gateways](https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html)  | Describes transit gateways and helps network administrators configure them\. | 
|  [Transit Gateway Network Manager Guide](https://docs.aws.amazon.com/vpc/latest/tgw/what-is-network-manager.html)  | Describes Transit Gateway Network Manager and helps you configure and monitor a global network\. | 
|  [Amazon VPC forum](https://forums.aws.amazon.com/forum.jspa?forumID=58)  |  A community\-based forum for discussing technical questions related to Amazon VPC\.  | 
|  [Getting Started Resource Center](https://aws.amazon.com/getting-started/)  |  Information to help you get started building on AWS\.  | 
|  [AWS Support Center](https://console.aws.amazon.com/support/home#/)  |  The home page for AWS Support\.  | 
|  [Contact Us](https://aws.amazon.com/contact-us/)  |  A central contact point for inquiries concerning AWS billing, accounts, and events\.  | 

## PCI DSS compliance<a name="pci-compliance"></a>

Amazon VPC supports the processing, storage, and transmission of credit card data by a merchant or service provider, and has been validated as being compliant with Payment Card Industry \(PCI\) Data Security Standard \(DSS\)\. For more information about PCI DSS, including how to request a copy of the AWS PCI Compliance Package, see [PCI DSS Level 1](https://aws.amazon.com/compliance/pci-dss-level-1-faqs/)\. 