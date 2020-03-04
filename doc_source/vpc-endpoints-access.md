# Controlling Access to Services with VPC Endpoints<a name="vpc-endpoints-access"></a>

When you create an endpoint, you can attach an endpoint policy to it that controls access to the service to which you are connecting\. Endpoint policies must be written in JSON format\.

If you're using an endpoint to Amazon S3, you can also use Amazon S3 bucket policies to control access to buckets from specific endpoints, or specific VPCs\. For more information, see [Using Amazon S3 Bucket Policies](vpc-endpoints-s3.md#vpc-endpoints-s3-bucket-policies)\.

**Topics**
+ [Using VPC Endpoint Policies](#vpc-endpoint-policies)
+ [Security Groups](#vpc-endpoints-security-groups)

## Using VPC Endpoint Policies<a name="vpc-endpoint-policies"></a>

A VPC endpoint policy is an IAM resource policy that you attach to an endpoint when you create or modify the endpoint\. If you do not attach a policy when you create an endpoint, we attach a default policy for you that allows full access to the service\. An endpoint policy does not override or replace IAM user policies or service\-specific policies \(such as S3 bucket policies\)\. It is a separate policy for controlling access from the endpoint to the specified service\. 

You cannot attach more than one policy to an endpoint\. However, you can modify the policy at any time\. If you do modify a policy, it can take a few minutes for the changes to take effect\. For more information about writing policies, see [Overview of IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/PoliciesOverview.html) in the *IAM User Guide*\.

Your endpoint policy can be like any IAM policy; however, take note of the following:
+ Only the parts of the policy that relate to the specified service will work\. You cannot use an endpoint policy to allow resources in your VPC to perform other actions; for example, if you add EC2 actions to an endpoint policy for an endpoint to Amazon S3, they will have no effect\. 
+ Your policy must contain a [Principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html) element\. For gateway endpoints only, you cannot limit the principal to a specific IAM role or user\. Specify `"*"` to grant access to all IAM roles and users\. Additionally, for gateway endpoints only, if you specify the principal in the format `"AWS":"AWS-account-ID"` or `"AWS":"arn:aws:iam::AWS-account-ID:root"`, access is granted to the AWS account root user only, and not all IAM users and roles for the account\.
+ The size of an endpoint policy cannot exceed 20,480 characters \(including white space\)\.

The following services support endpoint policies:
+ [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-vpc-endpoint-policies.html)
+ [Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-vpc-endpoints.html)
+ [AWS Auto Scaling](https://docs.aws.amazon.com/autoscaling/plans/userguide/aws-auto-scaling-vpc-endpoints.html)
+ [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-and-interface-VPC.html)
+ [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/cloudwatch-events-and-interface-VPC.html)
+ [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/cloudwatch-logs-and-interface-VPC.html#CloudWatchLogs-VPC-endpoint-policy)
+ [AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/use-vpc-endpoints-with-codebuild.html#creating-vpc-endpoint-policy)
+ [AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/codecommit-and-interface-VPC.html#create-vpc-endpoint-policy-for-codecommit)
+ [Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-vpc-endpoints.html)
+ [Amazon Elastic File System](https://docs.aws.amazon.com/efs/latest/ug/efs-vpc-endpoints.html#create-vpce-policy-efs)
+ [Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/load-balancer-vpc-endpoints.html)
+ [Amazon Elastic Container Registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/vpc-endpoints.html#ecr-vpc-endpoint-policy)
+ [Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/interface-vpc-endpoint.html#api-private-link-policy)
+ [Amazon Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/vpc.html)
+ [Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/vpc.html#interface-vpc-endpoints-policies)
+ [Amazon Rekognition](https://docs.aws.amazon.com/rekognition/latest/dg/vpc.html)
+ [Amazon SageMaker and Amazon SageMaker Runtime](https://docs.aws.amazon.com/sagemaker/latest/dg/interface-vpc-endpoint.html#api-private-link-policy)
+ [Amazon SageMaker Notebook Instance](https://docs.aws.amazon.com/sagemaker/latest/dg/notebook-interface-endpoint.html#nbi-private-link-policy)
+ [AWS Secrets Manager ](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint-policy)
+ [AWS Security Token Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_sts_vpce.html)
+ [Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-vpc-endpoint-policy.html)
+ [Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-internetwork-traffic-privacy.html#sqs-vpc-endpoint-policy)
+ [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/vpc-iam.html)

For example endpoint policies for Amazon S3 and DynamoDB, see the following topics:
+ [Using Endpoint Policies for Amazon S3](vpc-endpoints-s3.md#vpc-endpoints-policies-s3)
+ [Using Endpoint Policies for DynamoDB](vpc-endpoints-ddb.md#vpc-endpoints-policies-ddb)

## Security Groups<a name="vpc-endpoints-security-groups"></a>

By default, Amazon VPC security groups allow all outbound traffic, unless you've specifically restricted outbound access\. 

When you create an interface endpoint, you can associate security groups with the endpoint network interface that is created in your VPC\. If you do not specify a security group, the default security group for your VPC is automatically associated with the endpoint network interface\. You must ensure that the rules for the security group allow communication between the endpoint network interface and the resources in your VPC that communicate with the service\.

For a gateway endpoint, if your security group's outbound rules are restricted, you must add a rule that allows outbound traffic from your VPC to the service that's specified in your endpoint\. To do this, you can use the service's prefix list ID as the destination in the outbound rule\. For more information, see [Modifying Your Security Group](vpce-gateway.md#vpc-endpoints-security)\.