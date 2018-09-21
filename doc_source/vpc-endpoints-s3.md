# Endpoints for Amazon S3<a name="vpc-endpoints-s3"></a>

If you've already set up access to your Amazon S3 resources from your VPC, you can continue to use Amazon S3 DNS names to access those resources after you've set up an endpoint\. However, take note of the following:
+ Your endpoint has a policy that controls the use of the endpoint to access Amazon S3 resources\. The default policy allows access by any user or service within the VPC, using credentials from any AWS account, to any Amazon S3 resource; including Amazon S3 resources for an AWS account other than the account with which the VPC is associated\. For more information, see [Controlling Access to Services with VPC Endpoints](vpc-endpoints-access.md)\.
+ The source IPv4 addresses from instances in your affected subnets as received by Amazon S3 change from public IPv4 addresses to the private IPv4 addresses from your VPC\. An endpoint switches network routes, and disconnects open TCP connections\. Your tasks are interrupted during the changeover, and any previous connections using public IPv4 addresses are not resumed\. We recommend that you do not have any critical tasks running when you create or modify an endpoint; or that you test to ensure that your software can automatically reconnect to Amazon S3 after the connection break\.
+ You cannot use an IAM policy or bucket policy to allow access from a VPC IPv4 CIDR range \(the private IPv4 address range\)\. VPC CIDR blocks can be overlapping or identical, which may lead to unexpected results\. Therefore, you cannot use the `aws:SourceIp` condition in your IAM policies for requests to Amazon S3 through a VPC endpoint\. This applies to IAM policies for users and roles, and any bucket policies\. If a statement includes the `aws:SourceIp` condition, the value fails to match any provided IP address or range\. Instead, you can do the following:
  + Use your route tables to control which instances can access resources in Amazon S3 via the endpoint\.
  + For bucket policies, you can restrict access to a specific endpoint or to a specific VPC\. For more information, see [Using Amazon S3 Bucket Policies](#vpc-endpoints-s3-bucket-policies)\. 
+ Endpoints currently do not support cross\-region requests—ensure that you create your endpoint in the same region as your bucket\. You can find the location of your bucket by using the Amazon S3 console, or by using the [get\-bucket\-location](https://docs.aws.amazon.com/cli/latest/reference/s3api/get-bucket-location.html) command\. Use a region\-specific Amazon S3 endpoint to access your bucket; for example, `mybucket.s3-us-west-2.amazonaws.com`\. For more information about region\-specific endpoints for Amazon S3, see [Amazon Simple Storage Service \(S3\)](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) in *Amazon Web Services General Reference*\. If you use the AWS CLI to make requests to Amazon S3, set your default region to the same region as your bucket, or use the `--region` parameter in your requests\.
**Note**  
Treat Amazon S3's US Standard region as mapped to the `us-east-1` region\.
+ Endpoints are currently supported for IPv4 traffic only\.

Before you use endpoints with Amazon S3, ensure that you have also read the following general limitations: [Gateway Endpoint Limitations](vpce-gateway.md#vpc-endpoints-limitations)\.

If you use other AWS services in your VPC, they may use S3 buckets for certain tasks\. Ensure that your endpoint policy allows full access to Amazon S3 \(the default policy\), or that it allows access to the specific buckets that are used by these services\. Alternatively, only create an endpoint in a subnet that is not used by any of these services, to allow the services to continue accessing S3 buckets using public IP addresses\.

The following table lists AWS services that may be affected by an endpoint, and any specific information for each service\.


| AWS service | Note | 
| --- | --- | 
| Amazon AppStream 2\.0 | Your endpoint policy must allow access to the specific buckets that are used by AppStream 2\.0 for storing user content\. For more information, see [Home Folders and VPC Endpoints](https://docs.aws.amazon.com/appstream2/latest/developerguide/managing-network.html#managing-network-vpce-iam-policy) in the Amazon AppStream 2\.0 Developer Guide\. | 
| AWS CloudFormation | If you have resources in your VPC that must respond to a wait condition or custom resource request, your endpoint policy must allow at least access to the specific buckets that are used by these resources\. For more information, see [AWS CloudFormation and VPC Endpoints](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-vpce-bucketnames.html)\. | 
| AWS CodeDeploy | Your endpoint policy must allow full access to Amazon S3, or allow access to any S3 buckets that you've created for your AWS CodeDeploy deployments\. | 
| Elastic Beanstalk | Your endpoint policy must allow at least access to any S3 buckets used for Elastic Beanstalk applications\. For more information, see [Using Elastic Beanstalk with Amazon S3](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/AWSHowTo.S3.html) in the AWS Elastic Beanstalk Developer Guide\. | 
| AWS OpsWorks | Your endpoint policy must allow at least access to specific buckets that are used by AWS OpsWorks\. For more information, see [Running a Stack in a VPC](https://docs.aws.amazon.com/opsworks/latest/userguide/workingstacks-vpc.html) in the AWS OpsWorks User Guide\.  | 
| AWS Systems Manager |  Your endpoint policy must allow access to the Amazon S3 buckets used by Patch Manager for patch baseline operations in your AWS Region\. These buckets contain the code that is retrieved and run on instances by the patch baseline service\. For more information, see [Setting Up VPC Endpoints for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-setting-up-vpc.html) in the *AWS Systems Manager User Guide*\. For a list of S3 bucket permissions required by SSM Agent for its operations, see [Minimum S3 Bucket Permissions for SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent-minimum-s3-permissions.html) in the *AWS Systems Manager User Guide*\.  | 
| Amazon WorkDocs | If you use an Amazon WorkDocs client in Amazon WorkSpaces or an EC2 instance, your endpoint policy must allow full access to Amazon S3\. | 
| Amazon WorkSpaces | Amazon WorkSpaces does not directly depend on Amazon S3\. However, if you provide Amazon WorkSpaces users with internet access, then take note that websites, HTML emails, and internet services from other companies may depend on Amazon S3\. Ensure that your endpoint policy allows full access to Amazon S3 to allow these services to continue to work correctly\.  | 

Traffic between your VPC and S3 buckets does not leave the Amazon network\.

## Using Endpoint Policies for Amazon S3<a name="vpc-endpoints-policies-s3"></a>

The following are example endpoint policies for accessing Amazon S3\. For more information, see [Using VPC Endpoint Policies](vpc-endpoints-access.md#vpc-endpoint-policies)\.

**Important**  
All types of policies — IAM user policies, endpoint policies, S3 bucket policies, and Amazon S3 ACL policies \(if any\) — must grant the necessary permissions for access to Amazon S3 to succeed\. 

**Example Example: Restricting Access to a Specific Bucket**  
You can create a policy that restricts access to specific S3 buckets only\. This is useful if you have other AWS services in your VPC that use S3 buckets\. The following is an example of a policy that restricts access to `my_secure_bucket` only\.  

```
{
  "Statement": [
    {
      "Sid": "Access-to-specific-bucket-only",
      "Principal": "*",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::my_secure_bucket",
                   "arn:aws:s3:::my_secure_bucket/*"]
    }
  ]
}
```

**Example Example: Enabling Access to the Amazon Linux AMI Repositories**  
The Amazon Linux AMI repositories are Amazon S3 buckets in each region\. If you want instances in your VPC to access the repositories through an endpoint, create an endpoint policy that enables access to these buckets\.  
The following policy allows access to the Amazon Linux repositories\.  

```
{
  "Statement": [
    {
      "Sid": "AmazonLinuxAMIRepositoryAccess",
      "Principal": "*",
      "Action": [
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": [
        "arn:aws:s3:::packages.*.amazonaws.com/*",
        "arn:aws:s3:::repo.*.amazonaws.com/*"
      ]
    }
  ]
}
```
The following policy allows access to the Amazon Linux 2 repositories\.  

```
{
  "Statement": [
    {
      "Sid": "AmazonLinux2AMIRepositoryAccess",
      "Principal": "*",
      "Action": [
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": [
        "arn:aws:s3:::amazonlinux.*.amazonaws.com/*"
      ]
    }
  ]
}
```

## Using Amazon S3 Bucket Policies<a name="vpc-endpoints-s3-bucket-policies"></a>

You can use bucket policies to control access to buckets from specific endpoints, or specific VPCs\.

You cannot use the `aws:SourceIp` condition in your bucket policies for requests to Amazon S3 through a VPC endpoint\. The condition fails to match any specified IP address or IP address range, and may have an undesired effect when you make requests to an Amazon S3 bucket\. For example:
+ You have a bucket policy with a `Deny` effect and a `NotIpAddress` condition that's intended to grant access from a single or limited range of IP addresses only\. For requests to the bucket through an endpoint, the `NotIpAddress` condition is always matched, and the statement's effect applies, assuming other constraints in the policy match\. Access to the bucket is denied\.
+ You have a bucket policy with a `Deny` effect and an `IpAddress` condition that's intended to deny access to a single or limited range of IP addresses only\. For requests to the bucket through an endpoint, the condition is not matched, and the statement does not apply\. Access to the bucket is allowed, assuming there are other statements that allow access without an `IpAddress` condition\.

Adjust your bucket policy to limit access to a specific VPC or a specific endpoint instead\. 

For more information about bucket policies for Amazon S3, see [Using Bucket Policies and User Policies](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-iam-policies.html) in *Amazon Simple Storage Service Developer Guide*\.

**Example Example: Restricting Access to a Specific Endpoint**  
The following is an example of an S3 bucket policy that allows access to a specific bucket, `my_secure_bucket`, from endpoint `vpce-1a2b3c4d` only\. The policy denies all access to the bucket if the specified endpoint is not being used\. The `aws:sourceVpce` condition is used to specify the endpoint\. The `aws:sourceVpce` condition does not require an ARN for the VPC endpoint resource, only the endpoint ID\.  

```
{
  "Version": "2012-10-17",
  "Id": "Policy1415115909152",
  "Statement": [
    {
      "Sid": "Access-to-specific-VPCE-only",
      "Principal": "*",
      "Action": "s3:*",
      "Effect": "Deny",
      "Resource": ["arn:aws:s3:::my_secure_bucket",
                   "arn:aws:s3:::my_secure_bucket/*"],
      "Condition": {
        "StringNotEquals": {
          "aws:sourceVpce": "vpce-1a2b3c4d"
        }
      }
    }
  ]
}
```

**Example Example: Restricting Access to a Specific VPC**  
You can create a bucket policy that restricts access to a specific VPC by using the `aws:sourceVpc` condition\. This is useful if you have multiple endpoints configured in the same VPC, and you want to manage access to your S3 buckets for all of your endpoints\. The following is an example of a policy that allows VPC `vpc-111bbb22` to access `my_secure_bucket` and its objects\. The policy denies all access to the bucket if the specified VPC is not being used\. The `aws:sourceVpc` condition does not require an ARN for the VPC resource, only the VPC ID\.  

```
{
  "Version": "2012-10-17",
  "Id": "Policy1415115909152",
  "Statement": [
    {
      "Sid": "Access-to-specific-VPC-only",
      "Principal": "*",
      "Action": "s3:*",
      "Effect": "Deny",
      "Resource": ["arn:aws:s3:::my_secure_bucket",
                   "arn:aws:s3:::my_secure_bucket/*"],
      "Condition": {
        "StringNotEquals": {
          "aws:sourceVpc": "vpc-111bbb22"
        }
      }
    }
  ]
}
```