# Publish flow logs to CloudWatch Logs<a name="flow-logs-cwl"></a>

Flow logs can publish flow log data directly to Amazon CloudWatch\.

When publishing to CloudWatch Logs, flow log data is published to a log group, and each network interface has a unique log stream in the log group\. Log streams contain flow log records\. You can create multiple flow logs that publish data to the same log group\. If the same network interface is present in one or more flow logs in the same log group, it has one combined log stream\. If you've specified that one flow log should capture rejected traffic, and the other flow log should capture accepted traffic, then the combined log stream captures all traffic\.

In CloudWatch Logs, the **timestamp** field corresponds to the start time that's captured in the flow log record\. The **ingestionTime** field indicates the date and time when the flow log record was received by CloudWatch Logs\. This timestamp is later than the end time that's captured in the flow log record\.

For more information about CloudWatch Logs, see [Logs sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AWS-logs-and-resource-policy.html#AWS-logs-infrastructure-CWL) in the *Amazon CloudWatch Logs User Guide*\.

**Pricing**  
Data ingestion and archival charges for vended logs apply when you publish flow logs to CloudWatch Logs\. For more information, open [Amazon CloudWatch Pricing](http://aws.amazon.com/cloudwatch/pricing), select **Logs** and find **Vended Logs**\.

**Topics**
+ [IAM role for publishing flow logs to CloudWatch Logs](#flow-logs-iam-role)
+ [Permissions for IAM principals that publish flow logs to CloudWatch Logs](#flow-logs-iam-user)
+ [Create a flow log that publishes to CloudWatch Logs](#flow-logs-cwl-create-flow-log)
+ [View flow log records](#view-flow-log-records-cwl)
+ [Search flow log records](#search-flow-log-records-cwl)
+ [Process flow log records in CloudWatch Logs](#process-records-cwl)

## IAM role for publishing flow logs to CloudWatch Logs<a name="flow-logs-iam-role"></a>

The IAM role that's associated with your flow log must have sufficient permissions to publish flow logs to the specified log group in CloudWatch Logs\. The IAM role must belong to your AWS account\.

The IAM policy that's attached to your IAM role must include at least the following permissions\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "logs:DescribeLogGroups",
        "logs:DescribeLogStreams"
      ],
      "Resource": "*"
    }
  ]
}
```

Ensure that your role has the following trust policy, which allows the flow logs service to assume the role\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "vpc-flow-logs.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

We recommend that you use the `aws:SourceAccount` and `aws:SourceArn` condition keys to protect yourself against [the confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\. For example, you could add the following condition block to the previous trust policy\. The source account is the owner of the flow log and the source ARN is the flow log ARN\. If you don't know the flow log ID, you can replace that portion of the ARN with a wildcard \(\*\) and then update the policy after you create the flow log\.

```
"Condition": {
    "StringEquals": {
        "aws:SourceAccount": "account_id"
    },
    "ArnLike": {
        "aws:SourceArn": "arn:aws:ec2:region:account_id:vpc-flow-log/flow-log-id"
    }
}
```

### Create an IAM role for flow logs<a name="create-flow-logs-role"></a>

You can update an existing role as described above\. Alternatively, you can use the following procedure to create a new role for use with flow logs\. You'll specify this role when you create the flow log\.

**To create an IAM role for flow logs**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. On the **Create policy** page, do the following:

   1. Choose **JSON**\.

   1. Replace the contents of this window with the permissions policy at the start of this section\.

   1. Choose **Next: Tags** and **Next: Review**\.

   1. Enter a name for your policy and an optional description, and then choose **Create policy**\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. For **Trusted entity type**, choose **Custom trust policy**\. For **Custom trust policy**, replace `"Principal": {},` with the following, then and choose **Next**\.

   ```
   "Principal": {
      "Service": "vpc-flow-logs.amazonaws.com"
   },
   ```

1. On the **Add permissions** page, select the checkbox for the policy that you created earlier in this procedure, and then choose **Next**\.

1. Enter a name for your role and optionally provide a description\.

1. Choose **Create role**\.

## Permissions for IAM principals that publish flow logs to CloudWatch Logs<a name="flow-logs-iam-user"></a>

Users must have permissions to use the `iam:PassRole` action for the IAM role that's associated with the flow log\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["iam:PassRole"],
      "Resource": "arn:aws:iam::account-id:role/flow-log-role-name"
    }
  ]
}
```

## Create a flow log that publishes to CloudWatch Logs<a name="flow-logs-cwl-create-flow-log"></a>

You can create flow logs for your VPCs, subnets, or network interfaces\. If you perform these steps as an IAM user, ensure that you have permissions to use the `iam:PassRole` action\. For more information, see [Permissions for IAM principals that publish flow logs to CloudWatch Logs](#flow-logs-iam-user)\.

**Prerequisites**
+ Create the destination log group\. Open the [Log groups page](https://console.aws.amazon.com/cloudwatch/home#logsV2:log-groups) in the CloudWatch console and choose **Create log group**\. Enter a name for the log group and choose **Create**\.
+ Create an IAM role, as described in [IAM role for publishing flow logs to CloudWatch Logs](#flow-logs-iam-role)\.

**To create a flow log using the console**

1. Do one of the following:
   + Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\. In the navigation pane, choose **Network Interfaces**\. Select the checkbox for the network interface\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Your VPCs**\. Select the checkbox for the VPC\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Subnets**\. Select the checkbox for the subnet\.

1. Choose **Actions**, **Create flow log**\.

1. For **Filter**, specify the type of traffic to log\. Choose **All** to log accepted and rejected traffic, **Reject** to log only rejected traffic, or **Accept** to log only accepted traffic\.

1. For **Maximum aggregation interval**, choose the maximum period of time during which a flow is captured and aggregated into one flow log record\.

1. For **Destination**, choose **Send to CloudWatch Logs**\.

1. For **Destination log group**, choose the name of the destination log group that you created\.

1. For **IAM role**, specify the name of the role that has permissions to publish logs to CloudWatch Logs\.

1. For **Log record format**, select the format for the flow log record\.
   + To use the default format, choose **AWS default format**\.
   + To use a custom format, choose **Custom format** and then select fields from **Log format**\.

1. \(Optional\) Choose **Add new tag** to apply tags to the flow log\.

1. Choose **Create flow log**\.

**To create a flow log using the command line**

Use one of the following commands\.
+ [create\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-flow-logs.html) \(AWS CLI\)
+ [New\-EC2FlowLogs](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)

The following AWS CLI example creates a flow log that captures all accepted traffic for the specified subnet\. The flow logs are delivered to the specified log group\. The `--deliver-logs-permission-arn` parameter specifies the IAM role required to publish to CloudWatch Logs\.

```
aws ec2 create-flow-logs --resource-type Subnet --resource-ids subnet-1a2b3c4d --traffic-type ACCEPT --log-group-name my-flow-logs --deliver-logs-permission-arn arn:aws:iam::123456789101:role/publishFlowLogs
```

## View flow log records<a name="view-flow-log-records-cwl"></a>

You can view your flow log records using the CloudWatch Logs console\. After you create your flow log, it might take a few minutes for it to be visible in the console\.

**To view flow log records published to CloudWatch Logs using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, **Log groups**\.

1. Select the name of the log group that contains your flow logs to open its details page\.

1. Select the name of the log stream that contains the flow log records\. For more information, see [Flow log records](flow-logs.md#flow-log-records)\.

**To view flow log records published to CloudWatch Logs using the command line**
+ [get\-log\-events](https://docs.aws.amazon.com/cli/latest/reference/logs/get-log-events.html) \(AWS CLI\)
+ [Get\-CWLLogEvent](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-CWLLogEvent.html) \(AWS Tools for Windows PowerShell\)

## Search flow log records<a name="search-flow-log-records-cwl"></a>

You can search your flow log records that are published to CloudWatch Logs using the CloudWatch Logs console\. You can use [metric filters](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html) to filter flow log records\. Flow log records are space delimited\.

**To search flow log records using the CloudWatch Logs console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, **Log groups**\.

1. Select the log group that contains your flow log, and then select the log stream, if you know the network interface that you are searching for\. Alternatively, choose **Search log group**\. This might take some time if there are many network interfaces in your log group, or depending on the time range that you select\.

1. For **Filter events**, enter the following string\. This assumes that the flow log record uses the [default format](flow-logs.md#flow-logs-default)\.

   ```
   [version, accountid, interfaceid, srcaddr, dstaddr, srcport, dstport, protocol, packets, bytes, start, end, action, logstatus]
   ```

1. Modify the filter as needed by specifying values for the fields\. The following examples filter by specific source IP addresses\.

   ```
   [version, accountid, interfaceid, srcaddr = 10.0.0.1, dstaddr, srcport, dstport, protocol, packets, bytes, start, end, action, logstatus]
   [version, accountid, interfaceid, srcaddr = 10.0.2.*, dstaddr, srcport, dstport, protocol, packets, bytes, start, end, action, logstatus]
   ```

   The following examples filter by destination port, the number of bytes, and whether the traffic was rejected\.

   ```
   [version, accountid, interfaceid, srcaddr, dstaddr, srcport, dstport = 80 || dstport = 8080, protocol, packets, bytes, start, end, action, logstatus]
   [version, accountid, interfaceid, srcaddr, dstaddr, srcport, dstport = 80 || dstport = 8080, protocol, packets, bytes >= 400, start, end, action = REJECT, logstatus]
   ```

## Process flow log records in CloudWatch Logs<a name="process-records-cwl"></a>

You can work with flow log records as you would with any other log events collected by CloudWatch Logs\. For more information about monitoring log data and metric filters, see [Searching and Filtering Log Data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/MonitoringLogData.html) in the *Amazon CloudWatch User Guide*\.

### Example: Create a CloudWatch metric filter and alarm for a flow log<a name="flow-logs-cw-alarm-example"></a>

In this example, you have a flow log for `eni-1a2b3c4d`\. You want to create an alarm that alerts you if there have been 10 or more rejected attempts to connect to your instance over TCP port 22 \(SSH\) within a 1\-hour time period\. First, you must create a metric filter that matches the pattern of the traffic for which to create the alarm\. Then, you can create an alarm for the metric filter\.

**To create a metric filter for rejected SSH traffic and create an alarm for the filter**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, **Log groups**\.

1. Select the check box for the log group, and then choose **Actions**, **Create metric filter**\.

1. For **Filter pattern**, enter the following string\.

   ```
   [version, account, eni, source, destination, srcport, destport="22", protocol="6", packets, bytes, windowstart, windowend, action="REJECT", flowlogstatus]
   ```

1. For **Select log data to test**, select the log stream for your network interface\. \(Optional\) To view the lines of log data that match the filter pattern, choose **Test pattern**\.

1. When you're ready, choose **Next**\.

1. Enter a filter name, metric namespace, and metric name\. Set the metric value to 1\. When you're done, choose **Next** and then choose **Create metric filter**\.

1. In the navigation pane, choose **Alarms**, **All alarms**\.

1. Choose **Create alarm**\.

1. Choose the namespace for the metric filter that you created\.

   It can take a few minutes for a new metric to display in the console\.

1. Select the metric name that you created, and then choose **Select metric**\.

1. Configure the alarm as follows, and then choose **Next**:
   + For **Statistic**, choose **Sum**\. This ensure that you capture the total number of data points for the specified time period\.
   + For **Period**, choose **1 hour**\.
   + For **Whenever**, choose **Greater/Equal** and enter 10 for the threshold\.
   + For **Additional configuration**, **Datapoints to alarm**, leave the default of 1\.

1. For **Notification**, select an existing SNS topic or choose **Create new topic** to create a new one\. Choose **Next**\.

1. Enter a name and description for the alarm and choose **Next**\.

1. When you are done configuring the alarm, choose **Create alarm**\.