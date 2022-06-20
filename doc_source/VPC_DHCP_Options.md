# DHCP option sets in Amazon VPC<a name="VPC_DHCP_Options"></a>

This section explains how network devices in your VPC use Dynamic Host Configuration Protocol \(DHCP\)\. It also explains the network communication parameters that are stored in DHCP option sets, and tells you how to customize the option sets used by devices in your VPC\.

DHCP option sets give you control over the following aspects of routing in your virtual network:
+ You can control the DNS servers, domain names, or Network Time Protocol \(NTP\) servers used by the devices in your VPC\.
+ You can disable DNS resolution completely in your VPC\.

**Topics**
+ [What is DHCP?](#DHCPOptionSets)
+ [DHCP option set concepts](DHCPOptionSetConcepts.md)
+ [Work with DHCP option sets](DHCPOptionSet.md)

## What is DHCP?<a name="DHCPOptionSets"></a>

Every device on a TCP/IP network requires an IP address to communicate over the network\. In the past, IP addresses had to be assigned to each device in your network manually\. Today, IP addresses are assigned dynamically by DHCP servers using the Dynamic Host Configuration Protocol \(DHCP\)\.

Applications running on EC2 instances in subnets can communicate with Amazon DHCP servers as needed to retrieve their IP address lease or other network configuration information \(such as the IP address of an Amazon DNS server or the IP address of the router in your VPC\)\.

Amazon VPC enables you to specify the network configurations that are provided by Amazon DHCP servers by using DHCP option sets\. 

**Note**  
If you have a VPC configuration that requires your applications to make direct requests to the Amazon IPv6 DHCP server, note the following limitation:   
An EC2 instance in a dual\-stack subnet can only retrieve its IPv6 address from the IPv6 DHCP server\. *It cannot retrieve any additional network configurations from the IPv6 DHCP server, such as DNS server names or domain names\.* 
An EC2 instance in a IPv6\-only subnet can retrieve its IPv6 address from the IPv6 DHCP server *and can retrieve additional networking configuration information, such as DNS server names and domain names\.* 

The Amazon DHCP servers can also provide an entire IPv4 or IPv6 prefix to an elastic network interface in your VPC using prefix delegation \(see [Assigning prefixes to Amazon EC2 network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-prefix-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\)\. IPv4 prefix delegation is not provided in DHCP responses\. IPv4 prefixes assigned to the interface can be retrieved using IMDS \(see [Instance metadata categories](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-categories.html) in the *Amazon EC2 User Guide for Linux Instances*\)\.

**What's next?**
+ [DHCP option set concepts](DHCPOptionSetConcepts.md) 
+ [Work with DHCP option sets](DHCPOptionSet.md) 