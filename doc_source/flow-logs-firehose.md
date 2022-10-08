# Publish flow logs to Kinesis Data Firehose<a name="flow-logs-firehose"></a>

Flow logs can publish flow log data directly to Kinesis Data Firehose\.

When publishing to Kinesis Data Firehose, flow log data is published to a Kinesis Data Firehose delivery stream, in plain text format\.

**Pricing**  
Standard ingestion and delivery charges apply\. For more information, open [Amazon CloudWatch Pricing](http://aws.amazon.com/cloudwatch/pricing), select **Logs** and find **Vended Logs**\.

**Topics**
+ [IAM roles for cross account delivery](#firehose-cross-account-delivery)
+ [Create a flow log that publishes to Kinesis Data Firehose](#flow-logs-firehose-create-flow-log)
+ [Process flow log records in Kinesis Data Firehose](#flow-logs-firehose-process-records)

## IAM roles for cross account delivery<a name="firehose-cross-account-delivery"></a>

When you publish to Kinesis Data Firehose, you can choose a delivery stream that's in the same account as the resource to monitor \(the source account\), or in a different account \(the destination account\)\. To enable cross account delivery of flow logs to Kinesis Data Firehose, you must create an IAM role in the source account and an IAM role in the destination account\.

**Topics**
+ [Source account role](#firehose-source-account-role)
+ [Destination account role](#firehose-destination-account-role)

### Source account role<a name="firehose-source-account-role"></a>

In the source account, create a role that grants the following permissions\. In this example, the name of the role is `mySourceRole`, but you can choose a different name for this role\. The last statement allows the role in the destination account to assume this role\. The condition statements ensure that this role is passed only to the log delivery service, and only when monitoring the specified resource\. When you create your policy, specify the VPCs, network interfaces, or subnets that you're monitoring with the condition key `iam:AssociatedResourceARN`\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "iam:PassRole",
      "Resource": "arn:aws:iam::source-account:role/mySourceRole",
      "Condition": {
          "StringEquals": {
              "iam:PassedToService": "delivery.logs.amazonaws.com"
          },
          "StringLike": {
              "iam:AssociatedResourceARN": [
                  "arn:aws:ec2:region:source-account:vpc/vpc-00112233344556677"
              ]
          }
      }
    },
    {
      "Effect": "Allow",
      "Action": [
          "logs:CreateLogDelivery",
          "logs:DeleteLogDelivery",
          "logs:ListLogDeliveries",
          "logs:GetLogDelivery"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::destination-account:role/AWSLogDeliveryFirehoseCrossAccountRole"      
    }
  ]
}
```

Ensure that this role has the following trust policy, which allows the log delivery service to assume the role\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "delivery.logs.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

From the source account, use the following procedure to create the role\.

**To create the source account role**

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

1. For **Trusted entity type**, choose **Custom trust policy**\. For **Custom trust policy**, replace `"Principal": {},` with the following, which specifies the log delivery service\. Choose **Next**\.

   ```
   "Principal": {
      "Service": "delivery.logs.amazonaws.com"
   },
   ```

1. On the **Add permissions** page, select the checkbox for the policy that you created earlier in this procedure, and then choose **Next**\.

1. Enter a name for your role and optionally provide a description\.

1. Choose **Create role**\.

### Destination account role<a name="firehose-destination-account-role"></a>

In the destination account, create a role with a name that starts with **AWSLogDeliveryFirehoseCrossAccountRole**\. This role must grant the following permissions\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
          "iam:CreateServiceLinkedRole",
          "firehose:TagDeliveryStream"
      ],
      "Resource": "*"
    }
  ]
}
```

Ensure that this role has the following trust policy, which allows the role that you created in the source account to assume this role\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
          "AWS": "arn:aws:iam::source-account:role/mySourceRole"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

From the destination account, use the following procedure to create the role\.

**To create the destination account role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. On the **Create policy** page, do the following:

   1. Choose **JSON**\.

   1. Replace the contents of this window with the permissions policy at the start of this section\.

   1. Choose **Next: Tags** and **Next: Review**\.

   1. Enter a name for your policy that starts with **AWSLogDeliveryFirehoseCrossAccountRole**, and then choose **Create policy**\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. For **Trusted entity type**, choose **Custom trust policy**\. For **Custom trust policy**, replace `"Principal": {},` with the following, which specifies the source account role\. Choose **Next**\.

   ```
   "Principal": {
      "AWS": "arn:aws:iam::source-account:role/mySourceRole"
   },
   ```

1. On the **Add permissions** page, select the checkbox for the policy that you created earlier in this procedure, and then choose **Next**\.

1. Enter a name for your role and optionally provide a description\.

1. Choose **Create role**\.

## Create a flow log that publishes to Kinesis Data Firehose<a name="flow-logs-firehose-create-flow-log"></a>

You can create flow logs for your VPCs, subnets, or network interfaces\.

**Prerequisites**
+ Create the destination Kinesis Data Firehose delivery stream\. Use **Direct Put** as the source\. For more information, see [Creating an Kinesis Data Firehose delivery stream](https://docs.aws.amazon.com/firehose/latest/dev/basic-create.html)\.
+ If you're publishing flow logs to a different account, create the required IAM roles, as described in [IAM roles for cross account delivery](#firehose-cross-account-delivery)\.

**To create a flow log that publishes to Kinesis Data Firehose**

1. Do one of the following:
   + Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\. In the navigation pane, choose **Network Interfaces**\. Select the checkbox for the network interface\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Your VPCs**\. Select the checkbox for the VPC\.
   + Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. In the navigation pane, choose **Subnets**\. Select the checkbox for the subnet\.

1. Choose **Actions**, **Create flow log**\.

1. For **Filter**, specify the type of traffic to log\.
   + **Accept** – Log only accepted traffic
   + **Reject** – Log only rejected traffic
   + **All** – Log accepted and rejected traffic

1. For **Maximum aggregation interval**, choose the maximum period of time during which a flow is captured and aggregated into one flow log record\.

1. For **Destination**, choose either of the following options:
   + **Send to Kinesis Data Firehose in the same account** – The delivery stream and the resource to monitor are in the same account\.
   + **Send to Kinesis Data Firehose in a different account** – The delivery stream and the resource to monitor are in different accounts\.

1. For **Kinesis Data Firehose delivery stream**, choose the delivery stream that you created\.

1. \[Cross account delivery only\] For **IAM roles**, specify the required roles \(see [IAM roles for cross account delivery](#firehose-cross-account-delivery)\)\.

1. \(Optional\) Choose **Add new tag** to apply tags to the flow log\.

1. Choose **Create flow log**\.

**To create a flow log that publishes to Kinesis Data Firehose using a command line tool**

Use one of the following commands:
+ [create\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-flow-logs.html) \(AWS CLI\)
+ [New\-EC2FlowLogs](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)

The following AWS CLI example creates a flow log that captures all traffic for the specified VPC and delivers the flow logs to the specified Kinesis Data Firehose delivery stream in the same account\.

```
aws ec2 create-flow-logs --traffic-type ALL \
  --resource-type VPC \
  --resource-ids vpc-00112233344556677 \
  --log-destination-type kinesis-data-firehose \
  --log-destination arn:aws:firehose:us-east-1:123456789012:deliverystream:flowlogs_stream
```

The following AWS CLI example creates a flow log that captures all traffic for the specified VPC and delivers the flow logs to the specified Kinesis Data Firehose delivery stream in a different account\.

```
aws ec2 create-flow-logs --traffic-type ALL \
  --resource-type VPC \
  --resource-ids vpc-00112233344556677 \
  --log-destination-type kinesis-data-firehose \
  --log-destination arn:aws:firehose:us-east-1:123456789012:deliverystream:flowlogs_stream \
  --deliver-logs-permission-arn arn:aws:iam::source-account:role/mySourceRole \ 
  --deliver-cross-account-role arn:aws:iam::destination-account:role/AWSLogDeliveryFirehoseCrossAccountRole
```

## Process flow log records in Kinesis Data Firehose<a name="flow-logs-firehose-process-records"></a>

You can get the flow log data from the destination that you configured for the delivery stream\.