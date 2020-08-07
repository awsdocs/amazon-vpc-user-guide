# NAT gateways<a name="vpc-nat-gateway"></a>

You can use a network address translation \(NAT\) gateway to enable instances in a private subnet to connect to the internet or other AWS services, but prevent the internet from initiating a connection with those instances\. For more information about NAT, see [NAT](vpc-nat.md)\.

You are charged for creating and using a NAT gateway in your account\. NAT gateway hourly usage and data processing rates apply\. Amazon EC2 charges for data transfer also apply\. For more information, see [Amazon VPC Pricing](http://aws.amazon.com/vpc/pricing/)\.

NAT gateways are not supported for IPv6 traffic—use an outbound\-only \(egress\-only\) internet gateway instead\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.

**Topics**
+ [NAT gateway basics](#nat-gateway-basics)
+ [Working with NAT gateways](#nat-gateway-working-with)
+ [Controlling the use of NAT gateways](#nat-gateway-iam)
+ [Tagging a NAT gateway](#nat-gateway-tagging)
+ [API and CLI overview](#nat-gateway-api-cli)
+ [Monitoring NAT gateways using Amazon CloudWatch](vpc-nat-gateway-cloudwatch.md)
+ [Troubleshooting NAT gateways](nat-gateway-troubleshooting.md)

## NAT gateway basics<a name="nat-gateway-basics"></a>

To create a NAT gateway, you must specify the public subnet in which the NAT gateway should reside\. For more information about public and private subnets, see [Subnet routing](VPC_Subnets.md#SubnetRouting)\. You must also specify an [Elastic IP address](vpc-eips.md) to associate with the NAT gateway when you create it\. The Elastic IP address cannot be changed after you associate it with the NAT Gateway\. After you've created a NAT gateway, you must update the route table associated with one or more of your private subnets to point internet\-bound traffic to the NAT gateway\. This enables instances in your private subnets to communicate with the internet\.

Each NAT gateway is created in a specific Availability Zone and implemented with redundancy in that zone\. You have a quota on the number of NAT gateways you can create in an Availability Zone\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

**Note**  
If you have resources in multiple Availability Zones and they share one NAT gateway, and if the NAT gateway’s Availability Zone is down, resources in the other Availability Zones lose internet access\. To create an Availability Zone\-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT gateway in the same Availability Zone\.

If you no longer need a NAT gateway, you can delete it\. Deleting a NAT gateway disassociates its Elastic IP address, but does not release the address from your account\. 

The following diagram illustrates the architecture of a VPC with a NAT gateway\. The main route table sends internet traffic from the instances in the private subnet to the NAT gateway\. The NAT gateway sends the traffic to the internet gateway using the NAT gateway’s Elastic IP address as the source IP address\. 

![\[A VPC with public and private subnets and a NAT gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/nat-gateway-diagram.png)

### NAT gateway rules and limitations<a name="nat-gateway-limits"></a>

A NAT gateway has the following characteristics and limitations:
+ A NAT gateway supports 5 Gbps of bandwidth and automatically scales up to 45 Gbps\. If you require more, you can distribute the workload by splitting your resources into multiple subnets, and creating a NAT gateway in each subnet\.
+ You can associate exactly one Elastic IP address with a NAT gateway\. You cannot disassociate an Elastic IP address from a NAT gateway after it's created\. To use a different Elastic IP address for your NAT gateway, you must create a new NAT gateway with the required address, update your route tables, and then delete the existing NAT gateway if it's no longer required\.
+ A NAT gateway supports the following protocols: TCP, UDP, and ICMP\.
+ You cannot associate a security group with a NAT gateway\. You can use security groups for your instances in the private subnets to control the traffic to and from those instances\.
+ You can use a network ACL to control the traffic to and from the subnet in which the NAT gateway is located\. The network ACL applies to the NAT gateway's traffic\. A NAT gateway uses ports 1024–65535\. For more information, see [Network ACLs](vpc-network-acls.md)\.
+ When a NAT gateway is created, it receives a network interface that's automatically assigned a private IP address from the IP address range of your subnet\. You can view the NAT gateway's network interface in the Amazon EC2 console\. For more information, see [Viewing details about a network interface](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#view_eni_details)\. You cannot modify the attributes of this network interface\.
+ A NAT gateway cannot be accessed by a ClassicLink connection that is associated with your VPC\.
+ You cannot route traffic to a NAT gateway through a VPC peering connection, a Site\-to\-Site VPN connection, or AWS Direct Connect\. A NAT gateway cannot be used by resources on the other side of these connections\.
+ A NAT gateway can support up to 55,000 simultaneous connections to each unique destination\. This limit also applies if you create approximately 900 connections per second to a single destination \(about 55,000 connections per minute\)\. If the destination IP address, the destination port, or the protocol \(TCP/UDP/ICMP\) changes, you can create an additional 55,000 connections\. For more than 55,000 connections, there is an increased chance of connection errors due to port allocation errors\. These errors can be monitored by viewing the `ErrorPortAllocation` CloudWatch metric for your NAT gateway\. For more information, see [Monitoring NAT gateways using Amazon CloudWatch](vpc-nat-gateway-cloudwatch.md)\. 

### Migrating from a NAT instance<a name="nat-instance-migrate"></a>

If you're already using a NAT instance, you can replace it with a NAT gateway\. To do this, you can create a NAT gateway in the same subnet as your NAT instance, and then replace the existing route in your route table that points to the NAT instance with a route that points to the NAT gateway\. To use the same Elastic IP address for the NAT gateway that you currently use for your NAT instance, you must first also disassociate the Elastic IP address from your NAT instance and then associate it with your NAT gateway when you create the gateway\.

**Note**  
If you change your routing from a NAT instance to a NAT gateway, or if you disassociate the Elastic IP address from your NAT instance, any current connections are dropped and have to be re\-established\. Ensure that you do not have any critical tasks \(or any other tasks that operate through the NAT instance\) running\.

### Best practice when sending traffic to Amazon S3 or DynamoDB in the same Region<a name="nat-gateway-s3-ddb"></a>

To avoid data processing charges for NAT gateways when accessing Amazon S3 and DynamoDB that are in the same Region, set up a gateway endpoint and route the traffic through the gateway endpoint instead of the NAT gateway\. There are no charges for using a gateway endpoint\. For more information, see [Gateway VPC endpoints](vpce-gateway.md)\.

## Working with NAT gateways<a name="nat-gateway-working-with"></a>

You can use the Amazon VPC console to create, view, and delete a NAT gateway\. You can also use the Amazon VPC wizard to create a VPC with a public subnet, a private subnet, and a NAT gateway\. For more information, see [VPC with public and private subnets \(NAT\)](VPC_Scenario2.md)\.

**Topics**
+ [Creating a NAT gateway](#nat-gateway-creating)
+ [Updating your route table](#nat-gateway-create-route)
+ [Deleting a NAT gateway](#nat-gateway-deleting)
+ [Testing a NAT gateway](#nat-gateway-testing)

### Creating a NAT gateway<a name="nat-gateway-creating"></a>

To create a NAT gateway, you must specify a subnet and an Elastic IP address\. Ensure that the Elastic IP address is currently not associated with an instance or a network interface\. If you are migrating from a NAT instance to a NAT gateway and you want to reuse the NAT instance's Elastic IP address, you must first disassociate the address from your NAT instance\. 

**To create a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **NAT Gateways**, **Create NAT Gateway**\.

1. Specify the subnet in which to create the NAT gateway, and select the allocation ID of an Elastic IP address to associate with the NAT gateway\. 

1. \(Optional\) Add or remove a tag\.

   \[Add a tag\] Choose **Add tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

1. Choose **Create a NAT Gateway**\. 

1. The NAT gateway displays in the console\. After a few moments, its status changes to `Available`, after which it's ready for you to use\.

If the NAT gateway goes to a status of `Failed`, there was an error during creation\. For more information, see [NAT gateway creation fails](nat-gateway-troubleshooting.md#nat-gateway-troubleshooting-failed)\.

### Updating your route table<a name="nat-gateway-create-route"></a>

After you've created your NAT gateway, you must update your route tables for your private subnets to point internet traffic to the NAT gateway\. We use the most specific route that matches the traffic to determine how to route the traffic \(longest prefix match\)\. For more information, see [Route priority](VPC_Route_Tables.md#route-tables-priority)\. 

**To create a route for a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the route table associated with your private subnet and choose **Routes**, **Edit**\.

1. Choose **Add another route**\. For **Destination**, enter `0.0.0.0/0`\. For **Target**, select the ID of your NAT gateway\.
**Note**  
If you're migrating from using a NAT instance, you can replace the current route that points to the NAT instance with a route to the NAT gateway\.

1. Choose **Save**\.

To ensure that your NAT gateway can access the internet, the route table associated with the subnet in which your NAT gateway resides must include a route that points internet traffic to an internet gateway\. For more information, see [Creating a custom route table](VPC_Internet_Gateway.md#Add_IGW_Routing)\. If you delete a NAT gateway, the NAT gateway routes remain in a `blackhole` status until you delete or update the routes\. For more information, see [Adding and removing routes from a route table](WorkWithRouteTables.md#AddRemoveRoutes)\.

### Deleting a NAT gateway<a name="nat-gateway-deleting"></a>

You can delete a NAT gateway using the Amazon VPC console\. After you've deleted a NAT gateway, its entry remains visible in the Amazon VPC console for a short time period \(usually an hour\), after which it's automatically removed\. You cannot remove this entry yourself\. 

**To delete a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **NAT Gateways**\.

1. Select the NAT gateway, and choose **Actions**, **Delete NAT Gateway**\.

1. In the confirmation dialog box, choose **Delete NAT Gateway**\.

### Testing a NAT gateway<a name="nat-gateway-testing"></a>

After you've created your NAT gateway and updated your route tables, you can ping a few remote addresses on the internet from an instance in your private subnet to test that it can connect to the internet\. For an example of how to do this, see [Testing the internet connection](#nat-gateway-testing-example)\.

If you're able to connect to the internet, you can also perform the following tests to determine if the internet traffic is being routed through the NAT gateway:
+ You can trace the route of traffic from an instance in your private subnet\. To do this, run the `traceroute` command from a Linux instance in your private subnet\. In the output, you should see the private IP address of the NAT gateway in one of the hops \(it's usually the first hop\)\.
+ Use a third\-party website or tool that displays the source IP address when you connect to it from an instance in your private subnet\. The source IP address should be the Elastic IP address of your NAT gateway\. You can get the Elastic IP address and private IP address of your NAT gateway by viewing its information on the **NAT Gateways** page in the Amazon VPC console\. 

If the preceding tests fail, see [Troubleshooting NAT gateways](nat-gateway-troubleshooting.md)\. 

#### Testing the internet connection<a name="nat-gateway-testing-example"></a>

The following example demonstrates how to test whether your instance in a private subnet can connect to the internet\.

1. Launch an instance in your public subnet \(you use this as a bastion host\)\. For more information, see [Launching an instance into your subnet](working-with-vpcs.md#VPC_Launch_Instance)\. In the launch wizard, ensure that you select an Amazon Linux AMI, and assign a public IP address to your instance\. Ensure that your security group rules allow inbound SSH traffic from the range of IP addresses for your local network, and outbound SSH traffic to the IP address range of your private subnet \(you can also use `0.0.0.0/0` for both inbound and outbound SSH traffic for this test\)\.

1. Launch an instance in your private subnet\. In the launch wizard, ensure that you select an Amazon Linux AMI\. Do not assign a public IP address to your instance\. Ensure that your security group rules allow inbound SSH traffic from the private IP address of your instance that you launched in the public subnet, and all outbound ICMP traffic\. You must choose the same key pair that you used to launch your instance in the public subnet\. 

1. Configure SSH agent forwarding on your local computer, and connect to your bastion host in the public subnet\. For more information, see [To configure SSH agent forwarding for Linux or macOS](#ssh-forwarding-linux) or [To configure SSH agent forwarding for Windows \(PuTTY\)](#ssh-forwarding-windows)\.

1. From your bastion host, connect to your instance in the private subnet, and then test the internet connection from your instance in the private subnet\. For more information, see [To test the internet connection](#test-internet-connection)\.<a name="ssh-forwarding-linux"></a>

**To configure SSH agent forwarding for Linux or macOS**

1. From your local machine, add your private key to the authentication agent\. 

   For Linux, use the following command\.

   ```
   ssh-add -c mykeypair.pem
   ```

   For macOS, use the following command\.

   ```
   ssh-add -K mykeypair.pem
   ```

1. Connect to your instance in the public subnet using the `-A` option to enable SSH agent forwarding, and use the instance's public address, as shown in the following example\.

   ```
   ssh -A ec2-user@54.0.0.123
   ```<a name="ssh-forwarding-windows"></a>

**To configure SSH agent forwarding for Windows \(PuTTY\)**

1. Download and install Pageant from the [PuTTY download page](http://www.chiark.greenend.org.uk/~sgtatham/putty/), if not already installed\.

1. Convert your private key to \.ppk format\. For more information, see [Converting your private key using PuTTYgen](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-private-key) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Start Pageant, right\-click the Pageant icon on the taskbar \(it may be hidden\), and choose **Add Key**\. Select the \.ppk file that you created, enter the passphrase if necessary, and choose **Open**\.

1. Start a PuTTY session and connect to your instance in the public subnet using its public IP address\. For more information, see [Connecting to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-ssh)\. In the **Auth** category, ensure that you select the **Allow agent forwarding** option, and leave the **Private key file for authentication** box blank\.<a name="test-internet-connection"></a>

**To test the internet connection**

1. From your instance in the public subnet, connect to your instance in your private subnet by using its private IP address as shown in the following example\.

   ```
   ssh ec2-user@10.0.1.123
   ```

1. From your private instance, test that you can connect to the internet by running the `ping` command for a website that has ICMP enabled\.

   ```
   ping ietf.org
   ```

   ```
   PING ietf.org (4.31.198.44) 56(84) bytes of data.
   64 bytes from mail.ietf.org (4.31.198.44): icmp_seq=1 ttl=47 time=86.0 ms
   64 bytes from mail.ietf.org (4.31.198.44): icmp_seq=2 ttl=47 time=75.6 ms
   ...
   ```

   Press **Ctrl\+C** on your keyboard to cancel the `ping` command\. If the `ping` command fails, see [Instances cannot access the internet](nat-gateway-troubleshooting.md#nat-gateway-troubleshooting-no-internet-connection)\.

1. \(Optional\) If you no longer require your instances, terminate them\. For more information, see [Terminate your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Controlling the use of NAT gateways<a name="nat-gateway-iam"></a>

By default, IAM users do not have permission to work with NAT gateways\. You can create an IAM user policy that grants users permissions to create, describe, and delete NAT gateways\. We currently do not support resource\-level permissions for any of the `ec2:*NatGateway*` API operations\. For more information about IAM policies for Amazon VPC, see [Identity and access management for Amazon VPC](security-iam.md)\.

## Tagging a NAT gateway<a name="nat-gateway-tagging"></a>

You can tag your NAT gateway to help you identify it or categorize it according to your organization's needs\. For information about working with tags, see [Tagging your Amazon EC2 resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Cost allocation tags are supported for NAT gateways\. Therefore, you can also use tags to organize your AWS bill and reflect your own cost structure\. For more information, see [Using cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\. For more information about setting up a cost allocation report with tags, see [Monthly cost allocation report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html) in *About AWS Account Billing*\. 

## API and CLI overview<a name="nat-gateway-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interfaces and a list of available API operations, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create a NAT gateway**
+ [create\-nat\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-nat-gateway.html) \(AWS CLI\)
+ [New\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [CreateNatGateway](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNatGateway.html) \(Amazon EC2 Query API\)

**Tag a NAT gateway**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)
+ [CreateTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateTags.html) \(Amazon EC2 Query API\)

**Describe a NAT gateway**
+ [describe\-nat\-gateways](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-nat-gateways.html) \(AWS CLI\)
+ [Get\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeNatGateways](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNatGateways.html) \(Amazon EC2 Query API\)

**Delete a NAT gateway**
+ [delete\-nat\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-nat-gateway.html) \(AWS CLI\)
+ [Remove\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteNatGateway](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNatGateway.html) \(Amazon EC2 Query API\)