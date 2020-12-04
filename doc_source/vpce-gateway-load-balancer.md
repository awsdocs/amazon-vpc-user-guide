# Gateway Load Balancer endpoints \(AWS PrivateLink\)<a name="vpce-gateway-load-balancer"></a>

A Gateway Load Balancer endpoint enables you to intercept traffic and route it to a service that you've configured using [Gateway Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html), for example, for security inspection\. The owner of the service is the *service provider*, and you, as the principal creating the Gateway Load Balancer endpoint, are the *service consumer*\.

The following are the general steps for setting up a Gateway Load Balancer endpoint:

1. Ensure that a Gateway Load Balancer endpoint service is configured\. For more information, see [VPC endpoint services for Gateway Load Balancer endpoints](vpc-endpoint-services-gwlbe.md)\.

1. Choose the VPC in which to create the Gateway Load Balancer endpoint, and provide the name of the service\.

1. Choose a subnet in your VPC to use the Gateway Load Balancer endpoint\. We create an *endpoint network interface* in the subnet\. An endpoint network interface is assigned a private IP address from the IP address range of your subnet, and keeps this IP address until the Gateway Load Balancer endpoint is deleted\.
**Note**  
An endpoint network interface is a requester\-managed network interface\. You can view it in your account, but you cannot manage it yourself\. For more information, see [Requester\-managed network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/requester-managed-eni.html)\.  
You can specify only one subnet for the Gateway Load Balancer endpoint\. You cannot change the subnet later\.

1. After you create the Gateway Load Balancer endpoint, it's available to use when it's accepted by the service provider\. The service provider can configure the service to accept requests automatically or manually\.

