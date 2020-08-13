# Interface VPC endpoints \(AWS PrivateLink\)<a name="vpce-interface"></a>

An interface VPC endpoint \(interface endpoint\) enables you to connect to services powered by AWS PrivateLink\. These services include some AWS services, services hosted by other AWS customers and Partners in their own VPCs \(referred to as *endpoint services*\), and supported AWS Marketplace Partner services\. The owner of the service is the *service provider*, and you, as the principal creating the interface endpoint, are the *service consumer*\.

The following are the general steps for setting up an interface endpoint:

1. Choose the VPC in which to create the interface endpoint, and provide the name of the AWS service, endpoint service, or AWS Marketplace service to which you're connecting\.

1. Choose a subnet in your VPC to use the interface endpoint\. We create an *endpoint network interface* in the subnet\. You can specify more than one subnet in different Availability Zones \(as supported by the service\) to help ensure that your interface endpoint is resilient to Availability Zone failures\. In that case, we create an endpoint network interface in each subnet that you specify\. 
**Note**  
An endpoint network interface is a requester\-managed network interface\. You can view it in your account, but you cannot manage it yourself\. For more information, see [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html)\.

1. Specify the security groups to associate with the endpoint network interface\. The security group rules control the traffic to the endpoint network interface from resources in your VPC\. If you do not specify a security group, we associate the default security group for the VPC\.

