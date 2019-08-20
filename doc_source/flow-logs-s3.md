# Publishing Flow Logs to Amazon S3<a name="flow-logs-s3"></a>

Flow logs can publish flow log data to Amazon S3\.

When publishing to Amazon S3, flow log data is published to an existing Amazon S3 bucket that you specify\. Flow log records for all of the monitored network interfaces are published to a series of log file objects that are stored in the bucket\. If the flow log captures data for a VPC, the flow log publishes flow log records for all of the network interfaces in the selected VPC\. For more information, see [Flow Log Records](flow-logs.md#flow-log-records)\.

To create an Amazon S3 bucket for use with flow logs, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

**Topics**
+ [Flow Log Files](#flow-logs-s3-path)
+ [IAM Roles for Publishing Flow Logs to Amazon S3](#flow-logs-s3-iam)
+ [Amazon S3 Bucket Permissions for Flow Logs](#flow-logs-s3-permissions)
+ [Required CMK Key Policy for Use with SSE\-KMS Buckets](#flow-logs-s3-cmk-policy)
+ [Amazon S3 Log File Permissions](#flow-logs-file-permissions)
+ [Creating a Flow Log that Publishes to Amazon S3](#flow-logs-s3-create-flow-log)
+ [Processing Flow Log Records in Amazon S3](#process-records-s3)

## Flow Log Files<a name="flow-logs-s3-path"></a>

Flow logs collect flow log records, consolidate them into log files, and then publish the log files to the Amazon S3 bucket at 5\-minute intervals\. Each log file contains flow log records for the IP traffic recorded in the previous five minutes\.

The maximum file size for a log file is 75 MB\. If the log file reaches the file size limit within the 5\-minute period, the flow log stops adding flow log records to it, publishes it to the Amazon S3 bucket, and then creates a new log file\.

Log files are saved to the specified Amazon S3 bucket using a folder structure that is determined by the flow log's ID, Region, and the date on which they are created\. The bucket folder structure uses the following format:

```
bucket_ARN/optional_folder/AWSLogs/aws_account_id/vpcflowlogs/region/year/month/day/log_file_name.log.gz
```

Similarly, the log file's file name is determined by the flow log's ID, Region, and the date and time it was created\. File names use the following format:

```
aws_account_id_vpcflowlogs_region_flow_log_id_timestamp_hash.log.gz
```

**Note**  
The time stamp uses the `YYYYMMDDTHHmmZ` format\.

For example, the following shows the folder structure and file name of a log file for a flow log created by AWS account `123456789012`, for a resource in the `us-east-1` region, on `June 20, 2018` at `16:20 UTC`, which includes flow log records for `16:15:00` to `16:19:59`: 

```
arn:aws:s3:::my-flow-log-bucket/AWSLogs/123456789012/vpcflowlogs/us-east-1/2018/06/20/123456789012_vpcflowlogs_us-east-1_fl-1234abcd_20180620T1620Z_fe123456.log.gz
```

## IAM Roles for Publishing Flow Logs to Amazon S3<a name="flow-logs-s3-iam"></a>

An IAM principal, such as an IAM user, must have sufficient permissions to publish flow logs to the Amazon S3 bucket\. The IAM policy must include the following permissions:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogDelivery",
        "logs:DeleteLogDelivery"
        ],
      "Resource": "*"
    }
  ]
}
```

## Amazon S3 Bucket Permissions for Flow Logs<a name="flow-logs-s3-permissions"></a>

By default, Amazon S3 buckets and the objects they contain are private\. Only the bucket owner can access the bucket and the objects stored in it\. The bucket owner however, can grant access to other resources and users by writing an access policy\.

If the user creating the flow log owns the bucket, we automatically attach the following policy to the bucket to give the flow log permission to publish logs to it\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSLogDeliveryWrite",
            "Effect": "Allow",
            "Principal": {"Service": "delivery.logs.amazonaws.com"},
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::bucket_name/optional_folder/AWSLogs/account_id/*",
            "Condition": {"StringEquals": {"s3:x-amz-acl": "bucket-owner-full-control"}}
        },
        {
            "Sid": "AWSLogDeliveryAclCheck",
            "Effect": "Allow",
            "Principal": {"Service": "delivery.logs.amazonaws.com"},
            "Action": "s3:GetBucketAcl",
            "Resource": "arn:aws:s3:::bucket_name"
        }
    ]
}
```

If the user creating the flow log does not own the bucket, or does not have the `GetBucketPolicy` and `PutBucketPolicy` permissions for the bucket, the flow log creation fails\. In this case, the bucket owner must manually add the above policy to the bucket and specify the flow log creator's AWS account ID\. For more information, see [How Do I Add an S3 Bucket Policy?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html) in the *Amazon Simple Storage Service Console User Guide*\. If the bucket receives flow logs from multiple accounts, add a `Resource` element entry to the `AWSLogDeliveryWrite` policy statement for each account\. For example, the following bucket policy allows AWS accounts `123123123123` and `456456456456` to publish flow logs to a folder named `flow-logs` in a bucket named `log-bucket`\.
Destination S3 Bucket should not have any Encryption enabled, otherwise you will get permission denied error\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSLogDeliveryWrite",
            "Effect": "Allow",
            "Principal": {"Service": "delivery.logs.amazonaws.com"},
            "Action": "s3:PutObject",
            "Resource": [
            	"arn:aws:s3:::log-bucket/flow-logs/AWSLogs/123123123123/*",
            	"arn:aws:s3:::log-bucket/flow-logs/AWSLogs/456456456456/*"
            	],
            "Condition": {"StringEquals": {"s3:x-amz-acl": "bucket-owner-full-control"}}
        },
        {
            "Sid": "AWSLogDeliveryAclCheck",
            "Effect": "Allow",
            "Principal": {"Service": "delivery.logs.amazonaws.com"},
            "Action": "s3:GetBucketAcl",
            "Resource": "arn:aws:s3:::log-bucket"
        }
    ]
}
```

**Note**  
We recommend that you grant the AWSLogDeliveryAclCheck and AWSLogDeliveryWrite permissions to the *log delivery* service principal instead of individual AWS account ARNs\.

## Required CMK Key Policy for Use with SSE\-KMS Buckets<a name="flow-logs-s3-cmk-policy"></a>

If you enabled server\-side encryption for your Amazon S3 bucket using AWS KMS\-managed keys \(SSE\-KMS\) with a customer\-managed Customer Master Key \(CMK\), you must add the following to the key policy for your CMK so that flow logs can write log files to the bucket\.

```
{
    "Sid": "Allow VPC Flow Logs to use the key",
    "Effect": "Allow",
    "Principal": {
        "Service": [
            "delivery.logs.amazonaws.com"
        ]
    },
   "Action": [
       "kms:Encrypt",
       "kms:Decrypt",
       "kms:ReEncrypt*",
       "kms:GenerateDataKey*",
       "kms:DescribeKey"
    ],
    "Resource": "*"
}
```

## Amazon S3 Log File Permissions<a name="flow-logs-file-permissions"></a>

In addition to the required bucket policies, Amazon S3 uses access control lists \(ACLs\) to manage access to the log files created by a flow log\. By default, the bucket owner has `FULL_CONTROL` permissions on each log file\. The log delivery owner, if different from the bucket owner, has no permissions\. The log delivery account has `READ` and `WRITE` permissions\. For more information, see [Access Control List \(ACL\) Overview](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) in the *Amazon Simple Storage Service Developer Guide*\.

## Creating a Flow Log that Publishes to Amazon S3<a name="flow-logs-s3-create-flow-log"></a>

After you have created and configured your Amazon S3 bucket, you can create flow logs for your VPCs, subnets, or network interfaces\.

**To create a flow log for a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select one or more network interfaces and choose **Actions**, **Create flow log**\.

1. For **Filter**, specify the type of IP traffic data to log\. Choose **All** to log accepted and rejected traffic, **Rejected** to record only rejected traffic, or **Accepted** to record only accepted traffic\.

1. For **Destination**, choose **Send to an Amazon S3 bucket**\.

1. For **S3 bucket ARN**, specify the Amazon Resource Name \(ARN\) of an existing Amazon S3 bucket\. You can include a subfolder in the bucket ARN\. The bucket cannot use `AWSLogs` as a subfolder name, as this is a reserved term\.

   For example, to specify a subfolder named `my-logs` in a bucket named `my-bucket`, use the following ARN:

   `arn:aws:s3:::my-bucket/my-logs/`

   If you own the bucket, we automatically create a resource policy and attach it to the bucket\. For more information, see [Amazon S3 Bucket Permissions for Flow Logs](#flow-logs-s3-permissions)\.

1. Choose **Create**\.

**To create a flow log for a VPC or a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs** or **Subnets**\.

1. Select one or more VPCs or subnets and then choose **Actions**, **Create flow log**\.

1. For **Filter**, specify the type of IP traffic data to log\. Choose **All** to log accepted and rejected traffic, **Rejected** to record only rejected traffic, or **Accepted** to record only accepted traffic\.

1. For **Destination**, choose **Send to an Amazon S3 bucket**\.

1. For **S3 bucket ARN**, specify the Amazon Resource Name \(ARN\) of an existing Amazon S3 bucket\. You can include a subfolder in the bucket ARN\. The bucket cannot use `AWSLogs` as a subfolder name, as this is a reserved term\.

   For example, to specify a subfolder named `my-logs` in a bucket named `my-bucket`, use the following ARN:

   `arn:aws:s3:::my-bucket/my-logs/`

   If you own the bucket, we automatically create a resource policy and attach it to the bucket\. For more information, see [Amazon S3 Bucket Permissions for Flow Logs](#flow-logs-s3-permissions)\.

1. Choose **Create**\.

## Processing Flow Log Records in Amazon S3<a name="process-records-s3"></a>

The log files are compressed\. If you open the log files using the Amazon S3 console, they are decompressed and the flow log records are displayed\. If you download the files, you must decompress them to view the flow log records\.

You can also query the flow log records in the log files using Amazon Athena\. Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL\. For more information, see [Querying Amazon VPC Flow Logs](https://docs.aws.amazon.com/athena/latest/ug/vpc-flow-logs.html) in the *Amazon Athena User Guide*\.
