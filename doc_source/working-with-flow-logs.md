# Work with flow logs<a name="working-with-flow-logs"></a>

You can work with flow logs using consoles for Amazon EC2 and Amazon VPC\.

**Topics**
+ [Control the use of flow logs](#controlling-use-of-flow-logs)
+ [Create a flow log](#create-flow-log)
+ [View a flow log](#view-flow-logs)
+ [Tag a flow log](#modify-tags-flow-logs)
+ [Delete a flow log](#delete-flow-log)
+ [API and CLI overview](#flow-logs-api-cli)

## Control the use of flow logs<a name="controlling-use-of-flow-logs"></a>

By default, IAM users do not have permission to work with flow logs\. You can create an IAM user policy that grants users the permissions to create, describe, and delete flow logs\.

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

 For more information, see [How Amazon VPC works with IAM](security_iam_service-with-iam.md)\.

## Create a flow log<a name="create-flow-log"></a>

You can create flow logs for your VPCs, subnets, or network interfaces\. When you create a flow log, you must specify a destination for the flow log\. For more information, see the following:
+ [Create a flow log that publishes to CloudWatch Logs](flow-logs-cwl.md#flow-logs-cwl-create-flow-log)
+ [Create a flow log that publishes to Amazon S3](flow-logs-s3.md#flow-logs-s3-create-flow-log)
+ [Create a flow log that publishes to Kinesis Data Firehose](flow-logs-firehose.md#flow-logs-firehose-create-flow-log)

## View a flow log<a name="view-flow-logs"></a>

You can view information about the flow logs for a resource, such as a network interface\. The information displayed includes the ID of the flow log, the flow log configuration, and information about the status of the flow log\.

**To view information about flow logs**

1. Do one of the following:
   + Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\. In the navigation pane, choose **Network Interfaces**\. Select the checkbox for the network interface\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Your VPCs**\. Select the checkbox for the VPC\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Subnets**\. Select the checkbox for the subnet\.

1. Choose **Flow Logs**\.

1. \(Optional\) To view the flow log data, open the log destination\.

## Tag a flow log<a name="modify-tags-flow-logs"></a>

You can add or remove tags for a flow log at any time\.

**To manage tags for a flow log**

1. Do one of the following:
   + Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\. In the navigation pane, choose **Network Interfaces**\. Select the checkbox for the network interface\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Your VPCs**\. Select the checkbox for the VPC\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Subnets**\. Select the checkbox for the subnet\.

1. Choose **Flow Logs**\.

1. Choose **Actions**, **Manage tags**\.

1. To add a new tag, choose **Add new tag** and enter the key and value\. To remove a tag, choose **Remove**\.

1. When you are finished adding or removing tags, choose **Save**\.

## Delete a flow log<a name="delete-flow-log"></a>

You can delete a flow log at any time\. After you delete a flow log, it can take several minutes to stop collecting data\.

Deleting a flow log does not delete the log data from the destination or modify the destination resource\. You must delete the existing flow log data directly from the destination, and clean up the destination resource, using the console for the destination service\.

**To delete a flow log**

1. Do one of the following:
   + Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\. In the navigation pane, choose **Network Interfaces**\. Select the checkbox for the network interface\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Your VPCs**\. Select the checkbox for the VPC\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Subnets**\. Select the checkbox for the subnet\.

1. Choose **Flow Logs**\.

1. Choose **Actions**, **Delete flow logs**\.

1. When prompted for confirmation, type **delete** and then choose **Delete**\.

## API and CLI overview<a name="flow-logs-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interfaces and a list of available API actions, see [Working with Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create a flow log**
+ [create\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-flow-logs.html) \(AWS CLI\)
+ [New\-EC2FlowLog](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2FlowLog.html) \(AWS Tools for Windows PowerShell\)
+ [CreateFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateFlowLogs.html) \(Amazon EC2 Query API\)

**Describe a flow log**
+ [describe\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-flow-logs.html) \(AWS CLI\)
+ [Get\-EC2FlowLog](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2FlowLog.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeFlowLogs.html) \(Amazon EC2 Query API\)

**Tag a flow log**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) and [delete\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) and [Remove\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html) \(AWS Tools for Windows PowerShell\)
+ [CreateTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateTags.html) and [DeleteTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteTags.html) \(Amazon EC2 Query API\)

**Delete a flow log**
+ [delete\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-flow-logs.html) \(AWS CLI\)
+ [Remove\-EC2FlowLog](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2FlowLog.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteFlowLogs.html) \(Amazon EC2 Query API\)