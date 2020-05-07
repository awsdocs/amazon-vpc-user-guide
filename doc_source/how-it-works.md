# How Amazon VPC works<a name="how-it-works"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources into a virtual network that you've defined\. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS\.

## Amazon VPC concepts<a name="Overview"></a>

As you get started with Amazon VPC, you should understand the key concepts of this virtual network, and how it is similar to or different from your own networks\. This section provides a brief description of the key concepts for Amazon VPC\.

Amazon VPC is the networking layer for Amazon EC2\. If you're new to Amazon EC2, see [What is Amazon EC2?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) in the *Amazon EC2 User Guide for Linux Instances* to get a brief overview\.

**Topics**
+ [VPCs and subnets](#how-it-works-subnet)
+ [Supported platforms](#what-is-supported-platform)
+ [Default and nondefault VPCs](#what-is-default-nondefault)
+ [Accessing the internet](#what-is-connectivity)
+ [Accessing a corporate or home network](#what-is-vpn)
+ [Accessing services through AWS PrivateLink](#what-is-privatelink)
+ [AWS private global network considerations](#what-is-aws-global-network)

### VPCs and subnets<a name="how-it-works-subnet"></a>

A *virtual private cloud* \(VPC\) is a virtual network dedicated to your AWS account\. It is logically isolated from other virtual networks in the AWS Cloud\. You can launch your AWS resources, such as Amazon EC2 instances, into your VPC\. You can specify an IP address range for the VPC, add subnets, associate security groups, and configure route tables\.

A *subnet* is a range of IP addresses in your VPC\. You can launch AWS resources into a specified subnet\. Use a public subnet for resources that must be connected to the internet, and a private subnet for resources that won't be connected to the internet\. For more information about public and private subnets, see [VPC and subnet basics](VPC_Subnets.md#vpc-subnet-basics)\.

To protect the AWS resources in each subnet, you can use multiple layers of security, including security groups and network access control lists \(ACL\)\. For more information, see [Internetwork traffic privacy in Amazon VPC](VPC_Security.md)\.

### Supported platforms<a name="what-is-supported-platform"></a>

The original release of Amazon EC2 supported a single, flat network that's shared with other customers called the *EC2\-Classic* platform\. Earlier AWS accounts still support this platform, and can launch instances into either EC2\-Classic or a VPC\. Accounts created after 2013\-12\-04 support EC2\-VPC only\. For more information, see [Detecting your supported platforms and whether you have a default VPC](default-vpc.md#detecting-platform)\.

By launching your instances into a VPC instead of EC2\-Classic, you gain the ability to:
+ Assign static private IPv4 addresses to your instances that persist across starts and stops
+ Optionally associate an IPv6 CIDR block to your VPC and assign IPv6 addresses to your instances
+ Assign multiple IP addresses to your instances
+ Define network interfaces, and attach one or more network interfaces to your instances
+ Change security group membership for your instances while they're running
+ Control the outbound traffic from your instances \(egress filtering\) in addition to controlling the inbound traffic to them \(ingress filtering\)
+ Add an additional layer of access control to your instances in the form of network access control lists \(ACL\)
+ Run your instances on single\-tenant hardware

### Default and nondefault VPCs<a name="what-is-default-nondefault"></a>

If your account supports the EC2\-VPC platform only, it comes with a *default VPC* that has a *default subnet* in each Availability Zone\. A default VPC has the benefits of the advanced features provided by EC2\-VPC, and is ready for you to use\. If you have a default VPC and don't specify a subnet when you launch an instance, the instance is launched into your default VPC\. You can launch instances into your default VPC without needing to know anything about Amazon VPC\. 

Regardless of which platforms your account supports, you can create your own VPC, and configure it as you need\. This is known as a *nondefault VPC*\. Subnets that you create in your nondefault VPC and additional subnets that you create in your default VPC are called *nondefault subnets*\.

### Accessing the internet<a name="what-is-connectivity"></a>

You control how the instances that you launch into a VPC access resources outside the VPC\.

Your default VPC includes an internet gateway, and each default subnet is a public subnet\. Each instance that you launch into a default subnet has a private IPv4 address and a public IPv4 address\. These instances can communicate with the internet through the internet gateway\. An internet gateway enables your instances to connect to the internet through the Amazon EC2 network edge\. 

![\[Using a default VPC\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/default-vpc-diagram.png)

By default, each instance that you launch into a nondefault subnet has a private IPv4 address, but no public IPv4 address, unless you specifically assign one at launch, or you modify the subnet's public IP address attribute\. These instances can communicate with each other, but can't access the internet\.

![\[Using a nondefault VPC\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/nondefault-vpc-diagram.png)

You can enable internet access for an instance launched into a nondefault subnet by attaching an internet gateway to its VPC \(if its VPC is not a default VPC\) and associating an Elastic IP address with the instance\.

![\[Using an internet gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/internet-gateway-diagram.png)

Alternatively, to allow an instance in your VPC to initiate outbound connections to the internet but prevent unsolicited inbound connections from the internet, you can use a network address translation \(NAT\) device for IPv4 traffic\. NAT maps multiple private IPv4 addresses to a single public IPv4 address\. A NAT device has an Elastic IP address and is connected to the internet through an internet gateway\. You can connect an instance in a private subnet to the internet through the NAT device, which routes traffic from the instance to the internet gateway, and routes any responses to the instance\.

For more information, see [NAT](vpc-nat.md)\.

You can optionally associate an IPv6 CIDR block with your VPC and assign IPv6 addresses to your instances\. Instances can connect to the internet over IPv6 through an internet gateway\. Alternatively, instances can initiate outbound connections to the internet over IPv6 using an egress\-only internet gateway\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\. IPv6 traffic is separate from IPv4 traffic; your route tables must include separate routes for IPv6 traffic\.

### Accessing a corporate or home network<a name="what-is-vpn"></a>

You can optionally connect your VPC to your own corporate data center using an IPsec AWS Site\-to\-Site VPN connection, making the AWS Cloud an extension of your data center\.

A Site\-to\-Site VPN connection consists of a virtual private gateway attached to your VPC and a customer gateway device located in your data center\. A virtual private gateway is the VPN concentrator on the Amazon side of the Site\-to\-Site VPN connection\. A customer gateway device is a physical device or software appliance on your side of the Site\-to\-Site VPN connection\.

![\[Using a virtual private gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/virtual-private-gateway-diagram.png)

For more information, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html) in the *AWS Site\-to\-Site VPN User Guide*\.

### Accessing services through AWS PrivateLink<a name="what-is-privatelink"></a>

AWS PrivateLink is a highly available, scalable technology that enables you to privately connect your VPC to supported AWS services, services hosted by other AWS accounts \(VPC endpoint services\), and supported AWS Marketplace partner services\. You do not require an internet gateway, NAT device, public IP address, AWS Direct Connect connection, or AWS Site\-to\-Site VPN connection to communicate with the service\. Traffic between your VPC and the service does not leave the Amazon network\.

To use AWS PrivateLink, create an interface VPC endpoint for a service in your VPC\. This creates an elastic network interface in your subnet with a private IP address that serves as an entry point for traffic destined to the service\. For more information, see [VPC endpoints](vpc-endpoints.md)\.

![\[Using an interface endpoint to access an AWS service\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-privatelink-diagram.png)

 You can create your own AWS PrivateLink\-powered service \(endpoint service\) and enable other AWS customers to access your service\. For more information, see [VPC endpoint services \(AWS PrivateLink\)](endpoint-service.md)\.

### AWS private global network considerations<a name="what-is-aws-global-network"></a>

AWS provides a high\-performance, and low\-latency private global network that delivers a secure cloud computing environment to support your networking needs\. AWS Regions are connected to multiple Internet Service Providers \(ISPs\) as well as to a private global network backbone, which provides improved network performance for cross\-Region traffic sent by customers\.

The following considerations apply:
+ Traffic that is in an Availability Zone, or between Availability Zones in all Regions, routes over the AWS private global network\.
+ Traffic that is between Regions always routes over the AWS private global network, except for China Regions\.

Network packet loss can be caused by a number of factors, including network flow collisions, lower level \(Layer 2\) errors, and other network failures\. We engineer and operate our networks to minimize packet loss\. We measure packet\-loss rate \(PLR\) across the global backbone that connects the AWS Regions\. We operate our backbone network to target a p99 of the hourly PLR of less than 0\.0001%\.