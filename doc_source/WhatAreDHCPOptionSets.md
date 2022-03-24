# What are options sets?<a name="WhatAreDHCPOptionSets"></a>

A DHCP options set is a group of network configurations used by EC2 instances in your VPC to communicate over your virtual network\. Each VPC has a default DHCP options set, but you can create a custom DHCP options set if, for example, you want instances in your VPC to use a third\-party DNS server for domain name resolution rather than the Amazon DNS server\. You can also disassociate all options sets from your VPC if you want to disable domain name resolution altogether\.

**Topics**
+ [What's in the default options set?](#WhatAreDHCPOptionSetsDefault)
+ [What's in a custom options set?](#WhatAreDHCPOptionSetsCustom)

## What's in the default options set?<a name="WhatAreDHCPOptionSetsDefault"></a>

Each VPC has a default options set that contains the following network configurations:
+ **DNS server**: The DNS name servers that your network interfaces will use for domain name resolution\.
+ **Domain name**: The domain name that EC2 instances in the VPC will use in their private hostnames\. 

If you use the default options set, instances launched into your VPC use the network configurations stored in the default options set \(1\) to reach the Amazon DNS server \(2\) and connect to other devices in the network via your VPC's router \(3\)\. The instances can, however, interact with the Amazon DHCP server at any time to get their IP address lease and their additional network configurations \(4\)\.

![\[Default DHCP option set\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/dhcp-default-update.png)

**What's next?**
+ [View your default options set](DHCPOptionSet.md#ViewDefaultDHCPOptionSet) 
+ [Change the options set associated with a VPC](DHCPOptionSet.md#ChangingDHCPOptionsofaVPC) 

## What's in a custom options set?<a name="WhatAreDHCPOptionSetsCustom"></a>

You can create your own DHCP options set in Amazon VPC\. This enables you to configure the following additional network configurations: 
+ **NTP servers**: The NTP servers that provide the time to the instances in your network\.
+ **NetBIOS name servers**: For Windows EC2 instances, the NetBIOS computer name is a friendly name assigned to the instance to identify it on the network\. A NetBIOS name server maintains a list of mappings between NetBIOS computer names and network addresses for networks that use NetBIOS as their naming service\.
+ **NetBIOS node type**: For Windows EC2 instances, the method that the instances use to resolve NetBIOS names to IP addresses\.

If you use a custom options set, instances launched into your VPC use the network configurations in the custom DHCP options set \(1\) to interact with non\-Amazon DNS, NTP, and NETBIOs servers \(2\), and then connect to other devices in the network via your VPC's router \(3\) \.

![\[Custom DHCP option set\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/dhcp-custom-update.png)

**What's next?**
+ [Create a DHCP options set](DHCPOptionSet.md#CreatingaDHCPOptionSet) 
+ [Change the options set associated with a VPC](DHCPOptionSet.md#ChangingDHCPOptionsofaVPC) 