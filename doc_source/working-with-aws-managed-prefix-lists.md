# Work with AWS\-managed prefix lists<a name="working-with-aws-managed-prefix-lists"></a>

AWS\-managed prefix lists are sets of IP address ranges for AWS services\.

**Topics**
+ [Use an AWS\-managed prefix list](#use-aws-managed-prefix-list)
+ [AWS\-managed prefix list weight](#aws-managed-prefix-list-weights)

## Use an AWS\-managed prefix list<a name="use-aws-managed-prefix-list"></a>

AWS\-managed prefix lists are created and maintained by AWS and can be used by anyone with an AWS account\. You cannot create, modify, share, or delete an AWS\-managed prefix list\.

You can see the available AWS\-managed prefix lists and the prefix list IDs in the following ways:
+ Open **Managed Prefix Lists** in the navigation pane of the Amazon VPC Console\.
+ Use the [describe\-managed\-prefix\-lists](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-managed-prefix-lists.html) AWS CLI command\.
+ Use the [DescribeManagedPrefixLists](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeManagedPrefixLists.html) API\.

The following AWS\-managed prefix lists are available:


| Prefix list name | AWS service | 
| --- | --- | 
|  com\.amazonaws\.*region*\.s3  | Amazon S3 | 
|  com\.amazonaws\.*region*\.dynamodb  | DynamoDB | 
|  com\.amazonaws\.global\.cloudfront\.origin\-facing  | Amazon CloudFront | 

As with customer\-managed prefix lists, AWS\-managed prefix lists can be used with AWS resources such as security groups and route tables\. For more information, see [Reference prefix lists in your AWS resources](managed-prefix-lists-referencing.md)\.

## AWS\-managed prefix list weight<a name="aws-managed-prefix-list-weights"></a>

The AWS\-managed prefix list weight refers to the number of entries a prefix list will take up in a resource\.


| Prefix list name | AWS service | Weight | 
| --- | --- | --- | 
|  com\.amazonaws\.*region*\.s3  | Amazon S3 |  1  | 
|  com\.amazonaws\.*region*\.dynamodb  | DynamoDB | 1 | 
|  com\.amazonaws\.global\.cloudfront\.origin\-facing  | Amazon CloudFront | 55 | 

The Amazon CloudFront managed prefix list weight is unique in how it affects Amazon VPC quotas:
+ It counts as 55 rules in a security group\. The [default quota](amazon-vpc-limits.md#vpc-limits-security-groups) is 60 rules, leaving room for only 5 additional rules in a security group\. You can [request a quota increase](https://console.aws.amazon.com/servicequotas/home/services/vpc/quotas/L-0EA8095F) for this quota\.
+ It counts as 55 routes in a route table\. The [default quota](amazon-vpc-limits.md#vpc-limits-route-tables) is 50 routes, so you must [request a quota increase](https://console.aws.amazon.com/servicequotas/home/services/vpc/quotas/L-93826ACB) before you can add the prefix list to a route table\.

For more information, see [Use the CloudFront managed prefix list](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/LocationsOfEdgeServers.html#managed-prefix-list) in the *Amazon CloudFront Developer Guide*\.