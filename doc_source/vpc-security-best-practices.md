# Security best practices for your VPC<a name="vpc-security-best-practices"></a>

 The following best practices are general guidelines and don’t represent a complete security solution\. Because these best practices might not be appropriate or sufficient for your environment, treat them as helpful considerations rather than prescriptions\.

The following are general best practices:
+ Use multiple Availability Zone deployments so you have high availability\.
+ Use security groups and network ACLs\. For more information, see [Security groups for your VPC](VPC_SecurityGroups.md) and [Network ACLs](vpc-network-acls.md)\.
+ Use IAM policies to control access\.
+ Use Amazon CloudWatch to monitor your VPC components and VPN connections\.
+ Use flow logs to capture information about IP traffic going to and from network interfaces in your VPC\. For more information, see [VPC Flow Logs](flow-logs.md)\.

## Additional resources<a name="seccurity-best-practices-additional-resources"></a>
+ Manage access to AWS resources and APIs using identity federation, IAM users, and IAM roles\. Establish credential management policies and procedures for creating, distributing, rotating, and revoking AWS access credentials\. For more information, see [IAM best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/IAMBestPractices.html) in the *IAM User Guide*\.
+ For answers to frequently asked questions for VPC security, see [Amazon VPC FAQs](https://aws.amazon.com/vpc/faqs/)\.