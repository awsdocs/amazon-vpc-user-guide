# VPC Endpoints<a name="vpc-endpoints"></a>

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\. 

Endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components that allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic\.

There are two types of VPC endpoints: *interface endpoints* and *gateway endpoints*\. Create the type of VPC endpoint required by the supported service\.

**Interface Endpoints \(Powered by [AWS PrivateLink\)](https://aws.amazon.com/privatelink/)**

An [interface endpoint](vpce-interface.md) is an elastic network interface with a private IP address from the IP address range of your subnet that serves as an entry point for traffic destined to a supported service\. The following services are supported:
+ [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html)
+ [Amazon AppStream 2\.0](https://docs.aws.amazon.com/appstream2/latest/developerguide/creating-streaming-from-interface-vpc-endpoints.html)
+ [AWS App Mesh](https://docs.aws.amazon.com//app-mesh/latest/userguide/vpc-endpoints.html)
+ [Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/application-auto-scaling-vpc-endpoints.html)
+ [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/interface-vpc-endpoint.html)
+ [AWS Auto Scaling](https://docs.aws.amazon.com/autoscaling/plans/userguide/aws-auto-scaling-vpc-endpoints.html)
+ [Amazon Cloud Directory](https://docs.aws.amazon.com/clouddirectory/latest/developerguide/getting_started_using_vpc_endpoints.html)
+ [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-vpce-bucketnames.html)
+ [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-and-interface-VPC.html)
+ [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-and-interface-VPC.html)
+ [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/cloudwatch-events-and-interface-VPC.html)
+ [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/cloudwatch-logs-and-interface-VPC.html)
+ [AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/use-vpc-endpoints-with-codebuild.html)
+ [AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/codecommit-and-interface-VPC.html)
+ [AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/vpc-support.html#use-vpc-endpoints-with-codepipeline)
+ [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/config-VPC-endpoints.html)
+ [AWS DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/datasync-in-vpc.html)
+ Amazon EC2 API
+ [Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-vpc-endpoints.html)
+ [Amazon Elastic File System](https://docs.aws.amazon.com/efs/latest/ug/efs-vpc-endpoints.html)
+ [Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/load-balancer-vpc-endpoints.html)
+ [Amazon Elastic Container Registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/vpc-endpoints.html)
+ [Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html)
+ [Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/interface-vpc-endpoint.html)
+ [AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/infrastructure-security.html)
+ [AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/kms-vpc-endpoint.html)
+ [Amazon Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/vpc-endpoint.html)
+ [Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/vpc.html)
+ [Amazon Rekognition](https://docs.aws.amazon.com/rekognition/latest/dg//vpc.html)
+ [Amazon SageMaker and Amazon SageMaker Runtime](https://docs.aws.amazon.com/sagemaker/latest/dg/interface-vpc-endpoint.html)
+ [Amazon SageMaker Notebook](https://docs.aws.amazon.com/sagemaker/latest/dg/notebook-interface-endpoint.html)
+ [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotation-network-rqmts.html)
+ [AWS Security Token Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_sts_vpce.html)
+ [AWS Server Migration Service](https://aws.amazon.com/server-migration-service)
+ AWS Service Catalog
+ [Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-vpc.html)
+ [Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-internetwork-traffic-privacy.html#sqs-vpc-endpoints)
+ [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/vpc-endpoints.html) 
+ [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-setting-up-vpc.html)
+ [AWS Storage Gateway](https://docs.aws.amazon.com/storagegateway/latest/userguide/gateway-private-link.html)
+ [AWS Transfer for SFTP](https://docs.aws.amazon.com/transfer/latest/userguide/create-server-vpc.html)
+  [Amazon WorkSpaces](https://docs.aws.amazon.com/workspaces/latest/adminguide/infrastructure-security.html#interface-vpc-endpoint) 
+ [Endpoint services](endpoint-service.md) hosted by other AWS accounts
+ Supported AWS Marketplace partner services

**Gateway Endpoints**

A [gateway endpoint](vpce-gateway.md) is a gateway that you specify as a target for a route in your route table for traffic destined to a supported AWS service\. The following AWS services are supported:
+ Amazon S3
+ DynamoDB

**Controlling the Use of VPC Endpoints**  
By default, IAM users do not have permission to work with endpoints\. You can create an IAM user policy that grants users the permissions to create, modify, describe, and delete endpoints\. We currently do not support resource\-level permissions for any of the `ec2:*VpcEndpoint*` API actions, or for the `ec2:DescribePrefixLists` action\. You cannot create an IAM policy that grants users the permissions to use a specific endpoint or prefix list\. The following is an example:

```
{
    "Version": "2012-10-17",
    "Statement":[{
    "Effect":"Allow",
    "Action":"ec2:*VpcEndpoint*",
    "Resource":"*"
    }
  ]
}
```

For information about controlling access to services using VPC endpoints, see [Controlling Access to Services with VPC Endpoints](vpc-endpoints-access.md)\.