1. \(Optional, AWS services and AWS Marketplace Partner services only\) Enable [private DNS](#vpce-private-dns) for the endpoint to enable you to make requests to the service using its default DNS hostname\.
**Important**  
Private DNS is enabled by default for endpoints created for AWS services and AWS Marketplace Partner services\.   
Private DNS is enabled in the other subnets which are in the same VPC and Availability Zone or Local Zone\.

1. When the service provider and the consumer are in different accounts, see [Interface endpoint Availability Zone considerations](#vpce-interface-availability-zones) for information about how to use Availability Zone IDs to identify the interface endpoint Availability Zone\.

1. After you create the interface endpoint, it's available to use when it's accepted by the service provider\. The service provider must configure the service to accept requests automatically or manually\. AWS services and AWS Marketplace services generally accept all endpoint requests automatically\. For more information about the lifecycle of an endpoint, see [Interface endpoint lifecycle](#vpce-interface-lifecycle)\.

Services cannot initiate requests to resources in your VPC through the endpoint\. An endpoint only returns responses to traffic that is initiated from resources in your VPC\. Before you integrate a service and an endpoint, review the service\-specific VPC endpoint documentation for any service\-specific configuration and limitations\. 

**Topics**
+ [Private DNS for interface endpoints](#vpce-private-dns)
+ [Interface endpoint properties and limitations](#vpce-interface-limitations)
+ [Connection to on\-premises data centers](#on-premises-connection)
+ [Interface endpoint lifecycle](#vpce-interface-lifecycle)
+ [Interface endpoint Availability Zone considerations](#vpce-interface-availability-zones)
+ [Pricing for interface endpoints](#vpce-interface-pricing)
+ [Viewing available AWS service names](#vpce-view-services)
+ [Creating an interface endpoint](#create-interface-endpoint)
+ [Viewing your interface endpoint](#describe-interface-endpoint)
+ [Creating and managing a notification for an interface endpoint](#create-notification-interface-endpoint)
+ [Accessing a service through an interface endpoint](#access-service-though-endpoint)
+ [Modifying an interface endpoint](#modify-interface-endpoint)

## Private DNS for interface endpoints<a name="vpce-private-dns"></a>

When you create an interface endpoint, we generate endpoint\-specific DNS hostnames that you can use to communicate with the service\. For AWS services and AWS Marketplace Partner services, the private DNS option \(enabled by default\) associates a private hosted zone with your VPC\. The hosted zone contains a record set for the default DNS name for the service \(for example, `ec2.us-east-1.amazonaws.com`\) that resolves to the private IP addresses of the endpoint network interfaces in your VPC\. This enables you to make requests to the service using its default DNS hostname instead of the endpoint\-specific DNS hostnames\. For example, if your existing applications make requests to an AWS service, they can continue to make requests through the interface endpoint without requiring any configuration changes\. 

In the example shown in the following diagram, there is an interface endpoint for Amazon Kinesis Data Streams and an endpoint network interface in subnet 2\. Private DNS for the interface endpoint is not enabled\. The route tables for the subnets have the following routes\.


|  | 
| --- |
| Subnet 1 | 
| Destination | Target | 
| 10\.0\.0\.0/16 | Local | 
| 0\.0\.0\.0/0 | internet\-gateway\-id | 
| Subnet 2 | 
| Destination | Target | 
| 10\.0\.0\.0/16 | Local | 

Instances in either subnet can send requests to Amazon Kinesis Data Streams through the interface endpoint using an endpoint\-specific DNS hostname\. Instances in subnet 1 can communicate with Amazon Kinesis Data Streams over public IP address space in the AWS Region using its default DNS name\.

![\[Using an interface endpoint and public internet to access Kinesis\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-kinesis-diagram.png)

In the next diagram, private DNS for the endpoint has been enabled\. Instances in either subnet can send requests to Amazon Kinesis Data Streams through the interface endpoint using either the default DNS hostname or the endpoint\-specific DNS hostname\.

![\[Using an interface endpoint to access Kinesis\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-kinesis-private-dns-diagram.png)

**Important**  
To use private DNS, you must set the following VPC attributes to `true`: `enableDnsHostnames` and `enableDnsSupport`\. For more information, see [Viewing and updating DNS support for your VPC](vpc-dns.md#vpc-dns-updating)\. IAM users must have permission to work with hosted zones\. For more information, see [Authentication and Access Control for Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/auth-and-access-control.html)\.

## Interface endpoint properties and limitations<a name="vpce-interface-limitations"></a>

To use interface endpoints, you need to be aware of their properties and current limitations:
+ For each interface endpoint, you can choose only one subnet per Availability Zone\.
+ Some services support the use of endpoint policies to control access to the service\. For more information about the services that support endpoint policies, see [Controlling access to services with VPC endpoints](vpc-endpoints-access.md)\.
+ Services might not be available in all Availability Zones through an interface endpoint\. To find out which Availability Zones are supported, use the [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command or use the Amazon VPC console\. For more information, see [Creating an interface endpoint](#create-interface-endpoint)\.
+ When you create an interface endpoint, the endpoint is created in the Availability Zone that is mapped to your account and that is independent from other accounts\. When the service provider and the consumer are in different accounts, see [Interface endpoint Availability Zone considerations](#vpce-interface-availability-zones) for information about how to use Availability Zone IDs to identify the interface endpoint Availability Zone\.
+ When the service provider and the consumer have different accounts and use multiple Availability Zones, and the consumer views the VPC endpoint service information, the response only includes the common Availability Zones\. For example, when the service provider account uses `us-east-1a` and `us-east-1c` and the consumer uses `us-east-1a` and `us-east-1b`, the response includes the VPC endpoint services in the common Availability Zone, `us-east-1a`\.
+ By default, each interface endpoint can support a bandwidth of up to 10 Gbps per Availability Zone\. and bursts of up to 40Gbps\. If your application needs higher bursts or sustained throughput, contact AWS support\. 
+ If the network ACL for your subnet restricts traffic, you might not be able to send traffic through the endpoint network interface\. Ensure that you add appropriate rules that allow traffic to and from the CIDR block of the subnet\.
+ Ensure that the security group that's associated with the endpoint network interface allows communication between the endpoint network interface and the resources in your VPC that communicate with the service\. If the security group restricts inbound HTTPS traffic \(port 443\) from resources in the VPC, you might not be able to send traffic through the endpoint network interface\.
+ An interface endpoint supports TCP traffic only\.
+ When you create an endpoint, you can attach an endpoint policy to it that controls access to the service to which you are connecting\. For more information, see [Policy Best Practices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-policy-examples.html#security_iam_service-with-iam-policy-best-practices) and [Controlling access to services with VPC endpoints](vpc-endpoints-access.md)\.
+ Review the service\-specific limits for your endpoint service\.
+ Endpoints are supported within the same Region only\. You cannot create an endpoint between a VPC and a service in a different Region\.
+ Endpoints support IPv4 traffic only\.
+ You cannot transfer an endpoint from one VPC to another, or from one service to another\.
+ You have a quota on the number of endpoints you can create per VPC\. For more information, see [VPC endpoints](amazon-vpc-limits.md#vpc-limits-endpoints)\.

## Connection to on\-premises data centers<a name="on-premises-connection"></a>

You can use the following types of connections for a connection between an interface endpoint and your on\-premises data center:
+ [AWS Direct Connect](https://docs.aws.amazon.com/directconnect/latest/UserGuide/)
+ [AWS Site\-to\-Site VPN](https://docs.aws.amazon.com/vpn/latest/s2svpn/)

## Interface endpoint lifecycle<a name="vpce-interface-lifecycle"></a>

An interface endpoint goes through various stages starting from when you create it \(the endpoint connection request\)\. At each stage, there might be actions that the service consumer and service provider can take\.

![\[Interface endpoint lifecycle\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/interface-endpoint-lifecycle-diagram.png)

The following rules apply:
+ A service provider can configure their service to accept interface endpoint requests automatically or manually\. AWS services and AWS Marketplace services generally accept all endpoint requests automatically\.
+ A service provider cannot delete an interface endpoint to their service\. Only the service consumer that requested the interface endpoint connection can delete the interface endpoint\.
+ A service provider can reject the interface endpoint after it has been accepted \(either manually or automatically\) and is in the `available` state\.

## Interface endpoint Availability Zone considerations<a name="vpce-interface-availability-zones"></a>

When you create an interface endpoint, the endpoint is created in the Availability Zone that is mapped to your account and that is independent from other accounts\. When the service provider and the consumer are in different accounts, use the Availability Zone ID to uniquely and consistently identify the interface endpoint Availability Zone\. For example, `use1-az1` is an Availability Zone ID for the `us-east-1` Region and maps to the same location in every AWS account\. For information about Availability Zone IDs, see [AZ IDs for Your Resources](https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html) in the *AWS RAM User Guide* or use [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html)\. 

Services might not be available in all Availability Zones through an interface endpoint\. You can use any of the following operations to find out which Availability Zones are supported for a service:
+ [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) \(AWS CLI\)
+ [DescribeVpcEndpointServices](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcEndpointServices.html) \(API\)
+ The Amazon VPC console when you create an interface endpoint\. For more information, see [Creating an interface endpoint](#create-interface-endpoint)\.

## Pricing for interface endpoints<a name="vpce-interface-pricing"></a>

You are charged for creating and using an interface endpoint to a service\. Hourly usage rates and data processing rates apply\. For more information about interface endpoint pricing, see [AWS PrivateLink Pricing](https://aws.amazon.com/privatelink/pricing/)\. You can view the total number of interface endpoints using the Amazon VPC Console, or the AWS CLI\.

## Viewing available AWS service names<a name="vpce-view-services"></a>

When you use the Amazon VPC console to create an endpoint, you can get a list of available AWS service names\.

When you use the AWS CLI to create an endpoint, you can use the [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command to view the service names, and then create the endpoint using the [create\-vpc\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint.html) command \.

------
#### [ Console ]

**To view available AWS services using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. In the **Service Name** section, the available services are listed\.

------
#### [ Command line ]

**To view available AWS services using the AWS CLI**
+ Use the [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command to get a list of available services\. In the output that's returned, take note of the name of the service to which to connect\. The `ServiceType` field indicates whether you connect to the service via an interface or gateway endpoint\. The `ServiceName` field provides the name of the service\.

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

**To view available AWS services using the AWS Tools for Windows PowerShell**
+ [Get\-EC2VpcEndpointService](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcEndpointService.html) 

**To view available AWS services using the API**
+ [DescribeVpcEndpointServices](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpointServices.html)

------

## Creating an interface endpoint<a name="create-interface-endpoint"></a>

To create an interface endpoint, you must specify the VPC in which to create the interface endpoint, and the service to which to establish the connection\. 

For AWS services, or AWS Marketplace Partner services, you can optionally enable [private DNS](#vpce-private-dns) for the endpoint to enable you to make requests to the service using its default DNS hostname\.

**Important**  
Private DNS is enabled by default for endpoints created for AWS services and AWS Marketplace Partner services\. 

------
#### [ Console ]

**To create an interface endpoint to an AWS service using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, ensure that **AWS services** is selected\.

1. For **Service Name**, choose the service to which to connect\. For **Type**, ensure that it indicates **Interface**\.

1. Complete the following information and then choose **Create endpoint**\.
   + For **VPC**, select a VPC in which to create the endpoint\.
   + For **Subnets**, select the subnets \(Availability Zones\) in which to create the endpoint network interfaces\.

     Not all Availability Zones may be supported for all AWS services\.
   + To enable private DNS for the interface endpoint, for **Enable Private DNS Name**, select the check box\.

     This option is enabled by default\. To use the private DNS option, the following attributes of your VPC must be set to `true`: `enableDnsHostnames` and `enableDnsSupport`\. For more information, see [Viewing and updating DNS support for your VPC](vpc-dns.md#vpc-dns-updating)\.
   + For **Security group**, select the security groups to associate with the endpoint network interfaces\.
   + \(Optional\) Add or remove a tag\.

     \[Add a tag\] Choose **Add tag** and do the following:
     + For **Key**, enter the key name\.
     + For **Value**, enter the key value\.

     \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

To create an interface endpoint to an endpoint service, you must have the name of the service to which to connect\. The service provider can provide you with the name\. 

**To create an interface endpoint to an endpoint service**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, choose **Find service by name**\.

1. For **Service Name**, enter the name of the service \(for example, `com.amazonaws.vpce.us-east-1.vpce-svc-0e123abc123198abc`\) and choose **Verify**\.

1. Complete the following information and then choose **Create endpoint**\.
   + For **VPC**, select a VPC in which to create the endpoint\.
   + For **Subnets**, select the subnets \(Availability Zones\) in which to create the endpoint network interfaces\.

     Not all Availability Zones may be supported for the service\.
   + For **Security group**, select the security groups to associate with the endpoint network interfaces\.
   + \(Optional\) Add or remove a tag\.

     \[Add a tag\] Choose **Add tag** and do the following:
     + For **Key**, enter the key name\.
     + For **Value**, enter the key value\.

     \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

**To create an interface endpoint to an AWS Marketplace Partner service**

1. Go to the [PrivateLink](https://aws.amazon.com/marketplace/saas/privatelink) page in AWS Marketplace and subscribe to a service from a software as a service \(SaaS\) provider\. Services that support interface endpoints include an option to connect via an endpoint\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, choose **Your AWS Marketplace services**\.

1. Choose the AWS Marketplace service to which you've subscribed\.

1. Complete the following information and then choose **Create endpoint**\.
   + For **VPC**, select a VPC in which to create the endpoint\.
   + For **Subnets**, select the subnets \(Availability Zones\) in which to create the endpoint network interfaces\.

     Not all Availability Zones may be supported for the service\.
   + For **Security group**, select the security groups to associate with the endpoint network interfaces\.
   + \(Optional\) Add or remove a tag\.

     \[Add a tag\] Choose **Add tag** and do the following:
     + For **Key**, enter the key name\.
     + For **Value**, enter the key value\.

     \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

------
#### [ Command line ]

**To create an interface endpoint using the AWS CLI**

1. Use the [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command to get a list of available services\. In the output that's returned, take note of the name of the service to which to connect\. The `ServiceType` field indicates whether you connect to the service via an interface or gateway endpoint\. The `ServiceName` field provides the name of the service\.

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

**To describe available services and create a VPC endpoint using the AWS Tools for Windows PowerShell**
+ [Get\-EC2VpcEndpointService](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcEndpointService.html) 
+ [New\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpoint.html)

**To describe available services and create a VPC endpoint using the API**
+ [DescribeVpcEndpointServices](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpointServices.html)
+ [CreateVpcEndpoint](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpoint.html)

------

## Viewing your interface endpoint<a name="describe-interface-endpoint"></a>

After you've created an interface endpoint, you can view information about it\.

------
#### [ Console ]

**To view information about an interface endpoint using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your interface endpoint\.

1. To view information about the interface endpoint, choose **Details**\. The **DNS Names** field displays the DNS names to use to access the service\. 

1. To view the subnets in which the interface endpoint has been created, and the ID of the endpoint network interface in each subnet, choose **Subnets**\. 

1. To view the security groups that are associated with the endpoint network interface, choose **Security Groups**\.

------
#### [ Command line ]

**To describe your interface endpoint using the AWS CLI**
+ You can describe your endpoint using the [describe\-vpc\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoints.html) command\.

  ```
  aws ec2 describe-vpc-endpoints --vpc-endpoint-ids vpce-088d25a4bbf4a7abc
  ```

**To describe your VPC endpoints using the AWS Tools for PowerShell or API**
+ [Get\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcEndpoint.html) \(Tools for Windows PowerShell\)
+ [DescribeVpcEndpoints](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpoints.html) \(Amazon EC2 Query API\)

------

## Creating and managing a notification for an interface endpoint<a name="create-notification-interface-endpoint"></a>

You can create a notification to receive alerts for specific events that occur on your interface endpoint\. For example, you can receive an email when the interface endpoint is accepted by the service provider\. To create a notification, you must associate an [Amazon SNS topic](https://docs.aws.amazon.com/sns/latest/dg/) with the notification\. You can subscribe to the SNS topic to receive an email notification when an endpoint event occurs\. 

The Amazon SNS topic that you use for notifications must have a topic policy that allows Amazon's VPC endpoint service to publish notifications on your behalf\. Ensure that you include the following statement in your topic policy\. For more information, see [Identity and Access Management in Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-authentication-and-access-control.html) in the *Amazon Simple Notification Service Developer Guide*\.

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

------
#### [ Console ]

**To create a notification for an interface endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your interface endpoint\.

1. Choose **Actions**, **Create notification**\.

1. Choose the ARN for the SNS topic to associate with the notification\.

1. For **Events**, select the endpoint events for which to receive notifications\.

1. Choose **Create Notification**\.

After you create a notification, you can change the SNS topic that's associated with the notification\. You can also specify different endpoint events for the notification\.

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

------
#### [ Command line ]

**To create and manage a notification using the AWS CLI**

1. To create a notification for an interface endpoint, use the [create\-vpc\-endpoint\-connection\-notification](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint-connection-notification.html) command\. Specify the ARN of the SNS topic, the events for which to be notified, and the ID of the endpoint, as shown in the following example\.

   ```
   aws ec2 create-vpc-endpoint-connection-notification --connection-notification-arn arn:aws:sns:us-east-2:123456789012:EndpointNotification --connection-events Accept Reject --vpc-endpoint-id vpce-123abc3420c1931d7
   ```

1. To view your notifications, use the [describe\-vpc\-endpoint\-connection\-notifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-connection-notifications.html) command\.

   ```
   aws ec2 describe-vpc-endpoint-connection-notifications
   ```

1. To change the SNS topic or endpoint events for the notification, use the [modify\-vpc\-endpoint\-connection\-notification](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-connection-notification.html) command\.

   ```
   aws ec2 modify-vpc-endpoint-connection-notification --connection-notification-id vpce-nfn-008776de7e03f5abc --connection-events Accept --connection-notification-arn arn:aws:sns:us-east-2:123456789012:mytopic
   ```

1. To delete a notification, use the [delete\-vpc\-endpoint\-connection\-notifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc-endpoint-connection-notifications.html) command\.

   ```
   aws ec2 delete-vpc-endpoint-connection-notifications --connection-notification-ids vpce-nfn-008776de7e03f5abc
   ```

------

## Accessing a service through an interface endpoint<a name="access-service-though-endpoint"></a>

After you've created an interface endpoint, you can submit requests to the supported service via an endpoint URL\. You can use the following:
+ If you have enabled private DNS for the endpoint \(a private hosted zone; applicable to AWS services and AWS Marketplace Partner services only\), the default DNS hostname for the AWS service for the Region\. For example, `ec2.us-east-1.amazonaws.com`\.
+ The endpoint\-specific Regional DNS hostname that we generate for the interface endpoint\. The hostname includes a unique endpoint identifier, service identifier, the Region, and `vpce.amazonaws.com` in its name\. For example, `vpce-0fe5b17a0707d6abc-29p5708s.ec2.us-east-1.vpce.amazonaws.com`\.
+ The endpoint\-specific zonal DNS hostname that we generate for each Availability Zone in which the endpoint is available\. The hostname includes the Availability Zone in its name\. For example, `vpce-0fe5b17a0707d6abc-29p5708s-us-east-1a.ec2.us-east-1.vpce.amazonaws.com`\. You might use this option if your architecture isolates Availability Zones \(for example, for fault containment or to reduce Regional data transfer costs\)\.

  A request to the zonal DNS hostname is destined to the corresponding Availability Zone location in the service provider's account, which might not have the same Availability Zone name as your account\. For more information, see [Region and Availability Zone Concepts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-regions-availability-zones)\.
+ The private IP address of the endpoint network interface in the VPC\.

To get the Regional and zonal DNS names, see [Viewing your interface endpoint](#describe-interface-endpoint)\.

For example, in a subnet in which you have an interface endpoint to Elastic Load Balancing and for which you have not enabled the private DNS option, use the following AWS CLI command from an instance to describe your load balancers\. The command uses the endpoint\-specific Regional DNS hostname to make the request using the interface endpoint\.

```
aws elbv2 describe-load-balancers --endpoint-url https://vpce-0f89a33420c193abc-bluzidnv.elasticloadbalancing.us-east-1.vpce.amazonaws.com/
```

If you enable the private DNS option, you do not have to specify the endpoint URL in the request\. The AWS CLI uses the default endpoint for the AWS service for the Region \(`elasticloadbalancing.us-east-1.amazonaws.com`\)\.

## Modifying an interface endpoint<a name="modify-interface-endpoint"></a>

You can modify the following attributes of an interface endpoint:
+ The subnet in which the interface endpoint is located
+ The security groups that are associated with the endpoint network interface
+ The tags
+ The private DNS option
+ The endpoint policy \(if supported by the service\)

 If you remove a subnet for the interface endpoint, the corresponding endpoint network interface in the subnet is deleted\.

------
#### [ Console ]

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

**To modify the private DNS option**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select the interface endpoint\.

1. Choose **Actions**, **Modify Private DNS names**\.

1. Enable or disable the option as required, and choose **Modify Private DNS names**\.

**To update the endpoint policy**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select the interface endpoint\.

1. Choose **Actions**, **Edit policy**\.

1. Choose **Full Access** to allow full access to the service, or choose **Custom** and specify a custom policy\. Choose **Save**\.

------
#### [ Command line ]

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

------