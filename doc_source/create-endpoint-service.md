# Creating a VPC endpoint service configuration<a name="create-endpoint-service"></a>

You can create an endpoint service configuration using the Amazon VPC console or the command line\. Before you begin, ensure that you have created one or more Network Load Balancers in your VPC for your service\. For more information, see [Getting Started with Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html) in the *User Guide for Network Load Balancers*\.

In your configuration, you can optionally specify that any interface endpoint connection requests to your service must be manually accepted by you\. You can [create a notification](create-notification-endpoint-service.md) to receive alerts when there are connection requests\. If you do not accept a connection, service consumers cannot access your service\.

**Note**  
Regardless of the acceptance settings, service consumers must also have [permissions](add-endpoint-service-permissions.md) to create a connection to your service\.

After you create an endpoint service configuration, you must add permissions to enable service consumers to create interface endpoints to your service\.

------
#### [ Console ]

**To create an endpoint service using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**, **Create Endpoint Service**\.

1. For **Associate Network Load Balancers**, select the Network Load Balancers to associate with the endpoint service\. 

1. For **Require acceptance for endpoint**, select the check box to accept connection requests to your service manually\. If you do not select this option, endpoint connections are automatically accepted\.

1. To associate a private DNS name with the service, select **Enable private DNS** name, and then for** Private DNS name**, enter the private DNS name\.

1. \(Optional\) Add or remove a tag\.

   \[Add a tag\] Choose **Add tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

1. Choose **Create service**\.

------
#### [ AWS CLI ]

To create an endpoint service using the AWS CLI

Use the [create\-vpc\-endpoint\-service\-configuration](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint-service-configuration.html) command and specify one or more ARNs for your Network Load Balancers\. You can optionally specify if acceptance is required for connecting to your service and if the service has a private DNS name\.

```
aws ec2 create-vpc-endpoint-service-configuration --network-load-balancer-arns arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/net/nlb-vpce/e94221227f1ba532 --acceptance-required --privateDnsName exampleservice.com
```

```
{
    "ServiceConfiguration": {
        "ServiceType": [
            {
                "ServiceType": "Interface"
            }
        ], 
        "NetworkLoadBalancerArns": [
            "arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/net/nlb-vpce/e94221227f1ba532"
        ], 
        "ServiceName": "com.amazonaws.vpce.us-east-1.vpce-svc-03d5ebb7d9579a2b3", 
        "ServiceState": "Available", 
        "ServiceId": "vpce-svc-03d5ebb7d9579a2b3", 
        "PrivateDnsName: "exampleService.com",
        "AcceptanceRequired": true, 
        "AvailabilityZones": [
            "us-east-1d"
        ], 
        "BaseEndpointDnsNames": [
            "vpce-svc-03d5ebb7d9579a2b3.us-east-1.vpce.amazonaws.com"
        ]
    }
}
```

------
#### [  AWS Tools for Windows PowerShell ]

Use [New\-EC2VpcEndpointServiceConfiguration](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpointServiceConfiguration.html)\.

------
#### [ API ]

Use [CreateVpcEndpointServiceConfiguration](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpointServiceConfiguration.html)\.

------