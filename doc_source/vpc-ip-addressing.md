# IP Addressing in your VPC<a name="vpc-ip-addressing"></a>

IP addresses enable resources in your VPC to communicate with each other, and with resources over the Internet\. Amazon EC2 and Amazon VPC support the IPv4 and IPv6 addressing protocols\. 

By default, Amazon EC2 and Amazon VPC use the IPv4 addressing protocol\. When you create a VPC, you must assign it an IPv4 CIDR block \(a range of private IPv4 addresses\)\. Private IPv4 addresses are not reachable over the Internet\. To connect to your instance over the Internet, or to enable communication between your instances and other AWS services that have public endpoints, you can assign a globally\-unique public IPv4 address to your instance\. 

You can optionally associate an IPv6 CIDR block with your VPC and subnets, and assign IPv6 addresses from that block to the resources in your VPC\. IPv6 addresses are public and reachable over the Internet\. 

**Note**  
To ensure that your instances can communicate with the Internet, you must also attach an Internet gateway to your VPC\. For more information, see [Internet gateways](VPC_Internet_Gateway.md)\.

Your VPC can operate in dual\-stack mode: your resources can communicate over IPv4, or IPv6, or both\. IPv4 and IPv6 addresses are independent of each other; you must configure routing and security in your VPC separately for IPv4 and IPv6\.

The following table summarizes the differences between IPv4 and IPv6 in Amazon EC2 and Amazon VPC\.


**IPv4 and IPv6 characteristics and restrictions**  

