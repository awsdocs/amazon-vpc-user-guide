# Troubleshooting<a name="flow-logs-troubleshooting"></a>

The following are possible issues you might have when working with flow logs\.

**Topics**
+ [Incomplete Flow Log Records](#flow-logs-troubleshooting-incomplete-records)
+ [Flow Log is Active, But No Flow Log Records or Log Group](#flow-logs-troubleshooting-no-log-group)
+ [Error: LogDestinationNotFoundException](#flow-logs-troubleshooting-not-found)

## Incomplete Flow Log Records<a name="flow-logs-troubleshooting-incomplete-records"></a>

If your flow log records are incomplete, or are no longer being published, there may be a problem delivering the flow logs to the CloudWatch Logs log group\. In either the Amazon EC2 console or the Amazon VPC console, choose the **Flow Logs** tab for the relevant resource\. For more information, see [Viewing Flow Logs](working-with-flow-logs.md#view-flow-logs)\. The flow logs table displays any errors in the **Status** column\. Alternatively, use the [describe\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-flow-logs.html) command, and check the value that's returned in the `DeliverLogsErrorMessage` field\. One of the following errors may be displayed:
+ `Rate limited`: This error can occur if CloudWatch logs throttling has been applied — when the number of flow log records for a network interface is higher than the maximum number of records that can be published within a specific timeframe\. This error can also occur if you've reached the limit on the number of CloudWatch Logs log groups that you can create\. For more information, see [CloudWatch Limits](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/cloudwatch_limits.html) in the *Amazon CloudWatch User Guide*\.
+ `Access error`: The IAM role for your flow log does not have sufficient permissions to publish flow log records to the CloudWatch log group\. For more information, see [IAM Roles for Publishing Flow Logs to CloudWatch Logs](flow-logs-cwl.md#flow-logs-iam)\.
+ `Unknown error`: An internal error has occurred in the flow logs service\. 

## Flow Log is Active, But No Flow Log Records or Log Group<a name="flow-logs-troubleshooting-no-log-group"></a>

You've created a flow log, and the Amazon VPC or Amazon EC2 console displays the flow log as `Active`\. However, you cannot see any log streams in CloudWatch Logs or log files in your Amazon S3 bucket\. The cause may be one of the following:
+ The flow log is still in the process of being created\. In some cases, it can take ten minutes or more after you've created the flow log for the log group to be created, and for data to be displayed\.
+ There has been no traffic recorded for your network interfaces yet\. The log group in CloudWatch Logs is only created when traffic is recorded\.

## Error: LogDestinationNotFoundException<a name="flow-logs-troubleshooting-not-found"></a>

You might get this error when creating a flow log that publishes data to an Amazon S3 bucket\. This error indicates that the specified Amazon S3 bucket could not be found\.

To resolve this error, ensure that you have specified the ARN for an existing Amazon S3 bucket, and that the ARN is in the correct format\.