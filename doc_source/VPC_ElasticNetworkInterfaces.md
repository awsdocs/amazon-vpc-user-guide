# Elastic network interfaces<a name="VPC_ElasticNetworkInterfaces"></a>

An elastic network interface \(referred to as a *network interface* in this documentation\) is a virtual network interface that can include the following attributes:
+ a primary private IPv4 address
+ one or more secondary private IPv4 addresses
+ one Elastic IP address per private IPv4 address
+ one public IPv4 address, which can be auto\-assigned to the network interface for eth0 when you launch an instance
+ one or more IPv6 addresses
+ one or more security groups
+ a MAC address
+ a source/destination check flag
+ a description

You can create a network interface, attach it to an instance, detach it from an instance, and attach it to another instance\. A network interface's attributes follow it as it is attached or detached from an instance and reattached to another instance\. When you move a network interface from one instance to another, network traffic is redirected to the new instance\.

Each instance in your VPC has a default network interface \(the primary network interface\) that is assigned a private IPv4 address from the IPv4 address range of your VPC\. You cannot detach a primary network interface from an instance\. You can create and attach an additional network interface to any instance in your VPC\. The number of network interfaces you can attach varies by instance type\. For more information, see [IP addresses per network interface per instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI) in the *Amazon EC2 User Guide for Linux Instances*\.

Attaching multiple network interfaces to an instance is useful when you want to:
+ Create a management network\.
+ Use network and security appliances in your VPC\.
+ Create dual\-homed instances with workloads/roles on distinct subnets\.
+ Create a low\-budget, high\-availability solution\.

For more information about network interfaces and instructions for working with them using the Amazon EC2 console, see [Elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.