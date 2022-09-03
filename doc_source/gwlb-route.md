# Inspect traffic using appliances in a security VPC<a name="gwlb-route"></a>

Consider the scenario where you need to inspect the traffic entering a VPC from the internet gateway and destined for a subnet using a fleet of security appliances configured behind a Gateway Load Balancer\. The owner of the service consumer VPC creates a Gateway Load Balancer endpoint in a subnet in their VPC \(represented by an endpoint network interface\)\. All traffic entering the VPC through the internet gateway is first routed to the Gateway Load Balancer endpoint for inspection before it's routed to the application subnet\. Similarly, all traffic leaving the application subnet is first routed to Gateway Load Balancer endpoint for inspection before it is routed to the internet\.

The middlebox routing wizard, automatically performs the following operations:
+ Creates the route tables\.
+ Adds the necessary routes to the new route tables\.
+ Disassociates the current route tables associated with the subnets\.
+ Associates the route tables that the middlebox routing wizard creates with the subnets\.
+ Creates a tag that indicates it was created by the middlebox routing wizard, and a tag that indicates the creation date\.

The middlebox routing wizard does not modify your existing route tables\. It creates new route tables, and then associates them with your gateway and subnet resources\. If your resources are already explicitly associated with existing route tables, the existing route tables are first disassociated, and then the new route tables are associated with your resources\. Your existing route tables are not deleted\.

If you do not use the middlebox routing wizard, you must manually configure, and then assign the route tables to the subnets and internet gateway\.

![\[Using a Gateway Load Balancer endpoint to access an endpoint service\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-service-gwlbe_updated.png)

## Internet gateway route table<a name="igw-route-table-table"></a>

The route table for the internet gateway has the following routes\.


| Destination | Target | Purpose | 
| --- | --- | --- | 
| Consumer VPC CIDR | Local | Local route | 
| Application subnet CIDR | endpoint\-id | Routes traffic destined for the application subnet to the Gateway Load Balancer endpoint\. | 

There is an edge association with the gateway\.

When you use the middlebox routing wizard, it associates the following tags with the route table:
+ The key is "Origin" and the value is "Middlebox wizard"
+ The key is "date\_created" and the value is the creation time \(for example, "2021\-02\-18T22:25:49\.137Z"\)

## Application subnet route table<a name="subnet1-route-table-table"></a>

The route table for the application subnet has the following routes\.


| Destination | Target | Purpose | 
| --- | --- | --- | 
| Consumer VPC CIDR | Local | Local route | 
| 0\.0\.0\.0/0 | endpoint\-id | Route traffic from the application servers to the Gateway Load Balancer endpoint before it is routed to the internet\. | 

When you use the middlebox routing wizard, it associates the following tags with the route table:
+ The key is "Origin" and the value is "Middlebox wizard"
+ The key is "date\_created" and the value is the creation time \(for example, "2021\-02\-18T22:25:49\.137Z"\)

## Provider subnet route table<a name="subnet2-route-table"></a>

The route table for the provider subnet has the following routes\.


| Destination | Target | Purpose | 
| --- | --- | --- | 
| Provider VPC CIDR | Local | Local route\. Ensures that traffic originating from the internet is routed to the application servers\. | 
| 0\.0\.0\.0/0 | igw\-id | Routes all traffic to the internet gateway | 

When you use the middlebox routing wizard, it associates the following tags with the route table:
+ The key is "Origin" and the value is "Middlebox wizard"
+ The key is "date\_created" and the value is the creation time \(for example, "2021\-02\-18T22:25:49\.137Z"\)