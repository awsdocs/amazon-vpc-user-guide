# Example of sharing public subnets and private subnets<a name="example-vpc-share"></a>

Consider this scenario where you want an account \(Account A\) to manage the infrastructure, including VPCs, subnets, route tables, gateways, and CIDR ranges, and other member accounts to use the subnets for their applications\. Account D has applications that need to connect to the internet\. Account B and Account C have applications that do not need to connect to the internet\.

Account A uses AWS Resource Access Manager to create a Resource Share for the subnets, and shares the public subnet with Account D and the private subnet with Account B and Account C\. Account B, Account C, and Account D can create resources in the subnets\. Each account can only see and create resources in the subnets that are shared with them\. Each account can control the resources that they create in these subnets \(for example, EC2 instances and security groups\)\.

There is no additional configuration required for shared subnets, so the route tables are the same as unshared subnet route tables\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-share-internet-gateway-example_updated.png)

Account A \(111111111111\) shares the public subnet with Account D \(444444444444\)\. Account D sees the following subnet, and the **Owner** column provides two indicators that the subnet is shared\.
+ The owner account ID is Account A \(111111111111\), not Account D \(444444444444\)\.
+ The word "shared" appears beside the owner account ID\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-share-screen.png)