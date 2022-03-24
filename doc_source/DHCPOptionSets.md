# What is DHCP?<a name="DHCPOptionSets"></a>

Every device on a TCP/IP network requires an IP address to communicate with the other devices across the network\. In the past, you assigned IP addresses to each device in your network manually\. Today, IP addresses are assigned dynamically by Dynamic Host Configuration Protocol \(DHCP\) servers\. To learn more about the interaction between a DHCP client and server, see [Dynamic Host Configuration Protocol \- Operation](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol#Operation)\.

The DHCP server is responsible for leasing IP addresses to DHCP clients and providing additional networking information that the clients need to communicate across the network, like the name of the DNS server in your network, the router IP address, and the subnet mask\.

In the AWS Cloud, EC2 instances running in VPC subnets can retrieve their IP address lease and additional networking information as needed from an AWS IPv4 or IPv6 DHCP server\.

**Note**  
If you have a VPC configuration that requires your applications to make direct requests to the Amazon DHCP server, the server may or may not provide the additional networking configuration information that you might be expecting\. Note the following limitations:   
An EC2 instance in a dual\-stack subnet can only retrieve it's IPv6 address from the IPv6 DHCP server\. It cannot retrieve any additional network configurations from the IPv6 DHCP server, such as DNS server names or domain names\.
An EC2 instance in a IPv6\-only subnet can retrieve it's IPv6 address from the IPv6 DHCP server and additional networking configuration information, such as DNS server names and domain names\. 

**What's next?**
+ [What are options sets?](WhatAreDHCPOptionSets.md) 
+ [Work with DHCP options sets](DHCPOptionSet.md) 