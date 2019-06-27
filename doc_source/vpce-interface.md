# Interface VPC Endpoints \(AWS PrivateLink\)<a name="vpce-interface"></a>

An interface VPC endpoint \(interface endpoint\) enables you to connect to services powered by AWS PrivateLink\. These services include some AWS services, services hosted by other AWS customers and partners in their own VPCs \(referred to as *endpoint services*\), and supported AWS Marketplace partner services\. The owner of the service is the *service provider*, and you, as the principal creating the interface endpoint, are the *service consumer*\.

The following services are supported:
+ [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html)
+ [App Mesh](https://docs.aws.amazon.com//app-mesh/latest/userguide/vpc-endpoints.html)
+ [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-vpce-bucketnames.html)
+ [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-and-interface-VPC.html)
+ [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-and-interface-VPC.html)
+ [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/cloudwatch-events-and-interface-VPC.html)
+ [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/cloudwatch-logs-and-interface-VPC.html)
+ [AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/use-vpc-endpoints-with-codebuild.html)
+ [AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/codecommit-and-interface-VPC.html)
+ [AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/vpc-support.html#use-vpc-endpoints-with-codepipeline)
+ [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/config-VPC-endpoints.html)
+ Amazon EC2 API
+ [Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/load-balancer-vpc-endpoints.html)
+ [Amazon Elastic Container Registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/vpc-endpoints.html)
+ [Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html)
+ [AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/vpc-endpoint.html)
+ [AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/kms-vpc-endpoint.html)
+ [Amazon Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/vpc-endpoint.html)
+ [Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/vpc.html)
+ [Amazon SageMaker and Amazon SageMaker Runtime](https://docs.aws.amazon.com/sagemaker/latest/dg/interface-vpc-endpoint.html)
+ [Amazon SageMaker Notebook Instance](https://docs.aws.amazon.com/sagemaker/latest/dg/notebook-interface-endpoint.html)
+ [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotation-network-rqmts.html)
+ [AWS Security Token Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_sts_vpce.html)
+ AWS Service Catalog
+ [Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-vpc.html)
+ [Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-vpc-endpoints.html)
+ [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-setting-up-vpc.html)
+ [AWS Storage Gateway](https://docs.aws.amazon.com/storagegateway/latest/userguide/gateway-private-link.html)
+ [AWS Transfer for SFTP](https://docs.aws.amazon.com/transfer/latest/userguide/create-server-vpc.html)
+ [Endpoint services](endpoint-service.md) hosted by other AWS accounts
+ Supported AWS Marketplace partner services

The following are the general steps for setting up an interface endpoint:

1. Choose the VPC in which to create the interface endpoint, and provide the name of the AWS service, endpoint service, or AWS Marketplace service to which you're connecting\.

1. Choose a subnet in your VPC to use the interface endpoint\. We create an *endpoint network interface* in the subnet\. You can specify more than one subnet in different Availability Zones \(as supported by the service\) to help ensure that your interface endpoint is resilient to Availability Zone failures\. In that case, we create an endpoint network interface in each subnet that you specify\. 
**Note**  
An endpoint network interface is a requester\-managed network interface\. You can view it in your account, but you cannot manage it yourself\. For more information, see [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html)\.

1. Specify the security groups to associate with the endpoint network interface\. The security group rules control the traffic to the endpoint network interface from resources in your VPC\. If you do not specify a security group, we associate the default security group for the VPC\.

1. \(Optional\) AWS services and AWS Marketplace partner services only\) Enable [private DNS](#vpce-private-dns) for the endpoint to enable you to make requests to the service using its default DNS hostname\.
**Important**  
Private DNS is enabled by default for endpoints created for AWS services and AWS Marketplace partner services\. 

1. When the service provider and the consumer are in different accounts, see [Interface Endpoint Availability Zone Considerations](#vpce-interface-availability-zones) for information about how to use Availability Zone IDs to identify the interface endpoint Availability Zone\.

1. After you create the interface endpoint, it's available to use when it's accepted by the service provider\. The service provider must configure the service to accept requests automatically or manually\. AWS services and AWS Marketplace services generally accept all endpoint requests automatically\. For more information about the lifecycle of an endpoint, see [Interface Endpoint Lifecycle](#vpce-interface-lifecycle)\.

Services cannot initiate requests to resources in your VPC through the endpoint\. An endpoint only returns responses to traffic initiated from resources in your VPC\. Before you integrate a service and an endpoint, review the service specific VPC endpoint documentation for any service specific configuration, and limitations\. 

**Topics**
+ [Private DNS](#vpce-private-dns)
+ [Interface Endpoint Properties and Limitations](#vpce-interface-limitations)
+ [Interface Endpoint Lifecycle](#vpce-interface-lifecycle)
+ [Interface Endpoint Availability Zone Considerations](#vpce-interface-availability-zones)
+ [Pricing for Interface Endpoints](#vpce-interface-pricing)
+ [Creating an Interface Endpoint](#create-interface-endpoint)
+ [Viewing Your Interface Endpoint](#describe-interface-endpoint)
+ [Creating and Managing a Notification for an Interface Endpoint](#create-notification-interface-endpoint)
+ [Accessing a Service Through an Interface Endpoint](#access-service-though-endpoint)
+ [Modifying an Interface Endpoint](#modify-interface-endpoint)

## Private DNS<a name="vpce-private-dns"></a>

When you create an interface endpoint, we generate endpoint\-specific DNS hostnames that you can use to communicate with the service\. For AWS services and AWS Marketplace partner services, private DNS \(enabled by default\) associates a private hosted zone with your VPC\. The hosted zone contains a record set for the default DNS name for the service \(for example, `ec2.us-east-1.amazonaws.com`\) that resolves to the private IP addresses of the endpoint network interfaces in your VPC\. This enables you to make requests to the service using its default DNS hostname instead of the endpoint\-specific DNS hostnames\. For example, if your existing applications make requests to an AWS service, they can continue to make requests through the interface endpoint without requiring any configuration changes\. 

In the following diagram, you have created an interface endpoint for Amazon Kinesis Data Streams and an endpoint network interface in subnet 2\. You have not enabled private DNS for the interface endpoint\. Instances in either subnet can communicate with Amazon Kinesis Data Streams through the interface endpoint using an endpoint\-specific DNS hostname \(DNS name B\)\. Instance in subnet 1 can communicate with Amazon Kinesis Data Streams over public IP address space in the AWS Region using the default DNS name for the service \(DNS name A\)\.

![\[Using an interface endpoint to access Kinesis\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-kinesis-diagram.png)

In the next diagram, you have enabled private DNS for the endpoint\. Instances in either subnet can communicate with Amazon Kinesis Data Streams through the interface endpoint using an endpoint\-specific DNS hostname \(DNS name B\) or the default DNS name for the service \(DNS name A\)\.

![\[Using an interface endpoint to access Kinesis\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-kinesis-private-dns-diagram.png)

**Important**  
To use private DNS, you must set the following VPC attributes to `true`: `enableDnsHostnames` and `enableDnsSupport`\. For more information, see [Updating DNS Support for Your VPC](vpc-dns.md#vpc-dns-updating)\. IAM users must have permission to work with hosted zones\. For more information, see [Authentication and Access Control for Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/auth-and-access-control.html)\.

## Interface Endpoint Properties and Limitations<a name="vpce-interface-limitations"></a>

To use interface endpoints, you need to be aware of their properties and current limitations:
+ For each interface endpoint, you can choose only one subnet per Availability Zone\.
+ Interface endpoints support the use of policies for services that support endpoint policies\. For information about the services that support policies, see [Controlling Access to Services with VPC Endpoints](vpc-endpoints-access.md)\.
+ Services may not be available in all Availability Zones through an interface endpoint\. To find out which Availability Zones are supported, use the [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command or use the Amazon VPC console\. For more information, see [Creating an Interface Endpoint](#create-interface-endpoint)\.
+ When you create an interface endpoint, the endpoint is created in the Availability Zone that is mapped to your account and is independent from other accounts\. When the service provider and the consumer are in different accounts, see [Interface Endpoint Availability Zone Considerations](#vpce-interface-availability-zones) for information about how to use Availability Zone IDs to identify the interface endpoint Availability Zone\.
+ Each interface endpoint can support a bandwidth of up to 10 Gbps per Availability Zone by default\. Additional capacity may be added automatically based on your usage\.
+ If the network ACL for your subnet restricts traffic, you may not be able to send traffic through the endpoint network interface\. Ensure that you add appropriate rules that allow traffic to and from the CIDR block of the subnet\.
+ An interface endpoint supports TCP traffic only\.
+ Endpoints are supported within the same region only\. You cannot create an endpoint between a VPC and a service in a different region\.
+ Endpoints support IPv4 traffic only\.
+ You cannot transfer an endpoint from one VPC to another, or from one service to another\.
+ You have a limit on the number of endpoints you can create per VPC\. For more information, see [VPC Endpoints](amazon-vpc-limits.md#vpc-limits-endpoints)\.

## Interface Endpoint Lifecycle<a name="vpce-interface-lifecycle"></a>

An interface endpoint goes through various stages starting from when you create it \(the endpoint connection request\)\. At each stage, there may be actions that the service consumer and service provider can take\.

![\[Interface endpoint lifecycle\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/interface-endpoint-lifecycle-diagram.png)

The following rules apply:
+ A service provider can configure their service to accept interface endpoint requests automatically or manually\. AWS services and AWS Marketplace services generally accept all endpoint requests automatically\.
+ A service provider cannot delete an interface endpoint to their service\. Only the service consumer that requested the interface endpoint connection can delete the interface endpoint\.
+ A service provider can reject the interface endpoint after it has been accepted \(either manually or automatically\) and is in the `available` state\.

## Interface Endpoint Availability Zone Considerations<a name="vpce-interface-availability-zones"></a>

When you create an interface endpoint, the endpoint is created in the Availability Zone that is mapped to your account and is independent from other accounts\. When the service provider and the consumer are in different accounts, use the Availability Zone ID to uniquely and consistently identify the interface endpoint Availability Zone\. For example, `use1-az1` is an AZ ID for the `us-east-1` Region and maps to the same location in every AWS account\. For information about Availability Zone IDs, see [AZ IDs for Your Resources](https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html) in the *AWS RAM User Guide* or use [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html)\. 

Services may not be available in all Availability Zones through an interface endpoint\. You can use any of the following operations to find out which Availability Zones are supported for a service:
+ [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) \(AWS CLI\)
+ [DescribeVpcEndpointServices](https://docs.aws.amazon.com/AWSEC2/latest/APIReference//API_DescribeVpcEndpointServices.html) \(API\)
+ The Amazon VPC console when you create an interface endpoint\. For more information, see [Creating an Interface Endpoint](#create-interface-endpoint)\.

## Pricing for Interface Endpoints<a name="vpce-interface-pricing"></a>

You are charged for creating and using an interface endpoint to a service\. Hourly usage rates and data processing rates apply\. For more information, see [Amazon VPC Pricing](http://aws.amazon.com/vpc/pricing/)\.

## Creating an Interface Endpoint<a name="create-interface-endpoint"></a>

To create an interface endpoint, you must specify the VPC in which to create the interface endpoint, and the service to which to establish the connection\. 

For AWS services, or AWS Marketplace partner services, you can optionally enable [private DNS](#vpce-private-dns) for the endpoint to enable you to make requests to the service using its default DNS hostname\.

**Important**  
Private DNS is enabled by default for endpoints created for AWS services and AWS Marketplace partner services\. 

For specific information for AWS services, see [VPC Endpoints](vpc-endpoints.md)\.

**To create an interface endpoint to an AWS service using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, ensure that **AWS services** is selected\.

1. For **Service Name**, choose the service to which to connect\. For **Type**, ensure that it indicates **Interface**\.

1. Complete the following information and then choose **Create endpoint**\.
   + For **VPC**, select a VPC in which to create the endpoint\.
   + For **Subnets**, select the subnets \(Availability Zones\) in which to create the endpoint network interfaces\.
**Note**  
Not all Availability Zones may be supported for all AWS services\.
   + To enable private DNS for the interface endpoint, for **Enable Private DNS Name**, select the check box\.
**Note**  
This option is enabled by default\. To use the private DNS option, the following attributes of your VPC must be set to `true`: `enableDnsHostnames` and `enableDnsSupport`\. For more information, see [Updating DNS Support for Your VPC](vpc-dns.md#vpc-dns-updating)\.
   + For **Security group**, select the security groups to associate with the endpoint network interfaces\.

To create an interface endpoint to an endpoint service, you must have the name of the service to which to connect\. The service provider can provide you with the name\. 

**To create an interface endpoint to an endpoint service**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, choose **Find service by name**\.

1. For **Service Name**, enter the name of the service \(for example, `com.amazonaws.vpce.us-east-1.vpce-svc-0e123abc123198abc`\) and choose **Verify**\.

1. Complete the following information and then choose **Create endpoint**\.
   + For **VPC**, select a VPC in which to create the endpoint\.
   + For **Subnets**, select the subnets \(Availability Zones\) in which to create the endpoint network interfaces\.
**Note**  
Not all Availability Zones may be supported for the service\.
   + For **Security group**, select the security groups to associate with the endpoint network interfaces\.

**To create an interface endpoint to an AWS Marketplace partner service**

1. Go to the [PrivateLink](https://aws.amazon.com/marketplace/saas/privatelink) page on AWS Marketplace and subscribe to a service from a software as a service \(SaaS\) provider\. Services that support interface endpoints include an option to connect via an endpoint\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, choose **Your AWS Marketplace services**\.

1. Choose the AWS Marketplace service to which you've subscribed\.

1. Complete the following information and then choose **Create endpoint**\.
   + For **VPC**, select a VPC in which to create the endpoint\.
   + For **Subnets**, select the subnets \(Availability Zones\) in which to create the endpoint network interfaces\.
**Note**  
Not all Availability Zones may be supported for the service\.
   + For **Security group**, select the security groups to associate with the endpoint network interfaces\.

**To create an interface endpoint using the AWS CLI**

1. Use the [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command to get a list of available services\. In the output that's returned, take note of the name of the service to which to connect\. The `ServiceType` field indicates whether you connect to the service via an interface or gateway endpoint\. The `ServiceName` field provides the name of the service\.

   ```
   aws ec2 describe-vpc-endpoint-services
   ```

   ```
   {
       "VpcEndpoints": [
           {
               "VpcEndpointId": "vpce-08a979e28f97a9f7c",
               "VpcEndpointType": "Interface",
               "VpcId": "vpc-06e4ab6c6c3b23ae3",
               "ServiceName": "com.amazonaws.us-east-2.monitoring",
               "State": "available",
               "PolicyDocument": "{\n  \"Statement\": [\n    {\n      \"Action\": \"*\", \n      \"Effect\": \"Allow\", \n      \"Principal\": \"*\", \n      \"Resource\": \"*\"\n    }\n  ]\n}",
               "RouteTableIds": [],
               "SubnetIds": [
                   "subnet-0931fc2fa5f1cbe44"
               ],
               "Groups": [
                   {
                       "GroupId": "sg-06e1d57ab87d8f182",
                       "GroupName": "default"
                   }
               ],
               "PrivateDnsEnabled": false,
               "RequesterManaged": false,
               "NetworkInterfaceIds": [
                   "eni-019b0bb3ede80ebfd"
               ],
               "DnsEntries": [
                   {
                       "DnsName": "vpce-08a979e28f97a9f7c-4r5zme9n.monitoring.us-east-2.vpce.amazonaws.com",
                       "HostedZoneId": "ZC8PG0KIFKBRI"
                   },
                   {
                       "DnsName": "vpce-08a979e28f97a9f7c-4r5zme9n-us-east-2c.monitoring.us-east-2.vpce.amazonaws.com",
                       "HostedZoneId": "ZC8PG0KIFKBRI"
                   }
               ],
               "CreationTimestamp": "2019-06-04T19:10:37.000Z",
               "Tags": [],
               "OwnerId": "123456789012"
           }
       ]
   ```

1. To create an interface endpoint, use the [create\-vpc\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint.html) command and specify the VPC ID, type of VPC endpoint \(interface\), service name, subnets that will use the endpoint, and security groups to associate with the endpoint network interfaces\.

   The following example creates an interface endpoint to the Elastic Load Balancing service\.

   ```
   aws ec2 create-vpc-endpoint --vpc-id vpc-ec43eb89 --vpc-endpoint-type Interface --service-name com.amazonaws.us-east-1.elasticloadbalancing --subnet-id subnet-abababab --security-group-id sg-1a2b3c4d
   ```

   ```
   {
       "VpcEndpoint": {
           "PolicyDocument": "{\n  \"Statement\": [\n    {\n      \"Action\": \"*\", \n      \"Effect\": \"Allow\", \n      \"Principal\": \"*\", \n      \"Resource\": \"*\"\n    }\n  ]\n}", 
           "VpcId": "vpc-ec43eb89", 
           "NetworkInterfaceIds": [
               "eni-bf8aa46b"
           ], 
           "SubnetIds": [
               "subnet-abababab"
           ], 
           "PrivateDnsEnabled": true, 
           "State": "pending", 
           "ServiceName": "com.amazonaws.us-east-1.elasticloadbalancing", 
           "RouteTableIds": [], 
           "Groups": [
               {
                   "GroupName": "default", 
                   "GroupId": "sg-1a2b3c4d"
               }
           ], 
           "VpcEndpointId": "vpce-088d25a4bbf4a7abc", 
           "VpcEndpointType": "Interface", 
           "CreationTimestamp": "2017-09-05T20:14:41.240Z", 
           "DnsEntries": [
               {
                   "HostedZoneId": "Z7HUB22UULQXV", 
                   "DnsName": "vpce-088d25a4bbf4a7abc-ks83awe7.elasticloadbalancing.us-east-1.vpce.amazonaws.com"
               }, 
               {
                   "HostedZoneId": "Z7HUB22UULQXV", 
                   "DnsName": "vpce-088d25a4bbf4a7abc-ks83awe7-us-east-1a.elasticloadbalancing.us-east-1.vpce.amazonaws.com"
               }, 
               {
                   "HostedZoneId": "Z1K56Z6FNPJRR", 
                   "DnsName": "elasticloadbalancing.us-east-1.amazonaws.com"
               }
           ]
       }
   }
   ```

   Alternatively, the following example creates an interface endpoint to an endpoint service in another AWS account \(the service provider provides you with the name of the endpoint service\)\.

   ```
   aws ec2 create-vpc-endpoint --vpc-id vpc-ec43eb89 --vpc-endpoint-type Interface --service-name com.amazonaws.vpce.us-east-1.vpce-svc-0e123abc123198abc --subnet-id subnet-abababab --security-group-id sg-1a2b3c4d
   ```

   In the output that's returned, take note of the `DnsName` fields\. You can use these DNS names to access the AWS service\.

**To describe available services using the AWS Tools for Windows PowerShell or API**
+ [Get\-EC2VpcEndpointService](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcEndpointService.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeVpcEndpointServices](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpointServices.html) \(Amazon EC2 Query API\)

**To create a VPC endpoint using the AWS Tools for Windows PowerShell or API**
+ [New\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpoint.html) \(AWS Tools for Windows PowerShell\)
+ [CreateVpcEndpoint](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpoint.html) \(Amazon EC2 Query API\)

## Viewing Your Interface Endpoint<a name="describe-interface-endpoint"></a>

After you've created an interface endpoint, you can view information about it\.

**To view information about an interface endpoint using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your interface endpoint\.

1. To view information about the interface endpoint, choose **Details**\. The **DNS Names** field displays the DNS names to use to access the service\. 

1. To view the subnets in which the interface endpoint has been created, and the ID of the endpoint network interface in each subnet, choose **Subnets**\. 

1. To view the security groups that are associated with the endpoint network interface, choose **Security Groups**\.

**To describe your interface endpoint using the AWS CLI**
+ You can describe your endpoint using the [describe\-vpc\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoints.html) command\.

  ```
  aws ec2 describe-vpc-endpoints --vpc-endpoint-ids vpce-088d25a4bbf4a7abc
  ```

**To describe your VPC endpoints using the AWS Tools for Windows PowerShell or API**
+ [Get\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcEndpoint.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeVpcEndpoints](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpoints.html) \(Amazon EC2 Query API\)

## Creating and Managing a Notification for an Interface Endpoint<a name="create-notification-interface-endpoint"></a>

You can create a notification to receive alerts for specific events that occur on your interface endpoint\. For example, you can receive an email when the interface endpoint is accepted by the service provider\. To create a notification, you must associate an [Amazon SNS topic](https://docs.aws.amazon.com/sns/latest/dg/) with the notification\. You can subscribe to the SNS topic to receive an email notification when an endpoint event occurs\. 

The Amazon SNS topic that you use for notifications must have a topic policy that allows Amazon's VPC endpoint service to publish notifications on your behalf\. Ensure that you include the following statement in your topic policy\. For more information, see [Managing Access to Your Amazon SNS Topics](https://docs.aws.amazon.com/sns/latest/dg/AccessPolicyLanguage.html) in the *Amazon Simple Notification Service Developer Guide*\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "vpce.amazonaws.com"
      },
      "Action": "SNS:Publish",
      "Resource": "arn:aws:sns:region:account:topic-name"
    }
  ]
}
```

**To create a notification for an interface endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your interface endpoint\.

1. Choose **Actions**, **Create notification**\.

1. Choose the ARN for the SNS topic to associate with the notification\.

1. For **Events**, select the endpoint events for which to receive notifications\.

1. Choose **Create Notification**\.

After you create a notification, you can change the SNS topic that's associated with the notification, or you can specify different endpoint events for the notification\.

**To modify a notification for an endpoint service**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your interface endpoint\.

1. Choose **Actions**, **Modify Notification**\.

1. Specify the ARN for the SNS topic and change the endpoint events as required\.

1. Choose **Modify Notification**\.

If you no longer need a notification, you can delete it\. 

**To delete a notification**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your interface endpoint\.

1. Choose **Actions**, **Delete notification**\.

1. Choose **Yes, Delete**\.

**To create and manage a notification using the AWS CLI**

1. To create a notification for an interface endpoint, use the [create\-vpc\-endpoint\-connection\-notification](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint-connection-notification.html) command and specify the ARN of the SNS topic, the events for which to be notified, and the ID of the endpoint; for example:

   ```
   aws ec2 create-vpc-endpoint-connection-notification --connection-notification-arn arn:aws:sns:us-east-2:123456789012:EndpointNotification --connection-events Accept Reject --vpc-endpoint-id vpce-123abc3420c1931d7
   ```

1. To view your notifications, use the [describe\-vpc\-endpoint\-connection\-notifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-connection-notifications.html) command:

   ```
   aws ec2 describe-vpc-endpoint-connection-notifications
   ```

1. To change the SNS topic or endpoint events for the notification, use the [modify\-vpc\-endpoint\-connection\-notification](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-connection-notification.html) command; for example:

   ```
   aws ec2 modify-vpc-endpoint-connection-notification --connection-notification-id vpce-nfn-008776de7e03f5abc --connection-events Accept --connection-notification-arn arn:aws:sns:us-east-2:123456789012:mytopic
   ```

1. To delete a notification, use the [delete\-vpc\-endpoint\-connection\-notifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc-endpoint-connection-notifications.html) command:

   ```
   aws ec2 delete-vpc-endpoint-connection-notifications --connection-notification-ids vpce-nfn-008776de7e03f5abc
   ```

## Accessing a Service Through an Interface Endpoint<a name="access-service-though-endpoint"></a>

After you've created an interface endpoint, you can submit requests to the supported service via an endpoint URL\. You can use the following:
+ The endpoint\-specific regional DNS hostname that we generate for the interface endpoint\. The hostname includes a unique endpoint identifier, service identifier, the Region, and `vpce.amazonaws.com` in its name; for example, `vpce-0fe5b17a0707d6abc-29p5708s.ec2.us-east-1.vpce.amazonaws.com`\.
+ The endpoint\-specific zonal DNS hostname that we generate for each Availability Zone in which the endpoint is available\. The hostname includes the Availability Zone in its name; for example, `vpce-0fe5b17a0707d6abc-29p5708s-us-east-1a.ec2.us-east-1.vpce.amazonaws.com`\. You might use this option if your architecture isolates Availability Zones \(for example, for fault containment or to reduce regional data transfer costs\)\.
**Note**  
A request to the zonal DNS hostname is destined to the corresponding Availability Zone location in the service provider's account, which might not have the same Availability Zone name as your account\. For more information, see [Region and Availability Zone Concepts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-regions-availability-zones)\.
+ If you have enabled private DNS for the endpoint \(a private hosted zone\), the default DNS hostname for the AWS service for the Region; for example, `ec2.us-east-1.amazonaws.com`\.
+ The private IP address of the endpoint network interface in the VPC\.

For example, in a subnet in which you have an interface endpoint to Elastic Load Balancing and for which you have not enabled the private DNS option, use the following AWS CLI command from an instance to describe your load balancers\. The command uses the endpoint\-specific regional DNS hostname to make the request using the interface endpoint:

```
aws elbv2 describe-load-balancers --endpoint-url https://vpce-0f89a33420c193abc-bluzidnv.elasticloadbalancing.us-east-1.vpce.amazonaws.com/
```

If you enable the private DNS option, you do not have to specify the endpoint URL in the request\. The AWS CLI uses the default endpoint for the service for the Region \(`elasticloadbalancing.us-east-1.amazonaws.com`\)\.

## Modifying an Interface Endpoint<a name="modify-interface-endpoint"></a>

You can modify an interface endpoint by changing the subnet in which the interface endpoint is located, changing the security groups that are associated with the endpoint network interface, and modifying the tags\. If you remove a subnet for the interface endpoint, the corresponding endpoint network interface in the subnet is deleted\.

**To change the subnets for an interface endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select the interface endpoint\.

1. Choose **Actions**, **Manage Subnets**\.

1. Select or deselect the subnets as required, and choose **Modify Subnets**\. 

**To add or remove the security groups associated with an interface endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select the interface endpoint\.

1. Choose **Actions**, **Manage security groups**\.

1. Select or deselect the security groups as required, and choose **Save**\.

**To add or remove an interface endpoint tag**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**\.

1. Select the interface endpoint and choose **Actions**, **Add/Edit Tags**\.

1. Add or remove a tag\.

   \[Add a tag\] Choose **Create tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

**To modify a VPC endpoint using the AWS CLI**

1. Use the [describe\-vpc\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoints.html) command to get the ID of your interface endpoint\.

   ```
   aws ec2 describe-vpc-endpoints
   ```

1. The following example uses the [modify\-vpc\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint.html) command to add subnet `subnet-aabb1122` to the interface endpoint\.

   ```
   aws ec2 modify-vpc-endpoint --vpc-endpoint-id vpce-0fe5b17a0707d6abc --add-subnet-id subnet-aabb1122
   ```

**To modify a VPC endpoint using the AWS Tools for Windows PowerShell or an API**
+ [Edit\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcEndpoint.html) \(AWS Tools for Windows PowerShell\)
+ [ModifyVpcEndpoint](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcEndpoint.html) \(Amazon EC2 Query API\)

**To add or remove a VPC endpoint tag using the AWS Tools for Windows PowerShell or an API**
+ [tag\-resource](https://docs.aws.amazon.com/cli/latest/reference/directconnect/tag-resource.html) \(AWS CLI\) 
+ [TagResource](https://docs.aws.amazon.com/directconnect/latest/APIReference/API_TagResource.html) \(AWS Tools for Windows PowerShell\)
+ [untag\-resource](https://docs.aws.amazon.com/cli/latest/reference/directconnect/untag-resource.html) \(AWS CLI\) 
+ [TagResource](https://docs.aws.amazon.com/directconnect/latest/APIReference/API_UntagResource.html) \(AWS Tools for Windows PowerShell\)