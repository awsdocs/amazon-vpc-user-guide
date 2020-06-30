# Working with flow logs<a name="working-with-flow-logs"></a>

You can work with flow logs using the Amazon EC2, Amazon VPC, CloudWatch, and Amazon S3 consoles\.

**Topics**
+ [Controlling the use of flow logs](#controlling-use-of-flow-logs)
+ [Creating a flow log](#create-flow-log)
+ [Viewing flow logs](#view-flow-logs)
+ [Adding or removing tags for flow logs](#modify-tags-flow-logs)
+ [Viewing flow log records](#view-flow-log-records)
+ [Searching flow log records](#search-flow-log-records)
+ [Deleting a flow log](#delete-flow-log)
+ [API and CLI overview](#flow-logs-api-cli)

## Controlling the use of flow logs<a name="controlling-use-of-flow-logs"></a>

By default, IAM users do not have permission to work with flow logs\. You can create an IAM user policy that grants users the permissions to create, describe, and delete flow logs\. For more information, see [Granting IAM Users Required Permissions for Amazon EC2 Resources](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ec2-api-permissions.html) in the *Amazon EC2 API Reference*\.

The following is an example policy that grants users full permissions to create, describe, and delete flow logs\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DeleteFlowLogs",
        "ec2:CreateFlowLogs",
        "ec2:DescribeFlowLogs"
      ],
      "Resource": "*"
    }
  ]
}
```

Some additional IAM role and permission configuration is required, depending on whether you're publishing to CloudWatch Logs or Amazon S3\. For more information, see [Publishing flow logs to CloudWatch Logs](flow-logs-cwl.md) and [Publishing flow logs to Amazon S3](flow-logs-s3.md)\.

## Creating a flow log<a name="create-flow-log"></a>

You can create flow logs for your VPCs, subnets, or network interfaces\. Flow logs can publish data to CloudWatch Logs or Amazon S3\.

For more information, see [Creating a flow log that publishes to CloudWatch Logs](flow-logs-cwl.md#flow-logs-cwl-create-flow-log) and [Creating a flow log that publishes to Amazon S3](flow-logs-s3.md#flow-logs-s3-create-flow-log)\.

## Viewing flow logs<a name="view-flow-logs"></a>

You can view information about your flow logs in the Amazon EC2 and Amazon VPC consoles by viewing the **Flow Logs** tab for a specific resource\. When you select the resource, all the flow logs for that resource are listed\. The information displayed includes the ID of the flow log, the flow log configuration, and information about the status of the flow log\.

**To view information about flow logs for your network interfaces**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface, and choose **Flow Logs**\. Information about the flow logs is displayed on the tab\. The **Destination type** column indicates the destination to which the flow logs are published\.

**To view information about flow logs for your VPCs or subnets**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs** or **Subnets**\.

1. Select your VPC or subnet, and choose **Flow Logs**\. Information about the flow logs is displayed on the tab\. The **Destination type** column indicates the destination to which the flow logs are published\.

## Adding or removing tags for flow logs<a name="modify-tags-flow-logs"></a>

You can add or remove tags for a flow log in the Amazon EC2 and Amazon VPC consoles\.

**To add or remove tags for a flow log for a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface, and choose **Flow Logs**\.

1. Choose **Manage tags** for the required flow log\.

1. To add a new tag, choose **Create Tag**\. To remove a tag, choose the delete button \(x\)\.

1. Choose **Save**\.

**To add or remove tags for a flow log for a VPC or subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs** or **Subnets**\.

1. Select your VPC or subnet, and choose **Flow Logs**\.

1. Select the flow log, and choose **Actions**, **Add/Edit Tags**\.

1. To add a new tag, choose **Create Tag**\. To remove a tag, choose the delete button \(x\)\.

1. Choose **Save**\.

## Viewing flow log records<a name="view-flow-log-records"></a>

You can view your flow log records using the CloudWatch Logs console or Amazon S3 console, depending on the chosen destination type\. It may take a few minutes after you've created your flow log for it to be visible in the console\.

**To view flow log records published to CloudWatch Logs**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, and select the log group that contains your flow log\. A list of log streams for each network interface is displayed\.

1.  Select the log stream that contains the ID of the network interface for which to view the flow log records\. For more information, see [Flow log records](flow-logs.md#flow-log-records)\.

**To view flow log records published to Amazon S3**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. For **Bucket name**, select the bucket to which the flow logs are published\.

1. For **Name**, select the check box next to the log file\. On the object overview panel, choose **Download**\.

## Searching flow log records<a name="search-flow-log-records"></a>

You can search your flow log records that are published to CloudWatch Logs by using the CloudWatch Logs console\. You can use [metric filters](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html) to filter flow log records\. Flow log records are space delimited\.

**To search flow log records using the CloudWatch Logs console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**, and select the log group that contains your flow log\. A list of log streams for each network interface is displayed\.

1. Select the individual log stream if you know the network interface that you are searching for\. Alternatively, choose **Search Log Group** to search the entire log group\. This might take some time if there are many network interfaces in your log group, or depending on the time range that you select\.

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

## Deleting a flow log<a name="delete-flow-log"></a>

You can delete a flow log using the Amazon EC2 and Amazon VPC consoles\. 

**Note**  
These procedures disable the flow log service for a resource\. Deleting a flow log does not delete the existing log streams from CloudWatch Logs and log files from Amazon S3\. Existing flow log data must be deleted using the respective service's console\. In addition, deleting a flow log that publishes to Amazon S3 does not remove the bucket policies and log file access control lists \(ACLs\)\.

**To delete a flow log for a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces** and select the network interface\.

1. Choose **Flow Logs**, and then choose the delete button \(a cross\) for the flow log to delete\.

1. In the confirmation dialog box, choose **Yes, Delete**\.

**To delete a flow log for a VPC or subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs** or **Subnets**, and then select the resource\.

1. Choose **Flow Logs**, and then choose the delete button \(a cross\) for the flow log to delete\.

1. In the confirmation dialog box, choose **Yes, Delete**\.

## API and CLI overview<a name="flow-logs-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interfaces and a list of available API actions, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create a flow log**
+ [create\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-flow-logs.html) \(AWS CLI\)
+ [New\-EC2FlowLog](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2FlowLog.html) \(AWS Tools for Windows PowerShell\)
+ [CreateFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateFlowLogs.html) \(Amazon EC2 Query API\)

**Describe your flow logs**
+ [describe\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-flow-logs.html) \(AWS CLI\)
+ [Get\-EC2FlowLog](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2FlowLog.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeFlowLogs.html) \(Amazon EC2 Query API\)

**View your flow log records \(log events\)**
+ [get\-log\-events](https://docs.aws.amazon.com/cli/latest/reference/logs/get-log-events.html) \(AWS CLI\)
+ [Get\-CWLLogEvent](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-CWLLogEvent.html) \(AWS Tools for Windows PowerShell\)
+ [GetLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html) \(CloudWatch API\)

**Delete a flow log**
+ [delete\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-flow-logs.html) \(AWS CLI\)
+ [Remove\-EC2FlowLog](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2FlowLog.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteFlowLogs.html) \(Amazon EC2 Query API\)