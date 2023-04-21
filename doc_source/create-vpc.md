# Create a VPC<a name="create-vpc"></a>

Use the following procedures to create a virtual private cloud \(VPC\)\. A VPC must have additional resources, such as subnets, route tables, and gateways, before you can create AWS resources in the VPC\.

**Topics**
+ [VPC configuration options](#create-vpc-options)
+ [Create a VPC plus other VPC resources](#create-vpc-and-other-resources)
+ [Create a VPC only](#create-vpc-only)
+ [Create a VPC using the AWS CLI](#create-vpc-cli)

## VPC configuration options<a name="create-vpc-options"></a>

You can specify the following configuration options when you create a VPC\.

**Availability Zones**  
Discrete data centers with redundant power, networking, and connectivity in an AWS Region\. You can use multiple AZs to operate production applications and databases that are more highly available, fault tolerant, and scalable than would be possible from a single data center\. If you partition your applications running in subnets across AZs, you are better isolated and protected from issues such as power outages, lightning strikes, tornadoes, and earthquakes\.

**CIDR blocks**  
You must specify IP address ranges for your VPC and subnets\. For more information, see [IP addressing for your VPCs and subnets](vpc-ip-addressing.md)\.

**DNS options**  
If you need public IPv4 DNS hostnames for the EC2 instances launched into your subnets, you must enable both of the DNS options\. For more information, see [DNS attributes for your VPC](vpc-dns.md)\.  
+ **Enable DNS hostnames**: EC2 instances launched in the VPC receive public DNS hostnames that correspond to their public IPv4 addresses\.
+ **Enable DNS resolution**: DNS resolution for private DNS hostnames is provided for the VPC by the Amazon DNS server, called the Route 53 Resolver\.

**Internet gateway**  
Connects your VPC to the internet\. The instances in a public subnet can access the internet because the subnet route table contains a route that sends traffic bound for the internet to the internet gateway\. If a server doesn't need to be directly reachable from the internet, you should not deploy it into a public subnet\. For more information, see [Internet gateways](VPC_Internet_Gateway.md)\.

**Name**  
The names that you specify for the VPC and the other VPC resources are used to create Name tags\. If you use the name tag auto\-generation feature in the console, the tag values have the format *name*\-*resource*\.

**NAT gateways**  
Enables instances in a private subnet to send outbound traffic to the internet, but prevents resources on the internet from connecting to the instances\. In production, we recommend that you deploy a NAT gateway in each active AZ\. For more information, see [NAT gateways](vpc-nat-gateway.md)\.

**Route tables**  
Contains a set of rules, called routes, that determine where network traffic from your subnet or gateway is directed\. For more information, see [Route tables](VPC_Route_Tables.md)\.

**Subnets**  
A range of IP addresses in your VPC\. You can launch AWS resources, such as EC2 instances, into your subnets\. Each subnet resides entirely within one Availability Zone\. By launching instances in at least two Availability Zones, you can protect your applications from the failure of a single Availability Zone\.  
A public subnet has a direct route to an internet gateway\. Resources in a public subnet can access the public internet\. A private subnet does not have a direct route to an internet gateway\. Resources in a private subnet require another component, such as a NAT device, to access the public internet\.  
For more information, see [Subnets](configure-subnets.md)\.

**Tenancy**  
If the tenancy of a VPC is `default`, EC2 instances running in the VPC run on hardware that's shared with other AWS accounts by default\. If the tenancy of the VPC is `dedicated`, the instances always run as [Dedicated Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html), which are instances that run on hardware that's dedicated for your use\.

## Create a VPC plus other VPC resources<a name="create-vpc-and-other-resources"></a>

Use the following procedure to create a VPC plus the additional VPC resources that you need to run your application, such as subnets, route tables, internet gateways, and NAT gateways\. To create these resources using the AWS CLI, see [Create a VPC using the AWS CLI](#create-vpc-cli)\.

**To create a VPC, subnets, and other VPC resources using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the VPC dashboard, choose **Create VPC**\.

1. For **Resources to create**, choose **VPC and more**\.

1. Keep **Name tag auto\-generation** selected to create Name tags for the VPC resources, or clear it to provide your own Name tags for the VPC resources\.

1. For **IPv4 CIDR block**, enter an IPv4 address range for the VPC\. A VPC must have an IPv4 address range\.

1. \(Optional\) To support IPv6 traffic, choose **IPv6 CIDR block**, **Amazon\-provided IPv6 CIDR block**\.

1. Keep **Tenancy** as **Default** unless you need to ensure that EC2 instances launched in this VPC run as Dedicated Instances regardless of the tenancy attribute specified at launch\.

   \[AWS Outposts\] If your Outpost requires private connectivity, you must use default tenancy\.

1. For **Number of Availability Zones \(AZs\)**, we recommend that you provision subnets in at least 2 Availability Zones for a production environment\. To choose the AZs for your subnets, expand **Customize AZs**\. Otherwise, let AWS choose them for you\.

1. To configure your subnets, choose values for **Number of public subnets** and **Number of private subnets**\. To choose the IP address ranges for your subnets, expand **Customize subnets CIDR blocks**\. Otherwise, let AWS choose them for you\.

1. \(Optional\) If resources in a private subnet need access to the public internet over IPv4, for **NAT gateways**, choose the number of AZs in which to create NAT gateways\. In production, we recommend that you deploy a NAT gateway in each AZ with resources that need access to the public internet\.

1. \(Optional\) If resources in a private subnet need access to the public internet over IPv6, for **Egress only internet gateway**, choose **Yes**\.

1. \(Optional\) If you need to access Amazon S3 directly from your VPC, choose **VPC endpoints**, **S3 Gateway**\. This creates a gateway VPC endpoint for Amazon S3\. For more information, see [Gateway VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-gateway.html) in the *AWS PrivateLink Guide*\.

1. \(Optional\) For **DNS options**, both options for domain name resolution are enabled by default\. If the default doesn't meet you needs, you can disable these options\.

1. \(Optional\) To add a tag to your VPC, expand **Additional tags**, choose **Add new tag**, and enter a tag key and a tag value\.

1. In the **Preview** pane, you can visualize the relationships between the VPC resources that you've configured\. Solid lines represent relationships between resources\. Dotted lines represent network traffic to NAT gateways, internet gateways, and gateway endpoints\. After you create the VPC, you can visualize the resources in your VPC in this format at any time using the **Resource map** tab\. For more information, see [Visualize the resources in your VPC](modify-vpcs.md#view-vpc-resource-map)\.

1. When you are finished configuring your VPC, choose **Create VPC**\.

## Create a VPC only<a name="create-vpc-only"></a>

Use the following procedure to create a VPC with no additional VPC resources using the Amazon VPC console\.

**To create a VPC with no additional VPC resources using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the VPC dashboard, choose **Create VPC**\.

1. For **Resources to create**, choose **VPC only**\.

1. \(Optional\) For **Name tag**, enter a name for your VPC\. Doing so creates a tag with a key of `Name` and the value that you specify\.

1. For **IPv4 CIDR block**, do one of the following:
   + Choose **IPv4 CIDR manual input** and enter an IPv4 address range for your VPC\.
   + Choose **IPAM\-allocated IPv4 CIDR block**, select your IPAM IPv4 address pool and a netmask\. The size of the CIDR block is limited by the allocation rules on the IPAM pool\.

     If you are using IPAM to manage your IP addresses, we recommend that you choose this option\. Otherwise, the CIDR block that you specify for your VPC might overlap with an IPAM CIDR allocation\.

1. \(Optional\) To create a dual stack VPC, specify an IPv6 address range for your VPC\. For **IPv6 CIDR block**, do one of the following:
   + Choose **IPAM\-allocated IPv6 CIDR block** and select your IPAM IPv6 address pool\. The size of the CIDR block is limited by the allocation rules on the IPAM pool\.
   + Choose **Amazon\-provided IPv6 CIDR block** to request an IPv6 CIDR block from an Amazon pool of IPv6 addresses\. For **Network Border Group**, select the group from which AWS advertises IP addresses\. Amazon provides a fixed IPv6 CIDR block size of /56\.
   + Choose **IPv6 CIDR owned by me** to use an IPv6 CIDR block that you brought to AWS using [bring your own IP addresses \(BYOIP\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-byoip.html)\. For **Pool**, choose the IPv6 address pool from which to allocate the IPv6 CIDR block\.

1. \(Optional\) To launch EC2 instances as Dedicated Instances, choose **Tenancy**, **Dedicated**\.

   \[AWS Outposts\] If your Outpost requires private connectivity, you must use default tenancy\.

1. \(Optional\) To add a tag to your VPC, choose **Add new tag** and enter a tag key and a tag value\.

1. Choose **Create VPC**\.

1. After you create a VPC, you can add subnets\. For more information, see [Create a subnet](create-subnets.md)\.

## Create a VPC using the AWS CLI<a name="create-vpc-cli"></a>

The following procedure contains example AWS CLI commands to create a VPC plus the additional VPC resources needed to run an application\. If you run all the commands in this procedure, you'll create a VPC, a public subnet, a private subnet, a route table for each subnet, an internet gateway, an egress\-only internet gateway, and a public NAT gateway\. If you do not need all of these resources, you can use only the example commands that you need\.

**Prerequisites**  
Before you begin, install and configure the AWS CLI\. When you configure the AWS CLI, you are prompted for AWS credentials\. The examples in this procedure assume that you also configured a default Region\. Otherwise, add the `--region` option to each command\. For more information, see [Installing or updating the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)\.

**Tagging**  
You can add tags to a resource after you create it using the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command\. Alternatively, you can add the `--tag-specification` option to the creation command for the resource as follows\.

```
--tag-specifications ResourceType=vpc,Tags=[{Key=Name,Value=my-project}]
```

**To create a VPC plus VPC resources using the AWS CLI**

1. Use the following [create\-vpc](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc.html) command to create a VPC with the specified IPv4 CIDR block\.

   ```
   aws ec2 create-vpc --cidr-block 10.0.0.0/24 --query Vpc.VpcId --output text
   ```

   Alternatively, to create a dual stack VPC, add the `--amazon-provided-ipv6-cidr-block` option to add an Amazon\-provided IPv6 CIDR block, as shown in the following example\.

   ```
   aws ec2 create-vpc --cidr-block 10.0.0.0/24 --amazon-provided-ipv6-cidr-block --query Vpc.VpcId --output text
   ```

   These commands return the ID of the new VPC\. The following is an example\.

   ```
   vpc-1a2b3c4d5e6f1a2b3
   ```

1. \[Dual stack VPC\] Get the IPv6 CIDR block that's associated with your VPC using the following [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) command\.

   ```
   aws ec2 describe-vpcs --vpc-id vpc-1a2b3c4d5e6f1a2b3 --query Vpcs[].Ipv6CidrBlockAssociationSet[].Ipv6CidrBlock --output text
   ```

   The following is example output\.

   ```
   2600:1f13:cfe:3600::/56
   ```

1. Create one or more subnets, depending on your use case\. In production, we recommend that you launch resources in at least two Availability Zones\. Use one of the following commands to create each subnet\.
   + **IPv4\-only subnet** – To create a subnet with a specific IPv4 CIDR block, use the following [create\-subnet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-subnet.html) command\.

     ```
     aws ec2 create-subnet --vpc-id vpc-1a2b3c4d5e6f1a2b3 --cidr-block 10.0.1/0/20 --availability-zone us-east-2a --query Subnet.SubnetId --output text
     ```
   + **Dual stack subnet** – If you created a dual stack VPC, you can use the `--ipv6-cidr-block` option to create a dual stack subnet, as shown in the following command\.

     ```
     aws ec2 create-subnet --vpc-id vpc-1a2b3c4d5e6f1a2b3 --cidr-block 10.0.1/0/20 --ipv6-cidr-block 2600:1f13:cfe:3600::/64 --availability-zone us-east-2a --query Subnet.SubnetId --output text
     ```
   + **IPv6\-only subnet** – If you created a dual stack VPC, you can use the `--ipv6-native` option to create an IPv6\-only subnet, as shown in the following command\.

     ```
     aws ec2 create-subnet --vpc-id vpc-1a2b3c4d5e6f1a2b3 --ipv6-native --ipv6-cidr-block 2600:1f13:cfe:3600::/64 --availability-zone us-east-2a --query Subnet.SubnetId --output text
     ```

   These commands return the ID of the new subnet\. The following is an example\.

   ```
   subnet-1a2b3c4d5e6f1a2b3
   ```

1. If you need a public subnet for your web servers, or for a NAT gateway, do the following:

   1. Create an internet gateway using the following [create\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-internet-gateway.html) command\. The command returns the ID of the new internet gateway\.

      ```
      aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text
      ```

   1. Attach the internet gateway to your VPC using the following [attach\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-internet-gateway.html) command\. Use the internet gateway ID returned from the previous step\.

      ```
      aws ec2 attach-internet-gateway --vpc-id vpc-1a2b3c4d5e6f1a2b3 --internet-gateway-id igw-id
      ```

   1. Create a custom route table for your public subnet using the following [create\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route-table.html) command\. The command returns the ID of the new route table\.

      ```
      aws ec2 create-route-table --vpc-id vpc-1a2b3c4d5e6f1a2b3 --query RouteTable.RouteTableId --output text
      ```

   1. Create a route in the route table that sends all IPv4 traffic to the internet gateway using the following [create\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) command\. Use the ID of the route table for the public subnet\.

      ```
      aws ec2 create-route --route-table-id rtb-id-public --destination-cidr-block 0.0.0.0/0 --gateway-id igw-id
      ```

   1. Associate the route table with the public subnet using the following [associate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) command\. Use the ID of the route table for the public subnet and the ID of the public subnet\.

      ```
      aws ec2 associate-route-table --route-table-id rtb-id-public --subnet-id subnet-id-public-subnet
      ```

1. \[IPv6\] You can add an egress\-only internet gateway so that instances in a private subnet can access the internet over IPv6 \(for example, to get software updates\), but hosts on the internet can't access your instances\.

   1. Create an egress\-only internet gateway using the following [create\-egress\-only\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-egress-onlyinternet-gateway.html) command\. The command returns the ID of the new internet gateway\.

      ```
      aws ec2 create-egress-only-internet-gateway --vpc-id vpc-1a2b3c4d5e6f1a2b3 --query EgressOnlyInternetGateway.EgressOnlyInternetGatewayId --output text
      ```

   1. Create a custom route table for your private subnet using the following [create\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route-table.html) command\. The command returns the ID of the new route table\.

      ```
      aws ec2 create-route-table --vpc-id vpc-1a2b3c4d5e6f1a2b3 --query RouteTable.RouteTableId --output text
      ```

   1. Create a route in the route table for the private subnet that sends all IPv6 traffic to the egress\-only internet gateway using the following [create\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) command\. Use the ID of the route table returned in the previous step\.

      ```
      aws ec2 create-route --route-table-id rtb-id-private --destination-cidr-block ::/0 --egress-only-internet-gateway eigw-id
      ```

   1. Associate the route table with the private subnet using the following [associate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) command\.

      ```
      aws ec2 associate-route-table --route-table-id rtb-id-private --subnet-id subnet-id-private-subnet
      ```

1. If you need a NAT gateway for your resources in a private subnet, do the following:

   1. Create an elastic IP address for the NAT gateway using the following [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) command\.

      ```
      aws ec2 allocate-address --domain vpc --query AllocationId --output text
      ```

   1. Create the NAT gateway using the following [create\-nat\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-nat-gateway.html) command\. Use the allocation ID returned from the previous step\.

      ```
      aws ec2 create-nat-gateway --subnet-id subnet-id-private-subnet --allocation-id eipalloc-id
      ```

   1. \(Optional\) If you already created a route table for the private subnet in step 5, skip this step\. Otherwise, use the following [create\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route-table.html) command to create a route table for your private subnet\. The command returns the ID of the new route table\.

      ```
      aws ec2 create-route-table --vpc-id vpc-1a2b3c4d5e6f1a2b3 --query RouteTable.RouteTableId --output text
      ```

   1. Create a route in the route table for the private subnet that sends all IPv4 traffic to the NAT gateway using the following [create\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) command\. Use the ID of the route table for the private subnet, which you created either in this step or in step 5\.

      ```
      aws ec2 create-route --route-table-id rtb-id-private --destination-cidr-block 0.0.0.0/0 --gateway-id nat-id
      ```

   1. \(Optional\) If you already associated a route table with the private subnet in step 5, skip this step\. Otherwise, use the following [associate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) command to associate the route table with the private subnet\. Use the ID of the route table for the private subnet, which you created either in this step or in step 5\.

      ```
      aws ec2 associate-route-table --route-table-id rtb-id-private --subnet-id subnet-id-private-subnet
      ```