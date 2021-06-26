# NAT gateways<a name="vpc-nat-gateway"></a>

A NAT gateway is a Network Address Translation \(NAT\) service\. You can use a NAT gateway so that instances in a private subnet can connect to services outside your VPC but external services cannot initiate a connection with those instances\.

The NAT gateway replaces the source IPv4 address of the instances with the private IP address of the NAT gateway\. When sending response traffic to the instances, the NAT device translates the addresses back to the original source IPv4 addresses\.

When you create a NAT gateway, you specify one of the following connectivity types:
+ **Public** – \(Default\) Instances in private subnets can connect to the internet through a public NAT gateway, but cannot receive unsolicited inbound connections from the internet\. You create a public NAT gateway in a public subnet and must associate an elastic IP address with the NAT gateway at creation\. You route traffic from the NAT gateway to the internet gateway for the VPC\. Alternatively, you can use a public NAT gateway to connect to other VPCs or your on\-premises network\. In this case, you route traffic from the NAT gateway through a transit gateway or a virtual private gateway\.
+ **Private** – Instances in private subnets can connect to other VPCs or your on\-premises network through a private NAT gateway\. You can route traffic from the NAT gateway through a transit gateway or a virtual private gateway\. You cannot associate an elastic IP address with a private NAT gateway\. You can attach an internet gateway to a VPC with a private NAT gateway, but if you route traffic from the private NAT gateway to the internet gateway, the internet gateway drops the traffic\.

