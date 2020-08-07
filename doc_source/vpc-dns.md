# Using DNS with your VPC<a name="vpc-dns"></a>

Domain Name System \(DNS\) is a standard by which names used on the Internet are resolved to their corresponding IP addresses\. A DNS hostname is a name that uniquely and absolutely names a computer; it's composed of a host name and a domain name\. DNS servers resolve DNS hostnames to their corresponding IP addresses\. 

Public IPv4 addresses enable communication over the Internet, while private IPv4 addresses enable communication within the network of the instance \(either EC2\-Classic or a VPC\)\. For more information, see [IP Addressing in your VPC](vpc-ip-addressing.md)\.

We provide an Amazon DNS server\. To use your own DNS server, create a new set of DHCP options for your VPC\. For more information, see [DHCP options sets](VPC_DHCP_Options.md)\.

**Topics**
+ [DNS hostnames](#vpc-dns-hostnames)
+ [DNS support in your VPC](#vpc-dns-support)
+ [DNS quotas](#vpc-dns-limits)
+ [Viewing DNS hostnames for your EC2 instance](#vpc-dns-viewing)
+ [Viewing and updating DNS support for your VPC](#vpc-dns-updating)
+ [Using private hosted zones](#vpc-private-hosted-zones)

## DNS hostnames<a name="vpc-dns-hostnames"></a>

When you launch an instance into a default VPC, we provide the instance with public and private DNS hostnames that correspond to the public IPv4 and private IPv4 addresses for the instance\. When you launch an instance into a nondefault VPC, we provide the instance with a private DNS hostname and we might provide a public DNS hostname, depending on the [DNS attributes](#vpc-dns-support) you specify for the VPC and if your instance has a public IPv4 address\. For more information, see [Public IPv4 addresses and external DNS hostnames](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#concepts-public-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.

An Amazon\-provided private \(internal\) DNS hostname resolves to the private IPv4 address of the instance, and takes the form `ip-private-ipv4-address.ec2.internal` for the `us-east-1` Region, and `ip-private-ipv4-address.region.compute.internal` for other Regions \(where `private-ipv4-address` is the reverse lookup IP address\)\. You can use the private DNS hostname for communication between instances in the same network, but we can't resolve the DNS hostname outside the network that the instance is in\.

A public \(external\) DNS hostname takes the form `ec2-public-ipv4-address.compute-1.amazonaws.com` for the `us-east-1` Region, and `ec2-public-ipv4-address.region.compute.amazonaws.com` for other Regions\. The Amazon DNS server resolves a public DNS hostname to the public IPv4 address of the instance outside the network of the instance, and to the private IPv4 address of the instance from within the network of the instance\.

We do not provide DNS hostnames for IPv6 addresses\.

## DNS support in your VPC<a name="vpc-dns-support"></a>

Your VPC has attributes that determine whether instances launched in the VPC receive public DNS hostnames that correspond to their public IP addresses, and whether DNS resolution through the Amazon DNS server is supported for the VPC\. 


****  

| Attribute | Description | 
| --- | --- | 
| enableDnsHostnames |  Indicates whether instances with public IP addresses get corresponding public DNS hostnames\. If this attribute is `true`, instances in the VPC get public DNS hostnames, but only if the `enableDnsSupport` attribute is also set to `true`\.  | 
| enableDnsSupport |  Indicates whether the DNS resolution is supported\. If this attribute is `false`, the Amazon Route 53 Resolver server that resolves public DNS hostnames to IP addresses is not enabled\. If this attribute is `true`, queries to the Amazon provided DNS server at the 169\.254\.169\.253 IP address, or the reserved IP address at the base of the VPC IPv4 network range plus two will succeed\. For more information, see [Amazon DNS server](VPC_DHCP_Options.md#AmazonDNS)\.  | 

If both attributes are set to `true`, the following occurs:
+ Instances with a public IP address receive corresponding public DNS hostnames\.
+ The Amazon Route 53 Resolver server can resolve Amazon\-provided public DNS hostnames\.

If either or both of the attributes is set to `false`, the following occurs:
+ Instances with a public IP address do not receive corresponding public DNS hostnames\.
+ The Amazon Route 53 Resolver cannot resolve Amazon\-provided public DNS hostnames\.
+ Instances receive custom private DNS hostnames if there is a custom domain name in the [DHCP options set](VPC_DHCP_Options.md)\. If you are not using the Amazon Route 53 Resolver server, your custom domain name servers must resolve the hostname as appropriate\.

By default, both attributes are set to `true` in a default VPC or a VPC created by the VPC wizard\. By default, only the `enableDnsSupport` attribute is set to `true` in a VPC created any other way\. To check if your VPC is enabled for these attributes, see [Viewing and updating DNS support for your VPC](#vpc-dns-updating)\.

**Important**  
If you use custom DNS domain names defined in a private hosted zone in Amazon Route 53, or use private DNS with interface VPC endpoints \(AWS PrivateLink\), you must set the `enableDnsHostnames` and `enableDnsSupport` attributes to `true`\.

The Amazon Route 53 Resolver can resolve private DNS hostnames to private IPv4 addresses for all address spaces, including where the IPv4 address range of your VPC falls outside of the private IPv4 addresses ranges specified by [RFC 1918](https://tools.ietf.org/html/rfc1918)\.

**Important**  
If you created your VPC before October 2016, the Amazon DNS server does not resolve private DNS hostnames if your VPC's IPv4 address range falls outside of the private IPv4 addresses ranges specified by RFC 1918\. If you want to enable the Amazon DNS server to resolve private DNS hostnames for these addresses, contact [AWS Support](https://aws.amazon.com/contact-us/)\.

If you enable DNS hostnames and DNS support in a VPC that didn't previously support them, an instance that you already launched into that VPC gets a public DNS hostname if it has a public IPv4 address or an Elastic IP address\.

## DNS quotas<a name="vpc-dns-limits"></a>

Each EC2 instance limits the number of packets that can be sent to the Amazon Route 53 Resolver \(specifically the \.2 address, such as 10\.0\.0\.2\) to a maximum of 1024 packets per second per network interface\. This quota cannot be increased\. The number of DNS queries per second supported by the Amazon Route 53 Resolver varies by the type of query, the size of response, and the protocol in use\. For more information and recommendations for a scalable DNS architecture, see the [Hybrid Cloud DNS Solutions for Amazon VPC](https://d1.awsstatic.com/whitepapers/hybrid-cloud-dns-options-for-vpc.pdf) whitepaper\.

## Viewing DNS hostnames for your EC2 instance<a name="vpc-dns-viewing"></a>

You can view the DNS hostnames for a running instance or a network interface using the Amazon EC2 console or the command line\.

The **Public DNS \(IPv4\)** and **Private DNS** fields are available when the DNS options are enabled for the VPC that is associated with the instance\. For more information, see [DNS support in your VPC](#vpc-dns-support)\.

### Instance<a name="instance-dns"></a>

**To view DNS hostnames for an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance from the list\.

1. In the details pane, the **Public DNS \(IPv4\)** and **Private DNS** fields display the DNS hostnames, if applicable\.

**To view DNS hostnames for an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

### Network interface<a name="eni-dns"></a>

**To view the private DNS hostname for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface from the list\.

1. In the details pane, the **Private DNS \(IPv4\)** field displays the private DNS hostname\.

**To view DNS hostnames for a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) \(AWS CLI\)
+ [Get\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

## Viewing and updating DNS support for your VPC<a name="vpc-dns-updating"></a>

You can view and update the DNS support attributes for your VPC using the Amazon VPC console\.

**To describe and update DNS support for a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC from the list\.

1. Review the information in the **Description** tab\. In this example, both settings are enabled\.  
![\[The DNS Settings tab\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/dns-settings-gwt.png)

1. To update these settings, choose **Actions** and either **Edit DNS Resolution** or **Edit DNS Hostnames**\. In the dialog box that opens, choose **Yes** or **No**, and then choose **Save**\.

**To describe DNS support for a VPC using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [describe\-vpc\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-attribute.html) \(AWS CLI\)
+ [Get\-EC2VpcAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcAttribute.html) \(AWS Tools for Windows PowerShell\)

**To update DNS support for a VPC using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [modify\-vpc\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-attribute.html) \(AWS CLI\)
+ [Edit\-EC2VpcAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcAttribute.html) \(AWS Tools for Windows PowerShell\)

## Using private hosted zones<a name="vpc-private-hosted-zones"></a>

If you want to access the resources in your VPC using custom DNS domain names, such as example\.com, instead of using private IPv4 addresses or AWS\-provided private DNS hostnames, you can create a private hosted zone in Route 53\. A private hosted zone is a container that holds information about how you want to route traffic for a domain and its subdomains within one or more VPCs without exposing your resources to the Internet\. You can then create Route 53 resource record sets, which determine how Route 53 responds to queries for your domain and subdomains\. For example, if you want browser requests for example\.com to be routed to a web server in your VPC, you'll create an A record in your private hosted zone and specify the IP address of that web server\. For more information about creating a private hosted zone, see [Working with private hosted zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html) in the *Amazon Route 53 Developer Guide*\. 

To access resources using custom DNS domain names, you must be connected to an instance within your VPC\. From your instance, you can test that your resource in your private hosted zone is accessible from its custom DNS name by using the `ping` command; for example, `ping mywebserver.example.com`\. \(You must ensure that your instance's security group rules allow inbound ICMP traffic for the `ping` command to work\.\)

You can access a private hosted zone from an EC2\-Classic instance that is linked to your VPC using ClassicLink, provided your VPC is enabled for ClassicLink DNS support\. For more information, see [Enabling ClassicLink DNS support](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html#classiclink-enable-dns-support) in the *Amazon EC2 User Guide for Linux Instances*\. Otherwise, private hosted zones do not support transitive relationships outside of the VPC; for example, you cannot access your resources using their custom private DNS names from the other side of a VPN connection\. For more information, see [ClassicLink limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html#classiclink-limitations) in the *Amazon EC2 User Guide for Linux Instances*\.

**Important**  
If you use custom DNS domain names defined in a private hosted zone in Amazon Route 53, the `enableDnsHostnames` and `enableDnsSupport` attributes must be set to `true`\.