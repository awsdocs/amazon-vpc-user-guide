# NAT Instances<a name="VPC_NAT_Instance"></a>

You can use a network address translation \(NAT\) instance in a public subnet in your VPC to enable instances in the private subnet to initiate outbound IPv4 traffic to the Internet or other AWS services, but prevent the instances from receiving inbound traffic initiated by someone on the Internet\.

For more information about public and private subnets, see [Subnet Routing](VPC_Subnets.md#SubnetRouting)\. For more information about NAT, see [NAT](vpc-nat.md)\.

NAT is not supported for IPv6 traffic—use an egress\-only Internet gateway instead\. For more information, see [Egress\-Only Internet Gateways](egress-only-internet-gateway.md)\.

**Note**  
You can also use a NAT gateway, which is a managed NAT service that provides better availability, higher bandwidth, and requires less administrative effort\. For common use cases, we recommend that you use a NAT gateway rather than a NAT instance\. For more information, see [NAT Gateways](vpc-nat-gateway.md) and [Comparison of NAT Instances and NAT Gateways](vpc-nat-comparison.md)\.

**Topics**
+ [NAT Instance Basics](#basics)
+ [Setting up the NAT Instance](#NATInstance)
+ [Creating the NATSG Security Group](#NATSG)
+ [Disabling Source/Destination Checks](#EIP_Disable_SrcDestCheck)
+ [Updating the Main Route Table](#nat-routing-table)
+ [Testing Your NAT Instance Configuration](#nat-test-configuration)

## NAT Instance Basics<a name="basics"></a>

The following figure illustrates the NAT instance basics\. The main route table is associated with the private subnet and sends the traffic from the instances in the private subnet to the NAT instance in the public subnet\. The NAT instance sends the traffic to the Internet gateway for the VPC\. The traffic is attributed to the Elastic IP address of the NAT instance\. The NAT instance specifies a high port number for the response; if a response comes back, the NAT instance sends it to an instance in the private subnet based on the port number for the response\. 

![\[NAT instance setup\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/nat-instance-diagram.png)

Amazon provides Amazon Linux AMIs that are configured to run as NAT instances\. These AMIs include the string `amzn-ami-vpc-nat` in their names, so you can search for them in the Amazon EC2 console\. 

When you launch an instance from a NAT AMI, the following configuration occurs on the instance:
+ IPv4 forwarding is enabled and ICMP redirects are disabled in `/etc/sysctl.d/10-nat-settings.conf`
+ A script located at `/usr/sbin/configure-pat.sh` runs at startup and configures iptables IP masquerading\. 

**Note**  
We recommend that you always use the latest version of the NAT AMI to take advantage of configuration updates\.   
If you add and remove secondary IPv4 CIDR blocks on your VPC, ensure that you use AMI version `amzn-ami-vpc-nat-hvm-2017.03.1.20170623-x86_64-ebs` or later\. 

Your NAT instance limit depends on your instance type limit for the region\. For more information, see the [EC2 FAQs](http://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. For a list of available NAT AMIs, see the [Amazon Linux AMI matrix](http://aws.amazon.com/amazon-linux-ami/)\.

## Setting up the NAT Instance<a name="NATInstance"></a>

You can use the VPC wizard to set up a VPC with a NAT instance; for more information, see [Scenario 2: VPC with Public and Private Subnets \(NAT\)](VPC_Scenario2.md)\. The wizard performs many of the configuration steps for you, including launching a NAT instance, and setting up the routing\. However, if you prefer, you can create and configure a VPC and a NAT instance manually using the steps below\. 

1. Create a VPC with two subnets\.
**Note**  
The steps below are for manually creating and configuring a VPC; not for creating a VPC using the VPC wizard\.

   1. Create a VPC \(see [Creating a VPC](working-with-vpcs.md#Create-VPC)\)

   1. Create two subnets \(see [Creating a Subnet](VPC_Internet_Gateway.md#Add_IGW_Create_Subnet)\)

   1. Attach an Internet gateway to the VPC \(see [Creating and Attaching an Internet Gateway](VPC_Internet_Gateway.md#Add_IGW_Attach_Gateway)\)

   1. Create a custom route table that sends traffic destined outside the VPC to the Internet gateway, and then associate it with one subnet, making it a public subnet \(see [Creating a Custom Route Table](VPC_Internet_Gateway.md#Add_IGW_Routing)\)

1. Create the NATSG security group \(see [Creating the NATSG Security Group](#NATSG)\)\. You'll specify this security group when you launch the NAT instance\.

1. Launch an instance into your public subnet from an AMI that's been configured to run as a NAT instance\. Amazon provides Amazon Linux AMIs that are configured to run as NAT instances\. These AMIs include the string `amzn-ami-vpc-nat` in their names, so you can search for them in the Amazon EC2 console\.

   1. Open the Amazon EC2 console\.

   1. On the dashboard, choose the **Launch Instance** button, and complete the wizard as follows:

      1. On the **Choose an Amazon Machine Image \(AMI\)** page, select the **Community AMIs** category, and search for `amzn-ami-vpc-nat`\. In the results list, each AMI's name includes the version to enable you to select the most recent AMI, for example, `2013.09`\. Choose **Select**\.

      1. On the **Choose an Instance Type** page, select the instance type, then choose **Next: Configure Instance Details**\.

      1. On the **Configure Instance Details** page, select the VPC you created from the **Network** list, and select your public subnet from the **Subnet** list\. 

      1. \(Optional\) Select the **Public IP** check box to request that your NAT instance receives a public IP address\. If you choose not to assign a public IP address now, you can allocate an Elastic IP address and assign it to your instance after it's launched\. For more information about assigning a public IP at launch, see [Assigning a Public IPv4 Address During Instance Launch](vpc-ip-addressing.md#vpc-public-ip)\. Choose **Next: Add Storage**\.

      1. You can choose to add storage to your instance, and on the next page, you can add tags\. Choose **Next: Configure Security Group** when you are done\. 

      1. On the **Configure Security Group** page, select the **Select an existing security group** option, and select the NATSG security group that you created\. Choose **Review and Launch**\.

      1. Review the settings that you've chosen\. Make any changes that you need, and then choose **Launch** to choose a key pair and launch your instance\.

1. \(Optional\) Connect to the NAT instance, make any modifications that you need, and then create your own AMI that's configured to run as a NAT instance\. You can use this AMI the next time that you need to launch a NAT instance\. For more information about creating an AMI, see [Creating Amazon EBS\-Backed AMIs](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Disable the `SrcDestCheck` attribute for the NAT instance \(see [Disabling Source/Destination Checks](#EIP_Disable_SrcDestCheck)\)

1. If you did not assign a public IP address to your NAT instance during launch \(step 3\), you need to associate an Elastic IP address with it\.

   1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

   1. In the navigation pane, choose **Elastic IPs**, and then choose **Allocate new address**\.

   1. Choose **Allocate**\.

   1. Select the Elastic IP address from the list, and then choose **Actions**, **Associate address**\.

   1. Select the network interface resource, then select the network interface for the NAT instance\. Select the address to associate the Elastic IP with from the **Private IP** list, and then choose **Associate**\.

1. Update the main route table to send traffic to the NAT instance\. For more information, see [Updating the Main Route Table](#nat-routing-table)\.

### Launching a NAT Instance Using the Command Line<a name="launch-nat-instance-cli"></a>

To launch a NAT instance into your subnet, use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon VPC](VPC_Introduction.md#VPCInterfaces)\.
+ [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)
+ [New\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

To get the ID of an AMI that's configured to run as a NAT instance, use a command to describe images, and use filters to return results only for AMIs that are owned by Amazon, and that have the `amzn-ami-vpc-nat` string in their names\. The following example uses the AWS CLI:

```
aws ec2 describe-images --filter Name="owner-alias",Values="amazon" --filter Name="name",Values="amzn-ami-vpc-nat*" 
```

## Creating the NATSG Security Group<a name="NATSG"></a>

Define the NATSG security group as described in the following table to enable your NAT instance to receive Internet\-bound traffic from instances in a private subnet, as well as SSH traffic from your network\. The NAT instance can also send traffic to the Internet, which enables the instances in the private subnet to get software updates\. 


**NATSG: Recommended Rules**  

|  | 
| --- |
| Inbound | 
|  Source  |  Protocol  |  Port Range  |  Comments  | 
|  `10.0.1.0/24`  |  TCP  |  80  |  Allow inbound HTTP traffic from servers in the private subnet  | 
|  `10.0.1.0/24`  |  TCP  |  443  |  Allow inbound HTTPS traffic from servers in the private subnet  | 
|  Public IP address range of your home network   |  TCP  |  22  |  Allow inbound SSH access to the NAT instance from your home network \(over the Internet gateway\)  | 
|   `Outbound`   | 
|  Destination  |  Protocol  |  Port Range  |  Comments  | 
|  `0.0.0.0/0`  |  TCP  |  80  |  Allow outbound HTTP access to the Internet  | 
|  `0.0.0.0/0`  |  TCP  |  443  |  Allow outbound HTTPS access to the Internet  | 

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

For more information about security groups, see [Security Groups for Your VPC](VPC_SecurityGroups.md)\.

## Disabling Source/Destination Checks<a name="EIP_Disable_SrcDestCheck"></a>

Each EC2 instance performs source/destination checks by default\. This means that the instance must be the source or destination of any traffic it sends or receives\. However, a NAT instance must be able to send and receive traffic when the source or destination is not itself\. Therefore, you must disable source/destination checks on the NAT instance\.

You can disable the `SrcDestCheck` attribute for a NAT instance that's either running or stopped using the console or the command line\.

**To disable source/destination checking using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the NAT instance, choose **Actions**, **Networking**, **Change Source/Dest\. Check**\.

1. For the NAT instance, verify that this attribute is disabled\. Otherwise, choose **Yes, Disable**\.

1. If the NAT instance has a secondary network interface, choose it from **Network interfaces** on the **Description** tab and choose the interface ID to go to the network interfaces page\. Choose **Actions**, **Change Source/Dest\. Check**, disable the setting, and choose **Save**\.

**To disable source/destination checking using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon VPC](VPC_Introduction.md#VPCInterfaces)\.
+ [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Updating the Main Route Table<a name="nat-routing-table"></a>

The private subnet in your VPC is not associated with a custom route table, therefore it uses the main route table\. By default, the main route table enables the instances in your VPC to communicate with each other\. You must add route that sends all other subnet traffic to the NAT instance\.

**To update the main route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the main route table for your VPC \(the **Main** column displays **Yes**\) \. The details pane displays tabs for working with its routes, associations, and route propagation\.

1. On the **Routes** tab, choose **Edit**, specify `0.0.0.0/0` in the **Destination** box, select the instance ID of the NAT instance from the **Target** list, and then choose **Save**\. 

1. On the **Subnet Associations** tab, choose **Edit**, and then select the **Associate** check box for the subnet\. Choose **Save**\.

For more information about route tables, see [Route Tables](VPC_Route_Tables.md)\.

## Testing Your NAT Instance Configuration<a name="nat-test-configuration"></a>

After you have launched a NAT instance and completed the configuration steps above, you can perform a test to check if an instance in your private subnet can access the Internet through the NAT instance by using the NAT instance as a bastion server\. To do this, update your NAT instance's security group rules to allow inbound and outbound ICMP traffic and allow outbound SSH traffic, launch an instance into your private subnet, configure SSH agent forwarding to access instances in your private subnet, connect to your instance, and then test the Internet connectivity\.

**To update your NAT instance's security group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\. 

1. Find the security group associated with your NAT instance, and choose **Edit** in the **Inbound** tab\.

1. Choose **Add Rule**, select **All ICMP \- IPv4** from the **Type** list, and select **Custom** from the **Source** list\. Enter the IP address range of your private subnet, for example, `10.0.1.0/24`\. Choose **Save**\.

1. In the **Outbound** tab, choose **Edit**\.

1. Choose **Add Rule**, select **SSH** from the **Type** list, and select **Custom** from the **Destination** list\. Enter the IP address range of your private subnet, for example, `10.0.1.0/24`\. Choose **Save**\.

1. Choose **Add Rule**, select **All ICMP \- IPv4** from the **Type** list, and select **Custom** from the **Destination** list\. Enter `0.0.0.0/0`, and then choose **Save**\.

**To launch an instance into your private subnet**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Launch an instance into your private subnet\. For more information, see [Launching an Instance into Your Subnet](working-with-vpcs.md#VPC_Launch_Instance)\. Ensure that you configure the following options in the launch wizard, and then choose **Launch**:
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

1. Convert your private key to \.ppk format\. For more information, see [Converting Your Private Key Using PuTTYgen](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-private-key)\.

1. Start Pageant, right\-click the Pageant icon on the taskbar \(it may be hidden\), and choose **Add Key**\. Select the \.ppk file you created, enter the passphrase if required, and choose **Open**\.

1. Start a PuTTY session to connect to your NAT instance\. In the **Auth** category, ensure that you select the **Allow agent forwarding** option, and leave the **Private key file for authentication** field blank\.

**To test the Internet connection**

1. Test that your NAT instance can communicate with the Internet by running the `ping` command for a website that has ICMP enabled; for example:

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

1. From your private instance, test that you can connect to the Internet by running the `ping` command:

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
   + Check that you've configured your route tables correctly\. For more information, see [Updating the Main Route Table](#nat-routing-table)\.
   + Ensure that you've disabled source/destination checking for your NAT instance\. For more information, see [Disabling Source/Destination Checks](#EIP_Disable_SrcDestCheck)\.
   + Ensure that you are pinging a website that has ICMP enabled\. If not, you will not receive reply packets\. To test this, perform the same `ping` command from the command line terminal on your own computer\. 

1. \(Optional\) Terminate your private instance if you no longer require it\. For more information, see [Terminate Your Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.