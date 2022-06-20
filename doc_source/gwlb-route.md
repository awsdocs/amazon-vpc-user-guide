# Security appliances behind a Gateway Load Balancer in the security VPC<a name="gwlb-route"></a>

In the following example, you want to inspect the traffic entering a VPC from the internet gateway and destined for subnet 1 using a fleet of security appliances configured behind a Gateway Load Balancer in the security VPC\. The owner of the service consumer VPC creates a Gateway Load Balancer endpoint in subnet 2 in their VPC \(represented by an endpoint network interface\)\. All traffic entering the VPC through the internet gateway is first routed to the Gateway Load Balancer endpoint for inspection in the security VPC before it's routed to the destination subnet 1\. Similarly, all traffic leaving the subnet 1 is first routed to Gateway Load Balancer endpoint for inspection in the security VPC before it is routed to the internet\.

The middlebox routing wizard, automatically performs the following operations:
+ Creates the route tables\.
+ Adds the necessary routes to the new route tables\.
+ Disassociates the current route tables associated with the subnets\.
+ Associates the route tables that the middlebox routing wizard creates with the subnets\.
+ Creates a tag that indicates it was created by the middlebox routing wizard, and a tag that indicates the creation date\.

The middlebox routing wizard does not modify your existing route tables\. It creates new route tables, and then associates them with your gateway and subnet resources\. If your resources are already explicitly associated with existing route tables, the existing route tables are first disassociated, and then the new route tables are associated with your resources\. Your existing route tables are not deleted\.

If you do not use the middlebox routing wizard, you must manually configure, and then assign the route tables to the subnets and internet gateway\. 

![\[Using a Gateway Load Balancer endpoint to access an endpoint service\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-service-gwlbe_updated.png)

## Gateway route table<a name="igw-route-table-table"></a>

The internet gateway route table has the following routes:


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local | 
| 10\.0\.1\.0/24 | vpc\-endpoint\-id | Routes traffic destined for subnet 1 to the Gateway Load Balancer endpoint\. | 

There is an edge association with the gateway\.

When you use the middlebox routing wizard, the following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.

## Subnet A route table<a name="subnet1-route-table-table"></a>

Subnet A route table has the following routes\.


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local route | 
| 0\.0\.0\.0/0 | vpc\-endpoint\-id | Route non\-local traffic to the Gateway Load Balancer endpoint\. This ensures that all traffic leaving the subnet \(destined for the internet\) is first routed to the Gateway Load Balancer endpoint\. | 

There is a subnet association with subnet A\. 

When you use the middlebox routing wizard, the following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.

## Subnet B route table<a name="subnet2-route-table"></a>

Subnet B route table has the following routes\. 


| Destination | Target | Purpose | 
| --- | --- | --- | 
| 10\.0\.0\.0/16 | Local | Local route \- for the traffic that originated from the internet, the local route ensures that it is routed to its destination in subnet B | 
| 0\.0\.0\.0/0 | igw\-id | Routes all traffic to the internet gateway | 

There is a subnet association with subnet B\. 

The following tags are associated with the route table:
+ A tag with a Key set to "Origin" and a Value set to "Middlebox wizard"\.
+ A tag with a Key set to "date\_created" and a Value set to the creation time, for example, "2021\-02\-18T22:25:49\.137Z "\.