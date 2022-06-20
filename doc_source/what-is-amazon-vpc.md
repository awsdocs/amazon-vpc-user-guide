# What is Amazon VPC?<a name="what-is-amazon-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources into a virtual network that you've defined\. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS\.

## Access Amazon VPC<a name="VPCInterfaces"></a>

You can create, access, and manage your VPCs using any of the following interfaces:
+ **AWS Management Console** — Provides a web interface that you can use to access your VPCs\.
+ **AWS Command Line Interface \(AWS CLI\)** — Provides commands for a broad set of AWS services, including Amazon VPC, and is supported on Windows, Mac, and Linux\. For more information, see [AWS Command Line Interface](https://aws.amazon.com/cli/)\.
+ **AWS SDKs** — Provides language\-specific APIs and takes care of many of the connection details, such as calculating signatures, handling request retries, and error handling\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.
+ **Query API** — Provides low\-level API actions that you call using HTTPS requests\. Using the Query API is the most direct way to access Amazon VPC, but it requires that your application handle low\-level details such as generating the hash to sign the request, and error handling\. For more information, see [Amazon VPC actions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/OperationList-query-vpc.html) in the *Amazon EC2 API Reference*\.

## Pricing for Amazon VPC<a name="Paying"></a>

There's no additional charge for using a VPC\. There are charges for some VPC components, such as NAT gateways, Reachability Analyzer, and traffic mirroring\. For more information, see [Amazon VPC Pricing](http://aws.amazon.com/vpc/pricing/)\.