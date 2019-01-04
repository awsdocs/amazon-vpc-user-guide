# VPC Flow Logs<a name="flow-logs"></a>

VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC\. Flow log data can be published to Amazon CloudWatch Logs and Amazon S3\. After you've created a flow log, you can retrieve and view its data in the chosen destination\.

Flow logs can help you with a number of tasks; for example, to troubleshoot why specific traffic is not reaching an instance, which in turn helps you diagnose overly restrictive security group rules\. You can also use flow logs as a security tool to monitor the traffic that is reaching your instance\. 

CloudWatch Logs charges apply when using flow logs, whether you send them to CloudWatch Logs or to Amazon S3\. For more information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing)\.

**Topics**
+ [Flow Logs Basics](#flow-logs-basics)
+ [Flow Log Records](#flow-log-records)
+ [Flow Log Limitations](#flow-logs-limitations)
+ [Publishing Flow Logs to CloudWatch Logs](flow-logs-cwl.md)
+ [Publishing Flow Logs to Amazon S3](flow-logs-s3.md)
+ [Working With Flow Logs](working-with-flow-logs.md)
+ [Troubleshooting](flow-logs-troubleshooting.md)

## Flow Logs Basics<a name="flow-logs-basics"></a>

You can create a flow log for a VPC, a subnet, or a network interface\. If you create a flow log for a subnet or VPC, each network interface in the VPC or subnet is monitored\. 

Flow log data for a monitored network interface is recorded as *flow log records*, which are log events consisting of fields that describe the traffic flow\. For more information, see [Flow Log Records](#flow-log-records)\.

To create a flow log, you specify the resource for which to create the flow log, the type of traffic to capture \(accepted traffic, rejected traffic, or all traffic\), and the destinations to which you want to publish the flow log data\. After you've created a flow log, it can take several minutes to begin collecting and publishing data to the chosen destinations\. Flow logs do not capture real\-time log streams for your network interfaces\. For more information, see [Creating a Flow Log](working-with-flow-logs.md#create-flow-log)\. 

If you launch more instances into your subnet after you've created a flow log for your subnet or VPC, then a new log stream \(for CloudWatch Logs\) or log file object \(for Amazon S3\) is created for each new network interface as soon as any network traffic is recorded for that network interface\.

You can create flow logs for network interfaces that are created by other AWS services; for example, Elastic Load Balancing, Amazon RDS, Amazon ElastiCache, Amazon Redshift, and Amazon WorkSpaces\. However, you cannot use these service consoles or APIs to create the flow logs; you must use the Amazon EC2 console or the Amazon EC2 API\. Similarly, you cannot use the CloudWatch Logs or Amazon S3 consoles or APIs to create flow logs for your network interfaces\.

If you no longer require a flow log, you can delete it\. Deleting a flow log disables the flow log service for the resource, and no new flow log records are created or published to CloudWatch Logs or Amazon S3\. It does not delete any existing flow log records or log streams \(for CloudWatch Logs\) or log file objects \(for Amazon S3\) for a network interface\. To delete an existing log stream, use the CloudWatch Logs console\. To delete existing log file objects, use the Amazon S3 console\. After you've deleted a flow log, it can take several minutes to stop collecting data\. For more information, see [Deleting a Flow Log](working-with-flow-logs.md#delete-flow-log)\.

## Flow Log Records<a name="flow-log-records"></a>

A flow log record represents a network flow in your flow log\. Each record captures the network flow for a specific 5\-tuple, for a specific capture window\. A 5\-tuple is a set of five different values that specify the source, destination, and protocol for an internet protocol \(IP\) flow\. The capture window is a duration of time during which the flow logs service aggregates data before publishing flow log records\. The capture window is approximately 10 minutes, but can take up to 15 minutes\. 

### Flow Log Record Syntax<a name="flow-logs-records-syntax"></a>

A flow log record is a space\-separated string that has the following format:

```
<version> <account-id> <interface-id> <srcaddr> <dstaddr> <srcport> <dstport> <protocol> <packets> <bytes> <start> <end> <action> <log-status>
```

The following table describes the fields of a flow log record\.


| Field | Description | 
| --- | --- | 
| version | The VPC Flow Logs version\. | 
| account\-id | The AWS account ID for the flow log\. | 
| interface\-id | The ID of the network interface for which the traffic is recorded\. | 
| srcaddr | The source IPv4 or IPv6 address\. The IPv4 address of the network interface is always its private IPv4 address\. | 
| dstaddr | The destination IPv4 or IPv6 address\. The IPv4 address of the network interface is always its private IPv4 address\. | 
| srcport | The source port of the traffic\. | 
| dstport | The destination port of the traffic\. | 
| protocol | The IANA protocol number of the traffic\. For more information, see [ Assigned Internet Protocol Numbers](http://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)\. | 
| packets | The number of packets transferred during the capture window\. | 
| bytes | The number of bytes transferred during the capture window\. | 
| start | The time, in Unix seconds, of the start of the capture window\. | 
| end | The time, in Unix seconds, of the end of the capture window\. | 
| action | The action associated with the traffic:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) | 
| log\-status | The logging status of the flow log:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) | 

**Note**  
If a field is not applicable for a specific record, the record displays a '\-' symbol for that entry\.

### Flow Log Record Examples<a name="flow-logs-records-examples"></a>

**Flow Log Records for Accepted and Rejected Traffic**

The following is an example of a flow log record in which SSH traffic \(destination port 22, TCP protocol\) to network interface `eni-abc123de` in account `123456789010` was allowed:

```
2 123456789010 eni-abc123de 172.31.16.139 172.31.16.21 20641 22 6 20 4249 1418530010 1418530070 ACCEPT OK
```

The following is an example of a flow log record in which RDP traffic \(destination port 3389, TCP protocol\) to network interface `eni-abc123de` in account `123456789010` was rejected:

```
2 123456789010 eni-abc123de 172.31.9.69 172.31.9.12 49761 3389 6 20 4249 1418530010 1418530070 REJECT OK
```

**Flow Log Records for No Data and Skipped Records**

The following is an example of a flow log record in which no data was recorded during the capture window:

```
2 123456789010 eni-1a2b3c4d - - - - - - - 1431280876 1431280934 - NODATA
```

The following is an example of a flow log record in which records were skipped during the capture window:

```
2 123456789010 eni-4b118871 - - - - - - - 1431280876 1431280934 - SKIPDATA
```

**Security Group and Network ACL Rules**

If you're using flow logs to diagnose overly restrictive or permissive security group rules or network ACL rules, then be aware of the statefulness of these resources\. Security groups are stateful — this means that responses to allowed traffic are also allowed, even if the rules in your security group do not permit it\. Conversely, network ACLs are stateless, therefore responses to allowed traffic are subject to network ACL rules\.

For example, you use the `ping` command from your home computer \(IP address is `203.0.113.12`\) to your instance \(the network interface's private IP address is `172.31.16.139`\)\. Your security group's inbound rules allow ICMP traffic and the outbound rules do not allow ICMP traffic; however, because security groups are stateful, the response ping from your instance is allowed\. Your network ACL permits inbound ICMP traffic but does not permit outbound ICMP traffic\. Because network ACLs are stateless, the response ping is dropped and does not reach your home computer\. In a flow log, this is displayed as two flow log records: 
+ An `ACCEPT` record for the originating ping that was allowed by both the network ACL and the security group, and therefore was allowed to reach your instance\. 
+ A `REJECT` record for the response ping that the network ACL denied\.

```
2 123456789010 eni-1235b8ca 203.0.113.12 172.31.16.139 0 0 1 4 336 1432917027 1432917142 ACCEPT OK
```

```
2 123456789010 eni-1235b8ca 172.31.16.139 203.0.113.12 0 0 1 4 336 1432917094 1432917142 REJECT OK
```

If your network ACL permits outbound ICMP traffic, the flow log displays two `ACCEPT` records \(one for the originating ping and one for the response ping\)\. If your security group denies inbound ICMP traffic, the flow log displays a single `REJECT` record, because the traffic was not permitted to reach your instance\.

**Flow Log Record for IPv6 Traffic**

The following is an example of a flow log record in which SSH traffic \(port 22\) from IPv6 address `2001:db8:1234:a100:8d6e:3477:df66:f105` to network interface `eni-f41c42bf` in account `123456789010` was allowed\.

```
2 123456789010 eni-f41c42bf 2001:db8:1234:a100:8d6e:3477:df66:f105 2001:db8:1234:a102:3304:8879:34cf:4071 34892 22 6 54 8855 1477913708 1477913820 ACCEPT OK
```

## Flow Log Limitations<a name="flow-logs-limitations"></a>

To use flow logs, you need to be aware of the following limitations:
+ You cannot enable flow logs for network interfaces that are in the EC2\-Classic platform\. This includes EC2\-Classic instances that have been linked to a VPC through ClassicLink\. 
+ You can't enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account\.
+ You cannot tag a flow log\.
+ After you've created a flow log, you cannot change its configuration; for example, you can't associate a different IAM role with the flow log\. Instead, you can delete the flow log and create a new one with the required configuration\. 
+ None of the flow log API actions \(`ec2:*FlowLogs`\) support resource\-level permissions\. To create an IAM policy to control the use of the flow log API actions, you must grant users permissions to use all resources for the action by using the \* wildcard for the resource element in your statement\. For more information, see [Controlling Access to Amazon VPC Resources](VPC_IAM.md)\.
+ If your network interface has multiple IPv4 addresses and traffic is sent to a secondary private IPv4 address, the flow log displays the primary private IPv4 address in the destination IP address field\.
+  If traffic is sent to an ENI and the destination is not any of the ENI IP addresses, the flow log displays the primary private IPv4 address in the destination IP address field\.
+ If traffic is sent from an ENI and the source is not any of the ENI IP addresses, the flow log displays the primary private IPv4 address in the source IP address field\.

Flow logs do not capture all IP traffic\. The following types of traffic are not logged:
+ Traffic generated by instances when they contact the Amazon DNS server\. If you use your own DNS server, then all traffic to that DNS server is logged\. 
+ Traffic generated by a Windows instance for Amazon Windows license activation\.
+ Traffic to and from `169.254.169.254` for instance metadata\.
+ Traffic to and from `169.254.169.123` for the Amazon Time Sync Service\.
+ DHCP traffic\.
+ Traffic to the reserved IP address for the default VPC router\. For more information, see [VPC and Subnet Sizing](VPC_Subnets.md#VPC_Sizing)\.
+ Traffic between an endpoint network interface and a Network Load Balancer network interface\. For more information, see [VPC Endpoint Services \(AWS PrivateLink\)](endpoint-service.md)\.