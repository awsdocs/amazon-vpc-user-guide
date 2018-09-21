# Publishing Flow Logs to Amazon S3<a name="flow-logs-s3"></a>

Flow logs can publish flow log data directly to Amazon S3\.

When publishing to Amazon S3, flow log data is published to an existing Amazon S3 bucket that you specify\. Flow log records for all of the monitored network interfaces are published to a series of log file objects that are stored in the bucket\. If the flow log captures data for a VPC, the flow log publishes flow log records for all of the network interfaces in the selected VPC\. For more information, see [Flow Log Records](flow-logs.md#flow-log-records)\.

**Topics**
+ [Flow Log Files](#flow-logs-s3-path)
+ [Processing Flow Log Records](#process-records-s3)
+ [IAM Roles for Publishing Flow Logs to Amazon S3](#flow-logs-s3-iam)
+ [Amazon S3 Bucket Permissions for Flow Logs](#flow-logs-s3-permissions)
+ [Amazon S3 Log File Permissions](#flow-logs-file-permissions)

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

For example, the following shows the folder structure and file name of a log file for a flow log created by AWS account `123456789012`, for a resource in the `us-east-1` region, on `June 20, 2018` at `16:20 UTC`, which includes flow log records for 16:15:01 to 16:20:00: 

```
arn:aws:s3:::my-flow-log-bucket/AWSLogs/123456789012/vpcflowlogs/us-east-1/2018/06/20/123456789012_vpcflowlogs_us-east-1_fl-1234abcd_20180620T1620Z_fe123456.log.gz
```

## Processing Flow Log Records<a name="process-records-s3"></a>

The log files are compressed\. If you open the log files using the Amazon S3 console, they are decompressed and the flow log records are displayed\. If you download the files, you must decompress them to view the flow log records\.

You can also query the flow log records in the log files using Amazon Athena\. Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL\. For more information, see [Querying Amazon VPC Flow Logs](https://docs.aws.amazon.com/athena/latest/ug/vpc-flow-logs.html) in the *Amazon Athena User Guide*\.

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

## Amazon S3 Log File Permissions<a name="flow-logs-file-permissions"></a>

In addition to the required bucket policies, Amazon S3 uses access control lists \(ACLs\) to manage access to the log files created by a flow log\. By default, the bucket owner has `FULL_CONTROL` permissions on each log file\. The log delivery owner, if different from the bucket owner, has no permissions\. The log delivery account has `READ` and `WRITE` permissions\. For more information, see [Access Control List \(ACL\) Overview](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) in the *Amazon Simple Storage Service Developer Guide*\.