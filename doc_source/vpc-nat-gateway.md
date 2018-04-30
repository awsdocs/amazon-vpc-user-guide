# NAT Gateways<a name="vpc-nat-gateway"></a>

You can use a network address translation \(NAT\) gateway to enable instances in a private subnet to connect to the internet or other AWS services, but prevent the internet from initiating a connection with those instances\. For more information about NAT, see [NAT](vpc-nat.md)\.

You are charged for creating and using a NAT gateway in your account\. NAT gateway hourly usage and data processing rates apply\. Amazon EC2 charges for data transfer also apply\. For more information, see [Amazon VPC Pricing](http://aws.amazon.com/vpc/pricing/)\.

NAT gateways are not supported for IPv6 traffic—use an egress\-only internet gateway instead\. For more information, see [Egress\-Only Internet Gateways](egress-only-internet-gateway.md)\.

**Topics**
+ [NAT Gateway Basics](#nat-gateway-basics)
+ [Working with NAT Gateways](#nat-gateway-working-with)
+ [Troubleshooting NAT Gateways](#nat-gateway-troubleshooting)
+ [Controlling the Use of NAT Gateways](#nat-gateway-iam)
+ [Tagging a NAT Gateway](#nat-gateway-tagging)
+ [API and CLI Overview](#nat-gateway-api-cli)
+ [Monitoring Your NAT Gateway with Amazon CloudWatch](vpc-nat-gateway-cloudwatch.md)

## NAT Gateway Basics<a name="nat-gateway-basics"></a>

To create a NAT gateway, you must specify the public subnet in which the NAT gateway should reside\. For more information about public and private subnets, see [Subnet Routing](VPC_Subnets.md#SubnetRouting)\. You must also specify an [Elastic IP address](vpc-eips.md) to associate with the NAT gateway when you create it\. After you've created a NAT gateway, you must update the route table associated with one or more of your private subnets to point Internet\-bound traffic to the NAT gateway\. This enables instances in your private subnets to communicate with the internet\. 

Each NAT gateway is created in a specific Availability Zone and implemented with redundancy in that zone\. You have a limit on the number of NAT gateways you can create in an Availability Zone\. For more information, see [Amazon VPC Limits](VPC_Appendix_Limits.md)\. 

**Note**  
If you have resources in multiple Availability Zones and they share one NAT gateway, in the event that the NAT gateway’s Availability Zone is down, resources in the other Availability Zones lose internet access\. To create an Availability Zone\-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT gateway in the same Availability Zone\.

If you no longer need a NAT gateway, you can delete it\. Deleting a NAT gateway disassociates its Elastic IP address, but does not release the address from your account\. 

The following diagram illustrates the architecture of a VPC with a NAT gateway\. The main route table sends internet traffic from the instances in the private subnet to the NAT gateway\. The NAT gateway sends the traffic to the internet gateway using the NAT gateway’s Elastic IP address as the source IP address\. 

![\[A VPC with public and private subnets and a NAT gateway\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/nat-gateway-diagram.png)

**Topics**
+ [NAT Gateway Rules and Limitations](#nat-gateway-limits)
+ [Migrating from a NAT Instance](#nat-instance-migrate)
+ [Using a NAT Gateway with VPC Endpoints, VPN, AWS Direct Connect, or VPC Peering](#nat-gateway-other-services)

### NAT Gateway Rules and Limitations<a name="nat-gateway-limits"></a>

A NAT gateway has the following characteristics and limitations:
+ A NAT gateway supports 5 Gbps of bandwidth and automatically scales up to 45 Gbps\. If you require more, you can distribute the workload by splitting your resources into multiple subnets, and creating a NAT gateway in each subnet\.
+ You can associate exactly one Elastic IP address with a NAT gateway\. You cannot disassociate an Elastic IP address from a NAT gateway after it's created\. To use a different Elastic IP address for your NAT gateway, you must create a new NAT gateway with the required address, update your route tables, and then delete the existing NAT gateway if it's no longer required\.
+ A NAT gateway supports the following protocols: TCP, UDP, and ICMP\.
+ You cannot associate a security group with a NAT gateway\. You can use security groups for your instances in the private subnets to control the traffic to and from those instances\.
+ You can use a network ACL to control the traffic to and from the subnet in which the NAT gateway is located\. The network ACL applies to the NAT gateway's traffic\. A NAT gateway uses ports 1024–65535\. For more information, see [Network ACLs](VPC_ACLs.md)\.
+ When a NAT gateway is created, it receives a network interface that's automatically assigned a private IP address from the IP address range of your subnet\. You can view the NAT gateway's network interface in the Amazon EC2 console\. For more information, see [Viewing Details about a Network Interface](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#view_eni_details)\. You cannot modify the attributes of this network interface\.
+ A NAT gateway cannot be accessed by a ClassicLink connection associated with your VPC\.
+ A NAT gateway can support up to 55,000 simultaneous connections to each unique destination\. This limit also applies if you create approximately 900 connections per second to a single destination \(about 55,000 connections per minute\)\. If the destination IP address, the destination port, or the protocol \(TCP/UDP/ICMP\) changes, you can create an additional 55,000 connections\. For more than 55,000 connections, there is an increased chance of connection errors due to port allocation errors\. These errors can be monitored by viewing the `ErrorPortAllocation` CloudWatch metric for your NAT gateway\. For more information, see [Monitoring Your NAT Gateway with Amazon CloudWatch](vpc-nat-gateway-cloudwatch.md)\. 

### Migrating from a NAT Instance<a name="nat-instance-migrate"></a>

If you're already using a NAT instance, you can replace it with a NAT gateway\. To do this, you can create a NAT gateway in the same subnet as your NAT instance, and then replace the existing route in your route table that points to the NAT instance with a route that points to the NAT gateway\. To use the same Elastic IP address for the NAT gateway that you currently use for your NAT instance, you must first also disassociate the Elastic IP address for your NAT instance and associate it with your NAT gateway when you create the gateway\.

**Note**  
If you change your routing from a NAT instance to a NAT gateway, or if you disassociate the Elastic IP address from your NAT instance, any current connections are dropped and have to be re\-established\. Ensure that you do not have any critical tasks \(or any other tasks that operate through the NAT instance\) running\.

### Using a NAT Gateway with VPC Endpoints, VPN, AWS Direct Connect, or VPC Peering<a name="nat-gateway-other-services"></a>

A NAT gateway cannot send traffic over VPC endpoints, VPN connections, AWS Direct Connect, or VPC peering connections\. If your instances in the private subnet must access resources over a VPC endpoint, a VPN connection, or AWS Direct Connect, use the private subnet’s route table to route the traffic directly to these devices\. 

For example, your private subnet’s route table has the following routes: internet\-bound traffic \(0\.0\.0\.0/0\) is routed to a NAT gateway, Amazon S3 traffic \(pl\-xxxxxxxx; a specific IP address range for Amazon S3\) is routed to a VPC endpoint, and 10\.25\.0\.0/16 traffic is routed to a VPC peering connection\. The pl\-xxxxxxxx and 10\.25\.0\.0/16 IP address ranges are more specific than 0\.0\.0\.0/0; when your instances send traffic to Amazon S3 or the peered VPC, the traffic is sent to the VPC endpoint or the VPC peering connection\. When your instances send traffic to the internet \(other than the Amazon S3 IP addresses\), the traffic is sent to the NAT gateway\. 

You cannot route traffic to a NAT gateway through a VPC peering connection, a VPN connection, or AWS Direct Connect\. A NAT gateway cannot be used by resources on the other side of these connections\.

## Working with NAT Gateways<a name="nat-gateway-working-with"></a>

You can use the Amazon VPC console to create, view, and delete a NAT gateway\. You can also use the Amazon VPC wizard to create a VPC with a public subnet, a private subnet, and a NAT gateway\. For more information, see [Scenario 2: VPC with Public and Private Subnets \(NAT\)](VPC_Scenario2.md)\.

**Topics**
+ [Creating a NAT Gateway](#nat-gateway-creating)
+ [Updating Your Route Table](#nat-gateway-create-route)
+ [Deleting a NAT Gateway](#nat-gateway-deleting)
+ [Testing a NAT Gateway](#nat-gateway-testing)

### Creating a NAT Gateway<a name="nat-gateway-creating"></a>

To create a NAT gateway, you must specify a subnet and an Elastic IP address\. Ensure that the Elastic IP address is currently not associated with an instance or a network interface\. If you are migrating from a NAT instance to a NAT gateway and you want to reuse the NAT instance's Elastic IP address, you must first disassociate the address from your NAT instance\. 

**To create a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **NAT Gateways**, **Create NAT Gateway**\.

1. Specify the subnet in which to create the NAT gateway, and select the allocation ID of an Elastic IP address to associate with the NAT gateway\. When you're done, choose **Create a NAT Gateway**\. 

1. The NAT gateway displays in the console\. After a few moments, its status changes to `Available`, after which it's ready for you to use\. 

If the NAT gateway goes to a status of `Failed`, there was an error during creation\. For more information, see [NAT Gateway Goes to a Status of Failed](#nat-gateway-troubleshooting-failed)\.

### Updating Your Route Table<a name="nat-gateway-create-route"></a>

After you've created your NAT gateway, you must update your route tables for your private subnets to point internet traffic to the NAT gateway\. We use the most specific route that matches the traffic to determine how to route the traffic \(longest prefix match\)\. For more information, see [Route Priority](VPC_Route_Tables.md#route-tables-priority)\. 

**To create a route for a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the route table associated with your private subnet and choose **Routes**, **Edit**\.

1. Choose **Add another route**\. For **Destination**, type `0.0.0.0/0`\. For **Target**, select the ID of your NAT gateway\.
**Note**  
If you're migrating from using a NAT instance, you can replace the current route that points to the NAT instance with a route to the NAT gateway\.

1. Choose **Save**\.

To ensure that your NAT gateway can access the internet, the route table associated with the subnet in which your NAT gateway resides must include a route that points internet traffic to an internet gateway\. For more information, see [Creating a Custom Route Table](VPC_Internet_Gateway.md#Add_IGW_Routing)\. If you delete a NAT gateway, the NAT gateway routes remain in a `blackhole` status until you delete or update the routes\. For more information, see [Adding and Removing Routes from a Route Table](VPC_Route_Tables.md#AddRemoveRoutes)\.

### Deleting a NAT Gateway<a name="nat-gateway-deleting"></a>

You can delete a NAT gateway using the Amazon VPC console\. After you've deleted a NAT gateway, its entry remains visible in the Amazon VPC console for a short while \(usually an hour\), after which it's automatically removed\. You cannot remove this entry yourself\. 

**To delete a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **NAT Gateways**\.

1. Select the NAT gateway, and choose **Actions**, **Delete NAT Gateway**\.

1. In the confirmation dialog box, choose **Delete NAT Gateway**\.

### Testing a NAT Gateway<a name="nat-gateway-testing"></a>

After you've created your NAT gateway and updated your route tables, you can ping the internet from an instance in your private subnet to test that it can connect to the internet\. For an example of how to do this, see [Testing the internet Connection](#nat-gateway-testing-example)\.

If you're able to connect to the internet, you can also perform the following tests to determine if the internet traffic is being routed through the NAT gateway:
+ You can trace the route of traffic from an instance in your private subnet\. To do this, run the `traceroute` command from a Linux instance in your private subnet\. In the output, you should see the private IP address of the NAT gateway in one of the hops \(it's usually the first hop\)\.
+ Use a third\-party website or tool that displays the source IP address when you connect to it from an instance in your private subnet\. The source IP address should be the Elastic IP address of your NAT gateway\. You can get the Elastic IP address and private IP address of your NAT gateway by viewing its information on the **NAT Gateways** page in the Amazon VPC console\. 

If the preceding tests fail, see [Troubleshooting NAT Gateways](#nat-gateway-troubleshooting)\. 

#### Testing the internet Connection<a name="nat-gateway-testing-example"></a>

The following example demonstrates how to test if your instance in a private subnet can connect to the internet\.

1. Launch an instance in your public subnet \(you use this as a bastion host\)\. For more information, see [Launching an Instance into Your Subnet](working-with-vpcs.md#VPC_Launch_Instance)\. In the launch wizard, ensure that you select an Amazon Linux AMI, and assign a public IP address to your instance\. Ensure that your security group rules allow inbound SSH traffic from the range of IP addresses for your local network \(you can also use `0.0.0.0/0` for this test\), and outbound SSH traffic to the IP address range of your private subnet\.

1. Launch an instance in your private subnet\. In the launch wizard, ensure that you select an Amazon Linux AMI\. Do not assign a public IP address to your instance\. Ensure that your security group rules allow inbound SSH traffic from the private IP address of your instance that you launched in the public subnet, and all outbound ICMP traffic\. You must choose the same key pair that you used to launch your instance in the public subnet\. 

1. Configure SSH agent forwarding on your local computer, and connect to your bastion host in the public subnet\. For more information, see [To configure SSH agent forwarding for Linux or macOS](#ssh-forwarding-linux) or [To configure SSH agent forwarding for Windows \(PuTTY\)](#ssh-forwarding-windows)\.

1. From your bastion host, connect to your instance in the private subnet, and then test the internet connection from your instance in the private subnet\. For more information, see [To test the internet connection](#test-internet-connection)\.<a name="ssh-forwarding-linux"></a>

**To configure SSH agent forwarding for Linux or macOS**

1. From your local machine, add your private key to the authentication agent\. 

   For Linux, use the following command:

   ```
   ssh-add -c mykeypair.pem
   ```

   For macOS, use the following command:

   ```
   ssh-add -K mykeypair.pem
   ```

1. Connect to your instance in the public subnet using the `-A` option to enable SSH agent forwarding, and use the instance's public address; for example:

   ```
   ssh -A ec2-user@54.0.0.123
   ```<a name="ssh-forwarding-windows"></a>

**To configure SSH agent forwarding for Windows \(PuTTY\)**

1. Download and install Pageant from the [PuTTY download page](http://www.chiark.greenend.org.uk/~sgtatham/putty/), if not already installed\.

1. Convert your private key to \.ppk format\. For more information, see [Converting Your Private Key Using PuTTYgen](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-private-key) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Start Pageant, right\-click the Pageant icon on the taskbar \(it may be hidden\), and choose **Add Key**\. Select the \.ppk file that you created, type the passphrase if necessary, and choose **Open**\.

1. Start a PuTTY session and connect to your instance in the public subnet using its public IP address\. For more information, see [Starting a PuTTY Session](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-ssh)\. In the **Auth** category, ensure that you select the **Allow agent forwarding** option, and leave the **Private key file for authentication** box blank\.<a name="test-internet-connection"></a>

**To test the internet connection**

1. From your instance in the public subnet, connect to your instance in your private subnet by using its private IP address, for example:

   ```
   ssh ec2-user@10.0.1.123
   ```

1. From your private instance, test that you can connect to the internet by running the `ping` command for a website that has ICMP enabled, for example:

   ```
   ping ietf.org
   ```

   ```
   PING ietf.org (4.31.198.44) 56(84) bytes of data.
   64 bytes from mail.ietf.org (4.31.198.44): icmp_seq=1 ttl=47 time=86.0 ms
   64 bytes from mail.ietf.org (4.31.198.44): icmp_seq=2 ttl=47 time=75.6 ms
   ...
   ```

   Press **Ctrl\+C** on your keyboard to cancel the `ping` command\. If the `ping` command fails, see [Instances in Private Subnet Cannot Access internet](#nat-gateway-troubleshooting-no-internet-connection)\.

1. \(Optional\) If you no longer require your instances, terminate them\. For more information, see [Terminate Your Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Troubleshooting NAT Gateways<a name="nat-gateway-troubleshooting"></a>

The following topics help you to troubleshoot common problems you may encounter when creating or using a NAT gateway\.

**Topics**
+ [NAT Gateway Goes to a Status of Failed](#nat-gateway-troubleshooting-failed)
+ [You've Reached Your Elastic IP Address or NAT Gateway Limit](#nat-gateway-troubleshooting-limits)
+ [The Availability Zone Is Unsupported \(`NotAvailableInZone`\)](#nat-gateway-troubleshooting-unsupported-az)
+ [You Created a NAT Gateway and It's No Longer Visible](#nat-gateway-troubleshooting-gateway-removed)
+ [NAT Gateway Doesn't Respond to a Ping Command](#nat-gateway-troubleshooting-ping)
+ [Instances in Private Subnet Cannot Access internet](#nat-gateway-troubleshooting-no-internet-connection)
+ [TCP Connection to a Specific Endpoint Fails](#nat-gateway-troubleshooting-fragmentation)
+ [Traceroute Output Does Not Display NAT Gateway Private IP Address](#nat-gateway-troubleshooting-traceroute)
+ [Internet Connection Drops After 5 Minutes](#nat-gateway-troubleshooting-timeout)
+ [IPsec Connection Cannot Be Established](#nat-gateway-troubleshooting-ipsec)
+ [Cannot Initiate More Connections to a Destination](#nat-gateway-troubleshooting-simultaneous-connections)

### NAT Gateway Goes to a Status of Failed<a name="nat-gateway-troubleshooting-failed"></a>

If you create a NAT gateway and it goes to a status of `Failed`, there was an error when it was created\. To view the error message, go to the Amazon VPC console, choose **NAT Gateways**, select your NAT gateway, and then view the error message in the **Status** box in the details pane\. 

**Note**  
A failed NAT gateway is automatically deleted after a short period; usually about an hour\.

The following table lists the possible causes of the failure as indicated in the Amazon VPC console\. After you've applied any of the remedial steps indicated, you can try to create a NAT gateway again\.


| Displayed error | Reason | Remedial steps | 
| --- | --- | --- | 
| Subnet has insufficient free addresses to create this NAT gateway | The subnet you specified does not have any free private IP addresses\. The NAT gateway requires a network interface with a private IP address allocated from the subnet's range\.  | You can check how many IP addresses are available in your subnet by going to the Subnets page in the Amazon VPC console, and viewing the Available IPs box in the details pane for your subnet\. To create free IP addresses in your subnet, you can delete unused network interfaces, or terminate instances that you do not require\.  | 
| Network vpc\-xxxxxxxx has no Internet gateway attached | A NAT gateway must be created in a VPC with an internet gateway\. | Create and attach an internet gateway to your VPC\. For more information, see [Creating and Attaching an Internet Gateway](VPC_Internet_Gateway.md#Add_IGW_Attach_Gateway)\.  | 
| Elastic IP address eipalloc\-xxxxxxxx could not be associated with this NAT gateway | The Elastic IP address that you specified does not exist or could not be found\.  | Check the allocation ID of the Elastic IP address to ensure that you entered it correctly\. Ensure that you have specified an Elastic IP address that's in the same region in which you're creating the NAT gateway\. | 
| Elastic IP address eipalloc\-xxxxxxxx is already associated | The Elastic IP address that you specified is already associated with another resource, and cannot be associated with the NAT gateway\. | You can check which resource is associated with the Elastic IP address by going to the Elastic IPs page in the Amazon VPC console, and viewing the values specified for the instance ID or network interface ID\. If you do not require the Elastic IP address for that resource, you can disassociate it\. Alternatively, allocate a new Elastic IP address to your account\. For more information, see [Working with Elastic IP Addresses](vpc-eips.md#WorkWithEIPs)\. | 
| Network interface eni\-xxxxxxxx, created and used internally by this NAT gateway is in an invalid state\. Please try again\. | There was a problem creating or using the network interface for the NAT gateway\. | You cannot fix this error\. Try creating a NAT gateway again\. | 

### You've Reached Your Elastic IP Address or NAT Gateway Limit<a name="nat-gateway-troubleshooting-limits"></a>

If you've reached your Elastic IP address limit, you can disassociate an Elastic IP address from another resource, or you can request a limit increase using the [Amazon VPC Limits form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-vpc)\. 

If you've reached your NAT gateway limit, you can do one of the following:
+ Request a limit increase using the [Amazon VPC Limits form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-vpc)\. The NAT gateway limit is enforced per Availability Zone\.
+ Check the status of your NAT gateway\. A status of `Pending`, `Available`, or `Deleting` counts against your limit\. If you've recently deleted a NAT gateway, wait a few minutes for the status to go from `Deleting` to `Deleted`, then try creating a new NAT gateway\.
+ If you do not need your NAT gateway in a specific Availability Zone, try creating a NAT gateway in an Availability Zone where you haven't reached your limit\. 

For more information about limits, see [Amazon VPC Limits](VPC_Appendix_Limits.md)\. 

### The Availability Zone Is Unsupported \(`NotAvailableInZone`\)<a name="nat-gateway-troubleshooting-unsupported-az"></a>

In some cases, you may be trying to create the NAT gateway in a constrained Availability Zone — a zone in which our ability to expand is constrained\. We cannot support NAT gateways in these zones\. You can create a NAT gateway in another Availability Zone and use it for private subnets in the constrained zone\. You can also move your resources to an unconstrained Availability Zone so that your resources and your NAT gateway are in the same Availability Zone\.

### You Created a NAT Gateway and It's No Longer Visible<a name="nat-gateway-troubleshooting-gateway-removed"></a>

There may have been an error when your NAT gateway was being created, and it failed\. A NAT gateway with a status of `failed` is visible in the Amazon VPC console for a short while \(usually an hour\), after which it's automatically deleted\. Review the information in [NAT Gateway Goes to a Status of Failed](#nat-gateway-troubleshooting-failed), and try creating a new NAT gateway\.

### NAT Gateway Doesn't Respond to a Ping Command<a name="nat-gateway-troubleshooting-ping"></a>

If you try to ping a NAT gateway's Elastic IP address or private IP address from the internet \(for example, from your home computer\) or from any instance in your VPC, you do not get a response\. A NAT gateway only passes traffic from an instance in a private subnet to the internet\.

To test that your NAT gateway is working, see [Testing a NAT Gateway](#nat-gateway-testing)\.

### Instances in Private Subnet Cannot Access internet<a name="nat-gateway-troubleshooting-no-internet-connection"></a>

If you followed the preceding steps to test your NAT gateway and the `ping` command fails, or your instances cannot access the internet, check the following information:
+ Check that the NAT gateway is in the `Available` state\. In the Amazon VPC console, go to the **NAT Gateways** page and view the status information in the details pane\. If the NAT gateway is in a failed state, there may have been an error when it was created\. For more information, see [NAT Gateway Goes to a Status of Failed](#nat-gateway-troubleshooting-failed)\.
+ Check that you've configured your route tables correctly:
  + The NAT gateway must be in a public subnet with a route table that routes internet traffic to an internet gateway\. For more information, see [Creating a Custom Route Table](VPC_Internet_Gateway.md#Add_IGW_Routing)\.
  + Your instance must be in a private subnet with a route table that routes internet traffic to the NAT gateway\. For more information, see [Updating Your Route Table](#nat-gateway-create-route)\.
  + Check that there are no other route table entries that route all or part of the internet traffic to another device instead of the NAT gateway\.
+ Ensure that your security group rules for your private instance allow outbound internet traffic\. For the `ping` command to work, the rules must also allow outbound ICMP traffic\.
**Note**  
 The NAT gateway itself allows all outbound traffic and traffic received in response to an outbound request \(it is therefore stateful\)\.
+ Ensure that the network ACLs that are associated with the private subnet and public subnets do not have rules that block inbound or outbound internet traffic\. For the `ping` command to work, the rules must also allow inbound and outbound ICMP traffic\.
**Note**  
You can enable flow logs to help you diagnose dropped connections because of network ACL or security group rules\. For more information, see [VPC Flow Logs](flow-logs.md)\.
+ If you are using the `ping` command, ensure that you are pinging a website that has ICMP enabled\. If not, you will not receive reply packets\. To test this, perform the same `ping` command from the command line terminal on your own computer\. 
+ Check that your instance is able to ping other resources, for example, other instances in the private subnet \(assuming that security group rules allow this\)\.
+ Ensure that your connection is using a TCP, UDP, or ICMP protocol only\.

### TCP Connection to a Specific Endpoint Fails<a name="nat-gateway-troubleshooting-fragmentation"></a>

If a TCP connection to a specific endpoint or host is failing even though TCP connections to other endpoints are working normally, verify whether the endpoint to which you are trying to connect is responding with fragmented TCP packets\. A NAT gateway currently does not support IP fragmentation for TCP\. For more information, see [Comparison of NAT Instances and NAT Gateways](vpc-nat-comparison.md)\.

To check if the endpoint is sending fragmented TCP packets, use an instance in a public subnet with a public IP address to do the following:
+ Trigger a response large enough to cause fragmentation from the specific endpoint\.
+ Use the `tcpdump` utility to verify that the endpoint is sending fragmented packets\.

**Important**  
You must use an instance in a public subnet to perform these checks; you cannot use the instance from which the original connection was failing, or an instance in a private subnet behind a NAT gateway or a NAT instance\.

If the endpoint is sending fragmented TCP packets, you can use a NAT instance instead of a NAT gateway\. 

**Note**  
A NAT gateway also doesn't support IP fragmentation for the ICMP protocol\. Diagnostic tools that send or receive large ICMP packets will report packet loss\. For example, the command `ping -s 10000 example.com` does not work behind a NAT gateway\.

### Traceroute Output Does Not Display NAT Gateway Private IP Address<a name="nat-gateway-troubleshooting-traceroute"></a>

Your instance can access the internet, but when you perform the `traceroute` command, the output does not display the private IP address of the NAT gateway\. In this case, your instance is accessing the internet using a different device, such as an internet gateway\. In the route table of the subnet in which your instance is located, check the following information:
+ Ensure that there is a route that sends internet traffic to the NAT gateway\. 
+ Ensure that there isn't a more specific route that's sending internet traffic to other devices, such as a virtual private gateway or an internet gateway\. 

### Internet Connection Drops After 5 Minutes<a name="nat-gateway-troubleshooting-timeout"></a>

If a connection that's using a NAT gateway is idle for 5 minutes or more, the connection times out\. You can initiate more traffic over the connection or use a TCP keepalive to prevent the connection from being dropped\.

### IPsec Connection Cannot Be Established<a name="nat-gateway-troubleshooting-ipsec"></a>

NAT gateways currently do not support the IPsec protocol; however, you can use NAT\-Traversal \(NAT\-T\) to encapsulate IPsec traffic in UDP, which is a supported protocol for NAT gateways\. Ensure that you test your NAT\-T and IPsec configuration to verify that your IPsec traffic is not dropped\.

### Cannot Initiate More Connections to a Destination<a name="nat-gateway-troubleshooting-simultaneous-connections"></a>

You may have reached the limit for simultaneous connections\. For more information, see [NAT Gateway Rules and Limitations](#nat-gateway-limits)\. If your instances in the private subnet create a large number of connections, you may reach this limit\. You can do one of the following:
+ Create a NAT gateway per Availability Zone and spread your clients across those zones\.
+ Create additional NAT gateways in the public subnet and split your clients into multiple private subnets, each with a route to a different NAT gateway\.
+ Limit the number of connections your clients can create to the destination\.
+ To release the capacity, close idle connections\.

## Controlling the Use of NAT Gateways<a name="nat-gateway-iam"></a>

By default, IAM users do not have permission to work with NAT gateways\. You can create an IAM user policy that grants users permissions to create, describe, and delete NAT gateways\. We currently do not support resource\-level permissions for any of the `ec2:*NatGateway*` API operations\. For more information about IAM policies for Amazon VPC, see [Controlling Access to Amazon VPC Resources](VPC_IAM.md)\.

## Tagging a NAT Gateway<a name="nat-gateway-tagging"></a>

You can tag your NAT gateway to help you identify it or categorize it according to your organization's needs\. For information about working with tags, see [Tagging Your Amazon EC2 Resources](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Cost allocation tags are supported for NAT gateways, therefore you can also use tags to organize your AWS bill and reflect your own cost structure\. For more information, see [Using Cost Allocation Tags](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\. For more information about setting up a cost allocation report with tags, see [Monthly Cost Allocation Report](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html) in *About AWS Account Billing*\. 

## API and CLI Overview<a name="nat-gateway-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interfaces and a list of available API operations, see [Accessing Amazon VPC](VPC_Introduction.md#VPCInterfaces)\.

**Create a NAT gateway**
+ [create\-nat\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-nat-gateway.html) \(AWS CLI\)
+ [New\-EC2NatGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [CreateNatGateway](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNatGateway.html) \(Amazon EC2 Query API\)

**Tag a NAT gateway**
+ [create\-tags](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)
+ [CreateTags](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateTags.html) \(Amazon EC2 Query API\)

**Describe a NAT gateway**
+ [describe\-nat\-gateways](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-nat-gateways.html) \(AWS CLI\)
+ [Get\-EC2NatGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeNatGateways](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNatGateways.html) \(Amazon EC2 Query API\)

**Delete a NAT gateway**
+ [delete\-nat\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-nat-gateway.html) \(AWS CLI\)
+ [Remove\-EC2NatGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteNatGateway](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNatGateway.html) \(Amazon EC2 Query API\)