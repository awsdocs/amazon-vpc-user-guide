# Creating and managing a notification for an endpoint service<a name="create-notification-endpoint-service"></a>

You can create a notification to receive alerts for specific events that occur on the endpoints that are attached to your endpoint service\. For example, you can receive an email when an endpoint request is accepted or rejected for your endpoint service\. To create a notification, you must associate an Amazon SNS topic with the notification\. You can subscribe to the SNS topic to receive an email notification when an endpoint event occurs\. For more information, see the [Amazon Simple Notification Service Developer Guide](https://docs.aws.amazon.com/sns/latest/dg/)\.

The Amazon SNS topic that you use for notifications must have a topic policy that allows the Amazon VPC endpoint service to publish notifications on your behalf\. Ensure that you include the following statement in your topic policy\. For more information, see [Managing Access to Your Amazon SNS Topics](https://docs.aws.amazon.com/sns/latest/dg/AccessPolicyLanguage.html) in the *Amazon Simple Notification Service Developer Guide*\.

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

**To create a notification for an endpoint service**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Notifications**, **Create Notification**\.

1. Choose the ARN for the SNS topic to associate with the notification\.

1. For **Events**, select the endpoint events for which to receive notifications\.

1. Choose **Create Notification**\.

After you create a notification, you can change the SNS topic that's associated with the notification\. You can also specify different endpoint events for the notification\.

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

------
#### [ AWS CLI ]

**To create and manage a notification using the AWS CLI**

1. To create a notification for an endpoint service, use the [create\-vpc\-endpoint\-connection\-notification](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint-connection-notification.html) command and specify the ARN of the SNS topic, the events for which to be notified, and the ID of the endpoint service\.

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

1. To view your notifications, use the [describe\-vpc\-endpoint\-connection\-notifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-connection-notifications.html) command\.

   ```
   aws ec2 describe-vpc-endpoint-connection-notifications
   ```

1. To change the SNS topic or endpoint events for the notification, use the [modify\-vpc\-endpoint\-connection\-notification](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-connection-notification.html) command\.

   ```
   aws ec2 modify-vpc-endpoint-connection-notification --connection-notification-id vpce-nfn-008776de7e03f5abc --connection-events Accept Reject --connection-notification-arn arn:aws:sns:us-east-2:123456789012:mytopic
   ```

1. To delete a notification, use the [delete\-vpc\-endpoint\-connection\-notifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc-endpoint-connection-notifications.html) command\.

   ```
   aws ec2 delete-vpc-endpoint-connection-notifications --connection-notification-ids vpce-nfn-008776de7e03f5abc
   ```

------
#### [  AWS Tools for Windows PowerShell ]

Use [New\-EC2VpcEndpointConnectionNotification](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpointConnectionNotification.html), [Get\-EC2EndpointConnectionNotification](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2EndpointConnectionNotification.html), [Edit\-EC2VpcEndpointConnectionNotification](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcEndpointConnectionNotification.html), and [Remove\-EC2EndpointConnectionNotification](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2EndpointConnectionNotification.html)\.

------
#### [ API ]

Use [CreateVpcEndpointConnectionNotification](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpointConnectionNotification.html), [DescribeVpcEndpointConnectionNotifications](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpointConnectionNotifications.html), [ModifyVpcEndpointConnectionNotification](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcEndpointConnectionNotification.html), and [DeleteVpcEndpointConnectionNotifications](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteVpcEndpointConnectionNotifications.html)\.

------