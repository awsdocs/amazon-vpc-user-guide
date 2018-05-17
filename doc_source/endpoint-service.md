# VPC Endpoint Services \(AWS PrivateLink\)<a name="endpoint-service"></a>

You can create your own application in your VPC and configure it as an AWS PrivateLink\-powered service \(referred to as an *endpoint service*\)\. Other AWS principals can create a connection from their VPC to your endpoint service using an [interface VPC endpoint](vpce-interface.md)\. You are the *service provider*, and the AWS principals that create connections to your service are *service consumers*\.

The following are the general steps to create an endpoint service\.

1. Create a Network Load Balancer for your application in your VPC and configure it for each subnet \(Availability Zone\) in which the service should be available\. The load balancer receives requests from service consumers and routes it to your service\. For more information, see [Getting Started with Network Load Balancers](http://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html) in the *User Guide for Network Load Balancers*\. We recommend that you configure your service in all Availability Zones within the region\.

1. Create a VPC endpoint service configuration and specify your Network Load Balancer\.

The following are the general steps to enable service consumers to connect to your service\.

1. Grant permissions to specific service consumers \(AWS accounts, IAM users, and IAM roles\) to create a connection to your endpoint service\.

1. A service consumer that has been granted permissions creates an interface endpoint to your service, optionally in each Availability Zone in which you've configured your service\.

1. To activate the connection, accept the interface endpoint connection request\. By default, connection requests must be manually accepted\. However, you can configure the acceptance settings for your endpoint service so that any connection requests are automatically accepted\.

The combination of permissions and acceptance settings can help you control which service consumers \(AWS principals\) can access to your service\. For example, you can grant permissions to selected principals that you trust and automatically accept all connection requests, or you can grant permissions to a wider group of principals and manually accept specific connection requests that you trust\.

In the following diagram, the account owner of VPC B is a service provider, and has a service running on instances in subnet B\. The owner of VPC B has a service endpoint \(vpce\-svc\-1234\) with an associated Network Load Balancer that points to the instances in subnet B as targets\. Instances in subnet A of VPC A use an interface endpoint to access the services in subnet B\.

![\[Using an interface endpoint to access an endpoint service\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/vpc-endpoint-service.png)

For low latency and fault tolerance, we recommend using a Network Load Balancer with targets in every Availability Zone of the AWS Region\. To help achieve high availability for service consumers that use [zonal DNS hostnames](vpce-interface.md#access-service-though-endpoint) to access the service, you can enable cross\-zone load balancing\. Cross\-zone load balancing enables the load balancer to distribute traffic across the registered targets in all enabled Availability Zones\. For more information, see [Cross\-Zone Load Balancing](http://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancers.html#cross-zone-load-balancing) in the *User Guide for Network Load Balancers*\. Regional data transfer charges may apply to your account when you enable cross\-zone load balancing\.

In the following diagram, the owner of VPC B is ther service provider, and has configured a Network Load Balancer with targets in two different Availability Zones\. The service consumer \(VPC A\) has created interface endpoints in the same two Availability Zones in their VPC\. Requests to the service from instances in VPC A can use either interface endpoint\.

![\[Using interface endpoints to access an endpoint service\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/images/vpc-endpoint-service-multi-az.png)

**Topics**
+ [Endpoint Service Limitations](#endpoint-service-limits)
+ [Creating a VPC Endpoint Service Configuration](#create-endpoint-service)
+ [Adding and Removing Permissions for Your Endpoint Service](#add-endpoint-service-permissions)
+ [Changing the Network Load Balancers and Acceptance Settings](#modify-endpoint-service)
+ [Accepting and Rejecting Interface Endpoint Connection Requests](#accept-reject-endpoint-requests)
+ [Creating and Managing a Notification for an Endpoint Service](#create-notification-endpoint-service)
+ [Using Proxy Protocol for Connection Information](#endpoint-service-proxy-protocol)
+ [Deleting an Endpoint Service Configuration](#delete-endpoint-service)

## Endpoint Service Limitations<a name="endpoint-service-limits"></a>

To use endpoint services, you need to be aware of the current rules and limitations:
+ You cannot tag an endpoint service\.
+ An endpoint service supports IPv4 traffic over TCP only\.
+ Service consumers must use the endpoint\-specific DNS hostnames to access the endpoint service\. Private DNS is not supported\. For more information, see [Accessing a Service Through an Interface Endpoint](vpce-interface.md#access-service-though-endpoint)\.
+ Endpoint services are only available in the AWS Region in which they are created\.
+ If an endpoint service is associated with multiple Network Load Balancers, then for a specific Availability Zone, an interface endpoint will establish a connection with one load balancer only\.
+ Availability Zones in your account might not map to the same locations as Availability Zones in another account; for example, your Availability Zone `us-east-1a` might not be the same location as `us-east-1a` for another account\. For more information, see [Region and Availability Zone Concepts](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-regions-availability-zones)\. When you configure an endpoint service, it's configured in the Availability Zones as mapped to your account\.

## Creating a VPC Endpoint Service Configuration<a name="create-endpoint-service"></a>

You can create an endpoint service configuration using the Amazon VPC console or the command line\. Before you begin, ensure that you have created one or more Network Load Balancers in your VPC for your service\. For more information, see [Getting Started with Network Load Balancers](http://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html) in the *User Guide for Network Load Balancers*\.

In your configuration, you can optionally specify that any interface endpoint connection requests to your service must be manually accepted by you\. You can [create a notification](#create-notification-endpoint-service) to receive alerts when there are connection requests\. If you do not accept a connection, service consumers cannot access your service\.

**Note**  
Regardless of the acceptance settings, service consumers must also have [permissions](#add-endpoint-service-permissions) to create a connection to your service\.

**To create an endpoint service using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**, **Create Endpoint Service**\.

1. For **Associate Network Load Balancers**, select the Network Load Balancers to associate with the endpoint service\. 

1. For **Require acceptance for endpoint**, select the check box to accept connection requests to your service manually\. If you do not select this option, endpoint connections are automatically accepted\.

1. Choose **Create service**\.

After you create an endpoint service configuration, you must add permissions to enable service consumers to create interface endpoints to your service\.

**To create an endpoint service using the AWS CLI**
+ Use the [create\-vpc\-endpoint\-service\-configuration](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint-service-configuration.html) command and specify one or more ARNs for your Network Load Balancers\. You can optionally specify if acceptance is required for connecting to your service\.

  ```
  aws ec2 create-vpc-endpoint-service-configuration --network-load-balancer-arns arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/net/nlb-vpce/e94221227f1ba532 --acceptance-required
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

**To create an endpoint service using the AWS Tools for Windows PowerShell or API**
+ [New\-EC2VpcEndpointServiceConfiguration](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpointServiceConfiguration.html) \(AWS Tools for Windows PowerShell\)
+ [CreateVpcEndpointServiceConfiguration](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpointServiceConfiguration.html) \(Amazon EC2 Query API\)

## Adding and Removing Permissions for Your Endpoint Service<a name="add-endpoint-service-permissions"></a>

After you've created your endpoint service configuration, you can control which service consumers can create an interface endpoint to connect to your service\. Service consumers are [IAM principals](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html)—IAM users, IAM roles, and AWS accounts\. To add or remove permissions for a principal, you need its Amazon Resource Name \(ARN\)\.
+ For an AWS account \(and therefore all principals in the account\), the ARN is in the form `arn:aws:iam::aws-account-id:root`\.
+ For a specific IAM user, the ARN is in the form `arn:aws:iam::aws-account-id:user/user-name`\.
+ For a specific IAM role, the ARN is in the form `arn:aws:iam::aws-account-id:role/role-name`\.

**To add or remove permissions using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Actions**, **Add principals to whitelist**\.

1. Specify the ARN for the principal for which to add permissions\. To add more principals, choose **Add principal**\. To remove a principal, choose the cross icon next to the entry\.
**Note**  
Specify `*` to add permissions for all principals\. This enables all principals in all AWS accounts to create an interface endpoint to your endpoint service\.

1. Choose **Add to Whitelisted principals**\.

1. To remove a principal, select it in the list and choose **Delete**\.

**To add and remove permissions using the AWS CLI**

1. To add permissions for your endpoint service, use the [modify\-vpc\-endpoint\-service\-permissions](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html) command and use the `--add-allowed-principals` parameter to add one or more ARNs for the principals\.

   ```
   aws ec2 modify-vpc-endpoint-service-permissions --service-id vpce-svc-03d5ebb7d9579a2b3 --add-allowed-principals '["arn:aws:iam::123456789012:root"]'
   ```

1. To view the permissions you've added for your endpoint service, use the [describe\-vpc\-endpoint\-service\-permissions](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-service-permissions.html) command\.

   ```
   aws ec2 describe-vpc-endpoint-service-permissions --service-id vpce-svc-03d5ebb7d9579a2b3
   ```

   ```
   {
       "AllowedPrincipals": [
           {
               "PrincipalType": "Account", 
               "Principal": "arn:aws:iam::123456789012:root"
           }
       ]
   }
   ```

1. To remove permissions for your endpoint service, use the [modify\-vpc\-endpoint\-service\-permissions](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html) command and use the `--remove-allowed-principals` parameter to remove one or more ARNs for the principals\.

   ```
   aws ec2 modify-vpc-endpoint-service-permissions --service-id vpce-svc-03d5ebb7d9579a2b3 --remove-allowed-principals '["arn:aws:iam::123456789012:root"]'
   ```

**To modify endpoint service permissions using the AWS Tools for Windows PowerShell or API**
+ [Edit\-EC2EndpointServicePermission](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2EndpointServicePermission.html) \(AWS Tools for Windows PowerShell\)
+ [ModifyVpcEndpointServicePermissions](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcEndpointServicePermissions.html) \(Amazon EC2 Query API\)

## Changing the Network Load Balancers and Acceptance Settings<a name="modify-endpoint-service"></a>

You can modify your endpoint service configuration by changing the Network Load Balancers that are associated with the endpoint service, and by changing whether acceptance is required for requests to connect to your endpoint service\.

You cannot disassociate a load balancer if there are interface endpoints attached to your endpoint service\.

**To change the network load balancers for your endpoint service using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Actions**, **Associate/Disassociate Network Load Balancers**\.

1. Select or deselect the load balancers as required, and choose **Save**\.

**To modify the acceptance setting using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Actions**, **Modify endpoint acceptance setting**\.

1. Select or deselect **Require acceptance for endpoint**, and choose **Modify**\.

**To modify the load balancers and acceptance settings using the AWS CLI**

1. To change the load balancers for your endpoint service, use the [modify\-vpc\-endpoint\-service\-configuration](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-configuration.html) command and use the `--add-network-load-balancer-arn` or `--remove-network-load-balancer-arn` parameter; for example: 

   ```
   aws ec2 modify-vpc-endpoint-service-configuration --service-id vpce-svc-09222513e6e77dc86 --remove-network-load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/net/nlb-vpce/e94221227f1ba532
   ```

1. To change whether acceptance is required, use the [modify\-vpc\-endpoint\-service\-configuration](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-configuration.html) command and specify `--acceptance-required` or `--no-acceptance-required`; for example:

   ```
   aws ec2 modify-vpc-endpoint-service-configuration --service-id vpce-svc-09222513e6e77dc86 --no-acceptance-required
   ```

**To modify an endpoint service configuration using the AWS Tools for Windows PowerShell or API**
+ [Edit\-EC2VpcEndpointServiceConfiguration](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcEndpointServiceConfiguration.html) \(AWS Tools for Windows PowerShell\)
+ [ModifyVpcEndpointServiceConfiguration](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcEndpointServiceConfiguration.html) \(Amazon EC2 Query API\)

## Accepting and Rejecting Interface Endpoint Connection Requests<a name="accept-reject-endpoint-requests"></a>

After you've created an endpoint service, service consumers for which you've added permission can create an interface endpoint to connect to your service\. For more information about creating an interface endpoint, see [Interface VPC Endpoints \(AWS PrivateLink\)](vpce-interface.md)\.

If you specified that acceptance is required for connection requests, you must manually accept or reject interface endpoint connection requests to your endpoint service\. After an interface endpoint is accepted, it becomes `available`\.

You can reject an interface endpoint connection after it's in the `available` state\.

**To accept or reject a connection request using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. The **Endpoint Connections** tab lists endpoint connections that are currently pending your approval\. Select the endpoint, choose **Actions**, and choose **Accept endpoint connection request** to accept the connection or **Reject endpoint connection request** to reject it\.

**To accept or reject a connection request using the AWS CLI**

1. The view the endpoint connections that are pending acceptance, use the [describe\-vpc\-endpoint\-connections](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-connections.html) command and filter by the `pendingAcceptance` state\.

   ```
   aws ec2 describe-vpc-endpoint-connections --filters Name=vpc-endpoint-state,Values=pendingAcceptance
   ```

   ```
   {
       "VpcEndpointConnections": [
           {
               "VpcEndpointId": "vpce-0c1308d7312217abc", 
               "ServiceId": "vpce-svc-03d5ebb7d9579a2b3", 
               "CreationTimestamp": "2017-11-30T10:00:24.350Z", 
               "VpcEndpointState": "pendingAcceptance", 
               "VpcEndpointOwner": "123456789012"
           }
       ]
   }
   ```

1. To accept an endpoint connection request, use the [accept\-vpc\-endpoint\-connections](http://docs.aws.amazon.com/cli/latest/reference/ec2/accept-vpc-endpoint-connections.html) command and specify the endpoint ID and endpoint service ID\.

   ```
   aws ec2 accept-vpc-endpoint-connections --service-id vpce-svc-03d5ebb7d9579a2b3 --vpc-endpoint-ids vpce-0c1308d7312217abc
   ```

1. To reject an endpoint connection request, use the [reject\-vpc\-endpoint\-connections](http://docs.aws.amazon.com/cli/latest/reference/ec2/reject-vpc-endpoint-connections.html) command\.

   ```
   aws ec2 reject-vpc-endpoint-connections --service-id vpce-svc-03d5ebb7d9579a2b3 --vpc-endpoint-ids vpce-0c1308d7312217abc
   ```

**To accept and reject endpoint connections using the AWS Tools for Windows PowerShell or API**
+ [Confirm\-EC2EndpointConnection](http://docs.aws.amazon.com/powershell/latest/reference/items/Confirm-EC2EndpointConnection.html) and [Deny\-EC2EndpointConnection](http://docs.aws.amazon.com/powershell/latest/reference/items/Deny-EC2EndpointConnection.html) \(AWS Tools for Windows PowerShell\)
+ [AcceptVpcEndpointConnections](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-AcceptVpcEndpointConnections.html) and [RejectVpcEndpointConnections](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-RejectVpcEndpointConnections.html) \(Amazon EC2 Query API\)

## Creating and Managing a Notification for an Endpoint Service<a name="create-notification-endpoint-service"></a>

You can create a notification to receive alerts for specific events that occur on the endpoints that are attached to your endpoint service\. For example, you can receive an email when an endpoint request is accepted or rejected for your endpoint service\. To create a notification, you must associate an Amazon SNS topic with the notification\. You can subscribe to the SNS topic to receive an email notification when an endpoint event occurs\. For more information, see the [Amazon Simple Notification Service Developer Guide](http://docs.aws.amazon.com/sns/latest/dg/)\.

The Amazon SNS topic that you use for notifications must have a topic policy that allows the Amazon VPC endpoint service to publish notifications on your behalf\. Ensure that you include the following statement in your topic policy\. For more information, see [Managing Access to Your Amazon SNS Topics](http://docs.aws.amazon.com/sns/latest/dg/AccessPolicyLanguage.html) in the *Amazon Simple Notification Service Developer Guide*\.

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

**To create a notification for an endpoint service**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Notifications**, **Create Notification**\.

1. Choose the ARN for the SNS topic to associate with the notification\.

1. For **Events**, select the endpoint events for which to receive notifications\.

1. Choose **Create Notification**\.

After you create a notification, you can change the SNS topic that's associated with the notification, or you can specify different endpoint events for the notification\.

**To modify a notification for an endpoint service**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Notifications**, **Actions**, **Modify Notification**\.

1. Specify the ARN for the SNS topic and select or deselect the endpoint events as required\.

1. Choose **Modify Notification**\.

If you no longer need a notification, you can delete it\.

**To delete a notification**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Notifications**, **Actions**, **Delete Notification**\.

1. Choose **Yes, Delete**\.

**To create and manage a notification using the AWS CLI**

1. To create a notification for an endpoint service, use the [create\-vpc\-endpoint\-connection\-notification](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint-connection-notification.html) command and specify the ARN of the SNS topic, the events for which to be notified, and the ID of the endpoint service; for example:

   ```
   aws ec2 create-vpc-endpoint-connection-notification --connection-notification-arn arn:aws:sns:us-east-2:123456789012:VpceNotification --connection-events Connect Accept Delete Reject --service-id vpce-svc-1237881c0d25a3abc
   ```

   ```
   {
       "ConnectionNotification": {
           "ConnectionNotificationState": "Enabled", 
           "ConnectionNotificationType": "Topic", 
           "ServiceId": "vpce-svc-1237881c0d25a3abc", 
           "ConnectionEvents": [
               "Reject",
               "Accept",
               "Delete",
               "Connect"
           ], 
           "ConnectionNotificationId": "vpce-nfn-008776de7e03f5abc", 
           "ConnectionNotificationArn": "arn:aws:sns:us-east-2:123456789012:VpceNotification"
       }
   }
   ```

1. To view your notifications, use the [describe\-vpc\-endpoint\-connection\-notifications](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-connection-notifications.html) command:

   ```
   aws ec2 describe-vpc-endpoint-connection-notifications
   ```

1. To change the SNS topic or endpoint events for the notification, use the [modify\-vpc\-endpoint\-connection\-notification](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-connection-notification.html) command; for example:

   ```
   aws ec2 modify-vpc-endpoint-connection-notification --connection-notification-id vpce-nfn-008776de7e03f5abc --connection-events Accept Reject --connection-notification-arn arn:aws:sns:us-east-2:123456789012:mytopic
   ```

1. To delete a notification, use the [delete\-vpc\-endpoint\-connection\-notifications](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc-endpoint-connection-notifications.html) command:

   ```
   aws ec2 delete-vpc-endpoint-connection-notifications --connection-notification-ids vpce-nfn-008776de7e03f5abc
   ```

**To create and manage a notification using the AWS Tools for Windows PowerShell or API**
+ [New\-EC2VpcEndpointConnectionNotification](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpointConnectionNotification.html), [Get\-EC2EndpointConnectionNotification](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2EndpointConnectionNotification.html), [Edit\-EC2VpcEndpointConnectionNotification](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcEndpointConnectionNotification.html), and [Remove\-EC2EndpointConnectionNotification](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2EndpointConnectionNotification.html) \(AWS Tools for Windows PowerShell\)
+ [CreateVpcEndpointConnectionNotification](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpointConnectionNotification.html), [DescribeVpcEndpointConnectionNotifications](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpointConnectionNotifications.html), [ModifyVpcEndpointConnectionNotification](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcEndpointConnectionNotification.html), and [DeleteVpcEndpointConnectionNotifications](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteVpcEndpointConnectionNotifications.html) \(Amazon EC2 Query API\)

## Using Proxy Protocol for Connection Information<a name="endpoint-service-proxy-protocol"></a>

A Network Load Balancer provides source IP addresses to your application \(your service\)\. When service consumers send traffic to your service through an interface endpoint, the source IP addresses provided to your application are the private IP addresses of the Network Load Balancer nodes, and not the IP addresses of the service consumers\.

If you need the IP addresses of the service consumers and their corresponding interface endpoint IDs, enable Proxy Protocol on your load balancer and get the client IP addresses from the Proxy Protocol header\. For more information, see [Proxy Protocol](http://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html#proxy-protocol) in the *User Guide for Network Load Balancers*\.

## Deleting an Endpoint Service Configuration<a name="delete-endpoint-service"></a>

You can delete an endpoint service configuration\. Deleting the configuration does not delete the application hosted in your VPC or the associated load balancers\. 

Before you delete the endpoint service configuration, you must reject any `available` or `pending-acceptance` VPC endpoints that are attached to the service\. For more information, see [Accepting and Rejecting Interface Endpoint Connection Requests](#accept-reject-endpoint-requests)\.

**To delete an endpoint service configuration using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select the service\.

1. Choose **Actions**, **Delete**\.

1. Choose **Yes, Delete**\.

**To delete an endpoint service configuration using the AWS CLI**
+ Use the [delete\-vpc\-endpoint\-service\-configurations](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc-endpoint-service-configurations.html) command and specify the ID of the service\. 

  ```
  aws ec2 delete-vpc-endpoint-service-configurations --service-ids vpce-svc-03d5ebb7d9579a2b3
  ```

**To delete an endpoint service configuration using the AWS Tools for Windows PowerShell or API**
+ [Remove\-EC2EndpointServiceConfiguration](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2EndpointServiceConfiguration.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteVpcEndpointServiceConfigurations](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteVpcEndpointServiceConfigurations.html) \(Amazon EC2 Query API\)