| IPv4 | IPv6 | 
| --- | --- | 
| The format is 32\-bit, 4 groups of up to 3 decimal digits\. | The format is 128\-bit, 8 groups of 4 hexadecimal digits\. | 
| Default and required for all VPCs; cannot be removed\. | Opt\-in only\. | 
| The VPC CIDR block size can be from /16 to /28\. | The VPC CIDR block size is fixed at /56\. | 
| The subnet CIDR block size can be from /16 to /28\. | The subnet CIDR block size is fixed at /64\. | 
| You can choose the private IPv4 CIDR block for your VPC\. | We choose the IPv6 CIDR block for your VPC from Amazon's pool of IPv6 addresses\. You cannot select your own range\. | 
| There is a distinction between private and public IP addresses\. To enable communication with the Internet, a public IPv4 address is mapped to the primary private IPv4 address through network address translation \(NAT\)\.  | No distinction between public and private IP addresses\. IPv6 addresses are public\. | 
| Supported on all instance types\. | Supported on all current generation instance types and the C3, R3, and I2 previous generation instance types\. For more information, see [Instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html)\. | 
| Supported in EC2\-Classic, and EC2\-Classic connections with a VPC via ClassicLink\. | Not supported in EC2\-Classic, and not supported for EC2\-Classic connections with a VPC via ClassicLink\. | 
| Supported on all AMIs\. | Automatically supported on AMIs that are configured for DHCPv6\. Amazon Linux versions 2016\.09\.0 and later and Windows Server 2008 R2 and later are configured for DHCPv6\. For other AMIs, you must [manually configure your instance](vpc-migrate-ipv6.md#vpc-migrate-ipv6-dhcpv6) to recognize any assigned IPv6 addresses\. | 
| An instance receives an Amazon\-provided private DNS hostname that corresponds to its private IPv4 address, and if applicable, a public DNS hostname that corresponds to its public IPv4 or Elastic IP address\. | Amazon\-provided DNS hostnames are not supported\. | 
| Elastic IPv4 addresses are supported\. | Elastic IPv6 addresses are not supported\.  | 
| Supported for customer gateways, virtual private gateways, NAT devices, and VPC endpoints\. | Not supported for customer gateways, virtual private gateways, NAT devices, and VPC endpoints\. | 

We support IPv6 traffic over a virtual private gateway to an AWS Direct Connect connection\. For more information, see the [AWS Direct Connect User Guide](https://docs.aws.amazon.com/directconnect/latest/UserGuide/)\.

## Private IPv4 addresses<a name="vpc-private-ipv4-addresses"></a>

Private IPv4 addresses \(also referred to as *private IP addresses* in this topic\) are not reachable over the Internet, and can be used for communication between the instances in your VPC\. When you launch an instance into a VPC, a primary private IP address from the IPv4 address range of the subnet is assigned to the default network interface \(eth0\) of the instance\. Each instance is also given a private \(internal\) DNS hostname that resolves to the private IP address of the instance\. If you don't specify a primary private IP address, we select an available IP address in the subnet range for you\. For more information about network interfaces, see [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.

You can assign additional private IP addresses, known as secondary private IP addresses, to instances that are running in a VPC\. Unlike a primary private IP address, you can reassign a secondary private IP address from one network interface to another\. A private IP address remains associated with the network interface when the instance is stopped and restarted, and is released when the instance is terminated\. For more information about primary and secondary IP addresses, see [Multiple IP Addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/MultipleIP.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Note**  
We refer to private IP addresses as the IP addresses that are within the IPv4 CIDR range of the VPC\. Most VPC IP address ranges fall within the private \(non\-publicly routable\) IP address ranges specified in RFC 1918; however, you can use publicly routable CIDR blocks for your VPC\. Regardless of the IP address range of your VPC, we do not support direct access to the Internet from your VPC's CIDR block, including a publicly\-routable CIDR block\. You must set up Internet access through a gateway; for example, an Internet gateway, virtual private gateway, a AWS Site\-to\-Site VPN connection, or AWS Direct Connect\.

## Public IPv4 addresses<a name="vpc-public-ipv4-addresses"></a>

All subnets have an attribute that determines whether a network interface created in the subnet automatically receives a public IPv4 address \(also referred to as a *public IP address* in this topic\)\. Therefore, when you launch an instance into a subnet that has this attribute enabled, a public IP address is assigned to the primary network interface \(eth0\) that's created for the instance\. A public IP address is mapped to the primary private IP address through network address translation \(NAT\)\. 

You can control whether your instance receives a public IP address by doing the following: 
+ Modifying the public IP addressing attribute of your subnet\. For more information, see [Modifying the public IPv4 addressing attribute for your subnet](#subnet-public-ip)\.
+ Enabling or disabling the public IP addressing feature during instance launch, which overrides the subnet's public IP addressing attribute\. For more information, see [Assigning a public IPv4 address during instance launch](#vpc-public-ip)\.

A public IP address is assigned from Amazon's pool of public IP addresses; it's not associated with your account\. When a public IP address is disassociated from your instance, it's released back into the pool, and is no longer available for you to use\. You cannot manually associate or disassociate a public IP address\. Instead, in certain cases, we release the public IP address from your instance, or assign it a new one\. For more information, see [Public IP addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#concepts-public-addresses) in the *Amazon EC2 User Guide for Linux Instances*\.

If you require a persistent public IP address allocated to your account that can be assigned to and removed from instances as you require, use an Elastic IP address instead\. For more information, see [Elastic IP addresses](vpc-eips.md)\.

If your VPC is enabled to support DNS hostnames, each instance that receives a public IP address or an Elastic IP address is also given a public DNS hostname\. We resolve a public DNS hostname to the public IP address of the instance outside the instance network, and to the private IP address of the instance from within the instance network\. For more information, see [Using DNS with your VPC](vpc-dns.md)\.

## IPv6 addresses<a name="vpc-ipv6-addresses"></a>

You can optionally associate an IPv6 CIDR block with your VPC and subnets\. For more information, see the following topics:
+ [Associating an IPv6 CIDR block with your VPC](working-with-vpcs.md#vpc-associate-ipv6-cidr)
+ [Associating an IPv6 CIDR block with your subnet](working-with-vpcs.md#subnet-associate-ipv6-cidr)

Your instance in a VPC receives an IPv6 address if an IPv6 CIDR block is associated with your VPC and your subnet, and if one of the following is true:
+ Your subnet is configured to automatically assign an IPv6 address to the primary network interface of an instance during launch\.
+ You manually assign an IPv6 address to your instance during launch\.
+ You assign an IPv6 address to your instance after launch\.
+ You assign an IPv6 address to a network interface in the same subnet, and attach the network interface to your instance after launch\. 

When your instance receives an IPv6 address during launch, the address is associated with the primary network interface \(eth0\) of the instance\. You can disassociate the IPv6 address from the primary network interface\. We do not support IPv6 DNS hostnames for your instance\.

An IPv6 address persists when you stop and start your instance, and is released when you terminate your instance\. You cannot reassign an IPv6 address while it's assigned to another network interface—you must first unassign it\.

You can assign additional IPv6 addresses to your instance by assigning them to a network interface attached to your instance\. The number of IPv6 addresses you can assign to a network interface, and the number of network interfaces you can attach to an instance varies per instance type\. For more information, see [IP Addresses Per Network Interface Per Instance Type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI) in the *Amazon EC2 User Guide*\.

IPv6 addresses are globally unique, and therefore reachable over the Internet\. You can control whether instances are reachable via their IPv6 addresses by controlling the routing for your subnet, or by using security group and network ACL rules\. For more information, see [Internetwork traffic privacy in Amazon VPC](VPC_Security.md)\.

For more information about reserved IPv6 address ranges, see [IANA IPv6 Special\-Purpose Address Registry](http://www.iana.org/assignments/iana-ipv6-special-registry/iana-ipv6-special-registry.xhtml) and [RFC4291](https://tools.ietf.org/html/rfc4291)\.

## IP addressing behavior for your subnet<a name="vpc-ip-addressing-subnet"></a>

All subnets have a modifiable attribute that determines whether a network interface created in that subnet is assigned a public IPv4 address and, if applicable, an IPv6 address\. This includes the primary network interface \(eth0\) that's created for an instance when you launch an instance in that subnet\.

Regardless of the subnet attribute, you can still override this setting for a specific instance during launch\. For more information, see [Assigning a public IPv4 address during instance launch](#vpc-public-ip) and [Assigning an IPv6 address during instance launch](#vpc-ipv6-during-launch)\. 

## Working with IP addresses<a name="vpc-working-with-ip-addresses"></a>

You can modify the IP addressing behavior of your subnet, assign a public IPv4 address to your instance during launch, and assign or unassign IPv6 addresses to and from your instance\.

**Topics**
+ [Modifying the public IPv4 addressing attribute for your subnet](#subnet-public-ip)
+ [Modifying the IPv6 addressing attribute for your subnet](#subnet-ipv6)
+ [Assigning a public IPv4 address during instance launch](#vpc-public-ip)
+ [Assigning an IPv6 address during instance launch](#vpc-ipv6-during-launch)
+ [Assigning an IPv6 address to an instance](#vpc-assign-ipv6-instance)
+ [Unassigning an IPv6 address from an instance](#vpc-unassign-ipv6-instance)
+ [API and Command overview](#ip-addressing-api-cli)

### Modifying the public IPv4 addressing attribute for your subnet<a name="subnet-public-ip"></a>

By default, nondefault subnets have the IPv4 public addressing attribute set to `false`, and default subnets have this attribute set to `true`\. An exception is a nondefault subnet created by the Amazon EC2 launch instance wizard — the wizard sets the attribute to `true`\. You can modify this attribute using the Amazon VPC console\.

**To modify your subnet's public IPv4 addressing behavior**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Subnet Actions**, **Modify auto\-assign IP settings**\.

1. The **Enable auto\-assign public IPv4 address** check box, if selected, requests a public IPv4 address for all instances launched into the selected subnet\. Select or clear the check box as required, and then choose **Save**\.

### Modifying the IPv6 addressing attribute for your subnet<a name="subnet-ipv6"></a>

By default, all subnets have the IPv6 addressing attribute set to `false`\. You can modify this attribute using the Amazon VPC console\. If you enable the IPv6 addressing attribute for your subnet, network interfaces created in the subnet receive an IPv6 address from the range of the subnet\. Instances launched into the subnet receive an IPv6 address on the primary network interface\. 

Your subnet must have an associated IPv6 CIDR block\.

**Note**  
If you enable the IPv6 addressing feature for your subnet, your network interface or instance only receives an IPv6 address if it's created using version `2016-11-15` or later of the Amazon EC2 API\. The Amazon EC2 console uses the latest API version\.

**To modify your subnet's IPv6 addressing behavior**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet and choose **Subnet Actions**, **Modify auto\-assign IP settings**\.

1. The **Enable auto\-assign IPv6 address** check box, if selected, requests an IPv6 address for all network interfaces created in the selected subnet\. Select or clear the check box as required, and then choose **Save**\.

### Assigning a public IPv4 address during instance launch<a name="vpc-public-ip"></a>

You can control whether your instance in a default or nondefault subnet is assigned a public IPv4 address during launch\.

**Important**  
You can't manually disassociate the public IPv4 address from your instance after launch\. Instead, it's automatically released in certain cases, after which you cannot reuse it\. If you require a persistent public IP address that you can associate or disassociate at will, associate an Elastic IP address with the instance after launch instead\. For more information, see [Elastic IP addresses](vpc-eips.md)\.

**To assign a public IPv4 address to an instance during launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. Choose an AMI and an instance type and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select a VPC from the **Network** list\. The **Auto\-assign Public IP** list is displayed\. Select **Enable** or **Disable** to override the default setting for the subnet\.
**Important**  
A public IPv4 address cannot be assigned if you specify more than one network interface\. Additionally, you cannot override the subnet setting using the auto\-assign public IPv4 feature if you specify an existing network interface for eth0\.

1. Follow the remaining steps in the wizard to launch your instance\.

1. On the **Instances** screen, select your instance\. On the **Description** tab, in the **IPv4 Public IP** field, you can view your instance's public IP address\. Alternatively, in the navigation pane, choose **Network Interfaces** and select the eth0 network interface for your instance\. You can view the public IP address in the **IPv4 Public IP** field\.
**Note**  
The public IPv4 address is displayed as a property of the network interface in the console, but it's mapped to the primary private IPv4 address through NAT\. Therefore, if you inspect the properties of your network interface on your instance, for example, through `ipconfig` on a Windows instance, or `ifconfig` on a Linux instance, the public IP address is not displayed\. To determine your instance's public IP address from within the instance, you can use instance metadata\. For more information, see [Instance metadata and user data](https://docs.aws.amazon.com/AWSEC2/latest/DeveloperGuide/ec2-instance-metadata.html)\.

This feature is only available during launch\. However, whether or not you assign a public IPv4 address to your instance during launch, you can associate an Elastic IP address with your instance after it's launched\. For more information, see [Elastic IP addresses](vpc-eips.md)\.

### Assigning an IPv6 address during instance launch<a name="vpc-ipv6-during-launch"></a>

You can auto\-assign an IPv6 address to your instance during launch\. To do this, you must launch your instance into a VPC and subnet that has an [associated IPv6 CIDR block](working-with-vpcs.md#vpc-associate-ipv6-cidr)\. The IPv6 address is assigned from the range of the subnet, and is assigned to the primary network interface \(eth0\)\. 

**To auto\-assign an IPv6 address to an instance during launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. Select an AMI and an instance type and choose **Next: Configure Instance Details**\.
**Note**  
Select an instance type that supports IPv6 addresses\.

1. On the **Configure Instance Details** page, select a VPC from **Network** and a subnet from **Subnet**\. For **Auto\-assign IPv6 IP**, choose **Enable**\.

1. Follow the remaining steps in the wizard to launch your instance\.

Alternatively, if you want to assign a specific IPv6 address from the subnet range to your instance during launch, you can assign the address to the primary network interface for your instance\.

**To assign a specific IPv6 address to an instance during launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. Select an AMI and an instance type and choose **Next: Configure Instance Details**\.
**Note**  
Select an instance type that supports IPv6 addresses\.

1. On the **Configure Instance Details** page, select a VPC from **Network** and a subnet from **Subnet**\.

1. Go to the **Network interfaces** section\. For the eth0 network interface, under **IPv6 IPs**, choose **Add IP**\.

1. Enter an IPv6 address from the range of the subnet\.

1. Follow the remaining steps in the wizard to launch your instance\.

For more information about assigning multiple IPv6 addresses to your instance during launch, see [Working with Multiple IPv6 Addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/MultipleIP.html#working-with-multiple-ipv6) in the *Amazon EC2 User Guide for Linux Instances*

### Assigning an IPv6 address to an instance<a name="vpc-assign-ipv6-instance"></a>

If your instance is in a VPC and subnet with an [associated IPv6 CIDR block](working-with-vpcs.md#vpc-associate-ipv6-cidr), you can use the Amazon EC2 console to assign an IPv6 address to your instance from the range of the subnet\. 

**To associate an IPv6 address with your instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. Choose **Actions**, **Networking**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Assign new IP**\. You can specify an IPv6 address from the range of the subnet, or leave the **Auto\-assign** value to let Amazon choose an IPv6 address for you\.

1. Choose **Save**\.

Alternatively, you can assign an IPv6 address to a network interface\. For more information, see [Assigning an IPv6 Address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#eni-assign-ipv6) in the *Elastic Network Interfaces* topic in the *Amazon EC2 User Guide for Linux Instances*\. 

### Unassigning an IPv6 address from an instance<a name="vpc-unassign-ipv6-instance"></a>

If you no longer need an IPv6 address for your instance, you can disassociate it from the instance using the Amazon EC2 console\.

**To disassociate an IPv6 address from your instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. Choose **Actions**, **Networking**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Unassign** for the IPv6 address\.

1. Choose **Save**\.

Alternatively, you can disassociate an IPv6 address from a network interface\. For more information, see [Unassigning an IPv6 Address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#eni-unassign-ipv6) in the *Elastic Network Interfaces* topic in the *Amazon EC2 User Guide for Linux Instances*\. 

### API and Command overview<a name="ip-addressing-api-cli"></a>

You can perform the tasks described on this page using the command line or an API\. For more information about the command line interfaces and a list of available APIs, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Assign a public IPv4 address during launch**
+ Use the `--associate-public-ip-address` or the `--no-associate-public-ip-address` option with the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. \(AWS CLI\)
+ Use the `-AssociatePublicIp` parameter with the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command\. \(AWS Tools for Windows PowerShell\)

**Assign an IPv6 address during launch**
+ Use the `--ipv6-addresses` option with the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. \(AWS CLI\)
+ Use the `-Ipv6Addresses` parameter with the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command\. \(AWS Tools for Windows PowerShell\)

**Modify a subnet's IP addressing behavior**
+ [modify\-subnet\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-subnet-attribute.html) \(AWS CLI\)
+ [Edit\-EC2SubnetAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2SubnetAttribute.html) \(AWS Tools for Windows PowerShell\)

**Assign an IPv6 address to a network interface**
+ [assign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html) \(AWS CLI\)
+ [Register\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)

**Unassign an IPv6 address from a network interface**
+ [unassign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-ipv6-addresses.html) \(AWS CLI\)
+ [Unregister\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)