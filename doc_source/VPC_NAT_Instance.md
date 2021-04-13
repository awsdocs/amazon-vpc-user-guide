# NAT instances<a name="VPC_NAT_Instance"></a>

**Important**  
NAT AMI is built on the last version of Amazon Linux, 2018\.03 which reached the end of standard support on December 31, 2020\. For more information, see the following blog post: [Amazon Linux AMI end of life](http://aws.amazon.com/blogs/aws/update-on-amazon-linux-ami-end-of-life/)\. This feature will only receive critical security updates \(there will be no regular updates\)\.   
If you use an existing NAT AMI, AWS recommends that you migrate to a NAT gateway or create your own NAT AMI on Amazon Linux 2 as soon as possible\. For information about how to migrate your instance, see [Migrating from a NAT instance](vpc-nat-gateway.md#nat-instance-migrate)\.

You can create your own network address translation AMI and run it on an EC2 instance as NAT instance in a public subnet in your VPC to enable instances in the private subnet to initiate outbound IPv4 traffic to the internet or other AWS services, but prevent the instances from receiving inbound traffic initiated by someone on the internet\.

For more information about public and private subnets, see [Subnet routing](VPC_Subnets.md#SubnetRouting)\. For more information about NAT, see [NAT devices for your VPC](vpc-nat.md)\.

NAT is not supported for IPv6 traffic—use an egress\-only internet gateway instead\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.

Your NAT instance quota depends on your instance quota for the region\. For more information, see the [EC2 FAQs](http://aws.amazon.com/ec2/faqs/#EC2_On-Demand_Instance_limits)\. 

**Note**  
You can also use a NAT gateway, which is a managed NAT service that provides better availability, higher bandwidth, and requires less administrative effort\. For common use cases, we recommend that you use a NAT gateway rather than a NAT instance\. For more information, see [NAT gateways](vpc-nat-gateway.md) and [Comparing NAT gateways and NAT instances](vpc-nat-comparison.md)\.

**Topics**
+ [NAT instance basics](#basics)
+ [NAT instance AMI](#nat-instance-ami)
+ [Setting up the NAT instance](#NATInstance)
+ [Creating the NATSG security group](#NATSG)
+ [Disabling source/destination checks](#EIP_Disable_SrcDestCheck)
+ [Updating the main route table](#nat-routing-table)
+ [Testing your NAT instance configuration](#nat-test-configuration)

## NAT instance basics<a name="basics"></a>

The following figure illustrates the NAT instance basics\. The main route table is associated with the private subnet and sends the traffic from the instances in the private subnet to the NAT instance in the public subnet\. The NAT instance then sends the traffic to the internet gateway for the VPC\. The traffic is attributed to the Elastic IP address of the NAT instance\. The NAT instance specifies a high port number for the response; if a response comes back, the NAT instance sends it to an instance in the private subnet based on the port number for the response\.

Internet traffic from the instances in the private subnet is routed to the NAT instance, which then communicates with the internet\. Therefore, the NAT instance must have internet access\. It must be in a public subnet \(a subnet that has a route table with a route to the internet gateway\), and it must have a public IP address or an Elastic IP address\.

![\[NAT instance setup\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/nat-instance-diagram.png)

## NAT instance AMI<a name="nat-instance-ami"></a>

Even though Amazon provides Amazon Linux AMIs that are configured to run as NAT instances, they are built on last version of Amazon Linux, 2018\.03 which reached the end of standard support on December 31, 2020 and will only receive critical security updates \(there will be no regular updates\)\. If you use an existing NAT AMI \(these AMIs include the string `amzn-ami-vpc-nat` in their names\), AWS recommends that you migrate to a NAT gateway or create your own NAT AMI on Amazon Linux 2 as soon as possible\.

### Creating your NAT AMI<a name="create-nat-ami"></a>

You can start with an existing Amazon AMI and make appropriate customizations to create your own AMI to run as a NAT instance\. You can use this AMI the next time that you need to launch a NAT instance\. We recommend that you use the latest Amazon Linux 2 AMI to build your own NAT AMI\. For information about how to create the AMI, see [Creating Amazon EBS\-backed AMIs ](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html)in the* Amazon EC2 User Guide for Linux Instances*\.

### Updating your existing NAT instance<a name="update-nat-instance"></a>

If you already use a NAT AMI, we recommend you migrate to NAT Gateway or create your own NAT AMI on Amazon Linux 2\.

## Setting up the NAT instance<a name="NATInstance"></a>

Before you begin, create an AMI that's configured to run as a NAT instance\. For more information, see [Creating your NAT AMI](#create-nat-ami)\. This AMI is displayed in the Amazon Elastic Compute Cloud Console navigation pane, under **Images** when you filter to **Owned by me**\. 

To set up the VPC and NAT instance using the console, follow these steps:

1. Create a VPC with two subnets\.

   1. Create a VPC \(see [Creating a VPC](working-with-vpcs.md#Create-VPC)\)

   1. Create two subnets \(see [Creating a subnet](VPC_Internet_Gateway.md#Add_IGW_Create_Subnet)\)

   1. Attach an internet gateway to the VPC \(see [Creating and attaching an internet gateway](VPC_Internet_Gateway.md#Add_IGW_Attach_Gateway)\)

   1. Create a custom route table that sends traffic destined outside the VPC to the internet gateway, and then associate it with one subnet, making it a public subnet \(see [Creating a custom route table](VPC_Internet_Gateway.md#Add_IGW_Routing)\)

1. Create the NATSG security group \(see [Creating the NATSG security group](#NATSG)\)\. You'll specify this security group when you launch the NAT instance\.

1. Launch an instance into your public subnet from an AMI that's been configured to run as a NAT instance\.

   1. Open the Amazon EC2 console\.

   1. On the dashboard, choose the **Launch Instance** button, and complete the wizard as follows:

      1. On the **Choose an Amazon Machine Image \(AMI\)** page, set the filter to **Owned by me**, and then select your AMI\.

      1. On the **Choose an Instance Type** page, select the instance type, then choose **Next: Configure Instance Details**\.

      1. On the **Configure Instance Details** page, select the VPC you created from the **Network** list, and select your public subnet from the **Subnet** list\. 

      1. \(Optional\) Select the **Public IP** check box to request that your NAT instance receives a public IP address\. If you choose not to assign a public IP address now, you can allocate an Elastic IP address and assign it to your instance after it's launched\. For more information about assigning a public IP at launch, see [Assigning a public IPv4 address during instance launch](vpc-ip-addressing.md#vpc-public-ip)\. Choose **Next: Add Storage**\.

      1. You can choose to add storage to your instance, and on the next page, you can add tags\. Choose **Next: Configure Security Group** when you are done\. 

      1. On the **Configure Security Group** page, select the **Select an existing security group** option, and select the NATSG security group that you created\. Choose **Review and Launch**\.

      1. Review the settings that you've chosen\. Make any changes that you need, and then choose **Launch** to choose a key pair and launch your instance\.

1. Disable the `SrcDestCheck` attribute for the NAT instance \(see [Disabling source/destination checks](#EIP_Disable_SrcDestCheck)\)

1. If you did not assign a public IP address to your NAT instance during launch \(step 3\), you need to associate an Elastic IP address with it\.

   1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

   1. In the navigation pane, choose **Elastic IPs**, and then choose **Allocate new address**\.

   1. Choose **Allocate**\.

   1. Select the Elastic IP address from the list, and then choose **Actions**, **Associate address**\.

   1. Select the network interface resource, then select the network interface for the NAT instance\. Select the address to associate the Elastic IP with from the **Private IP** list, and then choose **Associate**\.

1. Update the main route table to send traffic to the NAT instance\. For more information, see [Updating the main route table](#nat-routing-table)\.

### Launching a NAT instance using the command line<a name="launch-nat-instance-cli"></a>

To launch a NAT instance into your subnet, use one of the following commands\. For more information, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\. You can use the AMI ID of the AMI that you configured to run as a NAT instance\. For information about how to create an AMI on Amazon Linux 2, see [Creating Amazon EBS\-backed AMIs ](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html)in the* Amazon EC2 User Guide for Linux Instances*\.
+ [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)
+ [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

## Creating the NATSG security group<a name="NATSG"></a>

Define the NATSG security group as described in the following table to enable your NAT instance to receive internet\-bound traffic from instances in a private subnet, as well as SSH traffic from your network\. The NAT instance can also send traffic to the internet, which enables the instances in the private subnet to get software updates\. 


**NATSG: recommended rules**  

| 
| 
| `Inbound` | 
| --- |
|  Source  |  Protocol  |  Port range  |  Comments  | 
|  `10.0.1.0/24`  |  TCP  |  80  |  Allow inbound HTTP traffic from servers in the private subnet  | 
|  `10.0.1.0/24`  |  TCP  |  443  |  Allow inbound HTTPS traffic from servers in the private subnet  | 
|  Public IP address range of your home network   |  TCP  |  22  |  Allow inbound SSH access to the NAT instance from your home network \(over the internet gateway\)  | 
|   `Outbound`   | 
| --- |
|  Destination  |  Protocol  |  Port range  |  Comments  | 
|  `0.0.0.0/0`  |  TCP  |  80  |  Allow outbound HTTP access to the internet  | 
|  `0.0.0.0/0`  |  TCP  |  443  |  Allow outbound HTTPS access to the internet  | 

**To create the NATSG security group**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**, and then choose **Create Security Group**\.

1. In the **Create Security Group** dialog box, specify `NATSG` as the name of the security group, and provide a description\. Select the ID of your VPC from the **VPC** list, and then choose **Yes, Create**\.

1. Select the NATSG security group that you just created\. The details pane displays the details for the security group, plus tabs for working with its inbound and outbound rules\.

1. Add rules for inbound traffic using the **Inbound Rules** tab as follows:

   1. Choose **Edit**\.

   1. Choose **Add another rule**, and select **HTTP** from the **Type** list\. In the **Source** field, specify the IP address range of your private subnet\.

   1. Choose **Add another rule**, and select **HTTPS** from the **Type** list\. In the **Source** field, specify the IP address range of your private subnet\.

   1. Choose **Add another rule**, and select **SSH** from the **Type** list\. In the **Source** field, specify the public IP address range of your network\.

   1. Choose **Save**\.

1. Add rules for outbound traffic using the **Outbound Rules** tab as follows:

   1. Choose **Edit**\.

   1. Choose **Add another rule**, and select **HTTP** from the **Type** list\. In the **Destination** field, specify `0.0.0.0/0`

   1. Choose **Add another rule**, and select **HTTPS** from the **Type** list\. In the **Destination** field, specify `0.0.0.0/0`

   1. Choose **Save**\.

For more information, see [Security groups for your VPC](VPC_SecurityGroups.md)\.

## Disabling source/destination checks<a name="EIP_Disable_SrcDestCheck"></a>

Each EC2 instance performs source/destination checks by default\. This means that the instance must be the source or destination of any traffic it sends or receives\. However, a NAT instance must be able to send and receive traffic when the source or destination is not itself\. Therefore, you must disable source/destination checks on the NAT instance\.

You can disable the `SrcDestCheck` attribute for a NAT instance that's either running or stopped using the console or the command line\.

**To disable source/destination checking using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the NAT instance, choose **Actions**, **Networking**, **Change source/destination check**\.

1. Verify that source/destination checking is stopped\. Otherwise, choose **Stop**\.

1. Choose **Save**\.

1. If the NAT instance has a secondary network interface, choose it from **Network interfaces** on the **Networking** tab\. Choose the interface ID to go to the network interfaces page\. Choose **Actions**, **Change source/dest\. check**, clear **Enable**, and choose **Save**\.

**To disable source/destination checking using the command line**

You can use one of the following commands\. For more information, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Updating the main route table<a name="nat-routing-table"></a>

The private subnet in your VPC is not associated with a custom route table, therefore it uses the main route table\. By default, the main route table enables the instances in your VPC to communicate with each other\. You must add a route that sends all other subnet traffic to the NAT instance\.

**To update the main route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the main route table for your VPC \(the **Main** column displays **Yes**\) \. The details pane displays tabs for working with its routes, associations, and route propagation\.

1. On the **Routes** tab, choose **Edit**, specify `0.0.0.0/0` in the **Destination** box, select the instance ID of the NAT instance from the **Target** list, and then choose **Save**\. 

1. On the **Subnet Associations** tab, choose **Edit**, and then select the **Associate** check box for the private subnet\. Choose **Save**\.

For more information, see [Route tables for your VPC](VPC_Route_Tables.md)\.

## Testing your NAT instance configuration<a name="nat-test-configuration"></a>

After you have launched a NAT instance and completed the configuration steps above, you can perform a test to check if an instance in your private subnet can access the internet through the NAT instance by using the NAT instance as a bastion server\. To do this, update your NAT instance's security group rules to allow inbound and outbound ICMP traffic and allow outbound SSH traffic, launch an instance into your private subnet, configure SSH agent forwarding to access instances in your private subnet, connect to your instance, and then test the internet connectivity\.

**To update your NAT instance's security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the checkbox for the security group associated with your NAT instance\.

1. Choose **Edit inbound rules** on the **Inbound rules** tab\.

1. Choose **Add rule**\. Choose **All ICMP \- IPv4** for **Type**\. Choose **Custom** for **Source** and enter the IP address range of your private subnet \(for example, `10.0.1.0/24`\)\. Choose **Save rules**\.

1. Choose **Edit outbound rules** on the **Outbound rules** tab\.

1. Choose **Add rule**\. Choose **SSH** for **Type**\. Choose **Custom** for **Destination** and enter the IP address range of your private subnet \(for example, `10.0.1.0/24`\)\.

1. Choose **Add rule**\. Choose **All ICMP \- IPv4** for **Type**\. Choose **Custom** for **Destination** and enter `0.0.0.0/0`\. Choose **Save rules**\.

**To launch an instance into your private subnet**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Launch an instance into your private subnet\. For more information, see [Launching an instance into your subnet](working-with-vpcs.md#VPC_Launch_Instance)\. Ensure that you configure the following options in the launch wizard, and then choose **Launch**:
   + On the **Choose an Amazon Machine Image \(AMI\)** page, select an Amazon Linux AMI from the **Quick Start** category\.
   + On the **Configure Instance Details** page, select your private subnet from the **Subnet** list, and do not assign a public IP address to your instance\.
   + On the **Configure Security Group** page, ensure that your security group includes an inbound rule that allows SSH access from your NAT instance's private IP address, or from the IP address range of your public subnet, and ensure that you have an outbound rule that allows outbound ICMP traffic\.
   + In the **Select an existing key pair or create a new key pair** dialog box, select the same key pair you used to launch the NAT instance\.

**To configure SSH agent forwarding for Linux or OS X**

1. From your local machine, add your private key to the authentication agent\. 

   For Linux, use the following command:

   ```
   ssh-add -c mykeypair.pem
   ```

   For OS X, use the following command:

   ```
   ssh-add -K mykeypair.pem
   ```

1. Connect to your NAT instance using the `-A` option to enable SSH agent forwarding, for example:

   ```
   ssh -A ec2-user@54.0.0.123
   ```

**To configure SSH agent forwarding for Windows \(PuTTY\)**

1. Download and install Pageant from the [PuTTY download page](http://www.chiark.greenend.org.uk/~sgtatham/putty/), if not already installed\.

1. Convert your private key to \.ppk format\. For more information, see [Converting your private key using PuTTYgen](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-private-key)\.

1. Start Pageant, right\-click the Pageant icon on the taskbar \(it may be hidden\), and choose **Add Key**\. Select the \.ppk file you created, enter the passphrase if required, and choose **Open**\.

1. Start a PuTTY session to connect to your NAT instance\. In the **Auth** category, ensure that you select the **Allow agent forwarding** option, and leave the **Private key file for authentication** field blank\.

**To test the internet connection**

1. Test that your NAT instance can communicate with the internet by running the `ping` command for a website that has ICMP enabled; for example:

   ```
   ping ietf.org
   ```

   ```
   PING ietf.org (4.31.198.44) 56(84) bytes of data.
   64 bytes from mail.ietf.org (4.31.198.44): icmp_seq=1 ttl=48 time=74.9 ms
   64 bytes from mail.ietf.org (4.31.198.44): icmp_seq=2 ttl=48 time=75.1 ms
   ...
   ```

   Press **Ctrl\+C** on your keyboard to cancel the `ping` command\.

1. From your NAT instance, connect to your instance in your private subnet by using its private IP address, for example:

   ```
   ssh ec2-user@10.0.1.123
   ```

1. From your private instance, test that you can connect to the internet by running the `ping` command:

   ```
   ping ietf.org
   ```

   ```
   PING ietf.org (4.31.198.44) 56(84) bytes of data.
   64 bytes from mail.ietf.org (4.31.198.44): icmp_seq=1 ttl=47 time=86.0 ms
   64 bytes from mail.ietf.org (4.31.198.44): icmp_seq=2 ttl=47 time=75.6 ms
   ...
   ```

   Press **Ctrl\+C** on your keyboard to cancel the `ping` command\.

   If the `ping` command fails, check the following information:
   + Check that your NAT instance's security group rules allow inbound ICMP traffic from your private subnet\. If not, your NAT instance cannot receive the `ping` command from your private instance\.
   + Check that you've configured your route tables correctly\. For more information, see [Updating the main route table](#nat-routing-table)\.
   + Ensure that you've disabled source/destination checking for your NAT instance\. For more information, see [Disabling source/destination checks](#EIP_Disable_SrcDestCheck)\.
   + Ensure that you are pinging a website that has ICMP enabled\. If not, you will not receive reply packets\. To test this, perform the same `ping` command from the command line terminal on your own computer\. 

1. \(Optional\) Terminate your private instance if you no longer require it\. For more information, see [Terminate your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.