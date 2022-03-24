# What is Amazon VPC?<a name="what-is-amazon-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources into a virtual network that you've defined\. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS\.

## Amazon VPC concepts<a name="Overview"></a>

Amazon VPC is the networking layer for Amazon EC2\. If you're new to Amazon EC2, see [What is Amazon EC2?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) in the *Amazon EC2 User Guide for Linux Instances* to get a brief overview\.

The following are the key concepts for VPCs:
+ **Virtual private cloud \(VPC\)** — A virtual network dedicated to your AWS account\. 
+ **Subnet** — A range of IP addresses in your VPC\. 
+ **CIDR block** —Classless Inter\-Domain Routing\. An internet protocol address allocation and route aggregation methodology\. For more information, see [Classless Inter\-Domain Routing](http://en.wikipedia.org/wiki/CIDR_notation) in Wikipedia\.
+ **Route table** — A set of rules, called routes, that are used to determine where network traffic is directed\. 
+ **DHCP options sets**: Configuration information \(such as domain name and domain name server\) passed to EC2 instances when they are launched into VPC subnets\.
+ **Internet gateway** — A gateway that you attach to your VPC to enable communication between resources in your VPC and the internet\.
+ **Egress\-only internet gateways**: A type of internet gateway that allows an EC2 instance in a subnet to access the internet but prevents resources on the internet from initiating communication with the instance\.
+ **VPC endpoint** — Enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\.
+ **NAT gateway**: A managed AWS service that allows EC2 instances in private subnets to connect to the internet, other VPCs, or on\-premises networks\.
+ **NAT instance**: An EC2 instance in a public subnet that allows instances in private subnets to connect to the internet, other VPCs, or on\-premises networks\.
+ **Carrier gateways**: For subnets in Wavelength Zones, this type of gateway allows inbound traffic from a telecommunication carrier network in a specific location and outbound traffic to a telecommunication carrier network and the internet\.
+ **Prefix lists**: A collection of CIDR blocks that can be used to configure VPC security groups, VPC route tables, and AWS Transit Gateway route tables and can be shared with other AWS accounts using Resource Access Manager \(RAM\)\.
+ **Security groups**: Acts as a virtual firewall to control inbound and outbound traffic for an AWS resource, such as an EC2 instance\. Each VPC comes with a default security group, and you can create additional security groups\. A security group can be used only in the VPC for which it's created\.
+ **Network ACLs**: An optional layer of security for your VPC that acts as a firewall for controlling traffic in and out of your subnets\.

## Access Amazon VPC<a name="VPCInterfaces"></a>

You can create, access, and manage your VPCs using any of the following interfaces:
+ **AWS Management Console** — Provides a web interface that you can use to access your VPCs\.
+ **AWS Command Line Interface \(AWS CLI\)** — Provides commands for a broad set of AWS services, including Amazon VPC, and is supported on Windows, Mac, and Linux\. For more information, see [AWS Command Line Interface](https://aws.amazon.com/cli/)\.
+ **AWS SDKs** — Provides language\-specific APIs and takes care of many of the connection details, such as calculating signatures, handling request retries, and error handling\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.
+ **Query API** — Provides low\-level API actions that you call using HTTPS requests\. Using the Query API is the most direct way to access Amazon VPC, but it requires that your application handle low\-level details such as generating the hash to sign the request, and error handling\. For more information, see [Amazon VPC actions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/OperationList-query-vpc.html) in the *Amazon EC2 API Reference*\.

## Pricing for Amazon VPC<a name="Paying"></a>

There's no additional charge for using a VPC\. There are charges for some VPC components, such as NAT gateways, Reachability Analyzer, and traffic mirroring\. For more information, see [Amazon VPC Pricing](http://aws.amazon.com/vpc/pricing/)\.