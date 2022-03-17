# Inspect all traffic destined for a subnet<a name="internet-gateway-subnet"></a>

Consider the scenario where you have traffic coming into the VPC through an internet gateway and you want to inspect all traffic that is destined for a subnet, say subnet B, using a firewall appliance installed on an EC2 instance\. The firewall appliance should be installed and configured on an Amazon EC2 instance in a separate subnet from subnet B in your VPC, say subnet C\. You can then use the middlebox routing wizard to configure routes for traffic between subnet B and the internet gateway\.

 The middlebox routing wizard, automatically performs the following operations:
+ Creates three route tables, a route table for the internet gateway \(route table A\), a route table for subnet B \(route table B\), and a route table for subnet C \(Route table C\)\.
+ Adds the necessary routes to the new route tables as described in the following sections\.
+ Disassociates the current route tables associated with the internet gateway, subnet B, and subnet C\.
+ Associates route table A with the internet gateway \(the Source in the middlebox routing wizard\), route table C with subnet C \(the Middlebox in the middlebox routing wizard\), and route table B with subnet B \(the Destination in the middlebox routing wizard\)\.
+ Creates a tag that indicates it was created by the middlebox routing wizard, and a tag that indicates the creation date\.

The middlebox routing wizard does not modify your existing route tables\. It creates new route tables, and then associates them with your gateway and subnet resources\. If your resources are already explicitly associated with existing route tables, the existing route tables are first disassociated, and then the new route tables are associated with your resources\. Your existing route tables are not deleted\.

If you do not use the middlebox routing wizard, you must manually configure, and then assign the route tables to the subnets and internet gateway\.

![\[Inbound routing to a VPC\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/ingress-routing-firewall-ipv6.png)

## Internet gateway route table<a name="internet-gateway-igw-route-table"></a>

The route table for the internet gateway contains the following routes:


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local route for IPv4 | 
| 10\.0\.1\.0/24 | eni\-11223344556677889 | Route IPv4 traffic destined for subnet B to the middlebox | 
| 2001:db8:1234:1a00::/56 | Local | Local route for IPv6 | 
| 2001:db8:1234:1a00::/64 | eni\-11223344556677889 | Route IPv6 traffic destined for subnet B to the middlebox | 

There is an edge association between the internet gateway and the VPC\. 

When you use the middlebox routing wizard, the following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.

## Middlebox subnet route table<a name="internet-gateway-middlebox-subnet-route-table"></a>

The route table for the middlebox subnet \(Subnet C\) contains the following routes:


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local route for IPv4 | 
| 0\.0\.0\.0/0 | igw\-id | Route IPv4 traffic to the internet gateway | 
| 2001:db8:1234:1a00::/56 | Local | Local route for IPv6 | 
| ::/0 | eigw\-id | Route for IPv6 traffic to the egress\-only internet gateway | 

There is a subnet association with subnet B\. 

When you use the middlebox routing wizard, the following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.

## Subnet route table<a name="internet-gateway-subnet-route-table"></a>

The route table for the destination subnet \(Subnet B\) contains the following routes:


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local route  | 
| 0\.0\.0\.0/0 | eni\-11223344556677889 | Route IPv4 traffic destined for the internet to the middlebox | 
| 2001:db8:1234:1a00::/56 | Local | Local route for IPv6 | 
| ::/0 | eni\-11223344556677889 | Route IPv4 traffic destined for the internet to the middlebox | 

There is a subnet association with subnet C\. 

When you use the middlebox routing wizard, the following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.
