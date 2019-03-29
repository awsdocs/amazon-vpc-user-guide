# VPC Endpoints<a name="vpc-endpoints"></a>

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\. 

Endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components that allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic\.

There are two types of VPC endpoints: *interface endpoints* and *gateway endpoints*\. Create the type of VPC endpoint required by the supported service\.

**Interface Endpoints \(Powered by AWS PrivateLink\)**

An [interface endpoint](vpce-interface.md) is an elastic network interface with a private IP address that serves as an entry point for traffic destined to a supported service\. The following services are supported:
+ [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html)
+ [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-vpce-bucketnames.html)
+ [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-and-interface-VPC.html)
+ [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/cloudwatch-events-and-interface-VPC.html)
+ [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/cloudwatch-logs-and-interface-VPC.html)
+ [AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/use-vpc-endpoints-with-codebuild.html)
+ [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/config-VPC-endpoints.html)
+ Amazon EC2 API
+ Elastic Load Balancing API
+ [Amazon Elastic Container Registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/vpc-endpoints.html)
+ [Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html)
+ [AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/vpc-endpoint.html)
+ [Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/vpc.html)
+ [Amazon SageMaker and Amazon SageMaker Runtime](https://docs.aws.amazon.com/sagemaker/latest/dg/interface-vpc-endpoint.html)
+ [Amazon SageMaker Notebook Instance](https://docs.aws.amazon.com/sagemaker/latest/dg/notebook-interface-endpoint.html)
+ [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotation-network-rqmts.html)
+ [AWS Security Token Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_sts_vpce.html)
+ AWS Service Catalog
+ [Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-vpc.html)
+ [Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-vpc-endpoints.html)
+ [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-setting-up-vpc.html)
+ [Endpoint services](endpoint-service.md) hosted by other AWS accounts
+ Supported AWS Marketplace partner services

**Gateway Endpoints**

A [gateway endpoint](vpce-gateway.md) is a gateway that is a target for a specified route in your route table, used for traffic destined to a supported AWS service\. The following AWS services are supported:
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