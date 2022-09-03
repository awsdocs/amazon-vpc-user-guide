# Inspect traffic between subnets<a name="intra-vpc-route"></a>

Consider the scenario where you have multiple subnets in a VPC and you want to inspect the traffic between them using a firewall appliance\. Configure and install the firewall appliance on an EC2 instance in a separate subnet in your VPC\.

The following diagram shows a firewall appliance installed on an EC2 instance in subnet C\. The appliance inspects all traffic that travels from subnet A to subnet B \(see 1\) and from subnet B to subnet A \(see 2\)\.

![\[Inspect subnet traffic\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/middlebox-intra-vpc_updated.png)

You use the main route table for the VPC and the middlebox subnet\. Subnets A and B each have a custom route table\.

The middlebox routing wizard, automatically performs the following operations:
+ Creates the route tables\.
+ Adds the necessary routes to the new route tables\.
+ Disassociates the current route tables associated with the subnets\.
+ Associates the route tables that the middlebox routing wizard creates with the subnets\.
+ Creates a tag that indicates it was created by the middlebox routing wizard, and a tag that indicates the creation date\.

The middlebox routing wizard does not modify your existing route tables\. It creates new route tables, and then associates them with your gateway and subnet resources\. If your resources are already explicitly associated with existing route tables, the existing route tables are first disassociated, and then the new route tables are associated with your resources\. Your existing route tables are not deleted\.

If you do not use the middlebox routing wizard, you must manually configure, and then assign the route tables to the subnets and internet gateway\.

## Custom route table for subnet A<a name="subneta-route-table-table"></a>

The route table for subnet A has the following routes\.


| Destination | Target | Purpose | 
| --- | --- | --- | 
| VPC CIDR | Local | Local route | 
| Subnet B CIDR | appliance\-eni | Route traffic destined for subnet B to the middlebox | 

When you use the middlebox routing wizard, it associates the following tags with the route table:
+ The key is "Origin" and the value is "Middlebox wizard"
+ The key is "date\_created" and the value is the creation time \(for example, "2021\-02\-18T22:25:49\.137Z"\)

## Custom route table for subnet B<a name="subnetb-route-table-table"></a>

The route table for subnet B has the following routes\.


| Destination | Target | Purpose | 
| --- | --- | --- | 
| VPC CIDR | Local | Local route | 
| Subnet A CIDR | appliance\-eni | Route traffic destined for subnet A to the middlebox | 

When you use the middlebox routing wizard, it associates the following tags with the route table:
+ The key is "Origin" and the value is "Middlebox wizard"
+ The key is "date\_created" and the value is the creation time \(for example, "2021\-02\-18T22:25:49\.137Z"\)

## Main route table<a name="main-route-table"></a>

Subnet C uses the main route table\. The main route table has the following route\.


| Destination | Target | Purpose | 
| --- | --- | --- | 
| VPC CIDR | Local | Local route | 

When you use the middlebox routing wizard, it associates the following tags with the route table:
+ The key is "Origin" and the value is "Middlebox wizard"
+ The key is "date\_created" and the value is the creation time \(for example, "2021\-02\-18T22:25:49\.137Z"\)