1. Configure your subnet route table and gateway route table to point traffic to the Gateway Load Balancer endpoint\. For more information, see [Routing to a Gateway Load Balancer endpoint](route-table-options.md#route-tables-gwlbe)\.

**Topics**
+ [Gateway Load Balancer endpoint properties and limitations](#gwlbe-limitations)
+ [Gateway Load Balancer endpoint lifecycle](#gwlbe-lifecycle)
+ [Pricing for Gateway Load Balancer endpoints](#gwlbe-interface-pricing)
+ [Creating a Gateway Load Balancer endpoint](#create-gwlbe)
+ [Viewing your Gateway Load Balancer endpoint](#describe-gwlbe)
+ [Adding or removing tags for a Gateway Load Balancer endpoint](#update-gwlbe-tags)

## Gateway Load Balancer endpoint properties and limitations<a name="gwlbe-limitations"></a>

To use a Gateway Load Balancer endpoint, be aware of the following:
+ For each Gateway Load Balancer endpoint, you can choose only one Availability Zone \(subnet\) in your VPC\. You cannot change the subnet later\. To use a Gateway Load Balancer endpoint in a different subnet, create a new Gateway Load Balancer endpoint in that subnet\. You can create a single Gateway Load Balancer endpoint per Availability Zone for a service\.
+ Each Gateway Load Balancer endpoint supports a maximum bandwidth of up to 40 Gbps\. 
+ If the network ACL for your subnet restricts traffic, you might not be able to send traffic through the Gateway Load Balancer endpoint\. Ensure that you add appropriate rules that allow traffic to and from the CIDR block of the subnet\.
+ Security groups are not supported\.
+ Endpoint policies are not supported\.
+ A service might not be available in all Availability Zones through a Gateway Load Balancer endpoint\. To find out which Availability Zones are supported, use the [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command or use the Amazon VPC console\. For more information, see [Creating a Gateway Load Balancer endpoint](#create-gwlbe)\.
+ When you create a Gateway Load Balancer endpoint, the endpoint is created in the Availability Zone that is mapped to your account and that is independent from other accounts\. When the service provider and the consumer are in different accounts, use the Availability Zone ID to uniquely and consistently identify the endpoint Availability Zone\. For example, `use1-az1` is an Availability Zone ID for the `us-east-1` Region and maps to the same location in every AWS account\. For information about Availability Zone IDs, see [AZ IDs for Your Resources](https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html) in the *AWS RAM User Guide* or use [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html)\. 
+ To keep traffic within the same Availability Zone, we recommend that you create a Gateway Load Balancer endpoint in each Availability Zone that you will send traffic to\.
+ Endpoints are supported within the same Region only\. You cannot create an endpoint between a VPC and a service in a different Region\.
+ Endpoints support IPv4 traffic only\.
+ You cannot transfer an endpoint from one VPC to another, or from one service to another\.
+ You have a quota on the number of endpoints you can create per VPC\. For more information, see [VPC endpoints](amazon-vpc-limits.md#vpc-limits-endpoints)\.

## Gateway Load Balancer endpoint lifecycle<a name="gwlbe-lifecycle"></a>

A Gateway Load Balancer endpoint goes through various stages, starting from when you create it \(the endpoint connection request\)\. At each stage, there might be actions that the service consumer and service provider can take\.

![\[Gateway Load Balancer endpoint lifecycle\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/interface-endpoint-lifecycle-diagram.png)

The following rules apply:
+ A service provider can configure their service to accept Gateway Load Balancer endpoint requests automatically or manually\.
+ A service provider cannot delete a Gateway Load Balancer endpoint to their service\. Only the service consumer that requested the connection can delete the Gateway Load Balancer endpoint\.
+ A service provider can reject the Gateway Load Balancer endpoint after it has been accepted and is in the `available` state\.

## Pricing for Gateway Load Balancer endpoints<a name="gwlbe-interface-pricing"></a>

You are charged for creating and using a Gateway Load Balancer endpoint to a service\. Hourly usage rates and data processing rates apply\. For more information, see [AWS PrivateLink Pricing](https://aws.amazon.com/privatelink/pricing/)\. You can view the total number of Gateway Load Balancer endpoints using the Amazon VPC Console, or the AWS CLI\.

## Creating a Gateway Load Balancer endpoint<a name="create-gwlbe"></a>

To create a Gateway Load Balancer endpoint, you must specify the VPC in which to create the endpoint, and the service to which to establish the connection\. 

------
#### [ Console ]

**To create a Gateway Load Balancer endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service category**, choose **Find service by name**\.

1. For **Service Name**, enter the name of the service and choose **Verify**\.

1. Complete the following information and then choose **Create endpoint**\.
   + For **VPC**, select a VPC in which to create the endpoint\.
   + For **Subnets**, select the subnet \(Availability Zone\) in which to create the Gateway Load Balancer endpoint\.
   + \(Optional\) To add a tag, choose **Add tag** and then specify a key and value for the tag\.

------
#### [ Command line ]

**To create a Gateway Load Balancer endpoint using the AWS CLI**  
Use the [create\-vpc\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint.html) command and specify the VPC ID, type of VPC endpoint \(Gateway Load Balancer\), service name, and the subnet in which to create the Gateway Load Balancer endpoint\. 

```
aws ec2 create-vpc-endpoint --vpc-endpoint-type GatewayLoadBalancer --vpc-id vpc-id --subnet-ids subnet-id --service-name gateway-load-balancer-service-name 
```

**To create a VPC endpoint using the AWS Tools for Windows PowerShell or API**
+ [New\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpoint.html)
+ [CreateVpcEndpoint](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpoint.html)

------

## Viewing your Gateway Load Balancer endpoint<a name="describe-gwlbe"></a>

After you've created a Gateway Load Balancer endpoint, you can view information about it\.

------
#### [ Console ]

**To view information about a Gateway Load Balancer endpoint using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your Gateway Load Balancer endpoint\.

1. Choose **Details**\.

1. To view the subnet in which the Gateway Load Balancer endpoint has been created, and the ID of the endpoint network interface, choose **Subnets**\. 

------
#### [ Command line ]

**To describe your Gateway Load Balancer endpoint using a command line tool or API**
+  [describe\-vpc\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoints.html) \(AWS CLI\) 
+ [Get\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcEndpoint.html) \(Tools for Windows PowerShell\)
+ [DescribeVpcEndpoints](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpoints.html) \(Amazon EC2 Query API\)

------

## Adding or removing tags for a Gateway Load Balancer endpoint<a name="update-gwlbe-tags"></a>

You can add or remove the tags for your Gateway Load Balancer endpoint\. 

------
#### [ Console ]

**To add or remove a tag**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**\.

1. Select the Gateway Load Balancer endpoint and choose **Actions**, **Add/Edit Tags**\.

1. Add or remove a tag\.

   \[Add a tag\] Choose **Create tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

------
#### [ Command line ]

**To add or remove tags using a command line tool or an API**
+ Use [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) and [delete\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html)\. \(AWS CLI\)
+ Use [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) and [Remove\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html) \(AWS Tools for Windows PowerShell\)
+ Use [CreateTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateTags.html) and [DeleteTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteTags.html)\. \(Amazon EC2 Query API\)

------