# Troubleshooting<a name="flow-logs-troubleshooting"></a>

The following are possible issues you might have when working with flow logs\.

**Topics**
+ [Incomplete flow log records](#flow-logs-troubleshooting-incomplete-records)
+ [Flow log is active, but no flow log records or log group](#flow-logs-troubleshooting-no-log-group)
+ ['LogDestinationNotFoundException' or 'Access Denied for LogDestination' error](#flow-logs-troubleshooting-not-found)
+ [Exceeding the Amazon S3 bucket policy limit](#flow-logs-troubleshooting-policy-limit)

## Incomplete flow log records<a name="flow-logs-troubleshooting-incomplete-records"></a>

**Problem**  
Your flow log records are incomplete, or are no longer being published\.

**Cause**  
There may be a problem delivering the flow logs to the CloudWatch Logs log group\.

**Solution**  
In either the Amazon EC2 console or the Amazon VPC console, choose the **Flow Logs** tab for the relevant resource\. For more information, see [Viewing flow logs](working-with-flow-logs.md#view-flow-logs)\. The flow logs table displays any errors in the **Status** column\. Alternatively, use the [describe\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-flow-logs.html) command, and check the value that's returned in the `DeliverLogsErrorMessage` field\. One of the following errors may be displayed:
+ `Rate limited`: This error can occur if CloudWatch Logs throttling has been applied — when the number of flow log records for a network interface is higher than the maximum number of records that can be published within a specific timeframe\. This error can also occur if you've reached the quota for the number of CloudWatch Logs log groups that you can create\. For more information, see [CloudWatch Service Quotas](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/cloudwatch_limits.html) in the *Amazon CloudWatch User Guide*\.
+ `Access error`: This error can occur for one of the following reasons:
  + The IAM role for your flow log does not have sufficient permissions to publish flow log records to the CloudWatch log group
  + The IAM role does not have a trust relationship with the flow logs service
  + The trust relationship does not specify the flow logs service as the principal

  For more information, see [IAM roles for publishing flow logs to CloudWatch Logs](flow-logs-cwl.md#flow-logs-iam)\.
+ `Unknown error`: An internal error has occurred in the flow logs service\. 

## Flow log is active, but no flow log records or log group<a name="flow-logs-troubleshooting-no-log-group"></a>

**Problem**  
You've created a flow log, and the Amazon VPC or Amazon EC2 console displays the flow log as `Active`\. However, you cannot see any log streams in CloudWatch Logs or log files in your Amazon S3 bucket\. 

**Cause**  
The cause may be one of the following:
+ The flow log is still in the process of being created\. In some cases, it can take ten minutes or more after you've created the flow log for the log group to be created, and for data to be displayed\.
+ There has been no traffic recorded for your network interfaces yet\. The log group in CloudWatch Logs is only created when traffic is recorded\.

**Solution**  
Wait a few minutes for the log group to be created, or for traffic to be recorded\.

## 'LogDestinationNotFoundException' or 'Access Denied for LogDestination' error<a name="flow-logs-troubleshooting-not-found"></a>

**Problem**  
You get a `Access Denied for LogDestination` or a `LogDestinationNotFoundException` error when you try to create a flow log\.

**Cause**  
You might get these errors when creating a flow log that publishes data to an Amazon S3 bucket\. This error indicates that the specified S3 bucket could not be found or that there is an issue with the bucket policy\.

**Solution**  
Do one of the following: 
+ Ensure that you have specified the ARN for an existing S3 bucket, and that the ARN is in the correct format\.
+ If you do not own the S3 bucket, verify that the [bucket policy](flow-logs-s3.md#flow-logs-s3-permissions) has sufficient permissions to publish logs to it\. In the bucket policy, verify the account ID and bucket name\. 

## Exceeding the Amazon S3 bucket policy limit<a name="flow-logs-troubleshooting-policy-limit"></a>

**Problem**  
You get the following error when you try to create a flow log: `LogDestinationPermissionIssueException`\.

**Cause**  
Amazon S3 bucket policies are limited to 20 KB in size\.

Each time that you create a flow log that publishes to an Amazon S3 bucket, we automatically add the specified bucket ARN, which includes the folder path, to the `Resource` element in the bucket's policy\.

Creating multiple flow logs that publish to the same bucket could cause you to exceed the bucket policy limit\. 

**Solution**  
Do one of the following:
+ Clean up the bucket's policy by removing the flow log entries that are no longer needed\.
+ Grant permissions to the entire bucket by replacing the individual flow log entries with the following\.

  ```
  arn:aws:s3:::bucket_name/*
  ```

  If you grant permissions to the entire bucket, new flow log subscriptions do not add new permissions to the bucket policy\.