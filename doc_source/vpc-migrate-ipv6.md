# Migrating to IPv6<a name="vpc-migrate-ipv6"></a>

If you have an existing VPC that supports IPv4 only, and resources in your subnet that are configured to use IPv4 only, you can enable IPv6 support for your VPC and resources\. Your VPC can operate in dual\-stack mode — your resources can communicate over IPv4, or IPv6, or both\. IPv4 and IPv6 communication are independent of each other\.

You cannot disable IPv4 support for your VPC and subnets; this is the default IP addressing system for Amazon VPC and Amazon EC2\.

**Note**  
This information assumes that you have an existing VPC with public and private subnets\. For information about setting up a new VPC for use with IPv6, see [Overview for IPv6](VPC_Scenario1.md#vpc-scenario-1-overview-ipv6)\.

The following table provides an overview of the steps to enable your VPC and subnets to use IPv6\.


| Step | Notes | 
| --- | --- | 
| [Step 1: Associate an IPv6 CIDR block with your VPC and subnets](#vpc-migrate-ipv6-cidr) | Associate an Amazon\-provided IPv6 CIDR block with your VPC and with your subnets\. | 
| [Step 2: Update your route tables](#vpc-migrate-ipv6-routes) | Update your route tables to route your IPv6 traffic\. For a public subnet, create a route that routes all IPv6 traffic from the subnet to the internet gateway\. For a private subnet, create a route that routes all internet\-bound IPv6 traffic from the subnet to an egress\-only internet gateway\. | 
| [Step 3: Update your security group rules](#vpc-migrate-ipv6-sg-rules) | Update your security group rules to include rules for IPv6 addresses\. This enables IPv6 traffic to flow to and from your instances\. If you've created custom network ACL rules to control the flow of traffic to and from your subnet, you must include rules for IPv6 traffic\. | 
| [Step 4: Change your instance type](#vpc-migrate-ipv6-instance-types) | If your instance type does not support IPv6, change the instance type\. | 
| [Step 5: Assign IPv6 addresses to your instances](#vpc-migrate-assign-ipv6-address) | Assign IPv6 addresses to your instances from the IPv6 address range of your subnet\. | 
| [Step 6: \(Optional\) Configure IPv6 on your instances](#vpc-migrate-ipv6-dhcpv6) | If your instance was launched from an AMI that is not configured to use DHCPv6, you must manually configure your instance to recognize an IPv6 address assigned to the instance\. | 

Before you migrate to using IPv6, ensure that you have read the features of IPv6 addressing for Amazon VPC: [IPv4 and IPv6 characteristics and restrictions](vpc-ip-addressing.md#vpc-ipv4-ipv6-comparison)\.

**Topics**
+ [Example: Enabling IPv6 in a VPC with a public and private subnet](#vpc-migrate-ipv6-example)
+ [Step 1: Associate an IPv6 CIDR block with your VPC and subnets](#vpc-migrate-ipv6-cidr)
+ [Step 2: Update your route tables](#vpc-migrate-ipv6-routes)
+ [Step 3: Update your security group rules](#vpc-migrate-ipv6-sg-rules)
+ [Step 4: Change your instance type](#vpc-migrate-ipv6-instance-types)
+ [Step 5: Assign IPv6 addresses to your instances](#vpc-migrate-assign-ipv6-address)
+ [Step 6: \(Optional\) Configure IPv6 on your instances](#vpc-migrate-ipv6-dhcpv6)

## Example: Enabling IPv6 in a VPC with a public and private subnet<a name="vpc-migrate-ipv6-example"></a>

In this example, your VPC has a public and a private subnet\. You have a database instance in your private subnet that has outbound communication with the internet through a NAT gateway in your VPC\. You have a public\-facing web server in your public subnet that has internet access through the internet gateway\. The following diagram represents the architecture of your VPC\.

![\[VPC with public and private subnets\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-migrate-ipv6-example-diagram.png)

The security group for your web server \(`sg-11aa22bb11aa22bb1`\) has the following inbound rules:


| **Type** | **Protocol** | **Port range** | **Source** | **Comment** | 
| --- | --- | --- | --- | --- | 
| All traffic | All | All | sg\-33cc44dd33cc44dd3 | Allows inbound access for all traffic from instances associated with sg\-33cc44dd33cc44dd3 \(the database instance\)\. | 
| HTTP | TCP | 80 | 0\.0\.0\.0/0 | Allows inbound traffic from the internet over HTTP\. | 
| HTTPS | TCP | 443 | 0\.0\.0\.0/0 | Allows inbound traffic from the internet over HTTPS\. | 
| SSH | TCP | 22 | 203\.0\.113\.123/32 | Allows inbound SSH access from your local computer; for example, when you need to connect to your instance to perform administration tasks\. | 

The security group for your database instance \(`sg-33cc44dd33cc44dd3`\) has the following inbound rule:


| **Type** | **Protocol** | **Port range** | **Source** | **Comment** | 
| --- | --- | --- | --- | --- | 
| MySQL | TCP | 3306 | sg\-11aa22bb11aa22bb1 | Allows inbound access for MySQL traffic from instances associated with sg\-11aa22bb11aa22bb1 \(the web server instance\)\. | 

Both security groups have the default outbound rule that allows all outbound IPv4 traffic, and no other outbound rules\.

Your web server is `t2.medium` instance type\. Your database server is an `m3.large`\.

You want your VPC and resources to be enabled for IPv6, and you want them to operate in dual\-stack mode; in other words, you want to use both IPv6 and IPv4 addressing between resources in your VPC and resources over the internet\.

After you've completed the steps, your VPC will have the following configuration\.

![\[VPC with public and private subnets\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-migrate-ipv6-example-2-diagram.png)

## Step 1: Associate an IPv6 CIDR block with your VPC and subnets<a name="vpc-migrate-ipv6-cidr"></a>

You can associate an IPv6 CIDR block with your VPC, and then associate a `/64` CIDR block from that range with each subnet\.

**To associate an IPv6 CIDR block with a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, choose **Actions**, **Edit CIDRs**\.

1. Choose **Add IPv6 CIDR**, choose one of the following options, and then choose **Select CIDR**:
   + **Amazon\-provided IPv6 CIDR block**: Requests an IPv6 CIDR block from Amazon's pool of IPv6 addresses\. For **Network Border Group**, select the group from which AWS advertises IP addresses\. 
   + **IPv6 CIDR owned by me**: \([BYOIP](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-byoip.html)\) Allocates an IPv6 CIDR block from your IPv6 address pool\. For **Pool,** choose the IPv6 address pool from which to allocate the IPv6 CIDR block\.

**To associate an IPv6 CIDR block with a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet, choose **Subnet Actions**, **Edit IPv6 CIDRs**\.

1. Choose **Add IPv6 CIDR**\. Specify the hexadecimal pair for the subnet \(for example, `00`\) and confirm the entry by choosing the tick icon\.

1. Choose **Close**\. Repeat the steps for the other subnets in your VPC\.

For more information, see [VPC and subnet sizing for IPv6](VPC_Subnets.md#vpc-sizing-ipv6)\.

## Step 2: Update your route tables<a name="vpc-migrate-ipv6-routes"></a>

For a public subnet, you must update the route table to enable instances \(such as web servers\) to use the internet gateway for IPv6 traffic\.

For a private subnet, you must update the route table to enable instances \(such as database instances\) to use an egress\-only internet gateway for IPv6 traffic\.

**To update your route table for a public subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables** and select the route table that's associated with the public subnet\.

1. On the **Routes** tab, choose **Edit**\.

1. Choose **Add another route**\. Specify `::/0` for **Destination**, select the ID of the internet gateway for **Target**, and then choose **Save**\. 

**To update your route table for a private subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. If you're using a NAT device in your private subnet, it does not support IPv6 traffic\. Instead, create an egress\-only internet gateway for your private subnet to enable outbound communication to the internet over IPv6 and prevent inbound communication\. An egress\-only internet gateway supports IPv6 traffic only\. For more information, see [Egress\-only internet gateways](egress-only-internet-gateway.md)\.

1. In the navigation pane, choose **Route Tables** and select the route table that's associated with the private subnet\.

1. On the **Routes** tab, choose **Edit**\.

1. Choose **Add another route**\. For **Destination**, specify `::/0`\. For **Target**, select the ID of the egress\-only internet gateway, and then choose **Save**\.

For more information, see [Example routing options](route-table-options.md)\.

## Step 3: Update your security group rules<a name="vpc-migrate-ipv6-sg-rules"></a>

To enable your instances to send and receive traffic over IPv6, you must update your security group rules to include rules for IPv6 addresses\.

For example, in the example above, you can update the web server security group \(`sg-11aa22bb11aa22bb1`\) to add rules that allow inbound HTTP, HTTPS, and SSH access from IPv6 addresses\. You do not need to make any changes to the inbound rules for your database security group; the rule that allows all communication from `sg-11aa22bb11aa22bb1` includes IPv6 communication by default\.

**To update your security group rules**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups** and select your web server security group\.

1. In the **Inbound Rules** tab, choose **Edit**\.

1. For each rule, choose **Add another rule**, and choose **Save** when you're done\. For example, to add a rule that allows all HTTP traffic over IPv6, for **Type**, select **HTTP** and for **Source**, enter `::/0`\.

By default, an outbound rule that allows all IPv6 traffic is automatically added your security groups when you associate an IPv6 CIDR block with your VPC\. However, if you modified the original outbound rules for your security group, this rule is not automatically added, and you must add equivalent outbound rules for IPv6 traffic\. For more information, see [Security groups for your VPC](VPC_SecurityGroups.md)\.

### Update your network ACL rules<a name="vpc-migrate-ipv6-nacl-rules"></a>

If you associate an IPv6 CIDR block with your VPC, we automatically add rules to the default network ACL to allow IPv6 traffic, provided you haven't modified its default rules\. If you've modified your default network ACL or if you've created a custom network ACL with rules to control the flow of traffic to and from your subnet, you must manually add rules for IPv6 traffic\. For more information, see [Network ACLs](vpc-network-acls.md)\.

## Step 4: Change your instance type<a name="vpc-migrate-ipv6-instance-types"></a>

All current generation instance types support IPv6\. For more information, see [Instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html)\.

If your instance type does not support IPv6, you must resize the instance to a supported instance type\. In the example above, the database instance is an `m3.large` instance type, which does not support IPv6\. You must resize the instance to a supported instance type, for example, `m4.large`\. 

To resize your instance, be aware of the compatibility limitations\. For more information, see [Compatibility for resizing instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-resize.html#resize-limitations) in the *Amazon EC2 User Guide for Linux Instances*\. In this scenario, if your database instance was launched from an AMI that uses HVM virtualization, you can resize it to an `m4.large` instance type by using the following procedure\.

**Important**  
To resize your instance, you must stop it\. Stopping and starting an instance changes the public IPv4 address for the instance, if it has one\. If you have any data stored on instance store volumes, the data is erased\. 

**To resize your instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select the database instance\.

1. Choose **Actions**, **Instance State**, **Stop**\.

1. In the confirmation dialog box, choose **Yes, Stop**\.

1. With the instance still selected, choose **Actions**, **Instance Settings**, **Change Instance Type**\.

1. For **Instance Type**, choose the new instance type, and then choose **Apply**\.

1. To restart the stopped instance, select the instance and choose **Actions**, **Instance State**, **Start**\. In the confirmation dialog box, choose **Yes, Start**\.

If your instance is an instance store\-backed AMI, you can't resize your instance using the earlier procedure\. Instead, you can create an instance store\-backed AMI from your instance, and launch a new instance from your AMI using a new instance type\. For more information, see [Creating an instance store\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-instance-store.html) in the *Amazon EC2 User Guide for Linux Instances*, and [Creating an instance store\-backed Windows AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_InstanceStoreBacked_WinAMI.html) in the *Amazon EC2 User Guide for Windows Instances*\.

You may not be able to migrate to a new instance type if there are compatibility limitations\. For example, if your instance was launched from an AMI that uses PV virtualization, the only instance type that supports both PV virtualization and IPv6 is C3\. This instance type may not be suitable for your needs\. In this case, you may have to reinstall your software on a base HVM AMI, and launch a new instance\. 

If you launch an instance from a new AMI, you can assign an IPv6 address to your instance during launch\. 

## Step 5: Assign IPv6 addresses to your instances<a name="vpc-migrate-assign-ipv6-address"></a>

After you've verified that your instance type supports IPv6, you can assign an IPv6 address to your instance using the Amazon EC2 console\. The IPv6 address is assigned to the primary network interface \(eth0\) for the instance\.

**To assign an IPv6 address to your instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, and choose **Actions**, **Networking**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Assign new IP**\. You can enter a specific IPv6 address from the range of your subnet, or you can leave the default `Auto-Assign` value to let Amazon choose one for you\. 

1. Choose **Yes, Update**\.

Alternatively, if you launch a new instance \(for example, if you were unable to change the instance type and you created a new AMI instead\), you can assign an IPv6 address during launch\.

**To assign an IPv6 address to an instance during launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Select your AMI and an IPv6\-compatible instance type, and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select a VPC for **Network** and a subnet for **Subnet**\. For **Auto\-assign IPv6 IP**, select **Enable**\.

1. Follow the remaining steps in the wizard to launch your instance\.

You can connect to an instance using its IPv6 address\. If you're connecting from a local computer, ensure that your local computer has an IPv6 address and is configured to use IPv6\. For more information, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances* and [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Step 6: \(Optional\) Configure IPv6 on your instances<a name="vpc-migrate-ipv6-dhcpv6"></a>

If you launched your instance using Amazon Linux 2016\.09\.0 or later, or Windows Server 2008 R2 or later, your instance is configured for IPv6 and no additional steps are required\. 

If you launched your instance from a different AMI, it may not be configured for DHCPv6, which means that any IPv6 address that you assign to the instance is not automatically recognized on the primary network interface\. To verify if the IPv6 address is configured on your network interface, use the `ifconfig` command on Linux, or the `ipconfig` command on Windows\.

You can configure your instance using the following steps\. You'll need to connect to your instance using its public IPv4 address\. For more information, see [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances* and [Connecting to your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [Amazon Linux](#ipv6-dhcpv6-amazon-linux)
+ [Ubuntu](#ipv6-dhcpv6-ubuntu)
+ [RHEL/CentOS](#ipv6-dhcpv6-rhel)
+ [Windows](#ipv6-dhcpv6-windows)

### Amazon Linux<a name="ipv6-dhcpv6-amazon-linux"></a>

**To configure DHCPv6 on Amazon Linux**

1. Connect to your instance using the instance's public IPv4 address\. 

1. Get the latest software packages for your instance:

   ```
   sudo yum update -y
   ```

1. Using a text editor of your choice, open `/etc/sysconfig/network-scripts/ifcfg-eth0` and locate the following line:

   ```
   IPV6INIT=no
   ```

   Replace that line with the following:

   ```
   IPV6INIT=yes
   ```

   Add the following two lines, and save your changes:

   ```
   DHCPV6C=yes
   DHCPV6C_OPTIONS=-nw
   ```

1. Open `/etc/sysconfig/network`, remove the following lines, and save your changes:

   ```
   NETWORKING_IPV6=no
   IPV6INIT=no
   IPV6_ROUTER=no
   IPV6_AUTOCONF=no
   IPV6FORWARDING=no
   IPV6TO4INIT=no
   IPV6_CONTROL_RADVD=no
   ```

1. Open `/etc/hosts`, replace the contents with the following, and save your changes:

   ```
   127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
   ::1         localhost6 localhost6.localdomain6
   ```

1. Reboot your instance\. Reconnect to your instance and use the `ifconfig` command to verify that the IPv6 address is recognized on the primary network interface\.

### Ubuntu<a name="ipv6-dhcpv6-ubuntu"></a>

You can configure your Ubuntu instance to dynamically recognize any IPv6 address assigned to the network interface\. If your instance does not have an IPv6 address, this configuration may cause the boot time of your instance to be extended by up to 5 minutes\.

These steps must be performed as the root user\.

#### Ubuntu Server 16<a name="ipv6-dhcpv6-ubuntu-16"></a>

**To configure IPv6 on a running Ubuntu Server 16 instance**

1. Connect to your instance using the instance's public IPv4 address\.

1. View the contents of the `/etc/network/interfaces.d/50-cloud-init.cfg` file:

   ```
   cat /etc/network/interfaces.d/50-cloud-init.cfg
   ```

   ```
   # This file is generated from information provided by
   # the datasource.  Changes to it will not persist across an instance.
   # To disable cloud-init's network configuration capabilities, write a file
   # /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
   # network: {config: disabled}
   auto lo
   iface lo inet loopback
   
   auto eth0
   iface eth0 inet dhcp
   ```

   Verify that the loopback network device \(`lo`\) is configured, and take note of the name of the network interface\. In this example, the network interface name is `eth0`; the name may be different depending on the instance type\.

1. Create the file `/etc/network/interfaces.d/60-default-with-ipv6.cfg` and add the following line\. If required, replace `eth0` with the name of the network interface that you retrieved in the step above\.

   ```
   iface eth0 inet6 dhcp
   ```

1. Reboot your instance, or restart the network interface by running the following command\. If required, replace `eth0` with the name of your network interface\.

   ```
   sudo ifdown eth0 ; sudo ifup eth0
   ```

1. Reconnect to your instance and use the `ifconfig` command to verify that the IPv6 address is configured on the network interface\.

**To configure IPv6 using user data**
+ You can launch a new Ubuntu instance and ensure that any IPv6 address assigned to the instance is automatically configured on the network interface by specifying the following user data during launch:

  ```
  #!/bin/bash
  echo "iface eth0 inet6 dhcp" >> /etc/network/interfaces.d/60-default-with-ipv6.cfg
  dhclient -6
  ```

  In this case, you do not have to connect to the instance to configure the IPv6 address\. 

  For more information, see [Running Commands on Your Linux Instance at Launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html) in the *Amazon EC2 User Guide for Linux Instances*\.

#### Ubuntu Server 14<a name="ipv6-dhcpv6-ubuntu-14"></a>

If you're using Ubuntu Server 14, you must include a workaround for a [known issue](https://bugs.launchpad.net/ubuntu/+source/ifupdown/+bug/1013597) that occurs when restarting a dual\-stack network interface \(the restart results in an extended timeout during which your instance is unreachable\)\.

These steps must be performed as the root user\.

**To configure IPv6 on a running Ubuntu Server 14 instance**

1. Connect to your instance using the instance's public IPv4 address\.

1. Edit the `/etc/network/interfaces.d/eth0.cfg` file so that it contains the following:

   ```
   auto lo
   iface lo inet loopback
   auto eth0
   iface eth0 inet dhcp
       up dhclient -6 $IFACE
   ```

1. Reboot your instance:

   ```
   sudo reboot
   ```

1. Reconnect to your instance and use the `ifconfig` command to verify that the IPv6 address is configured on the network interface\.

#### Starting the DHCPv6 client<a name="ipv6-dhcpv6-ubuntu-start-client"></a>

Alternatively, to bring up the IPv6 address for the network interface immediately without performing any additional configuration, you can start the DHCPv6 client for the instance\. However, the IPv6 address does not persist on the network interface after reboot\.

**To start the DHCPv6 client on Ubuntu**

1. Connect to your instance using the instance's public IPv4 address\.

1. Start the DHCPv6 client:

   ```
   sudo dhclient -6
   ```

1. Use the `ifconfig` command to verify that the IPv6 address is recognized on the primary network interface\.

### RHEL/CentOS<a name="ipv6-dhcpv6-rhel"></a>

RHEL 7\.4 and CentOS 7 and later use [cloud\-init](http://cloudinit.readthedocs.io/en/latest/index.html) to configure your network interface and generate the `/etc/sysconfig/network-scripts/ifcfg-eth0` file\. You can create a custom `cloud-init` configuration file to enable DHCPv6, which generates an `ifcfg-eth0` file with settings that enable DHCPv6 after each reboot\.

**Note**  
Due to a known issue, if you're using RHEL/CentOS 7\.4 with the latest version of cloud\-init\-0\.7\.9, these steps might result in you losing connectivity to your instance after reboot\. As a workaround, you can manually edit the `/etc/sysconfig/network-scripts/ifcfg-eth0` file\.

**To configure DHCPv6 on RHEL 7\.4 or CentOS 7**

1. Connect to your instance using the instance's public IPv4 address\. 

1. Using a text editor of your choice, create a custom file, for example: 

   ```
   /etc/cloud/cloud.cfg.d/99-custom-networking.cfg
   ```

1. Add the following lines to your file, and save your changes:

   ```
   network:
     version: 1
     config:
     - type: physical
       name: eth0
       subnets:
         - type: dhcp
         - type: dhcp6
   ```

1. Using a text editor of your choice, add the following line to the interface\-specific file under `/etc/sysctl.d`\. If you disabled Consistent Network Device Naming, the network\-interface\-name is `ethX`, or the secondary interface\.

   ```
   net.ipv6.conf.network-interface-name.accept_ra=1
   ```

   In the following example, the network interface is `en5`\.

   ```
   net.ipv6.conf.en5.accept_ra=1
   ```

1. Reboot your instance\.

1. Reconnect to your instance and use the `ifconfig` command to verify that the IPv6 address is configured on the network interface\.

For RHEL versions 7\.3 and earlier, you can use the following procedure to modify the `/etc/sysconfig/network-scripts/ifcfg-eth0` file directly\.

**To configure DHCPv6 on RHEL 7\.3 and earlier**

1. Connect to your instance using the instance's public IPv4 address\. 

1. Using a text editor of your choice, open `/etc/sysconfig/network-scripts/ifcfg-eth0` and locate the following line:

   ```
   IPV6INIT="no"
   ```

   Replace that line with the following:

   ```
   IPV6INIT="yes"
   ```

   Add the following two lines, and save your changes:

   ```
   DHCPV6C=yes
   NM_CONTROLLED=no
   ```

1. Open `/etc/sysconfig/network`, add or amend the following line as follows, and save your changes:

   ```
   NETWORKING_IPV6=yes
   ```

1. Restart networking on your instance by running the following command:

   ```
   sudo service network restart
   ```

   You can use the `ifconfig` command to verify that the IPv6 address is recognized on the primary network interface\.

**To configure DHCPv6 on RHEL 6 or CentOS 6**

1. Connect to your instance using the instance's public IPv4 address\. 

1. Follow steps 2 \- 4 in the procedure above for configuring RHEL 7/CentOS 7\.

1. If you restart networking and you get an error that an IPv6 address cannot be obtained, open `/etc/sysconfig/network-scripts/ifup-eth` and locate the following line \(by default, it's line 327\):

   ```
   if /sbin/dhclient "$DHCLIENTARGS"; then
   ```

   Remove the quotes that surround `$DHCLIENTARGS` and save your changes\. Restart networking on your instance:

   ```
   sudo service network restart
   ```

### Windows<a name="ipv6-dhcpv6-windows"></a>

Use the following procedures to configure IPv6 on Windows Server 2003 and Windows Server 2008 SP2\.

To ensure that IPv6 is preferred over IPv4, download the fix named **Prefer IPv6 over IPv4 in prefix policies** from the following Microsoft support page: [https://support\.microsoft\.com/en\-us/help/929852/how\-to\-disable\-ipv6\-or\-its\-components\-in\-windows](https://support.microsoft.com/en-us/help/929852/how-to-disable-ipv6-or-its-components-in-windows)\.

**To enable and configure IPv6 on Windows Server 2003**

1. Get the IPv6 address of your instance by using the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) AWS CLI command, or by checking the **IPv6 IPs** field for the instance in the Amazon EC2 console\.

1. Connect to your instance using the instance's public IPv4 address\. 

1. From within your instance, choose **Start**, **Control Panel**, **Network Connections**, **Local Area Connection**\.

1. Choose **Properties**, and then choose **Install**\. 

1. Choose **Protocol**, and choose **Add**\. In the **Network Protocol** list, choose **Microsoft TCP/IP version 6**, and then choose **OK**\.

1. Open the command prompt and open the network shell\.

   ```
   netsh
   ```

1. Switch to the interface IPv6 context\.

   ```
   interface ipv6
   ```

1. Add the IPv6 address to the local area connection using the following command\. Replace the value for the IPv6 address with the IPv6 address for your instance\.

   ```
   add address "Local Area Connection" "ipv6-address"
   ```

   For example:

   ```
   add address "Local Area Connection" "2001:db8:1234:1a00:1a01:2b:12:d08b"
   ```

1. Exit the network shell\.

   ```
   exit
   ```

1. Use the `ipconfig` command to verify that the IPv6 address is recognized for the Local Area Connection\.

**To enable and configure IPv6 on Windows Server 2008 SP2**

1. Get the IPv6 address of your instance by using the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) AWS CLI command, or by checking the **IPv6 IPs** field for the instance in the Amazon EC2 console\.

1. Connect to your Windows instance using the instance's public IPv4 address\.

1. Choose **Start**, **Control Panel**\.

1. Open the **Network and Sharing Center**, then open **Network Connections**\.

1. Right\-click **Local Area Network** \(for the network interface\) and choose **Properties**\.

1. Choose the **Internet Protocol Version 6 \(TCP/IPv6\)** check box, and choose **OK**\.

1. Open the properties dialog box for Local Area Network again\. Choose **Internet Protocol Version 6 \(TCP/IPv6\)** and choose **Properties**\.

1. Choose **Use the following IPv6 address** and do the following:
   + For **IPv6 Address**, enter the IPv6 address you obtained in step 1\.
   + For **Subnet prefix length**, enter `64`\. 

1. Choose **OK** and close the properties dialog box\.

1. Open the command prompt\. Use the `ipconfig` command to verify that the IPv6 address is recognized for the Local Area Connection\.