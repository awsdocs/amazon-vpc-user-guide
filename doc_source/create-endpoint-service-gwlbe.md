# Creating a VPC endpoint service configuration for Gateway Load balancer endpoints<a name="create-endpoint-service-gwlbe"></a>

You can create an endpoint service configuration using the Amazon VPC console or the command line\. Before you begin, ensure that you have created one or more Gateway Load Balancers in your VPC for your service\. For more information, see [Getting started with Gateway Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/getting-started.html)\.

In your configuration, you can optionally specify that any Gateway Load Balancer endpoint connection requests to your service must be manually accepted by you\. You can [create a notification](create-notification-endpoint-service.md) to receive alerts when there are connection requests\. If you do not accept a connection, service consumers cannot access your service\. 

After you create an endpoint service configuration, you must add [permissions](add-endpoint-service-permissions.md) to enable service consumers to create a Gateway Load Balancer endpoint to your service\.

------
#### [ Console ]

**To create an endpoint service using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**, **Create Endpoint Service**\.

1. For **Associate Load Balancers**, select the Gateway Load Balancers to associate with the endpoint service\. 

1. For **Require acceptance for endpoint**, select the check box to accept connection requests to your service manually\. If you do not select this option, endpoint connections are automatically accepted\.

1. \(Optional\) To add a tag, choose **Add tag** and specify the key and value for the tag\.

1. Choose **Create service**\.

------
#### [ Command line and API ]

**To create an endpoint service using the AWS CLI**  
Use the [create\-vpc\-endpoint\-service\-configuration](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint-service-configuration.html) command and specify one or more ARNs for your Gateway Load Balancers\. You can optionally specify if acceptance is required for connecting to your service\.

```
aws ec2 create-vpc-endpoint-service-configuration --gateway-load-balancer-arns gateway-load-balancer-arn --no-acceptance-required
```

**To create an endpoint service using the AWS Tools for Windows PowerShell or API**
+ [New\-EC2VpcEndpointServiceConfiguration](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpointServiceConfiguration.html) \(Tools for PowerShell\)
+ [CreateVpcEndpointServiceConfiguration](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpointServiceConfiguration.html) \(API\)

------