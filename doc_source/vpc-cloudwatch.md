# CloudWatch metrics for your VPCs<a name="vpc-cloudwatch"></a>

Amazon VPC publishes data about your VPCs to Amazon CloudWatch\. You can retrieve statistics about your VPCs as an ordered set of time\-series data, known as *metrics*\. Think of a metric as a variable to monitor and the data as the value of that variable over time\. For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

**Topics**
+ [NAU metrics and dimensions](#nau-cloudwatch)
+ [Enable or disable NAU monitoring](#nau-monitoring-enable)
+ [NAU CloudWatch alarm example](#nau-cloudwatch-alarm-example)

## NAU metrics and dimensions<a name="nau-cloudwatch"></a>

[Network Address Usage](network-address-usage.md) \(NAU\) is a metric applied to resources in your virtual network to help you plan for an monitor the size of your VPC\. There is no cost to monitor NAU\. Monitoring NAU is helpful because if you exhaust the NAU or peered NAU quotas for your VPC, you can't launch new EC2 instances or provision new resources, such as Network Load Balancers VPC endpoints, Lambda functions, transit gateway attachments, or NAT gateways\.

If you've enabled Network Address Usage monitoring for a VPC, Amazon VPC sends metrics related to NAU to Amazon CloudWatch\. The size of a VPC is measured by the number of Network Address Usage \(NAU\) units that the VPC contains\.

You can use these metrics to understand the rate of your VPC growth, forecast when your VPC will reach its size limit, or create alarms when size thresholds are crossed\.

The `AWS/EC2`namespace includes the following metrics for monitoring NAU\.


| Metric | Description | 
| --- | --- | 
|  NetworkAddressUsage  |  The percentage of the NAU quota that is currently in use in a single VPC\. **Dimensions** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/vpc-cloudwatch.html)  | 
|  NetworkAddressUsagePeered  |  The percentage of the peered NAU quota that is currently in use between a VPC and all VPCs that it's peered with\. **Dimensions** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/vpc-cloudwatch.html)  | 

The `AWS/Usage`namespace includes the following metrics for monitoring NAU\.


| Metric | Description | 
| --- | --- | 
|  ResourceCount  |  The NAU count per VPC\. **Dimensions** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/vpc-cloudwatch.html)  | 
|  ResourceCount  |  The NAU count for the VPC and all VPCs that it's peered with\. **Dimensions** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/vpc-cloudwatch.html)  | 
|  ResourceCount  |  A combined view of NAU usage across VPCs\. **Dimensions** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/vpc-cloudwatch.html)  | 
|  ResourceCount  |  A combined view of NAU usage across peered VPCs\. **Dimensions** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/vpc-cloudwatch.html)  | 

## Enable or disable NAU monitoring<a name="nau-monitoring-enable"></a>

To view NAU metrics in CloudWatch, you must first enable monitoring on each VPC to monitor\.

**To enable or disable monitoring NAU**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the check box for the VPC\.

1. Select **Actions**, **Edit VPC settings**\.

1. Do one of the following:
   + To enable monitoring, select **Network mapping units metrics settings**, **Enable network address usage metrics**\.
   + To disable monitoring, clear **Network mapping units metrics settings**, **Enable network address usage metrics**\.

**To enable or disable monitoring using the command line**
+ [modify\-vpc\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-attribute) \(AWS CLI\)
+ [Edit\-EC2VpcAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcAttribute.html) \(AWS Tools for Windows PowerShell\)

## NAU CloudWatch alarm example<a name="nau-cloudwatch-alarm-example"></a>

You can use the following AWS CLI command and example `.json` to create an Amazon CloudWatch alarm and SNS notification when the VPC reaches 80% of NAU utilization\. This sample requires you to first create an Amazon SNS topic\. For more information, see [Getting started with Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-getting-started.html) in the *Amazon Simple Notification Service Developer Guide*\.

```
aws cloudwatch put-metric-alarm --cli-input-json file://nau-alarm.json
```

The following is an example of `nau-alarm.json`\.

```
{
    "Namespace": "EC2",
    "MetricName": "NetworkAddressUsage",
    "Dimensions": [{
        "Name": "Per-VPC Metrics",
        "Value": "vpc-0123456798"
    }],
    "AlarmActions": ["arn:aws:sns:us-west-1:123456789012:my_sns_topic"],
    "ComparisonOperator": "GreaterThanThreshold",
    "EvaluationPeriods": 1,
    "Threshold": 80,
    "AlarmDescription": "NAU Utilization of VPC with 80% as threshold",
    "AlarmName": "VPC NAU Utilization"
}
```