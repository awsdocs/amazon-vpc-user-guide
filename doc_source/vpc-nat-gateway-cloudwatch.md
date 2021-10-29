# Monitor NAT gateways using Amazon CloudWatch<a name="vpc-nat-gateway-cloudwatch"></a>

You can monitor your NAT gateway using CloudWatch, which collects information from your NAT gateway and creates readable, near real\-time metrics\. You can use this information to monitor and troubleshoot your NAT gateway\. NAT gateway metric data is provided at 1\-minute intervals, and statistics are recorded for a period of 15 months\.

For more information about Amazon CloudWatch, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\. For more information about pricing, see [Amazon CloudWatch Pricing](http://aws.amazon.com/cloudwatch/pricing)\.

## NAT gateway metrics and dimensions<a name="metrics-dimensions-nat-gateway"></a>

The following metrics are available for your NAT gateways\.


| Metric | Description | 
| --- | --- | 
| ActiveConnectionCount |  The total number of concurrent active TCP connections through the NAT gateway\. A value of zero indicates that there are no active connections through the NAT gateway\. Units: Count Statistics: The most useful statistic is `Max`\.  | 
| BytesInFromDestination |  The number of bytes received by the NAT gateway from the destination\. If the value for `BytesOutToSource` is less than the value for `BytesInFromDestination`, there might be data loss during NAT gateway processing, or traffic being actively blocked by the NAT gateway\. Units: Bytes Statistics: The most useful statistic is `Sum`\.  | 
| BytesInFromSource |  The number of bytes received by the NAT gateway from clients in your VPC\. If the value for `BytesOutToDestination` is less than the value for `BytesInFromSource`, there might be data loss during NAT gateway processing\. Units: Bytes Statistics: The most useful statistic is `Sum`\.  | 
| BytesOutToDestination |  The number of bytes sent out through the NAT gateway to the destination\. A value greater than zero indicates that there is traffic going to the internet from clients that are behind the NAT gateway\. If the value for `BytesOutToDestination` is less than the value for `BytesInFromSource`, there might be data loss during NAT gateway processing\. Unit: Bytes Statistics: The most useful statistic is `Sum`\.  | 
| BytesOutToSource |  The number of bytes sent through the NAT gateway to the clients in your VPC\. A value greater than zero indicates that there is traffic coming from the internet to clients that are behind the NAT gateway\. If the value for `BytesOutToSource` is less than the value for `BytesInFromDestination`, there might be data loss during NAT gateway processing, or traffic being actively blocked by the NAT gateway\. Units: Bytes Statistics: The most useful statistic is `Sum`\.  | 
| ConnectionAttemptCount |  The number of connection attempts made through the NAT gateway\. If the value for `ConnectionEstablishedCount` is less than the value for `ConnectionAttemptCount`, this indicates that clients behind the NAT gateway attempted to establish new connections for which there was no response\. Unit: Count Statistics: The most useful statistic is `Sum`\.  | 
| ConnectionEstablishedCount |  The number of connections established through the NAT gateway\. If the value for `ConnectionEstablishedCount` is less than the value for `ConnectionAttemptCount`, this indicates that clients behind the NAT gateway attempted to establish new connections for which there was no response\. Unit: Count Statistics: The most useful statistic is `Sum`\.  | 
| ErrorPortAllocation |  The number of times the NAT gateway could not allocate a source port\.  A value greater than zero indicates that too many concurrent connections are open through the NAT gateway\. Units: Count Statistics: The most useful statistic is `Sum`\.  | 
| IdleTimeoutCount |  The number of connections that transitioned from the active state to the idle state\. An active connection transitions to idle if it was not closed gracefully and there was no activity for the last 350 seconds\. A value greater than zero indicates that there are connections that have been moved to an idle state\. If the value for `IdleTimeoutCount` increases, it might indicate that clients behind the NAT gateway are re\-using stale connections\.  Unit: Count Statistics: The most useful statistic is `Sum`\.  | 
| PacketsDropCount |  The number of packets dropped by the NAT gateway\. A value greater than zero might indicate an ongoing transient issue with the NAT gateway\. If this value exceeds 0\.01 percent of the total traffic on the NAT gateway, check the [AWS service health dashboard](http://status.aws.amazon.com/)\. Units: Count Statistics: The most useful statistic is `Sum`\.  | 
| PacketsInFromDestination |  The number of packets received by the NAT gateway from the destination\. If the value for `PacketsOutToSource` is less than the value for `PacketsInFromDestination`, there might be data loss during NAT gateway processing, or traffic being actively blocked by the NAT gateway\. Unit: Count Statistics: The most useful statistic is `Sum`\.  | 
| PacketsInFromSource |  The number of packets received by the NAT gateway from clients in your VPC\. If the value for `PacketsOutToDestination` is less than the value for `PacketsInFromSource`, there might be data loss during NAT gateway processing\. Unit: Count Statistics: The most useful statistic is `Sum`\.  | 
| PacketsOutToDestination |  The number of packets sent out through the NAT gateway to the destination\. A value greater than zero indicates that there is traffic going to the internet from clients that are behind the NAT gateway\. If the value for `PacketsOutToDestination` is less than the value for `PacketsInFromSource`, there might be data loss during NAT gateway processing\. Unit: Count Statistics: The most useful statistic is `Sum`\.  | 
| PacketsOutToSource |  The number of packets sent through the NAT gateway to the clients in your VPC\. A value greater than zero indicates that there is traffic coming from the internet to clients that are behind the NAT gateway\. If the value for `PacketsOutToSource` is less than the value for `PacketsInFromDestination`, there might be data loss during NAT gateway processing, or traffic being actively blocked by the NAT gateway\. Unit: Count Statistics: The most useful statistic is `Sum`\.  | 

To filter the metric data, use the following dimension\.


| Dimension | Description | 
| --- | --- | 
| NatGatewayId | Filter the metric data by the NAT gateway ID\. | 

## View NAT gateway CloudWatch metrics<a name="viewing-metrics"></a>

NAT gateway metrics are sent to CloudWatch at 1\-minute intervals\. Metrics are grouped first by the service namespace, and then by the possible combinations of dimensions within each namespace\. You can view the metrics for your NAT gateways as follows\.

**To view metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**, **All metrics**\.

1. Choose the **NATGateway** metric namespace\.

1. Choose a metric dimension\.

**To view metrics using the AWS CLI**  
At a command prompt, use the following command to list the metrics that are available for the NAT gateway service\.

```
aws cloudwatch list-metrics --namespace "AWS/NATGateway"
```

## Create CloudWatch alarms to monitor a NAT gateway<a name="creating-alarms-nat-gateway"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period that you specify\. It sends a notification to an Amazon SNS topic based on the value of the metric relative to a given threshold over a number of time periods\. 

For example, you can create an alarm that monitors the amount of traffic coming in or leaving the NAT gateway\. The following alarm monitors the amount of outbound traffic from clients in your VPC through the NAT gateway to the internet\. It sends a notification when the number of bytes reaches a threshold of 5,000,000 during a 15\-minute period\.

**To create an alarm for outbound traffic through the NAT gateway**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **All alarms**\.

1. Choose **Create alarm**\.

1. Choose **Select metric**\.

1. Choose the **NATGateway** metric namespace and then choose a metric dimension\. When you get to the metrics, select the check box next to the **BytesOutToDestination** metric for the NAT gateway, and then choose **Select metric**\.

1. Configure the alarm as follows, and then choose **Next**:
   + For **Statistic**, choose **Sum**\.
   + For **Period**, choose **15 minutes**\.
   + For **Whenever**, choose **Greater/Equal** and enter `5000000` for the threshold\.

1. For **Notification**, select an existing SNS topic or choose **Create new topic** to create a new one\. Choose **Next**\.

1. Enter a name and description for the alarm and choose **Next**\.

1. When you done configuring the alarm, choose **Create alarm**\.

As another example, you can create an alarm that monitors port allocation errors and sends a notification when the value is greater than zero \(0\) for three consecutive 5\-minute periods\.

**To create an alarm to monitor port allocation errors**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **All alarms**\.

1. Choose **Create alarm**\.

1. Choose **Select metric**\.

1. Choose the **NATGateway** metric namespace and then choose a metric dimension\. When you get to the metrics, select the check box next to the **ErrorPortAllocation** metric for the NAT gateway, and then choose **Select metric**\.

1. Configure the alarm as follows, and then choose **Next**:
   + For **Statistic**, choose **Maximum**\.
   + For **Period**, choose **5 minutes**\.
   + For **Whenever**, choose **Greater** and enter 0 for the threshold\.
   + For **Additional configuration**, **Datapoints to alarm**, enter 3\.

1. For **Notification**, select an existing SNS topic or choose **Create new topic** to create a new one\. Choose **Next**\.

1. Enter a name and description for the alarm and choose **Next**\.

1. When you are done configuring the alarm, choose **Create alarm**\.

For more information, see [Using Amazon CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.