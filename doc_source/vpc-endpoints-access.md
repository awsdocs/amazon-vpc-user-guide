# Controlling Access to Services with VPC Endpoints<a name="vpc-endpoints-access"></a>

When you create a gateway endpoint, you can attach an endpoint policy to it that controls access to the service to which you are connecting\. Endpoint policies must be written in JSON format\.

If you're using an endpoint to Amazon S3, you can also use Amazon S3 bucket policies to control access to buckets from specific endpoints, or specific VPCs\. For more information, see [Using Amazon S3 Bucket Policies](vpc-endpoints-s3.md#vpc-endpoints-s3-bucket-policies)\.


+ [Using VPC Endpoint Policies](#vpc-endpoint-policies)
+ [Security Groups](#vpc-endpoints-security-groups)

## Using VPC Endpoint Policies<a name="vpc-endpoint-policies"></a>

A VPC endpoint policy is an IAM resource policy that you attach to an endpoint when you create or modify the endpoint\. If you do not attach a policy when you create an endpoint, we attach a default policy for you that allows full access to the service\. An endpoint policy does not override or replace IAM user policies or service\-specific policies \(such as S3 bucket policies\)\. It is a separate policy for controlling access from the endpoint to the specified service\. 

**Important**  
Only gateway endpoints support the use of endpoint policies\. Use the [describe\-vpc\-endpoint\-services](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command to verify if endpoint policies are supported\.

You cannot attach more than one policy to an endpoint; however, you can modify the policy at any time\. Note that if you do modify a policy, it can take a few minutes for the changes to take effect\. For more information about writing policies, see [Overview of IAM Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/PoliciesOverview.html) in the *IAM User Guide*\.

Your endpoint policy can be like any IAM policy; however, take note of the following:

+ Only the parts of the policy that relate to the specified service will work\. You cannot use an endpoint policy to allow resources in your VPC to perform other actions; for example, if you add EC2 actions to an endpoint policy for an endpoint to Amazon S3, they will have no effect\. 

+ Your policy must contain a `Principal` element\. For more information, see [Principal](http://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_ElementDescriptions.html#Principal) in the *IAM User Guide*\.

+ The size of an endpoint policy cannot exceed 20,480 characters \(including whitespace\)\.

For example endpoint policies, see the following topics:

+ [Using Endpoint Policies for Amazon S3](vpc-endpoints-s3.md#vpc-endpoints-policies-s3)

+ [Using Endpoint Policies for DynamoDB](vpc-endpoints-ddb.md#vpc-endpoints-policies-ddb)

## Security Groups<a name="vpc-endpoints-security-groups"></a>

By default, Amazon VPC security groups allow all outbound traffic, unless you've specifically restricted outbound access\. 

When you create an interface endpoint, you can associate security groups with the endpoint network interface that is created in your VPC\. If you do not specify a security group, the default security group for your VPC is automatically associated with the endpoint network interface\. You must ensure that the rules for the security group allow communication between the endpoint network interface and the resources in your VPC that communicate with the service\.

For a gateway endpoint, if your security group's outbound rules are restricted, you must add a rule that allows outbound traffic from your VPC to the service that's specified in your endpoint\. To do this, you can use the service's prefix list ID as the destination in the outbound rule\. For more information, see [Modifying Your Security Group](vpce-gateway.md#vpc-endpoints-security)\.