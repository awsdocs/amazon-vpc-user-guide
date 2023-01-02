# Work with AWS\-managed prefix lists<a name="working-with-aws-managed-prefix-lists"></a>

AWS\-managed prefix lists are sets of IP address ranges for AWS services\.

**Topics**
+ [Use an AWS\-managed prefix list](#use-aws-managed-prefix-list)
+ [AWS\-managed prefix list weight](#aws-managed-prefix-list-weights)
+ [Available AWS\-managed prefix lists](#available-aws-managed-prefix-lists)

## Use an AWS\-managed prefix list<a name="use-aws-managed-prefix-list"></a>

AWS\-managed prefix lists are created and maintained by AWS and can be used by anyone with an AWS account\. You cannot create, modify, share, or delete an AWS\-managed prefix list\.

As with customer\-managed prefix lists, you can use AWS\-managed prefix lists with AWS resources such as security groups and route tables\. For more information, see [Reference prefix lists in your AWS resources](managed-prefix-lists-referencing.md)\.

## AWS\-managed prefix list weight<a name="aws-managed-prefix-list-weights"></a>

The weight of an AWS\-managed prefix list refers to the number of entries that it takes up in a resource\.

For example, the weight of a Amazon CloudFront managed prefix list is 55\. Here's how the this affects your Amazon VPC quotas:
+ **Security groups** – The [default quota](amazon-vpc-limits.md#vpc-limits-security-groups) is 60 rules, leaving room for only 5 additional rules in a security group\. You can [request a quota increase](https://console.aws.amazon.com/servicequotas/home/services/vpc/quotas/L-0EA8095F) for this quota\.
+ **Route tables** – The [default quota](amazon-vpc-limits.md#vpc-limits-route-tables) is 50 routes, so you must [request a quota increase](https://console.aws.amazon.com/servicequotas/home/services/vpc/quotas/L-93826ACB) before you can add the prefix list to a route table\.

## Available AWS\-managed prefix lists<a name="available-aws-managed-prefix-lists"></a>

The following services provide AWS\-managed prefix lists\.


| AWS service | Prefix list name | Weight | 
| --- | --- | --- | 
| [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/LocationsOfEdgeServers.html#managed-prefix-list) | com\.amazonaws\.global\.cloudfront\.origin\-facing | 55 | 
| Amazon DynamoDB | com\.amazonaws\.region\.dynamodb | 1 | 
| Amazon S3 | com\.amazonaws\.region\.s3 | 1 | 
| Amazon VPC Lattice | com\.amazonaws\.region\.vpc\-lattice | 10 | 
| AWS Ground Station | com\.amazonaws\.global\.groundstation | 5 | 

**To view the AWS\-managed prefix lists using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Managed Prefix Lists**\.

1. In the search field, add the **Owner ID: AWS** filter\.

**To view the AWS\-managed prefix lists using the AWS CLI**  
Use the [describe\-managed\-prefix\-lists](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-managed-prefix-lists.html) command as follows\.

```
aws ec2 describe-managed-prefix-lists --filters Name=owner-id,Values=AWS
```