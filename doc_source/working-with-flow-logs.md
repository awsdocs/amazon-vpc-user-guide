# Working With Flow Logs<a name="working-with-flow-logs"></a>

You can work with flow logs using the Amazon EC2, Amazon VPC, CloudWatch, and Amazon S3 consoles\.

**Topics**
+ [Controlling the Use of Flow Logs](#controlling-use-of-flow-logs)
+ [Creating a Flow Log](#create-flow-log)
+ [Viewing Flow Logs](#view-flow-logs)
+ [Viewing Flow Log Records](#view-flow-log-records)
+ [Deleting a Flow Log](#delete-flow-log)
+ [API and CLI Overview](#flow-logs-api-cli)

## Controlling the Use of Flow Logs<a name="controlling-use-of-flow-logs"></a>

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

Some additional IAM role and permission configuration is required, depending on whether you're publishing to CloudWatch Logs or Amazon S3\. For more information, see [Publishing Flow Logs to CloudWatch Logs](flow-logs-cwl.md) and [Publishing Flow Logs to Amazon S3](flow-logs-s3.md)\.

## Creating a Flow Log<a name="create-flow-log"></a>

You can create a flow log from the **VPC** and **Subnet** pages in the Amazon VPC console, or from the **Network Interfaces** page in the Amazon EC2 console\.

**To create a flow log for a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface and choose **Flow Logs**, **Create Flow Log**\.

1. For **Filter**, specify the type of IP traffic data to log\. Choose **All** to log accepted and rejected traffic, **Rejected** to record only rejected traffic, or **Accepted** to record only accepted traffic\.

1. Specify the destinations to which to publish the flow log data\. Flow log data can be published to CloudWatch Logs and Amazon S3\.

   1. To publish the flow log data to CloudWatch Logs, do the following:

      1. Choose **Send to CloudWatch Logs**\.

      1. For **Destination log group**, enter the name of a log group in CloudWatch Logs to which the flow logs are to be published\. You can use an existing log group or enter a name for a new log group\. If you specify the name of a log group that does not exist, we attempt to create the log group for you\.

      1. For **IAM role**, specify the name of the role that has permissions to publish logs to CloudWatch Logs\.

   1. To publish the flow log data to Amazon S3, do the following:

      1. Make sure that the Amazon S3 bucket to which to publish the flow log already exists\. If it does not, create a new Amazon S3 bucket\. For more information, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)\.

      1. Choose **Send to an Amazon S3 bucket**\.

      1. For **S3 bucket ARN**, specify the Amazon Resource Name \(ARN\) of the existing Amazon S3 bucket to which to publish the flow log data\.

         You can also specify a subfolder in the bucket\. To specify a subfolder in the bucket, use the following ARN format: `bucket_ARN/subfolder_name/`\. For example, to specify a subfolder named `my-logs` in a bucket named `my-bucket`, use the following ARN: `arn:aws:s3:::my-bucket/my-logs/`\. You cannot use `AWSLogs` as a subfolder name\. This is a reserved term\.
**Note**  
We automatically create a resource policy and attach it to the specified Amazon S3 bucket\. For more information, see [Amazon S3 Bucket Permissions for Flow Logs](flow-logs-s3.md#flow-logs-s3-permissions)\.

1. Choose **Create Flow Log**\.

**To create a flow log for a VPC or a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs** or **Subnets**\.

1. Select your VPC or subnet and choose **Flow Logs**, **Create Flow Log**\.
**Note**  
To create flow logs for multiple VPCs, choose the VPCs, and choose **Actions**, **Create Flow Log**\. To create flow logs for multiple subnets, choose the subnets, and choose **Subnet Actions**, **Create Flow Log**\.

1. For **Filter**, specify the type of IP traffic data to log\. Choose **All** to log accepted and rejected traffic, **Rejected** to record only rejected traffic, or **Accepted** to record only accepted traffic\.

1. Specify the destinations to which to publish the flow log data\. Flow log data can be published to CloudWatch Logs and Amazon S3\.

   1. To publish the flow log data to CloudWatch Logs, do the following:

      1. Choose **Send to CloudWatch Logs**\.

      1. For **Destination log group**, enter the name of a log group in CloudWatch Logs to which the flow logs are to be published\. You can use an existing log group, or you can enter a name for a new log group\. If you specify the name of a log group that does not exist, we attempt to create the log group for you\.

      1. For **IAM role**, specify the name of the IAM role that has permissions to publish logs to CloudWatch Logs\.
**Note**  
The Amazon Resource Name \(ARN\) of the selected IAM role is indicated next to the **IAM Role ARN** label\.

   1. To publish the flow log data to Amazon S3, do the following:

      1. Make sure that the Amazon S3 bucket to which to publish the flow log already exists\. If it does not, create a new Amazon S3 bucket\. For more information, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)\.

      1. Choose **Send to an Amazon S3 bucket**\.

      1. For **S3 bucket ARN**, specify the ARN of the existing Amazon S3 bucket to which you want to publish the flow log data\.

         You can also specify a subfolder in the bucket\. To specify a subfolder in the bucket, use the following ARN format: `bucket_ARN/subfolder_name/`\. For example, to specify a subfolder named `my-logs` in a bucket named `my-bucket`, use the following ARN: `arn:aws:s3:::my-bucket/my-logs/`\.
**Note**  
We automatically create a resource policy and attach it to the specified Amazon S3 bucket\. For more information, see [Amazon S3 Bucket Permissions for Flow Logs](flow-logs-s3.md#flow-logs-s3-permissions)\.

1. Choose **Create Flow Log**\.

## Viewing Flow Logs<a name="view-flow-logs"></a>

You can view information about your flow logs in the Amazon EC2 and Amazon VPC consoles by viewing the **Flow Logs** tab for a specific resource\. When you select the resource, all the flow logs for that resource are listed\. The information displayed includes the ID of the flow log, the flow log configuration, and information about the status of the flow log\.

**To view information about your flow logs for your network interfaces**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface, and choose **Flow Logs**\. Information about the flow logs is displayed on the tab\. The **Destination type** column indicates the destination to which the flow logs are published\.

**To view information about your flow logs for your VPCs or subnets**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs** or **Subnets**\.

1. Select your VPC or subnet, and choose **Flow Logs**\. Information about the flow logs is displayed on the tab\. The **Destination type** column indicates the destination to which the flow logs are published\.

## Viewing Flow Log Records<a name="view-flow-log-records"></a>

You can view your flow log records using the CloudWatch Logs console or Amazon S3 console, depending on the chosen destination type\. It may take a few minutes after you've created your flow log for it to be visible in the console\.

**To view flow log records published to CloudWatch Logs**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, and select the log group that contains your flow log\. A list of log streams for each network interface is displayed\.

1.  Select the log stream that contains the ID of the network interface for which to view the flow log records\. For more information, see [Flow Log Records](flow-logs.md#flow-log-records)\.

**To view flow log records published to Amazon S3**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. For **Bucket name**, select the bucket to which the flow logs are published\.

1. For **Name**, select the check box next to the log file\. On the object overview panel, choose **Download**\.

## Deleting a Flow Log<a name="delete-flow-log"></a>

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

## API and CLI Overview<a name="flow-logs-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interfaces and a list of available API actions, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create a flow log**
+ [create\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-flow-logs.html) \(AWS CLI\)
+ [New\-EC2FlowLogs](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)
+ [CreateFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateFlowLogs.html) \(Amazon EC2 Query API\)

**Describe your flow logs**
+ [describe\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-flow-logs.html) \(AWS CLI\)
+ [Get\-EC2FlowLogs](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeFlowLogs.html) \(Amazon EC2 Query API\)

**View your flow log records \(log events\)**
+ [get\-log\-events](https://docs.aws.amazon.com/cli/latest/reference/logs/get-log-events.html) \(AWS CLI\)
+ [Get\-CWLLogEvents](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-CWLLogEvents.html) \(AWS Tools for Windows PowerShell\)
+ [GetLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html) \(CloudWatch API\)

**Delete a flow log**
+ [delete\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-flow-logs.html) \(AWS CLI\)
+ [Remove\-EC2FlowLogs](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteFlowLogs.html) \(Amazon EC2 Query API\)