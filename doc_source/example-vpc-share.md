# Example: Sharing public subnets and private subnets<a name="example-vpc-share"></a>

Consider this scenario where you want an account to be responsible for the infrastructure, including subnets, route tables, gateways, and CIDR ranges and other accounts that are in the same AWS Organization to use the subnets\. A VPC owner \(Account A\) creates the routing infrastructure, including the VPCs, subnets, route tables, gateways, and network ACLs\. Account D wants to create public facing applications\. Account B and Account C want to create private applications that do not need to connect to the internet and should reside in private subnets\. Account A can use AWS Resource Access Manager to create a Resource Share for the subnets and then share the subnets\. Account A shares the public subnet with Account D and the private subnet with Account B, and Account C\. Account B, Account C, and Account D can create resources in the subnets\. Each account can only see the subnets that are shared with them, for example, Account D can only see the public subnet\. Each of the accounts can control their resources, including instances, and security groups\.

Account A manages the IP infrastructure, including the route tables for the public subnets, and the private subnets\. There is no additional configuration required for shared subnets, so the route tables are the same as unshared subnet route tables\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/VPC-share-internet-gateway-example.png)

Account A \(Account ID 111111111111\) shares the public subnet with Account D \(444444444444\)\. Account D sees the following subnet, and the **Owner** column provides two indicators that the subnet is shared\.
+ The Account ID is the VPC owner \(111111111111\) and is different from Account D's ID \(444444444444\)\.
+ The word "shared" appears beside the owner account ID\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-share-screen.png)