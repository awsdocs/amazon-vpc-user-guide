# Example: VPC with servers in private subnets and NAT<a name="vpc-example-private-subnets-nat"></a>

This example demonstrates how to create a VPC that you can use for servers in a production environment\. For resiliency, you deploy the servers in two Availability Zones, using an Auto Scaling group and an Application Load Balancer\. For additional security,you deploy the servers in private subnets\. The servers receive requests through the load balancer\. The servers can connect to the internet using a NAT gateway\. For improved resiliency, you'll deploy the NAT gateway in both Availability Zones\.

**Topics**
+ [Overview](#overview-vpc-private-subnets-nat)
+ [Create the VPC](#create-vpc-private-subnets-nat)
+ [Deploy your application](#deploy-private-subnets)
+ [Test your configuration](#test-private-subnets)
+ [Clean up](#clean-up-private-subnets)

## Overview<a name="overview-vpc-private-subnets-nat"></a>

The following diagram provides an overview of the resources included in this example\. The VPC has public subnets and private subnets in two Availability Zones\. Each public subnet contains a NAT gateway and a load balancer node\. The servers run in the private subnets, are launched and terminated using an Auto Scaling group, and receive traffic from the load balancer\. The servers can connect to the internet using the NAT gateway\. The servers can connect to Amazon S3 using a gateway VPC endpoint\.

![\[A VPC with subnets in two Availability Zones.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-example-private-subnets.png)

### Routing<a name="routing-vpc-private-subnets-nat"></a>

When you create this VPC using the Amazon VPC console, we create a route table for the public subnets with local routes and routes to the internet gateway\. We also create a route table for the private subnets with local routes, and routes to the NAT gateway, egress\-only internet gateway, and gateway VPC endpoint\.

The following is an example of the route table for the public subnets, with routes for both IPv4 and IPv6\. If you create IPv4\-only subnets instead of dual stack subnets, your route table includes only the IPv4 routes\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | local | 
| 2001:db8:1234:1a00::/56 | local | 
| 0\.0\.0\.0/0 | igw\-id | 
| ::/0 | igw\-id | 

The following is an example of a route table for one of the private subnets, with routes for both IPv4 and IPv6\. If you created IPv4\-only subnets, the route table includes only the IPv4 routes\. The last route sends traffic destined for Amazon S3 to the gateway VPC endpoint\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | local | 
| 2001:db8:1234:1a00::/56 | local | 
| 0\.0\.0\.0/0 | nat\-gateway\-id | 
| ::/0 | eigw\-id | 
| s3\-prefix\-list\-id | s3\-gateway\-id | 

### Security<a name="security-vpc-private-subnets-nat"></a>

The following is an example of the rules that you might create for the security group that you associate with your servers\. The security group must allow traffic from the load balancer over the listener port and protocol\. It must also allow health check traffic\.


**Inbound**  

| Source | Protocol | Port range | Comments | 
| --- | --- | --- | --- | 
| ID of the load balancer security group | listener protocol | listener port | Allows inbound traffic from the load balancer on the listener port | 
| ID of the load balancer security group | health check protocol | health check port | Allows inbound health check traffic from the load balancer | 

## Create the VPC<a name="create-vpc-private-subnets-nat"></a>

Use the following procedure to create a VPC with a public subnet and a private subnet in two Availability Zones, and a NAT gateway in each Availability Zone\.

**To create the VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the dashboard, choose **Create VPC**\.

1. For **Resources to create**, choose **VPC and more**\.

1. **Configure the VPC**

   1. For **Name tag auto\-generation**, enter a name for the VPC\.

   1. For **IPv4 CIDR block**, you can keep the default suggestion, or alternatively you can enter the CIDR block required by your application or network\.

   1. If your application communicates using IPv6 addresses, choose **IPv6 CIDR block**, **Amazon\-provided IPv6 CIDR block**\.

1. **Configure the subnets**

   1. For **Number of Availability Zones**, choose **2**, so that you can launch instances in multiple Availability Zones to improve resiliency\.

   1. For **Number of public subnets**, choose **2**\.

   1. For **Number of private subnets**, choose **2**\.

   1. You can keep the default CIDR block for the public subnet, or alternatively you can expand **Customize subnet CIDR blocks** and enter a CIDR block\. For more information, see [Subnet CIDR blocks](subnet-sizing.md)\.

1. For **NAT gateways**, choose **1 per AZ** to improve resiliency\.

1. If your application communicates using IPv6 addresses, for **Egress only internet gateway**, choose **Yes**\.

1. For **VPC endpoints**, if your instances must access an S3 bucket, keep the default, **S3 Gateway**\. Otherwise, instances in your private subnet can't access Amazon S3\. There is no cost for this option, so you can keep the default if you might use an S3 bucket in the future\. If you choose **None**, you can always add a gateway VPC endpoint later on\.

1. For **DNS options**, clear **Enable DNS hostnames**\.

1. Choose **Create VPC**\.

## Deploy your application<a name="deploy-private-subnets"></a>

Ideally, you've finished testing your servers in a development or test environment, and created the scripts or images that you'll use to deploy your application in production\.

You can use [Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/) to deploy servers in multiple Availability Zones and maintain the minimum server capacity required by your application\.

**To launch instances using an Auto Scaling group**

1. Create a launch template to specify the configuration information needed to launch your EC2 instances using Amazon EC2 Auto Scaling\. For step\-by\-step directions, see [Create a launch template for your Auto Scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

1. Create an Auto Scaling group, which is a collection of EC2 instances with a minimum, maximum, and desired size\. For step\-by\-step directions, see [Create an Auto Scaling group using a launch template](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

1. Create a load balancer, which distributes traffic evenly across the instances in your Auto Scaling group, and attach the load balancer to your Auto Scaling group\. For more information, see the [Elastic Load Balancing User Guide](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/) and [Use Elastic Load Balancing](https://docs.aws.amazon.com/autoscaling/ec2/userguide/autoscaling-load-balancer.html) in the *Amazon EC2 Auto Scaling User Guide*\.

## Test your configuration<a name="test-private-subnets"></a>

After you've finished deploying your application, you can test it\. If your application can't send or receive the traffic that you expect, you can use Reachability Analyzer to help you troubleshoot\. For example, Reachability Analyzer can identify configuration issues with your route tables or security groups\. For more information, see the [Reachability Analyzer Guide](https://docs.aws.amazon.com/vpc/latest/reachability/)\.

## Clean up<a name="clean-up-private-subnets"></a>

When you are finished with this configuration, you can delete it\. Before you can delete the VPC, you must delete the Auto Scaling group, terminate your instances, delete the NAT gateways, and delete the load balancer\. For more information, see [Delete your VPC](delete-vpc.md)\.