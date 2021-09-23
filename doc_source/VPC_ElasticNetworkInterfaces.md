# Elastic network interfaces<a name="VPC_ElasticNetworkInterfaces"></a>

An *elastic network interface* \(referred to as a *network interface* in this documentation\) is a logical networking component in a VPC that represents a virtual network card\. It can include the following attributes:
+ A primary private IPv4 address
+ One or more secondary private IPv4 addresses
+ One Elastic IP address per private IPv4 address
+ One public IPv4 address, which can be auto\-assigned to the network interface for eth0 when you launch an instance
+ One or more IPv6 addresses
+ One or more security groups
+ A MAC address
+ A source/destination check flag
+ A description

You can create a network interface and attach it to an instance in the same Availability Zone\. The attributes as a network interface follow it as it is attached or detached from an instance and reattached to another instance\. When you move a network interface from one instance to another, network traffic is redirected to the new instance\.

For more information about network interfaces and instructions for working with them using the Amazon EC2 console, see [Elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Your account might also have *requester\-managed* network interfaces, which are created and managed by AWS services to enable you to use other resources and services\. You cannot manage these network interfaces yourself\. For more information, see [Requester\-managed network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/requester-managed-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.