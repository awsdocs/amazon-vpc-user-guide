# Testing the VPN Connection<a name="HowToTestEndToEnd_Linux"></a>

After you create theVPN connection and configure the customer gateway, you can launch an instance and test the connection by pinging the instance\. You need to use an AMI that responds to ping requests, and you need to ensure that your instance's security group is configured to enable inbound ICMP\. We recommend you use one of the Amazon Linux AMIs\. If you are using instances running Windows Server, you'll need to log in to the instance and enable inbound ICMPv4 on the Windows firewall in order to ping the instance\.

**Important**  
You must configure any security group or network ACL in your VPC that filters traffic to the instance to allow inbound and outbound ICMP traffic\.

**To test end\-to\-end connectivity**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, choose an AMI, and then choose **Select**\.

1. Choose an instance type, and then choose **Next: Configure Instance Details**\. 

1. On the **Configure Instance Details** page, for **Network**, select your VPC\. For **Subnet**, select your subnet\. Choose **Next** until you reach the **Configure Security Group** page\.

1. Select the **Select an existing security group** option, and then select the default group that you modified earlier\. Choose **Review and Launch**\.

1. Review the settings that you've chosen\. Make any changes that you need, and then choose **Launch** to select a key pair and launch the instance\.

1. After the instance is running, get its private IP address \(for example, `10.0.0.4`\)\. The Amazon EC2 console displays the address as part of the instance's details\.

1. From a computer in your network that is behind the customer gateway, use the `ping` command with the instance's private IP address\. A successful response is similar to the following:

   ```
   ping 10.0.0.4
   ```

   ```
   Pinging 10.0.0.4 with 32 bytes of data:
   
   Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
   
   Ping statistics for 10.0.0.4:
   Packets: Sent = 3, Received = 3, Lost = 0 (0% loss),
   
   Approximate round trip times in milliseconds:
   Minimum = 0ms, Maximum = 0ms, Average = 0ms
   ```

You can now use SSH or RDP to connect to your instance in the VPC\. For more information about how to connect to a Linux instance, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#EC2_ConnectToInstance_Linux) in the *Amazon EC2 User Guide for Linux Instances*\. For more information about how to connect to a Windows instance, see [Connect to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/EC2Win_GetStarted.html#EC2Win_ConnectToInstanceWindows) in the *Amazon EC2 User Guide for Windows Instances*\. 