**Pricing**  
NAT gateway hourly usage and data processing rates apply\. Amazon EC2 charges for data transfer also apply\. For more information, see [Amazon VPC Pricing](http://aws.amazon.com/vpc/pricing/)\.

**Topics**
+ [NAT gateway basics](#nat-gateway-basics)
+ [Control the use of NAT gateways](#nat-gateway-iam)
+ [Work with NAT gateways](#nat-gateway-working-with)
+ [NAT gateway scenarios](#nat-gateway-scenarios)
+ [Migrate from a NAT instance](#nat-instance-migrate)
+ [API and CLI overview](#nat-gateway-api-cli)
+ [Monitor using CloudWatch](vpc-nat-gateway-cloudwatch.md)
+ [Troubleshoot](nat-gateway-troubleshooting.md)

## NAT gateway basics<a name="nat-gateway-basics"></a>

Each NAT gateway is created in a specific Availability Zone and implemented with redundancy in that zone\. There is a quota on the number of NAT gateways that you can create in each Availability Zone\. For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

If you have resources in multiple Availability Zones and they share one NAT gateway, and if the NAT gateway’s Availability Zone is down, resources in the other Availability Zones lose internet access\. To create an Availability Zone\-independent architecture, create a NAT gateway in each Availability Zone and configure your routing to ensure that resources use the NAT gateway in the same Availability Zone\.

The following characteristics and rules apply to NAT gateways:
+ A NAT gateway supports the following protocols: TCP, UDP, and ICMP\.
+ NAT gateways are not supported for IPv6 traffic—use an outbound\-only \(egress\-only\) internet gateway instead\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.
+ A NAT gateway supports 5 Gbps of bandwidth and automatically scales up to 45 Gbps\. If you require more bandwidth, you can split your resources into multiple subnets and create a NAT gateway in each subnet\.
+ A NAT gateway can support up to 55,000 simultaneous connections to each unique destination\. This limit also applies if you create approximately 900 connections per second to a single destination \(about 55,000 connections per minute\)\. If the destination IP address, the destination port, or the protocol \(TCP/UDP/ICMP\) changes, you can create an additional 55,000 connections\. For more than 55,000 connections, there is an increased chance of connection errors due to port allocation errors\. These errors can be monitored by viewing the `ErrorPortAllocation` CloudWatch metric for your NAT gateway\. For more information, see [Monitor NAT gateways using Amazon CloudWatch](vpc-nat-gateway-cloudwatch.md)\.
+ You can associate exactly one Elastic IP address with a public NAT gateway\. You cannot disassociate an Elastic IP address from a NAT gateway after it's created\. To use a different Elastic IP address for your NAT gateway, you must create a new NAT gateway with the required address, update your route tables, and then delete the existing NAT gateway if it's no longer required\.
+ A private NAT gateway receives an available private IP address from the subnet in which it is configured\. You cannot detach this private IP address and you cannot attach additional private IP addresses\.
+ You cannot associate a security group with a NAT gateway\. You can associate security groups with your instances to control inbound and outbound traffic\.
+ You can use a network ACL to control the traffic to and from the subnet for your NAT gateway\. NAT gateways use ports 1024–65535\. For more information, see [Network ACLs](vpc-network-acls.md)\.
+ A NAT gateway receives a network interface that's automatically assigned a private IP address from the IP address range of the subnet\. You can view the network interface for the NAT gateway using the Amazon EC2 console\. For more information, see [Viewing details about a network interface](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#view_eni_details)\. You cannot modify the attributes of this network interface\.
+ A NAT gateway cannot be accessed through a ClassicLink connection that is associated with your VPC\.
+ You cannot route traffic to a NAT gateway through a VPC peering connection, a Site\-to\-Site VPN connection, or AWS Direct Connect\. A NAT gateway cannot be used by resources on the other side of these connections\.

## Control the use of NAT gateways<a name="nat-gateway-iam"></a>

By default, IAM users do not have permission to work with NAT gateways\. You can create an IAM user policy that grants users permissions to create, describe, and delete NAT gateways\. For more information, see [Identity and access management for Amazon VPC](security-iam.md)\.

## Work with NAT gateways<a name="nat-gateway-working-with"></a>

You can use the Amazon VPC console to create and manage your NAT gateways\. You can also use the Amazon VPC wizard to create a VPC with a public subnet, a private subnet, and a NAT gateway\. For more information, see [VPC with public and private subnets \(NAT\)](VPC_Scenario2.md)\.

**Topics**
+ [Create a NAT gateway](#nat-gateway-creating)
+ [Tag a NAT gateway](#nat-gateway-tagging)
+ [Delete a NAT gateway](#nat-gateway-deleting)

### Create a NAT gateway<a name="nat-gateway-creating"></a>

To create a NAT gateway, enter an optional name, a subnet, and an optional connectivity type\. With a public NAT gateway, you must specify an available elastic IP address\. A private NAT gateway receives a primary private IP address selected at random from its subnet\. You cannot detach the primary private IP address or add secondary private IP addresses\.

**To create a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **NAT Gateways**\.

1. Choose **Create NAT Gateway** and do the following:

   1. \(Optional\) Specify a name for the NAT gateway\. This creates a tag where the key is **Name** and the value is the name that you specify\.

   1. Select the subnet in which to create the NAT gateway\.

   1. For **Connectivity type**, select **Private** to create a private NAT gateway or **Public** \(the default\) to create a public NAT gateway\.

   1. \(Public NAT gateway only\) For **Elastic IP allocation ID**, select an Elastic IP address to associate with the NAT gateway\.

   1. \(Optional\) For each tag, choose **Add new tag** and enter the key name and value\.

   1. Choose **Create a NAT Gateway**\.

1. The initial status of the NAT gateway is `Pending`\. After the status changes to `Available`, the NAT gateway is ready for you to use\. Add a route to the NAT gateway to the route tables for the private subnets and add routes to the route table for the NAT gateway\.

   If the status of the NAT gateway changes to `Failed`, there was an error during creation\. For more information, see [NAT gateway creation fails](nat-gateway-troubleshooting.md#nat-gateway-troubleshooting-failed)\.

### Tag a NAT gateway<a name="nat-gateway-tagging"></a>

You can tag your NAT gateway to help you identify it or categorize it according to your organization's needs\. For information about working with tags, see [Tagging your Amazon EC2 resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Cost allocation tags are supported for NAT gateways\. Therefore, you can also use tags to organize your AWS bill and reflect your own cost structure\. For more information, see [Using cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\. For more information about setting up a cost allocation report with tags, see [Monthly cost allocation report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html) in *About AWS Account Billing*\. 

### Delete a NAT gateway<a name="nat-gateway-deleting"></a>

If you no longer need a NAT gateway, you can delete it\. After you delete a NAT gateway, its entry remains visible in the Amazon VPC console for about an hour, after which it's automatically removed\. You cannot remove this entry yourself\.

Deleting a NAT gateway disassociates its Elastic IP address, but does not release the address from your account\. If you delete a NAT gateway, the NAT gateway routes remain in a `blackhole` status until you delete or update the routes\.

**To delete a NAT gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **NAT Gateways**\.

1. Select the radio button for the NAT gateway, and then choose **Actions**, **Delete NAT gateway**\.

1. When prompted for confirmation, enter **delete** and then choose **Delete**\.

1. If you no longer need the Elastic IP address that was associated with a public NAT gateway, we recommend that you release it\. For more information, see [Release an Elastic IP address](vpc-eips.md#release-eip)\.

## NAT gateway scenarios<a name="nat-gateway-scenarios"></a>

The following are example use cases for public and private NAT gateways\.

**Topics**
+ [Access the internet from a private subnet](#public-nat-internet-access)
+ [Allow access to your network from allow\-listed IP addresses](#private-nat-allowed-range)

### Scenario: Access the internet from a private subnet<a name="public-nat-internet-access"></a>

You can use a public NAT gateway to enable instances in a private subnet to send outbound traffic to the internet, but the internet cannot establish connections to the instances\.

The following diagram illustrates the architecture for this use case\. The public subnet in Availability Zone A contains the NAT gateway\. The private subnet in Availability Zone B contains instances\. The router sends internet bound traffic from the instances in the private subnet to the NAT gateway\. The NAT gateway sends the traffic to the internet gateway, using the private IP address for the NAT gateway as the source IP address\.

![\[A VPC with public and private subnets and a NAT gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/nat-gateway-diagram.png)

The following is the route table associated with the public subnet in Availability Zone A\. The first entry is the default entry for local routing in the VPC; it enables the instances in the VPC to communicate with each other\. The second entry sends all other subnet traffic to the internet gateway; this enables the NAT gateway to access the internet\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | local | 
| 0\.0\.0\.0/0 | internet\-gateway\-id | 

The following is the route table associated with the private subnet in Availability Zone B\. The first entry is the default entry for local routing in the VPC; it enables the instances in the VPC to communicate with each other\. The second entry sends all other subnet traffic, such as internet bound traffic, to the NAT gateway\.


| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | local | 
| 0\.0\.0\.0/0 | nat\-gateway\-id | 

#### Test the public NAT gateway<a name="nat-gateway-testing"></a>

After you've created your NAT gateway and updated your route tables, you can ping remote addresses on the internet from an instance in your private subnet to test whether it can connect to the internet\. For an example of how to do this, see [Test the internet connection](#nat-gateway-testing-example)\.

If you can connect to the internet, you can also test whether internet traffic is routed through the NAT gateway:
+ Trace the route of traffic from an instance in your private subnet\. To do this, run the `traceroute` command from a Linux instance in your private subnet\. In the output, you should see the private IP address of the NAT gateway in one of the hops \(usually the first hop\)\.
+ Use a third\-party website or tool that displays the source IP address when you connect to it from an instance in your private subnet\. The source IP address should be the elastic IP address of the NAT gateway\. 

If these tests fail, see [Troubleshoot NAT gateways](nat-gateway-troubleshooting.md)\.

##### Test the internet connection<a name="nat-gateway-testing-example"></a>

The following example demonstrates how to test whether an instance in a private subnet can connect to the internet\.

1. Launch an instance in your public subnet \(use this as a bastion host\)\. For more information, see [Launch an instance into your subnet](working-with-vpcs.md#VPC_Launch_Instance)\. In the launch wizard, ensure that you select an Amazon Linux AMI, and assign a public IP address to your instance\. Ensure that your security group rules allow inbound SSH traffic from the range of IP addresses for your local network, and outbound SSH traffic to the IP address range of your private subnet \(you can also use `0.0.0.0/0` for both inbound and outbound SSH traffic for this test\)\.

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

### Scenario: Allow access to your network from allow\-listed IP addresses<a name="private-nat-allowed-range"></a>

Instead of assigning each instance a separate IP address from the IP address range that is allowed to access your on\-premises network, you can create a subnet in your VPC with the allowed IP address range, create a private NAT gateway in the subnet, and route the traffic from your VPC destined for your on\-premises network through the NAT gateway\.

## Migrate from a NAT instance<a name="nat-instance-migrate"></a>

If you're already using a NAT instance, you can replace it with a NAT gateway\. To do this, you can create a NAT gateway in the same subnet as your NAT instance, and then replace the existing route in your route table that points to the NAT instance with a route that points to the NAT gateway\. To use the same Elastic IP address for the NAT gateway that you currently use for your NAT instance, you must first also disassociate the Elastic IP address from your NAT instance and then associate it with your NAT gateway when you create the gateway\.

If you change your routing from a NAT instance to a NAT gateway, or if you disassociate the Elastic IP address from your NAT instance, any current connections are dropped and have to be re\-established\. Ensure that you do not have any critical tasks \(or any other tasks that operate through the NAT instance\) running\.

## API and CLI overview<a name="nat-gateway-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interfaces and a list of available API operations, see [Access Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create a NAT gateway**
+ [create\-nat\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-nat-gateway.html) \(AWS CLI\)
+ [New\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [CreateNatGateway](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNatGateway.html) \(Amazon EC2 Query API\)

**Describe a NAT gateway**
+ [describe\-nat\-gateways](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-nat-gateways.html) \(AWS CLI\)
+ [Get\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeNatGateways](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNatGateways.html) \(Amazon EC2 Query API\)

**Tag a NAT gateway**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)
+ [CreateTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateTags.html) \(Amazon EC2 Query API\)

**Delete a NAT gateway**
+ [delete\-nat\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-nat-gateway.html) \(AWS CLI\)
+ [Remove\-EC2NatGateway](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2NatGateway.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteNatGateway](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNatGateway.html) \(Amazon EC2 Query API\)
