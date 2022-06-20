# Publish flow logs to Amazon S3<a name="flow-logs-s3"></a>

Flow logs can publish flow log data to Amazon S3\.

When publishing to Amazon S3, flow log data is published to an existing Amazon S3 bucket that you specify\. Flow log records for all of the monitored network interfaces are published to a series of log file objects that are stored in the bucket\. If the flow log captures data for a VPC, the flow log publishes flow log records for all of the network interfaces in the selected VPC\.

Data ingestion and archival charges for vended logs apply when you publish flow logs to Amazon S3\. For more information, see [Amazon CloudWatch Pricing](http://aws.amazon.com/cloudwatch/pricing)\.

To create an Amazon S3 bucket for use with flow logs, see [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service User Guide*\.

For more information about multiple account logging, see [Central Logging](http://aws.amazon.com/solutions/implementations/centralized-logging/) in the AWS Solutions Library\.

For more information about CloudWatch Logs, see [Logs sent to Amazon S3](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AWS-logs-and-resource-policy.html#AWS-logs-infrastructure-S3) in the *Amazon CloudWatch Logs User Guide*\.

**Topics**
+ [Flow log files](#flow-logs-s3-path)
+ [IAM policy for IAM principals that publish flow logs to Amazon S3](#flow-logs-s3-iam)
+ [Amazon S3 bucket permissions for flow logs](#flow-logs-s3-permissions)
+ [Required key policy for use with SSE\-KMS](#flow-logs-s3-cmk-policy)
+ [Amazon S3 log file permissions](#flow-logs-file-permissions)
+ [Create a flow log that publishes to Amazon S3](#flow-logs-s3-create-flow-log)
+ [Process flow log records in Amazon S3](#process-records-s3)

## Flow log files<a name="flow-logs-s3-path"></a>

VPC Flow Logs collects flow log records, consolidates them into log files, and then publishes the log files to the Amazon S3 bucket at 5\-minute intervals\. Each log file contains flow log records for the IP traffic recorded in the previous five minutes\.

The maximum file size for a log file is 75 MB\. If the log file reaches the file size limit within the 5\-minute period, the flow log stops adding flow log records to it\. Then it publishes the flow log to the Amazon S3 bucket, and creates a new log file\.

In Amazon S3, the **Last modified** field for the flow log file indicates the date and time at which the file was uploaded to the Amazon S3 bucket\. This is later than the timestamp in the file name, and differs by the amount of time taken to upload the file to the Amazon S3 bucket\.

**Log file format**

You can specify one of the following formats for the log files\. Each file is compressed into a single Gzip file\.
+ **Text** – Plain text\. This is the default format\.
+ **Parquet** – Apache Parquet is a columnar data format\. Queries on data in Parquet format are 10 to 100 times faster compared to queries on data in plain text\. Data in Parquet format with Gzip compression takes 20 percent less storage space than plain text with Gzip compression\.

**Log file options**

You can optionally specify the following options\.
+ **Hive\-compatible S3 prefixes** – Enable Hive\-compatible prefixes instead of importing partitions into your Hive\-compatible tools\. Before you run queries, use the MSCK REPAIR TABLE command\.
+ **Hourly partitions** – If you have a large volume of logs and typically target queries to a specific hour, you can get faster results and save on query costs by partitioning logs on an hourly basis\.

**Log file S3 bucket structure**  
Log files are saved to the specified Amazon S3 bucket using a folder structure that is based on the flow log's ID, Region, creation date, and destination options\.

By default, the files are delivered to the following location\.

```
bucket-and-optional-prefix/AWSLogs/account_id/vpcflowlogs/region/year/month/day/
```

If you enable Hive\-compatible S3 prefixes, the files are delivered to the following location\.

```
bucket-and-optional-prefix/AWSLogs/aws-account-id=account_id/service=vpcflowlogs/aws-region=region/year=year/month=month/day=day/
```

If you enable hourly partitions, the files are delivered to the following location\.

```
bucket-and-optional-prefix/AWSLogs/account_id/vpcflowlogs/region/year/month/day/hour/
```

If you enable Hive\-compatible partitions and partition the flow log per hour, the files are delivered to the following location\.

```
bucket-and-optional-prefix/AWSLogs/aws-account-id=account_id/service=vpcflowlogs/aws-region=region/year=year/month=month/day=day/hour=hour/
```

**Log file names**  
The file name of a log file is based on the flow log ID, Region, and creation date and time\. File names use the following format\.

```
aws_account_id_vpcflowlogs_region_flow_log_id_YYYYMMDDTHHmmZ_hash.log.gz
```

The following is an example of a log file for a flow log created by AWS account 123456789012, for a resource in the us\-east\-1 Region, on June 20, 2018 at 16:20 UTC\. The file contains the flow log records with an end time between 16:20:00 and 16:24:59\.

```
123456789012_vpcflowlogs_us-east-1_fl-1234abcd_20180620T1620Z_fe123456.log.gz
```

## IAM policy for IAM principals that publish flow logs to Amazon S3<a name="flow-logs-s3-iam"></a>

The IAM principal that creates the flow log, such as an IAM user, must have the following permissions, which are required to publish flow logs to the destination Amazon S3 bucket\.

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

## Amazon S3 bucket permissions for flow logs<a name="flow-logs-s3-permissions"></a>

By default, Amazon S3 buckets and the objects they contain are private\. Only the bucket owner can access the bucket and the objects stored in it\. However, the bucket owner can grant access to other resources and users by writing an access policy\.

If the user creating the flow log owns the bucket and has `PutBucketPolicy` and `GetBucketPolicy` permissions for the bucket, we automatically attach the following policy to the bucket\. This policy overwrites any existing policy attached to the bucket\.

Otherwise, the bucket owner must add this policy to the bucket, specifying the AWS account ID of the flow log creator, or flow log creation fails\. For more information, see [Using bucket policies](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/bucket-policies.html) in the *Amazon Simple Storage Service User Guide*\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSLogDeliveryWrite",
            "Effect": "Allow",
            "Principal": {"Service": "delivery.logs.amazonaws.com"},
            "Action": "s3:PutObject",
            "Resource": "my-s3-arn",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "bucket-owner-full-control",
                    "aws:SourceAccount": account_id
                },
                "ArnLike": {
                    "aws:SourceArn": "arn:aws:logs:region:account_id:*"
                }
            }
        },
        {
            "Sid": "AWSLogDeliveryCheck",
            "Effect": "Allow",
            "Principal": {"Service": "delivery.logs.amazonaws.com"},
            "Action": ["s3:GetBucketAcl", "s3:ListBucket"],
            "Resource": "arn:aws:s3:::bucket_name",
            "Condition": {
                "StringEquals": {
                    "aws:SourceAccount": account_id
                },
                "ArnLike": {
                    "aws:SourceArn": "arn:aws:logs:region:account_id:*"
                }
            }
        }
    ]
}
```

The ARN that you specify for *my\-s3\-arn* depends on whether you use Hive\-compatible S3 prefixes\.
+ Default prefixes

  ```
  arn:aws:s3:::bucket_name/optional_folder/AWSLogs/account_id/*
  ```
+ Hive\-compatible S3 prefixes

  ```
  arn:aws:s3:::bucket_name/optional_folder/AWSLogs/aws-account-id=account_id/*
  ```

It is a best practice to grant these permissions to the log delivery service principal instead of individual AWS account ARNs\. It is also a best practice to use the `aws:SourceAccount` and `aws:SourceArn` condition keys to protect against [the confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\. The source account is the owner of the flow log and the source ARN is the wildcard \(\*\) ARN of the logs service\.

## Required key policy for use with SSE\-KMS<a name="flow-logs-s3-cmk-policy"></a>

You can protect the data in your Amazon S3 bucket by enabling either Server\-Side Encryption with Amazon S3\-Managed Keys \(SSE\-S3\) or Server\-Side Encryption with KMS Keys \(SSE\-KMS\)\. For more information, see [Protecting data using server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html) in the *Amazon S3 User Guide*\.

With SSE\-KMS, you can use either an AWS managed key or a customer managed key\. With an AWS managed key, you can't use cross\-account delivery\. Flow logs are delivered from the log delivery account, so you must grant access for cross\-account delivery\. To grant cross\-account access to your S3 bucket, use a customer managed key and specify the Amazon Resource Name \(ARN\) of the customer managed key when you enable bucket encryption\. For more information, see [Specifying server\-side encryption with AWS KMS](https://docs.aws.amazon.com/AmazonS3/latest/userguide/specifying-kms-encryption.html) in the *Amazon S3 User Guide*\.

When you use SSE\-KMS with a customer managed key, you must add the following to the key policy for your key \(not the bucket policy for your S3 bucket\), so that VPC Flow Logs can write to your S3 bucket\.

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

## Amazon S3 log file permissions<a name="flow-logs-file-permissions"></a>

In addition to the required bucket policies, Amazon S3 uses access control lists \(ACLs\) to manage access to the log files created by a flow log\. By default, the bucket owner has `FULL_CONTROL` permissions on each log file\. The log delivery owner, if different from the bucket owner, has no permissions\. The log delivery account has `READ` and `WRITE` permissions\. For more information, see [Access Control List \(ACL\) Overview](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) in the *Amazon Simple Storage Service User Guide*\.

## Create a flow log that publishes to Amazon S3<a name="flow-logs-s3-create-flow-log"></a>

After you have created and configured your Amazon S3 bucket, you can create flow logs for your network interfaces, subnets, and VPCs\.

**To create a flow log for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkboxes for one or more network interfaces\.

1. Choose **Actions**, **Create flow log**\.

1. Configure the flow log settings\. For more information, see [To configure flow log settings](#configure-flow-log)\.

**To create a flow log for a subnet using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the checkboxes for one or more subnets\.

1. Choose **Actions**, **Create flow log**\.

1. Configure the flow log settings\. For more information, see [To configure flow log settings](#configure-flow-log)\.

**To create a flow log for a VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the checkboxes for one or more VPCs\.

1. Choose **Actions**, **Create flow log**\.

1. Configure the flow log settings\. For more information, see [To configure flow log settings](#configure-flow-log)\.<a name="configure-flow-log"></a>

**To configure flow log settings using the console**

1. For **Filter**, specify the type of IP traffic data to log\.
   + **Accepted** – Log only accepted traffic\.
   + **Rejected** – Log only rejected traffic\.
   + **All** – Log accepted and rejected traffic\.

1. For **Maximum aggregation interval**, choose the maximum period of time during which a flow is captured and aggregated into one flow log record\.

1. For **Destination**, choose **Send to an S3 bucket**\.

1. For **S3 bucket ARN**, specify the Amazon Resource Name \(ARN\) of an existing Amazon S3 bucket\. You can optionally include a subfolder\. For example, to specify a subfolder named `my-logs` in a bucket named `my-bucket`, use the following ARN:

   `arn:aws::s3:::my-bucket/my-logs/`

   The bucket cannot use `AWSLogs` as a subfolder name, as this is a reserved term\.

   If you own the bucket, we automatically create a resource policy and attach it to the bucket\. For more information, see [Amazon S3 bucket permissions for flow logs](#flow-logs-s3-permissions)\.

1. For **Log record format**, specify the format for the flow log record\.
   + To use the default flow log record format, choose **AWS default format**\.
   + To create a custom format, choose **Custom format**\. For **Log format**, choose the fields to include in the flow log record\.

1. For **Log file format**, specify the format for the log file\.
   + **Text** – Plain text\. This is the default format\.
   + **Parquet** – Apache Parquet is a columnar data format\. Queries on data in Parquet format are 10 to 100 times faster compared to queries on data in plain text\. Data in Parquet format with Gzip compression takes 20 percent less storage space than plain text with Gzip compression\.

1. \(Optional\) To use Hive\-compatible S3 prefixes, choose **Hive\-compatible S3 prefix**, **Enable**\.

1. \(Optional\) To partition your flow logs per hour, choose **Every 1 hour \(60 mins\)**\.

1. \(Optional\) To add a tag to the flow log, choose **Add new tag** and specify the tag key and value\.

1. Choose **Create flow log**\.

**To create a flow log that publishes to Amazon S3 using a command line tool**

Use one of the following commands\.
+ [create\-flow\-logs](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-flow-logs.html) \(AWS CLI\)
+ [New\-EC2FlowLogs](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2FlowLogs.html) \(AWS Tools for Windows PowerShell\)
+ [CreateFlowLogs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateFlowLogs.html) \(Amazon EC2 Query API\)

The following AWS CLI example creates a flow log that captures all traffic for VPC `vpc-00112233344556677` and delivers the flow logs to an Amazon S3 bucket called `flow-log-bucket`\. The `--log-format` parameter specifies a custom format for the flow log records\.

```
aws ec2 create-flow-logs --resource-type VPC --resource-ids vpc-00112233344556677 --traffic-type ALL --log-destination-type s3 --log-destination arn:aws:s3:::flow-log-bucket/my-custom-flow-logs/ --log-format '${version} ${vpc-id} ${subnet-id} ${instance-id} ${srcaddr} ${dstaddr} ${srcport} ${dstport} ${protocol} ${tcp-flags} ${type} ${pkt-srcaddr} ${pkt-dstaddr}'
```

## Process flow log records in Amazon S3<a name="process-records-s3"></a>

The log files are compressed\. If you open the log files using the Amazon S3 console, they are decompressed and the flow log records are displayed\. If you download the files, you must decompress them to view the flow log records\.

You can also query the flow log records in the log files using Amazon Athena\. Amazon Athena is an interactive query service that makes it easier to analyze data in Amazon S3 using standard SQL\. For more information, see [Querying Amazon VPC Flow Logs](https://docs.aws.amazon.com/athena/latest/ug/vpc-flow-logs.html) in the *Amazon Athena User Guide*\.