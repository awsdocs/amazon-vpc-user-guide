# DHCP option set concepts<a name="DHCPOptionSetConcepts"></a>

A DHCP option set is a group of network configurations used by EC2 instances in your VPC to communicate over your virtual network\.

Each VPC in a Region uses the same default DHCP option set unless you choose to create a custom DHCP option set, or if you disassociate all option sets from your VPC\.

**Topics**
+ [Default DHCP option set](#ArchitectureDiagram)
+ [Custom DHCP option set](#CustomDHCPOptionSet)

## Default DHCP option set<a name="ArchitectureDiagram"></a>

Each VPC in a Region uses the same default DHCP option set, which contains the following network configurations:
+ **Domain name**: The domain name that a client should use when resolving hostnames via the Domain Name System\.
+ **Domain name servers**: The DNS servers that your network interfaces will use for domain name resolution\.

If you use the default option set, the Amazon DHCP server uses the network configurations stored in the default option set\. When you launch an instance into your VPC, the instance interacts with the DHCP server\(1\), interact with the Amazon DNS server \(2\), and connect to other devices in the network through your VPC's router \(3\)\. The instances can interact with the Amazon DHCP server at any time to get their IP address lease and additional network configurations\.

![\[Default DHCP option set\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/dhcp-default-update-new.png)

**What's next?**
+ [View your default option set](DHCPOptionSet.md#ViewDefaultDHCPOptionSet) 
+ [Change the option set associated with a VPC](DHCPOptionSet.md#ChangingDHCPOptionsofaVPC) 

## Custom DHCP option set<a name="CustomDHCPOptionSet"></a>

You can create your own DHCP option set in Amazon VPC\. This enables you to configure the following network configurations: 
+ **Domain name**: The domain name that a client should use when resolving hostnames via the Domain Name System\. 
+ **Domain name servers**: The DNS servers that your network interfaces will use for domain name resolution\.
+ **NTP servers**: The NTP servers that will provide the time to the instances in your network\.
+ **NetBIOS name servers**: For EC2 instances running a Windows OS, the NetBIOS computer name is a friendly name assigned to the instance to identify it on the network\. A NetBIOS name server maintains a list of mappings between NetBIOS computer names and network addresses for networks that use NetBIOS as their naming service\.
+ **NetBIOS node type**: For EC2 instances running a Windows OS, the method that the instances use to resolve NetBIOS names to IP addresses\.

If you use a custom option set, instances launched into your VPC use the network configurations in the custom DHCP option set \(1\), interact with non Amazon DNS, NTP, and NetBIOS servers \(2\), and then connect to other devices in the network through your VPC's router \(3\)\.

![\[Custom DHCP option set\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/dhcp-custom-update-new.png)

**What's next?**
+ [Create a DHCP option set](DHCPOptionSet.md#CreatingaDHCPOptionSet) 
+ [Change the option set associated with a VPC](DHCPOptionSet.md#ChangingDHCPOptionsofaVPC) 