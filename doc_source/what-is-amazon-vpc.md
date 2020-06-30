# What is Amazon VPC?<a name="what-is-amazon-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources into a virtual network that you've defined\. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS\.

## Amazon VPC concepts<a name="Overview"></a>

Amazon VPC is the networking layer for Amazon EC2\. If you're new to Amazon EC2, see [What is Amazon EC2?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) in the *Amazon EC2 User Guide for Linux Instances* to get a brief overview\.

The following are the key concepts for VPCs:
+ **Virtual private cloud \(VPC\)** — A virtual network dedicated to your AWS account\. 
+ **Subnet** — A range of IP addresses in your VPC\. 
+ **Route table** — A set of rules, called routes, that are used to determine where network traffic is directed\. 
+ **Internet gateway** — A gateway that you attach to your VPC to enable communication between resources in your VPC and the internet\.
+ **VPC endpoint** — Enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\.

## Accessing Amazon VPC<a name="VPCInterfaces"></a>

You can create, access, and manage your VPCs using any of the following interfaces:
+ **AWS Management Console** — Provides a web interface that you can use to access your VPCs\.
+ **AWS Command Line Interface \(AWS CLI\)** — Provides commands for a broad set of AWS services, including Amazon VPC, and is supported on Windows, Mac, and Linux\. For more information, see [AWS Command Line Interface](https://aws.amazon.com/cli/)\.
+ **AWS SDKs** — Provides language\-specific APIs and takes care of many of the connection details, such as calculating signatures, handling request retries, and error handling\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.
+ **Query API** — Provides low\-level API actions that you call using HTTPS requests\. Using the Query API is the most direct way to access Amazon VPC, but it requires that your application handle low\-level details such as generating the hash to sign the request, and error handling\. For more information, see the [Amazon EC2 API Reference](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/)\.

## Pricing for Amazon VPC<a name="Paying"></a>

There's no additional charge for using Amazon VPC\. You pay the standard rates for the instances and other Amazon EC2 features that you use\. There are charges for using a Site\-to\-Site VPN connection, PrivateLink, Traffic Mirroring, and a NAT gateway\. For more information, see the following pricing pages:
+ [Amazon VPC Pricing](https://aws.amazon.com/vpc/pricing/)
+ [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/)

## Amazon VPC quotas<a name="vpc-quotas"></a>

There are quotas on the number of Amazon VPC components that you can provision\. You can request an increase for some of these quotas\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

## PCI DSS compliance<a name="pci-compliance"></a>

Amazon VPC supports the processing, storage, and transmission of credit card data by a merchant or service provider, and has been validated as being compliant with Payment Card Industry \(PCI\) Data Security Standard \(DSS\)\. For more information about PCI DSS, including how to request a copy of the AWS PCI Compliance Package, see [PCI DSS Level 1](https://aws.amazon.com/compliance/pci-dss-level-1-faqs/)\. 