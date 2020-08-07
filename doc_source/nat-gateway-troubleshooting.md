# Troubleshooting NAT gateways<a name="nat-gateway-troubleshooting"></a>

The following topics help you to troubleshoot common issues that you might encounter when creating or using a NAT gateway\.

**Topics**
+ [NAT gateway creation fails](#nat-gateway-troubleshooting-failed)
+ [Elastic IP address and NAT gateway quotas](#nat-gateway-troubleshooting-limits)
+ [Availability Zone is unsupported](#nat-gateway-troubleshooting-unsupported-az)
+ [NAT gateway Is no longer visible](#nat-gateway-troubleshooting-gateway-removed)
+ [NAT gateway doesn't respond to a ping command](#nat-gateway-troubleshooting-ping)
+ [Instances cannot access the internet](#nat-gateway-troubleshooting-no-internet-connection)
+ [TCP connection to a destination fails](#nat-gateway-troubleshooting-tcp-issues)
+ [Traceroute output does not display NAT gateway private IP address](#nat-gateway-troubleshooting-traceroute)
+ [Internet connection drops after 350 seconds](#nat-gateway-troubleshooting-timeout)
+ [IPsec connection cannot be established](#nat-gateway-troubleshooting-ipsec)
+ [Cannot initiate more connections](#nat-gateway-troubleshooting-simultaneous-connections)

## NAT gateway creation fails<a name="nat-gateway-troubleshooting-failed"></a>

**Problem**  
You create a NAT gateway and it goes to a state of `Failed`\. 

**Cause**  
There was an error when the NAT gateway was created\. The returned state message provides the reason for the error\.

**Solution**  
To view the error message, go to the Amazon VPC console, and then choose **NAT Gateways**\. Select your NAT gateway, and then view the error message in the **Status message** field in the details pane\. 

The following table lists the possible causes of the failure as indicated in the Amazon VPC console\. After you've applied any of the remedial steps indicated, you can try to create a NAT gateway again\.

**Note**  
A failed NAT gateway is automatically deleted after a short period \(usually about an hour\)\.


| Displayed error | Cause | Solution | 
| --- | --- | --- | 
| Subnet has insufficient free addresses to create this NAT gateway | The subnet that you specified does not have any free private IP addresses\. The NAT gateway requires a network interface with a private IP address allocated from the subnet's range\.  | Check how many IP addresses are available in your subnet by going to the Subnets page in the Amazon VPC console\. You can view the Available IPs in the details pane for your subnet\. To create free IP addresses in your subnet, you can delete unused network interfaces, or terminate instances that you do not require\.  | 
| Network vpc\-xxxxxxxx has no internet gateway attached | A NAT gateway must be created in a VPC with an internet gateway\. | Create and attach an internet gateway to your VPC\. For more information, see [Creating and attaching an internet gateway](VPC_Internet_Gateway.md#Add_IGW_Attach_Gateway)\.  | 
| Elastic IP address eipalloc\-xxxxxxxx could not be associated with this NAT gateway | The Elastic IP address that you specified does not exist or could not be found\.  | Check the allocation ID of the Elastic IP address to ensure that you entered it correctly\. Ensure that you have specified an Elastic IP address that's in the same AWS Region in which you're creating the NAT gateway\. | 
| Elastic IP address eipalloc\-xxxxxxxx is already associated | The Elastic IP address that you specified is already associated with another resource, and cannot be associated with the NAT gateway\. | Check which resource is associated with the Elastic IP address\. Go to the Elastic IPs page in the Amazon VPC console, and view the values specified for the instance ID or network interface ID\. If you do not require the Elastic IP address for that resource, you can disassociate it\. Alternatively, allocate a new Elastic IP address to your account\. For more information, see [Working with Elastic IP addresses](vpc-eips.md#WorkWithEIPs)\. | 
| Network interface eni\-xxxxxxxx, created and used internally by this NAT gateway is in an invalid state\. Please try again\. | There was a problem creating or using the network interface for the NAT gateway\. | You cannot resolve this error\. Try creating a NAT gateway again\. | 

## Elastic IP address and NAT gateway quotas<a name="nat-gateway-troubleshooting-limits"></a>

**Problem**  
When you try to allocate an Elastic IP address, you get the following error\.

```
The maximum number of addresses has been reached.
```

When you try to create a NAT gateway, you get the following error\.

```
Performing this operation would exceed the limit of 5 NAT gateways
```

**Cause**  
There are 2 possible causes:
+ You've reached the quota for the number of Elastic IP addresses for your account for that Region\.
+ You've reached the quota for the number of NAT gateways for your account for that Availability Zone\.

**Solution**  
If you've reached your Elastic IP address quota, you can disassociate an Elastic IP address from another resource\. Alternatively, you can request a quota increase using the [Amazon VPC Limits form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=vpc)\. 

If you've reached your NAT gateway quota, you can do one of the following:
+ Request a quota increase using the [Amazon VPC Limits form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=vpc)\. The NAT gateway quota is enforced per Availability Zone\.
+ Check the status of your NAT gateway\. A status of `Pending`, `Available`, or `Deleting` counts against your quota\. If you've recently deleted a NAT gateway, wait a few minutes for the status to go from `Deleting` to `Deleted`\. Then try creating a new NAT gateway\.
+ If you do not need your NAT gateway in a specific Availability Zone, try creating a NAT gateway in an Availability Zone where you haven't reached your quota\. 

For more information, see [Amazon VPC quotas](amazon-vpc-limits.md)\.

## Availability Zone is unsupported<a name="nat-gateway-troubleshooting-unsupported-az"></a>

**Problem**  
When you try to create a NAT gateway, you get the following error: `NotAvailableInZone`\.

**Cause**  
You might be trying to create the NAT gateway in a constrained Availability Zone — a zone in which our ability to expand is constrained\. 

**Solution**  
We cannot support NAT gateways in these Availability Zones\. You can create a NAT gateway in another Availability Zone and use it for private subnets in the constrained zone\. You can also move your resources to an unconstrained Availability Zone so that your resources and your NAT gateway are in the same zone\.

## NAT gateway Is no longer visible<a name="nat-gateway-troubleshooting-gateway-removed"></a>

**Problem**  
You created a NAT gateway but it's no longer visible in the Amazon VPC console\.

**Cause**  
There may have been an error when your NAT gateway was being created, and it failed\. A NAT gateway with a status of `Failed` is visible in the Amazon VPC console for a short time \(usually an hour\)\. After an hour, it's automatically deleted\.

**Solution**  
Review the information in [NAT gateway creation fails](#nat-gateway-troubleshooting-failed), and try creating a new NAT gateway\.

## NAT gateway doesn't respond to a ping command<a name="nat-gateway-troubleshooting-ping"></a>

**Problem**  
When you try to ping a NAT gateway's Elastic IP address or private IP address from the internet \(for example, from your home computer\) or from an instance in your VPC, you do not get a response\. 

**Cause**  
A NAT gateway only passes traffic from an instance in a private subnet to the internet\.

**Solution**  
To test that your NAT gateway is working, see [Testing a NAT gateway](vpc-nat-gateway.md#nat-gateway-testing)\.

## Instances cannot access the internet<a name="nat-gateway-troubleshooting-no-internet-connection"></a>

**Problem**  
You created a NAT gateway and followed the steps to test it, but the `ping` command fails, or your instances in the private subnet cannot access the internet\.

**Causes**  
The cause of this problem might be one of the following: 
+ The NAT gateway is not ready to serve traffic\.
+ Your route tables are not configured correctly\.
+ Your security groups or network ACLs are blocking inbound or outbound traffic\.
+ You're using an unsupported protocol\.

**Solution**  
Check the following information:
+ Check that the NAT gateway is in the `Available` state\. In the Amazon VPC console, go to the **NAT Gateways** page and view the status information in the details pane\. If the NAT gateway is in a failed state, there may have been an error when it was created\. For more information, see [NAT gateway creation fails](#nat-gateway-troubleshooting-failed)\.
+ Check that you've configured your route tables correctly:
  + The NAT gateway must be in a public subnet with a route table that routes internet traffic to an internet gateway\. For more information, see [Creating a custom route table](VPC_Internet_Gateway.md#Add_IGW_Routing)\.
  + Your instance must be in a private subnet with a route table that routes internet traffic to the NAT gateway\. For more information, see [Updating your route table](vpc-nat-gateway.md#nat-gateway-create-route)\.
  + Check that there are no other route table entries that route all or part of the internet traffic to another device instead of the NAT gateway\.
+ Ensure that your security group rules for your private instance allow outbound internet traffic\. For the `ping` command to work, the rules must also allow outbound ICMP traffic\.
**Note**  
 The NAT gateway itself allows all outbound traffic and traffic received in response to an outbound request \(it is therefore stateful\)\.
+ Ensure that the network ACLs that are associated with the private subnet and public subnets do not have rules that block inbound or outbound internet traffic\. For the `ping` command to work, the rules must also allow inbound and outbound ICMP traffic\.
**Note**  
You can enable flow logs to help you diagnose dropped connections because of network ACL or security group rules\. For more information, see [VPC Flow Logs](flow-logs.md)\.
+ If you are using the `ping` command, ensure that you are pinging a host that has ICMP enabled\. If ICMP is not enabled, you will not receive reply packets\. To test this, perform the same `ping` command from the command line terminal on your own computer\. 
+ Check that your instance is able to ping other resources, for example, other instances in the private subnet \(assuming that security group rules allow this\)\.
+ Ensure that your connection is using a TCP, UDP, or ICMP protocol only\.

## TCP connection to a destination fails<a name="nat-gateway-troubleshooting-tcp-issues"></a>

**Problem**  
Some of your TCP connections from instances in a private subnet to a specific destination through a NAT gateway are successful, but some are failing or timing out\.

**Causes**  
The cause of this problem might be one of the following:
+ The destination endpoint is responding with fragmented TCP packets\. A NAT gateway currently does not support IP fragmentation for TCP or ICMP\. For more information, see [Comparison of NAT instances and NAT gateways](vpc-nat-comparison.md)\.
+ The `tcp_tw_recycle` option is enabled on the remote server, which is known to cause issues when there are multiple connections from behind a NAT device\.

**Solutions**  
Verify whether the endpoint to which you're trying to connect is responding with fragmented TCP packets by doing the following:

1. Use an instance in a public subnet with a public IP address to trigger a response large enough to cause fragmentation from the specific endpoint\.

1. Use the `tcpdump` utility to verify that the endpoint is sending fragmented packets\.
**Important**  
You must use an instance in a public subnet to perform these checks\. You cannot use the instance from which the original connection was failing, or an instance in a private subnet behind a NAT gateway or a NAT instance\.
**Note**  
Diagnostic tools that send or receive large ICMP packets will report packet loss\. For example, the command `ping -s 10000 example.com` does not work behind a NAT gateway\.

1. If the endpoint is sending fragmented TCP packets, you can use a NAT instance instead of a NAT gateway\.

If you have access to the remote server, you can verify whether the `tcp_tw_recycle` option is enabled by doing the following:

1. From the server, run the following command\. 

   ```
   cat /proc/sys/net/ipv4/tcp_tw_recycle
   ```

   If the output is `1`, then the `tcp_tw_recycle` option is enabled\.

1. If `tcp_tw_recycle` is enabled, we recommend disabling it\. If you need to reuse connections, `tcp_tw_reuse` is a safer option\.

If you don't have access to the remote server, you can test by temporarily disabling the `tcp_timestamps` option on an instance in the private subnet\. Then connect to the remote server again\. If the connection is successful, the cause of the previous failure is likely because `tcp_tw_recycle` is enabled on the remote server\. If possible, contact the owner of the remote server to verify if this option is enabled and request for it to be disabled\.

## Traceroute output does not display NAT gateway private IP address<a name="nat-gateway-troubleshooting-traceroute"></a>

**Problem**  
Your instance can access the internet, but when you perform the `traceroute` command, the output does not display the private IP address of the NAT gateway\. 

**Cause**  
Your instance is accessing the internet using a different gateway, such as an internet gateway\.

**Solution**  
In the route table of the subnet in which your instance is located, check the following information:
+ Ensure that there is a route that sends internet traffic to the NAT gateway\. 
+ Ensure that there isn't a more specific route that's sending internet traffic to other devices, such as a virtual private gateway or an internet gateway\. 

## Internet connection drops after 350 seconds<a name="nat-gateway-troubleshooting-timeout"></a>

**Problem**  
Your instances can access the internet, but the connection drops after 350 seconds\.

**Cause**  
If a connection that's using a NAT gateway is idle for 350 seconds or more, the connection times out\.

**Solution**  
To prevent the connection from being dropped, you can initiate more traffic over the connection\. Alternatively, you can enable TCP keepalive on the instance with a value less than 350 seconds\.

## IPsec connection cannot be established<a name="nat-gateway-troubleshooting-ipsec"></a>

**Problem**  
You cannot establish an IPsec connection to a destination\.

**Cause**  
NAT gateways currently do not support the IPsec protocol\.

**Solution**  
You can use NAT\-Traversal \(NAT\-T\) to encapsulate IPsec traffic in UDP, which is a supported protocol for NAT gateways\. Ensure that you test your NAT\-T and IPsec configuration to verify that your IPsec traffic is not dropped\.

## Cannot initiate more connections<a name="nat-gateway-troubleshooting-simultaneous-connections"></a>

**Problem**  
You have existing connections to a destination through a NAT gateway, but cannot establish more connections\.

**Cause**  
You might have reached the limit for simultaneous connections for a single NAT gateway\. For more information, see [NAT gateway rules and limitations](vpc-nat-gateway.md#nat-gateway-limits)\. If your instances in the private subnet create a large number of connections, you might reach this limit\. 

**Solution**  
Do one of the following:
+ Create a NAT gateway per Availability Zone and spread your clients across those zones\.
+ Create additional NAT gateways in the public subnet and split your clients into multiple private subnets, each with a route to a different NAT gateway\.
+ Limit the number of connections your clients can create to the destination\.
+ Use the [`IdleTimeoutCount`](vpc-nat-gateway-cloudwatch.md) metric in CloudWatch to monitor for increases in idle connections\. Close idle connections to release capacity\.