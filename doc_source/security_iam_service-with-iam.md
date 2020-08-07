# How Amazon VPC works with IAM<a name="security_iam_service-with-iam"></a>

Before you use IAM to manage access to Amazon VPC, you should understand what IAM features are available to use with Amazon VPC\. To get a high\-level view of how Amazon VPC and other AWS services work with IAM, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) in the *IAM User Guide*\.

**Topics**
+ [Actions](#security_iam_service-with-iam-id-based-policies-actions)
+ [Resources](#security_iam_service-with-iam-id-based-policies-resources)
+ [Condition keys](#security_iam_service-with-iam-id-based-policies-conditionkeys)
+ [Amazon VPC resource\-based policies](#security_iam_service-with-iam-resource-based-policies)
+ [Authorization based on tags](#security_iam_service-with-iam-tags)
+ [IAM roles](#security_iam_service-with-iam-roles)

With IAM identity\-based policies, you can specify allowed or denied actions\. For some actions, you can specify the resources and conditions under which actions are allowed or denied\. Amazon VPC supports specific actions, resources, and condition keys\. To learn about all of the elements that you use in a JSON policy, see [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\.

## Actions<a name="security_iam_service-with-iam-id-based-policies-actions"></a>

The `Action` element of an IAM identity\-based policy describes the specific action or actions that will be allowed or denied by the policy\. Policy actions usually have the same name as the associated AWS API operation\. The action is used in a policy to grant permissions to perform the associated operation\. 

Amazon VPC shares its API namespace with Amazon EC2\. Policy actions in Amazon VPC use the following prefix before the action: `ec2:`\. For example, to grant someone permission to create a VPC with the Amazon EC2 `CreateVpc` API operation, you include the `ec2:CreateVpc` action in their policy\. Policy statements must include either an `Action` or `NotAction` element\.

To specify multiple actions in a single statement, separate them with commas as shown in the following example\.

```
"Action": [
      "ec2:action1",
      "ec2:action2"
]
```

You can specify multiple actions using wildcards \(\*\)\. For example, to specify all actions that begin with the word `Describe`, include the following action\. 

```
"Action": "ec2:Describe*"
```

To see a list of Amazon VPC actions, see [Actions, Resources, and Condition Keys for Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html) in the *IAM User Guide*\.

## Resources<a name="security_iam_service-with-iam-id-based-policies-resources"></a>

The `Resource` element specifies the object or objects to which the action applies\. Statements must include either a `Resource` or a `NotResource` element\. You specify a resource using an ARN or using the wildcard \(\*\) to indicate that the statement applies to all resources\.

**Important**  
Currently, not all Amazon EC2 API actions support resource\-level permissions\. If an Amazon EC2 API action does not support resource\-level permissions, you can grant users permission to use the action, but you have to specify a \* for the resource element of your policy statement\. To view the actions for which you can specify an ARN for the resource element, see [Actions Defined by Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html#amazonec2-actions-as-permissions)\.

The VPC resource has the ARN shown in the following example\.

```
arn:${Partition}:ec2:${Region}:${Account}:vpc/${VpcId}
```

For more information about the format of ARNs, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\.

For example, to specify the `vpc-1234567890abcdef0` VPC in your statement, use the ARN shown in the following example\.

```
"Resource": "arn:aws:ec2:us-east-1:123456789012:vpc/vpc-1234567890abcdef0"
```

To specify all VPCs that belong to a specific account, use the wildcard \(\*\)\.

```
"Resource": "arn:aws:ec2:us-east-1:123456789012:vpc/*"
```

Some Amazon VPC actions, such as those for creating resources, cannot be performed on a specific resource\. In those cases, you must use the wildcard \(\*\)\.

```
"Resource": "*"
```

Many Amazon EC2 API actions involve multiple resources\. To specify multiple resources in a single statement, separate the ARNs with commas\. 

```
"Resource": [
      "resource1",
      "resource2"
]
```

To see a list of Amazon VPC resource types and their ARNs, see [Resources Defined by Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html#amazonec2-resources-for-iam-policies) in the *IAM User Guide*\.

## Condition keys<a name="security_iam_service-with-iam-id-based-policies-conditionkeys"></a>

The `Condition` element \(or `Condition` *block*\) lets you specify conditions in which a statement is in effect\. The `Condition` element is optional\. You can build conditional expressions that use [condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html), such as equals or less than, to match the condition in the policy with values in the request\. 

If you specify multiple `Condition` elements in a statement, or multiple keys in a single `Condition` element, AWS evaluates them using a logical `AND` operation\. If you specify multiple values for a single condition key, AWS evaluates the condition using a logical `OR` operation\. All of the conditions must be met before the statement's permissions are granted\.

 You can also use placeholder variables when you specify conditions\. For example, you can grant an IAM user permission to access a resource only if it is tagged with their IAM user name\. For more information, see [IAM Policy Elements: Variables and Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html) in the *IAM User Guide*\. 

Amazon VPC defines its own set of condition keys and also supports using some global condition keys\. To see all AWS global condition keys, see [AWS Global Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\. 

All Amazon EC2 actions support the `aws:RequestedRegion` and `ec2:Region` condition keys\. For more information, see [Example: Restricting Access to a Specific Region](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExamplePolicies_EC2.html#iam-example-region)\. 

To see a list of Amazon VPC condition keys, see [Condition Keys for Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html#amazonec2-policy-keys) in the *IAM User Guide*\. To learn with which actions and resources you can use a condition key, see [Actions Defined by Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html#amazonec2-actions-as-permissions)\.

## Amazon VPC resource\-based policies<a name="security_iam_service-with-iam-resource-based-policies"></a>

Resource\-based policies are JSON policy documents that specify what actions a specified principal can perform on the Amazon VPC resource and under what conditions\.

To enable cross\-account access, you can specify an entire account or IAM entities in another account as the [principal in a resource\-based policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html)\. Adding a cross\-account principal to a resource\-based policy is only half of establishing the trust relationship\. When the principal and the resource are in different AWS accounts, you must also grant the principal entity permission to access the resource\. Grant permission by attaching an identity\-based policy to the entity\. However, if a resource\-based policy grants access to a principal in the same account, no additional identity\-based policy is required\. For more information, see [How IAM Roles Differ from Resource\-based Policies ](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_compare-resource-policies.html)in the *IAM User Guide*\. 

## Authorization based on tags<a name="security_iam_service-with-iam-tags"></a>

You can attach tags to Amazon VPC resources or pass tags in a request\. To control access based on tags, you provide tag information in the [condition element](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) of a policy using the `ec2:ResourceTag/key-name`, `aws:RequestTag/key-name`, or `aws:TagKeys` condition keys\. For more information, see [Resource\-Level Permissions for Tagging](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-iam-actions-resources.html#supported-iam-actions-tagging) in the *Amazon EC2 User Guide*\.

To view an example identity\-based policy for limiting access to a resource based on the tags on that resource, see [Launching instances into a specific VPC](vpc-policy-examples.md#subnet-ami-example-iam)\.

## IAM roles<a name="security_iam_service-with-iam-roles"></a>

An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an entity within your AWS account that has specific permissions\.

### Using temporary credentials<a name="security_iam_service-with-iam-roles-tempcreds"></a>

You can use temporary credentials to sign in with federation, assume an IAM role, or to assume a cross\-account role\. You obtain temporary security credentials by calling AWS STS API operations such as [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) or [GetFederationToken](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)\. 

Amazon VPC supports using temporary credentials\. 

### Service\-linked roles<a name="security_iam_service-with-iam-roles-service-linked"></a>

[Service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role) allow AWS services to access resources in other services to complete an action on your behalf\. Service\-linked roles appear in your IAM account and are owned by the service\. An IAM administrator can view but not edit the permissions for service\-linked roles\.

[Transit gateways](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-service-linked-roles.html) support service\-linked roles\. 

### Service roles<a name="security_iam_service-with-iam-roles-service"></a>

This feature allows a service to assume a [service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-role) on your behalf\. This role allows the service to access resources in other services to complete an action on your behalf\. Service roles appear in your IAM account and are owned by the account\. This means that an IAM administrator can change the permissions for this role\. However, doing so might break the functionality of the service\.

Amazon VPC supports service roles for flow logs\. When you create a flow log, you must choose a role that allows the flow logs service to access CloudWatch Logs\. For more information, see [IAM roles for publishing flow logs to CloudWatch Logs](flow-logs-cwl.md#flow-logs-iam)\.