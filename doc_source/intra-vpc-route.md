# Inspect traffic between subnets<a name="intra-vpc-route"></a>

Consider the scenario where you have multiple subnets in a VPC and you want to inspect the traffic between subnets A and B by a firewall appliance installed in an EC2 instance\. Configure and install the firewall appliance on an EC2 instance in a separate subnet C in your VPC\. The appliance inspects all traffic that travels between subnets A and B\.

You use the main route for the VPC and the middlebox subnet\. Subnets A and B each have a custom route table\. 

The middlebox routing wizard, automatically performs the following operations:
+ Creates the route tables\.
+ Adds the necessary routes to the new route tables\.
+ Disassociates the current route tables associated with the subnets\.
+ Associates the route tables that the middlebox routing wizard creates with the subnets\.
+ Creates a tag that indicates it was created by the middlebox routing wizard, and a tag that indicates the creation date\.

The middlebox routing wizard does not modify your existing route tables\. It creates new route tables, and then associates them with your gateway and subnet resources\. If your resources are already explicitly associated with existing route tables, the existing route tables are first disassociated, and then the new route tables are associated with your resources\. Your existing route tables are not deleted\.

If you do not use the middlebox routing wizard, you must manually configure, and then assign the route tables to the subnets and internet gateway\.

 

![\[Inspect subnet traffic\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/middlebox-intra-vpc.png)

## Custom subnet A route table<a name="subneta-route-table-table"></a>

The route table for subnet A has the following routes:


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local route | 
| 10\.0\.2\.0/24 | eni\-c | Route traffic destined for subnet B to the middlebox | 

There is a subnet association with subnet A\. 

When you use the middlebox routing wizard, the following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.

## Custom subnet B route table<a name="subnetb-route-table-table"></a>

The route table for subnet B has the following routes:


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local route | 
| 10\.0\.1\.0/24 | eni\-c | Route traffic destined for subnet A to the middlebox | 

There is a subnet association with subnet B\. 

When you use the middlebox routing wizard, the following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.

## Main route table<a name="main-route-table"></a>

The main route table for the VPC and subnet C has the following route\.


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local route  | 

There is a subnet association with subnet C\. 

When you use the middlebox routing wizard, the following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.