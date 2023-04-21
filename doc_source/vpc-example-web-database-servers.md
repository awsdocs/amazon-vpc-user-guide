# Example: VPC for web and database servers<a name="vpc-example-web-database-servers"></a>

This example demonstrates how to create a VPC that you can use for a two\-tier architecture in a production environment\. To improve resiliency, you'll deploy the servers in multiple Availability Zones\. Because the web servers receive traffic from the internet, you deploy them in public subnets\. For additional security, you'll deploy the database servers in private subnets\.

**Topics**
+ [Overview](#overview-vpc-public-private-subnets-multi-az)
+ [Create the VPC](#create-vpc-public-private-subnets-multi-az)
+ [Deploy your application](#deploy-web-database-servers)
+ [Test your configuration](#test-web-database-servers)
+ [Clean up](#clean-up-web-database-servers)

## Overview<a name="overview-vpc-public-private-subnets-multi-az"></a>

The following diagram provides an overview of the resources included in this example\. The VPC has public subnets and private subnets in two Availability Zones\. The web servers run in the public subnets and receive traffic from clients through a load balancer\. The security group for the web servers allows traffic from the load balancer\. The database servers run in the private subnets and receive traffic from the web servers\. The security group for the database servers allows traffic from the web servers\. The database servers can connect to Amazon S3 using a gateway VPC endpoint\.

![\[A VPC with subnets in two Availability Zones.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-example-web-database.png)

### Routing<a name="routing-vpc-public-private-subnets-multi-az"></a>

When you create this VPC using the Amazon VPC console, we create a route table for the public subnets with local routes and routes to the internet gateway, and a route table for each private subnet with local routes and a route to the gateway VPC endpoint\.

The following is an example of a route table for the public subnets, with routes for both IPv4 and IPv6\. If you create IPv4\-only subnets instead of dual stack subnets, your route table has only the IPv4 routes\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | local | 
| 2001:db8:1234:1a00::/56 | local | 
| 0\.0\.0\.0/0 | igw\-id | 
| ::/0 | igw\-id | 

The following is an example of a route table for the private subnets, with local routes for both IPv4 and IPv6\. If you created IPv4\-only subnets, your route table has only the IPv4 route\. The last route sends traffic destined for Amazon S3 to the gateway VPC endpoint\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | local | 
| 2001:db8:1234:1a00::/56 | local | 
| s3\-prefix\-list\-id | s3\-gateway\-id | 

### Security<a name="security-vpc-public-private-subnets-multi-az"></a>

For this example configuration, you must create a security group for the web servers and a security group for the database servers\.

**Web servers**  
The following security group rules allow the web servers to receive HTTP and HTTPS traffic from clients, from all IPv4 and IPv6 addresses\. You can optionally allow the web servers to receive SSH or RDP traffic from your network\. The web servers can also send SQL or MySQL traffic to your database servers\.


**Inbound**  

| Source | Protocol | Port range | Description | 
| --- | --- | --- | --- | 
| 0\.0\.0\.0/0 | TCP | 80 | Allows inbound HTTP access from all IPv4 addresses | 
| ::/0 | TCP | 80 | Allows inbound HTTP access from all IPv6 addresses | 
| 0\.0\.0\.0/0 | TCP | 443 | Allows inbound HTTPS access from all IPv4 addresses | 
| ::/0 | TCP | 443 | Allows inbound HTTPS access from all IPv6 addresses | 
| Public IPv4 address range of your network | TCP | 22 | \(Optional\) Allows inbound SSH access from IPv4 IP addresses in your network | 
| IPv6 address range of your network | TCP | 22 | \(Optional\) Allows inbound SSH access from IPv6 IP addresses in your network | 
| Public IPv4 address range of your network | TCP | 3389 | \(Optional\) Allows inbound RDP access from IPv4 IP addresses in your network | 
| IPv6 address range of your network | TCP | 3389 | \(Optional\) Allows inbound RDP access from IPv6 IP addresses in your network | 


**Outbound**  

| Destination | Protocol | Port range | Description | 
| --- | --- | --- | --- | 
|  ID of the security group for instances running Microsoft SQL Server  |  TCP  |  1433  |  Allows outbound Microsoft SQL Server access  | 
|  ID of the security group for instances running MySQL  |  TCP  |  3306  |  Allows outbound MySQL access  | 

**Database servers**  
The following security group rules allow the database servers to receive read and write requests from the web servers\. The database servers can also initiate traffic bound for the internet \(the route tables send the traffic to the NAT gateway and then to the internet gateway\)\.


**Inbound**  

| Source | Protocol | Port range | Comments | 
| --- | --- | --- | --- | 
| ID of the web server security group | TCP | 1433 | Allows inbound Microsoft SQL Server access from the web servers | 
| ID of the web server security group | TCP | 3306 | Allows inbound MySQL Server access from the web servers | 


**Outbound**  

| Destination | Protocol | Port range | Comments | 
| --- | --- | --- | --- | 
| 0\.0\.0\.0/0 | TCP | 80 | Allows outbound HTTP access to the internet over IPv4 | 
| 0\.0\.0\.0/0 | TCP | 443 | Allows outbound HTTPS access to the internet over IPv4 | 

For more information about security groups for Amazon RDS DB instances, see [Controlling access with security groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.RDSSecurityGroups.html) in the *Amazon RDS User Guide*\.

## Create the VPC<a name="create-vpc-public-private-subnets-multi-az"></a>

Use the following procedure to create a VPC with a public subnet and a private subnet in two Availability Zones\.

**To create the VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the dashboard, choose **Create VPC**\.

1. For **Resources to create**, choose **VPC and more**\.

1. **Configure the VPC**

   1. For **Name tag auto\-generation**, enter a name for the VPC\.

   1. For **IPv4 CIDR block**, you can keep the default suggestion, or alternatively you can enter the CIDR block required by your application or network\. For more information, see [VPC CIDR blocks](vpc-cidr-blocks.md)\.

   1. \(Optional\) If your application communicates using IPv6 addresses, choose **IPv6 CIDR block**, **Amazon\-provided IPv6 CIDR block**\.

1. **Configure the subnets**

   1. For **Number of Availability Zones**, choose **2**, so that you can launch instances in two Availability Zones to improve resiliency\.

   1. For **Number of public subnets**, choose **2**\.

   1. For **Number of private subnets**, choose **2**\.

   1. You can keep the default CIDR blocks for the subnets, or alternatively you can expand **Customize subnet CIDR blocks** and enter a CIDR block\. For more information, see [Subnet CIDR blocks](subnet-sizing.md)\.

1. For **NAT gateways**, keep the default value, **None**\.

1. For **VPC endpoints**, keep the default value, **S3 Gateway**\. While there is no effect unless you access an S3 bucket, there is no cost to enable this VPC endpoint\.

1. For **DNS options**, keep both options selected\. As a result, your web servers will receive public DNS hostnames that correspond to their public IP addresses\.

1. Choose **Create VPC**\.

## Deploy your application<a name="deploy-web-database-servers"></a>

Ideally, you've already tested your web servers and database servers in a development or test environment, and created the scripts or images that you'll use to deploy your application in production\.

You can use EC2 instances for your web servers\. There are a variety of ways to deploy EC2 instances\. For example:
+ [Amazon EC2 launch instance wizard](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-instance-wizard.html)
+ [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/)
+ [Amazon Elastic Container Service \(Amazon ECS\)](https://docs.aws.amazon.com/ecs/)

To improve availability, you can use [Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/) to deploy servers in multiple Availability Zones and maintain the minimum server capacity required by your application\.

You can use [Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/) to distribute traffic evenly across your servers\. You can attach your load balancer to an Auto Scaling group\.

You can use EC2 instances for your database servers, or one of our purpose\-built database types\. For more information, see [Databases on AWS: How to choose](https://docs.aws.amazon.com/documentation/latest/databases-on-aws-how-to-choose/)\.

## Test your configuration<a name="test-web-database-servers"></a>

After you've finished deploying your application, you can test it\. If your application can't send or receive the traffic that you expect, you can use Reachability Analyzer to help you troubleshoot\. For example, Reachability Analyzer can identify configuration issues with your route tables or security groups\. For more information, see the [Reachability Analyzer Guide](https://docs.aws.amazon.com/vpc/latest/reachability/)\.

## Clean up<a name="clean-up-web-database-servers"></a>

When you are finished with this configuration, you can delete it\. Before you can delete the VPC, you must terminate your instances and delete the load balancer\. For more information, see [Delete your VPC](delete-vpc.md)\.