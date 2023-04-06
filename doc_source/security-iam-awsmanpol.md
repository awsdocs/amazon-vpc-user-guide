# AWS managed policies for Amazon Virtual Private Cloud<a name="security-iam-awsmanpol"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the **ReadOnlyAccess** AWS managed policy provides read\-only access to all AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.

## AWS managed policy: AmazonVPCFullAccess<a name="security-iam-awsmanpol-AmazonVPCFullAccess"></a>

You can attach the `AmazonVPCFullAccess` policy to your IAM identities\. This policy grants permissions that allow full access to Amazon VPC\.

To view the permissions for this policy, see [AmazonVPCFullAccess](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AmazonVPCFullAccess.html) in the *AWS Managed Policy Reference*\.

## AWS managed policy: AmazonVPCReadOnlyAccess<a name="security-iam-awsmanpol-AmazonVPCReadOnlyAccess"></a>

You can attach the `AmazonVPCReadOnlyAccess` policy to your IAM identities\. This policy grants permissions that allow read\-only access to Amazon VPC\.

To view the permissions for this policy, see [AmazonVPCReadOnlyAccess](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AmazonVPCReadOnlyAccess.html) in the *AWS Managed Policy Reference*\.

## AWS managed policy: AmazonVPCCrossAccountNetworkInterfaceOperations<a name="security-iam-awsmanpol-AmazonVPCCrossAccountNetworkInterfaceOperations"></a>

You can attach the `AmazonVPCCrossAccountNetworkInterfaceOperations` policy to your IAM identities\. This policy grants permissions that allow the identity to create network interfaces and attach them to cross\-account resources\.

To view the permissions for this policy, see [AmazonVPCCrossAccountNetworkInterfaceOperations](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AmazonVPCCrossAccountNetworkInterfaceOperations) in the *AWS Managed Policy Reference*\.

## Amazon VPC updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>

View details about updates to AWS managed policies for Amazon VPC since this service began tracking these changes in March 2021\.


| Change | Description | Date | 
| --- | --- | --- | 
| [AWS managed policy: AmazonVPCReadOnlyAccess](#security-iam-awsmanpol-AmazonVPCReadOnlyAccess) \- Update to an existing policy | Added the DescribeSecurityGroupRules action, which enables an IAM role to view [security group rules](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/security-group-rules.html)\. | August 2, 2021 | 
| [AWS managed policy: AmazonVPCFullAccess](#security-iam-awsmanpol-AmazonVPCFullAccess) \- Update to an existing policy | Added the DescribeSecurityGroupRules and the ModifySecurityGroupRules actions, which enable an IAM role to view and modify [security group rules](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/security-group-rules.html)\. | August 2, 2021 | 
| [AWS managed policy: AmazonVPCFullAccess](#security-iam-awsmanpol-AmazonVPCFullAccess) \- Update to an existing policy | Added actions for carrier gateways, IPv6 pools, local gateways, and local gateway route tables\. | June 23, 2021 | 
| [AWS managed policy: AmazonVPCReadOnlyAccess](#security-iam-awsmanpol-AmazonVPCReadOnlyAccess) \- Update to an existing policy | Added actions for carrier gateways, IPv6 pools, local gateways, and local gateway route tables\. | June 23, 2021 | 