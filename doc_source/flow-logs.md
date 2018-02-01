# VPC Flow Logs<a name="flow-logs"></a>

VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC\. Flow log data is stored using Amazon CloudWatch Logs\. After you've created a flow log, you can view and retrieve its data in Amazon CloudWatch Logs\.

Flow logs can help you with a number of tasks; for example, to troubleshoot why specific traffic is not reaching an instance, which in turn can help you diagnose overly restrictive security group rules\. You can also use flow logs as a security tool to monitor the traffic that is reaching your instance\. 

There is no additional charge for using flow logs; however, standard CloudWatch Logs charges apply\. For more information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing)\.


+ [Flow Logs Basics](#flow-logs-basics)
+ [Flow Log Limitations](#flow-logs-limitations)
+ [Flow Log Records](#flow-log-records)
+ [IAM Roles for Flow Logs](#flow-logs-iam)
+ [Working With Flow Logs](#working-with-flow-logs)
+ [Troubleshooting](#flow-logs-troubleshooting)
+ [API and CLI Overview](#flow-logs-api-cli)
+ [Examples: Flow Log Records](#flow-logs-records-examples)
+ [Example: Creating a CloudWatch Metric Filter and Alarm for a Flow Log](#flow-logs-cw-alarm-example)

## Flow Logs Basics<a name="flow-logs-basics"></a>

You can create a flow log for a VPC, a subnet, or a network interface\. If you create a flow log for a subnet or VPC, each network interface in the VPC or subnet is monitored\. Flow log data is published to a log group in CloudWatch Logs, and each network interface has a unique log stream\. Log streams contain *flow log records*, which are log events consisting of fields that describe the traffic for that network interface\. For more information, see [Flow Log Records](#flow-log-records)\. 

To create a flow log, you specify the resource for which you want to create the flow log, the type of traffic to capture \(accepted traffic, rejected traffic, or all traffic\), the name of a log group in CloudWatch Logs to which the flow log will be published, and the ARN of an IAM role that has sufficient permission to publish the flow log to the CloudWatch Logs log group\. If you specify the name of a log group that does not exist, we'll attempt to create the log group for you\. After you've created a flow log, it can take several minutes to begin collecting data and publishing to CloudWatch Logs\. Flow logs do not capture real\-time log streams for your network interfaces\.

You can create multiple flow logs that publish data to the same log group in CloudWatch Logs\. If the same network interface is present in one or more flow logs in the same log group, it has one combined log stream\. If you've specified that one flow log should capture rejected traffic, and the other flow log should capture accepted traffic, then the combined log stream captures all traffic\.

If you launch more instances into your subnet after you've created a flow log for your subnet or VPC, then a new log stream is created for each new network interface as soon as any network traffic is recorded for that network interface\.

You can create flow logs for network interfaces that are created by other AWS services; for example, Elastic Load Balancing, Amazon RDS, Amazon ElastiCache, Amazon Redshift, and Amazon WorkSpaces\. However, you cannot use these services' consoles or APIs to create the flow logs; you must use the Amazon EC2 console or the Amazon EC2 API\. Similarly, you cannot use the CloudWatch Logs console or API to create log streams for your network interfaces\.

If you no longer require a flow log, you can delete it\. Deleting a flow log disables the flow log service for the resource, and no new flow log records or log streams are created\. It does not delete any existing flow log records or log streams for a network interface\. To delete an existing log stream, you can use the CloudWatch Logs console\. After you've deleted a flow log, it can take several minutes to stop collecting data\.

## Flow Log Limitations<a name="flow-logs-limitations"></a>

To use flow logs, you need to be aware of the following limitations:

+ You cannot enable flow logs for network interfaces that are in the EC2\-Classic platform\. This includes EC2\-Classic instances that have been linked to a VPC through ClassicLink\. 

+ You cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account\.

+ You cannot tag a flow log\.

+ After you've created a flow log, you cannot change its configuration; for example, you can't associate a different IAM role with the flow log\. Instead, you can delete the flow log and create a new one with the required configuration\. 

+ None of the flow log API actions \(`ec2:*FlowLogs`\) support resource\-level permissions\. If you want to create an IAM policy to control the use of the flow log API actions, you must grant users permission to use all resources for the action by using the \* wildcard for the resource element in your statement\. For more information, see [Controlling Access to Amazon VPC Resources](VPC_IAM.md)\.

+ If your network interface has multiple IPv4 addresses and traffic is sent to a secondary private IPv4 address, the flow log displays the primary private IPv4 address in the destination IP address field\.

Flow logs do not capture all types of IP traffic\. The following types of traffic are not logged:

+ Traffic generated by instances when they contact the Amazon DNS server\. If you use your own DNS server, then all traffic to that DNS server is logged\. 

+ Traffic generated by a Windows instance for Amazon Windows license activation\.

+ Traffic to and from `169.254.169.254` for instance metadata\.

+ Traffic to and from `169.254.169.123` for the Amazon Time Sync Service\.

+ DHCP traffic\.

+ Traffic to the reserved IP address for the default VPC router\. For more information, see [VPC and Subnet Sizing](VPC_Subnets.md#VPC_Sizing)\.

## Flow Log Records<a name="flow-log-records"></a>

A flow log record represents a network flow in your flow log\. Each record captures the network flow for a specific 5\-tuple, for a specific capture window\. A 5\-tuple is a set of 5 different values that specify the source, destination, and protocol for an Internet protocol \(IP\) flow\. The capture window is a duration of time during which the flow logs service aggregates data before publishing flow log records\. The capture window is approximately 10 minutes, but can take up to 15 minutes\. A flow log record is a space\-separated string that has the following format:

*version account\-id interface\-id srcaddr dstaddr srcport dstport protocol packets bytes start end action log\-status*


****  

| Field | Description | 
| --- | --- | 
| version | The VPC flow logs version\. | 
| account\-id | The AWS account ID for the flow log\. | 
| interface\-id | The ID of the network interface for which the log stream applies\. | 
| srcaddr | The source IPv4 or IPv6 address\. The IPv4 address of the network interface is always its private IPv4 address\. | 
| dstaddr | The destination IPv4 or IPv6 address\. The IPv4 address of the network interface is always its private IPv4 address\. | 
| srcport | The source port of the traffic\. | 
| dstport | The destination port of the traffic\. | 
| protocol | The IANA protocol number of the traffic\. For more information, go to [Assigned Internet Protocol Numbers](http://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)\. | 
| packets | The number of packets transferred during the capture window\. | 
| bytes | The number of bytes transferred during the capture window\. | 
| start | The time, in Unix seconds, of the start of the capture window\. | 
| end | The time, in Unix seconds, of the end of the capture window\. | 
| action | The action associated with the traffic:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs.html) | 
| log\-status | The logging status of the flow log:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs.html) | 

If a field is not applicable for a specific record, the record displays a '\-' symbol for that entry\.

For examples of flow log records, see [Examples: Flow Log Records](#flow-logs-records-examples)

You can work with flow log records as you would with any other log events collected by CloudWatch Logs\. For more information about monitoring log data and metric filters, see [Searching and Filtering Log Data](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/MonitoringLogData.html) in the *Amazon CloudWatch User Guide*\. For an example of setting up a metric filter and alarm for a flow log, see [Example: Creating a CloudWatch Metric Filter and Alarm for a Flow Log](#flow-logs-cw-alarm-example)\. 

You can export log data to Amazon S3 and use Amazon Athena, an interactive query service, to analyze the data\. For more information, see [Querying Amazon VPC Flow Logs](http://docs.aws.amazon.com/athena/latest/ug/vpc-flow-logs.html) in the *Amazon Athena User Guide*\.

## IAM Roles for Flow Logs<a name="flow-logs-iam"></a>

The IAM role that's associated with your flow log must have sufficient permissions to publish flow logs to the specified log group in CloudWatch Logs\. The IAM policy that's attached to your IAM role must include at least the following permissions:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "logs:DescribeLogGroups",
        "logs:DescribeLogStreams"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

You must also ensure that your role has a trust relationship that allows the flow logs service to assume the role \(in the IAM console, choose your role, and then choose **Edit trust relationship** to view the trust relationship\):

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "vpc-flow-logs.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

Alternatively, you can follow the procedures below to create a new role for use with flow logs\.

### Creating a Flow Logs Role<a name="create-flow-logs-role"></a>

**To create an IAM role for flow logs**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, **Create role**\.

1. Choose **EC2** and then the **EC2** use case\. Choose **Next: Permissions**\.

1. On the **Attach Policy** page, choose **Next: Review**\.

1. Enter a name for your role; for example, `Flow-Logs-Role`, and optionally provide a description\. Choose **Create role**\.

1. Select the name of your role\. Under **Permissions**, choose **Add inline policy**\.

1. Choose the **JSON** tab\.

1. In the section [IAM Roles for Flow Logs](#flow-logs-iam) above, copy the first policy and paste it in the window\. Choose **Review policy**\.

1. Enter a name for your policy, and then choose **Create policy**\.

1. In the section [IAM Roles for Flow Logs](#flow-logs-iam) above, copy the second policy \(the trust relationship\), and then choose **Trust relationships**, **Edit trust relationship**\. Delete the existing policy document, and paste in the new one\. When you are done, choose **Update Trust Policy**\. 

1. On the **Summary** page, take note of the ARN for your role\. You will need this ARN when you create your flow log\. 

## Working With Flow Logs<a name="working-with-flow-logs"></a>

You can work with flow logs using the Amazon EC2, Amazon VPC, and CloudWatch consoles\.


+ [Creating a Flow Log](#create-flow-log)
+ [Viewing Flow Logs](#view-flow-logs)
+ [Deleting a Flow Log](#delete-flow-log)

### Creating a Flow Log<a name="create-flow-log"></a>

You can create a flow log from the VPC page and the Subnet page in the Amazon VPC console, or from the Network Interfaces page in the Amazon EC2 console\. 

**To create a flow log for a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface, choose the **Flow Logs** tab, and then **Create Flow Log**\.

1. In the dialog box, complete following information\. When you are done, choose **Create Flow Log**:

   + **Filter**: Select whether the flow log should capture rejected traffic, accepted traffic, or all traffic\.

   + **Role**: Specify the name of an IAM role that has permission to publish logs to CloudWatch Logs\.

   + **Destination Log Group**: Enter the name of a log group in CloudWatch Logs to which the flow logs will be published\. You can use an existing log group, or you can enter a name for a new log group, which we'll create for you\.

**To create a flow log for a VPC or a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**, or choose **Subnets**\.

1. Select your VPC or subnet, choose the **Flow Logs** tab, and then **Create Flow Log**\.
**Note**  
To create flow logs for multiple VPCs, choose the VPCs, and then select **Create Flow Log** from the **Actions** menu\. To create flow logs for multiple subnets, choose the subnets, and then select **Create Flow Log** from the **Subnet Actions** menu\.

1. In the dialog box, complete following information\. When you are done, choose **Create Flow Log**:

   + **Filter**: Select whether the flow log should capture rejected traffic, accepted traffic, or all traffic\.

   + **Role**: Specify the name of an IAM role that has permission to publish logs to CloudWatch Logs\.

   + **Destination Log Group**: Enter the name of a log group in CloudWatch Logs to which the flow logs will be published\. You can use an existing log group, or you can enter a name for a new log group, which we'll create for you\.

### Viewing Flow Logs<a name="view-flow-logs"></a>

You can view information about your flow logs in the Amazon EC2 and Amazon VPC consoles by viewing the **Flow Logs** tab for a specific resource\. When you select the resource, all the flow logs for that resource are listed\. The information displayed includes the ID of the flow log, the flow log configuration, and information about the status of the flow log\.

**To view information about your flow logs for your network interfaces**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface, and choose the **Flow Logs** tab\. Information about the flow logs is displayed on the tab\. 

**To view information about your flow logs for your VPCs or subnets**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**, or choose **Subnets**\.

1. Select your VPC or subnet, and then choose the **Flow Logs** tab\. Information about the flow logs is displayed on the tab\.

You can view your flow log records using the CloudWatch Logs console\. It may take a few minutes after you've created your flow log for it to be visible in the console\.

**To view your flow log records for a flow log**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. Choose the name of the log group that contains your flow log\.

1. A list of log streams for each network interface is displayed\. Choose the name of the log stream that contains the ID of the network interface for which you want to view the flow log records\. For more information about flow log records, see [Flow Log Records](#flow-log-records)\.

### Deleting a Flow Log<a name="delete-flow-log"></a>

You can delete a flow log using the Amazon EC2 and Amazon VPC consoles\. 

**Note**  
These procedures disable the flow log service for a resource\. To delete the log streams for your network interfaces, use the CloudWatch Logs console\.

**To delete a flow log for a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**, and then select the network interface\.

1. Choose the **Flow Logs** tab, and then choose the delete button \(a cross\) for the flow log to delete\.

1. In the confirmation dialog box, choose **Yes, Delete**\.

**To delete a flow log for a VPC or subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**, or choose your **Subnets**, and then select the resource\.

1. Choose the **Flow Logs** tab, and then choose the delete button \(a cross\) for the flow log to delete\.

1. In the confirmation dialog box, choose **Yes, Delete**\.

## Troubleshooting<a name="flow-logs-troubleshooting"></a>

### Incomplete Flow Log Records<a name="flow-logs-troubleshooting-incomplete-records"></a>

If your flow log records are incomplete, or are no longer being published, there may be a problem delivering the flow logs to the CloudWatch Logs log group\. In either the Amazon EC2 console or the Amazon VPC console, go to the **Flow Logs** tab for the relevant resource\. For more information, see [Viewing Flow Logs](#view-flow-logs)\. The flow logs table displays any errors in the **Status** column\. Alternatively, use the [describe\-flow\-logs](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-flow-logs.html) command, and check the value that's returned in the `DeliverLogsErrorMessage` field\. One of the following errors may be displayed:

+ `Rate limited`: This error can occur if CloudWatch logs throttling has been applied — when the number of flow log records for a network interface is higher than the maximum number of records that can be published within a specific timeframe\. This error can also occur if you've reached the limit on the number of CloudWatch Logs log groups that you can create\. For more information, see [CloudWatch Limits](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/cloudwatch_limits.html) in the *Amazon CloudWatch User Guide*\.

+ `Access error`: The IAM role for your flow log does not have sufficient permissions to publish flow log records to the CloudWatch log group\. For more information, see [IAM Roles for Flow Logs](#flow-logs-iam)\.

+ `Unknown error`: An internal error has occurred in the flow logs service\. 

### Flow Log is Active, But No Flow Log Records or Log Group<a name="flow-logs-troubleshooting-no-log-group"></a>

You've created a flow log, and the Amazon VPC or Amazon EC2 console displays the flow log as `Active`\. However, you cannot see any log streams in CloudWatch Logs, or your CloudWatch Logs log group has not been created\. The cause may be one of the following:

+ The flow log is still in the process of being created\. In some cases, it can take tens of minutes after you've created the flow log for the log group to be created, and for data to be displayed\.

+ There has been no traffic recorded for your network interfaces yet\. The log group in CloudWatch Logs is only created when traffic is recorded\.

## API and CLI Overview<a name="flow-logs-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interfaces and a list of available API actions, see [Accessing Amazon VPC](VPC_Introduction.md#VPCInterfaces)\.

**Create a flow log**

+ [create\-flow\-logs](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-flow-logs.html) \(AWS CLI\)

+ [New\-EC2FlowLogs](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)

+ [CreateFlowLogs](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateFlowLogs.html) \(Amazon EC2 Query API\)

**Describe your flow logs**

+ [describe\-flow\-logs](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-flow-logs.html) \(AWS CLI\)

+ [Get\-EC2FlowLogs](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)

+ [DescribeFlowLogs](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeFlowLogs.html) \(Amazon EC2 Query API\)

**View your flow log records \(log events\)**

+ [get\-log\-events](http://docs.aws.amazon.com/cli/latest/reference/logs/get-log-events.html) \(AWS CLI\)

+ [Get\-CWLLogEvents](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-CWLLogEvents.html) \(AWS Tools for Windows PowerShell\)

+ [GetLogEvents](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html) \(CloudWatch API\)

**Delete a flow log**

+ [delete\-flow\-logs](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-flow-logs.html) \(AWS CLI\)

+ [Remove\-EC2FlowLogs](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)

+ [DeleteFlowLogs](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteFlowLogs.html) \(Amazon EC2 Query API\)

## Examples: Flow Log Records<a name="flow-logs-records-examples"></a>

**Flow Log Records for Accepted and Rejected Traffic**

The following is an example of a flow log record in which SSH traffic \(destination port 22, TCP protocol\) to network interface `eni-abc123de` in account `123456789010` was allowed\.

```
2 123456789010 eni-abc123de 172.31.16.139 172.31.16.21 20641 22 6 20 4249 1418530010 1418530070 ACCEPT OK
```

The following is an example of a flow log record in which RDP traffic \(destination port 3389, TCP protocol\) to network interface `eni-abc123de` in account `123456789010` was rejected\.

```
2 123456789010 eni-abc123de 172.31.9.69 172.31.9.12 49761 3389 6 20 4249 1418530010 1418530070 REJECT OK
```

**Flow Log Records for No Data and Skipped Records**

The following is an example of a flow log record in which no data was recorded during the capture window\.

```
2 123456789010 eni-1a2b3c4d - - - - - - - 1431280876 1431280934 - NODATA
```

The following is an example of a flow log record in which records were skipped during the capture window\.

```
2 123456789010 eni-4b118871 - - - - - - - 1431280876 1431280934 - SKIPDATA
```

**Security Group and Network ACL Rules**

If you're using flow logs to diagnose overly restrictive or permissive security group rules or network ACL rules, then be aware of the statefulness of these resources\. Security groups are stateful — this means that responses to allowed traffic are also allowed, even if the rules in your security group do not permit it\. Conversely, network ACLs are stateless, therefore responses to allowed traffic are subject to network ACL rules\.

For example, you use the `ping` command from your home computer \(IP address is `203.0.113.12`\) to your instance \(the network interface's private IP address is `172.31.16.139`\)\. Your security group's inbound rules allow ICMP traffic and the outbound rules do not allow ICMP traffic; however, because security groups are stateful, the response ping from your instance is allowed\. Your network ACL permits inbound ICMP traffic but does not permit outbound ICMP traffic\. Because network ACLs are stateless, the response ping is dropped and will not reach your home computer\. In a flow log, this is displayed as 2 flow log records: 

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

## Example: Creating a CloudWatch Metric Filter and Alarm for a Flow Log<a name="flow-logs-cw-alarm-example"></a>

In this example, you have a flow log for `eni-1a2b3c4d`\. You want to create an alarm that alerts you if there have been 10 or more rejected attempts to connect to your instance over TCP port 22 \(SSH\) within a 1 hour time period\. First, you must create a metric filter that matches the pattern of the traffic for which you want to create the alarm\. Then, you can create an alarm for the metric filter\.

**To create a metric filter for rejected SSH traffic and create an alarm for the filter**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, select the flow log group for your flow log, and then choose **Create Metric Filter**\.

1. In the **Filter Pattern** field, enter the following:

   ```
   [version, account, eni, source, destination, srcport, destport="22", protocol="6", packets, bytes, windowstart, windowend, action="REJECT", flowlogstatus]
   ```

1. In the **Select Log Data to Test** list, select the log stream for your network interface\. You can optionally choose **Test Pattern** to view the lines of log data that match the filter pattern\. When you're ready, choose **Assign Metric**\.

1. Provide a metric namespace, a metric name, and ensure that the metric value is set to **1**\. When you're done, choose **Create Filter**\.

1. In the navigation pane, choose **Alarms**, and then choose **Create Alarm**\.

1. In the **Custom Metrics** section, choose the namespace for the metric filter that you created\.
**Note**  
It can take a few minutes for a new metric to display in the console\.

1. Select the metric name that you created, and then choose **Next**\.

1. Enter a name and description for the alarm\. In the **is** fields, choose **>=** and enter **10**\. In the **for** field, leave the default **1** for the consecutive periods\.

1. Choose **1 Hour** from the **Period** list, and **Sum** from the **Statistic** list\. The `Sum` statistic ensures that you are capturing the total number of data points for the specified time period\. 

1. In the **Actions** section, you can choose to send a notification to an existing list, or you can create a new list and enter the email addresses that should receive a notification when the alarm is triggered\. When you are done, choose **Create Alarm**\.