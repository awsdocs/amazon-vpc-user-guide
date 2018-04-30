# VPC Endpoints<a name="vpc-endpoints"></a>

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\. 

Endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components that allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic\.

There are two types of VPC endpoints: *interface* and *gateway*\.

**Interface Endpoints \(Powered by AWS PrivateLink\)**  
An interface endpoint is an elastic network interface with a private IP address that serves as an entry point for traffic destined to a supported service\. The following services are supported:
+ [Amazon CloudWatch Logs](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/cloudwatch-logs-and-interface-VPC.html)
+ Amazon EC2 API
+ [Amazon Kinesis Data Streams](http://docs.aws.amazon.com/streams/latest/dev/vpc.html)
+ [Amazon SNS](http://docs.aws.amazon.com/sns/latest/dg/sns-vpc.html)
+ [AWS KMS](http://docs.aws.amazon.com/kms/latest/developerguide/kms-vpc-endpoint.html)
+ AWS Service Catalog
+ [AWS Systems Manager](http://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-setting-up-vpc.html)
+ Elastic Load Balancing API
+ [Endpoint services](endpoint-service.md) hosted by other AWS accounts
+ Supported AWS Marketplace partner services

**Gateway Endpoints**  
A gateway endpoint is a gateway that is a target for a specified route in your route table, used for traffic destined to a supported AWS service\. The following AWS services are supported:
+ Amazon S3
+ DynamoDB

**Controlling the Use of VPC Endpoints**  
By default, IAM users do not have permission to work with endpoints\. You can create an IAM user policy that grants users the permissions to create, modify, describe, and delete endpoints\. We currently do not support resource\-level permissions for any of the `ec2:*VpcEndpoint*` API actions, or for the `ec2:DescribePrefixLists` action\. You cannot create an IAM policy that grants users the permissions to use a specific endpoint or prefix list\. For more information, see the following example: [8\. Creating and managing VPC endpoints](VPC_IAM.md#vpc-endpoints-iam)\. 