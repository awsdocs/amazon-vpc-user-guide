# Monitoring Your VPN Connection<a name="monitoring-overview-vpn"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of your VPN connection\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multi\-point failure if one occurs\. Before you start monitoring your VPN connection; however, you should create a monitoring plan that includes answers to the following questions:

+ What are your monitoring goals?

+ What resources will you monitor?

+ How often will you monitor these resources?

+ What monitoring tools will you use?

+ Who will perform the monitoring tasks?

+ Who should be notified when something goes wrong?

The next step is to establish a baseline for normal VPN performance in your environment, by measuring performance at various times and under different load conditions\. As you monitor your VPN, store historical monitoring data so that you can compare it with current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues\.

To establish a baseline, you should monitor the following items:

+ The state of your VPN tunnels

+ Data into the tunnel

+ Data out of the tunnel


+ [Monitoring Tools](#monitoring-automated-manual)
+ [Monitoring with Amazon CloudWatch](monitoring-cloudwatch-vpn.md)

## Monitoring Tools<a name="monitoring-automated-manual"></a>

AWS provides various tools that you can use to monitor a VPN connection\. You can configure some of these tools to do the monitoring for you, while some of the tools require manual intervention\. We recommend that you automate monitoring tasks as much as possible\.

### Automated Monitoring Tools<a name="monitoring-automated_tools"></a>

You can use the following automated monitoring tools to watch a VPN connection and report when something is wrong:

+ **Amazon CloudWatch Alarms** – Watch a single metric over a time period that you specify, and perform one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic\. CloudWatch alarms do not invoke actions simply because they are in a particular state; the state must have changed and been maintained for a specified number of periods\. For more information, see [Monitoring with Amazon CloudWatch](monitoring-cloudwatch-vpn.md)\.

+ **AWS CloudTrail Log Monitoring** – Share log files between accounts, monitor CloudTrail log files in real time by sending them to CloudWatch Logs, write log processing applications in Java, and validate that your log files have not changed after delivery by CloudTrail\. For more information, see [Logging API Calls Using AWS CloudTrail](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/using-cloudtrail.html) in the *Amazon EC2 API Reference* and [Working with CloudTrail Log Files](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-working-with-log-files.html) in the *AWS CloudTrail User Guide*

### Manual Monitoring Tools<a name="monitoring-manual-tools"></a>

Another important part of monitoring a VPN connection involves manually monitoring those items that the CloudWatch alarms don't cover\. The Amazon VPC and CloudWatch console dashboards provide an at\-a\-glance view of the state of your AWS environment\. 

+ The Amazon VPC dashboard shows:

  + Service health by region

  + VPN connections

  + VPN tunnel status \(In the navigation pane, choose **VPN Connections**, select a VPN connection, and then choose **Tunnel Details**\)

+ The CloudWatch home page shows:

  + Current alarms and status

  + Graphs of alarms and resources

  + Service health status

  In addition, you can use CloudWatch to do the following: 

  + Create [customized dashboards](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatch_Dashboards.html) to monitor the services you care about

  + Graph metric data to troubleshoot issues and discover trends

  + Search and browse all your AWS resource metrics

  + Create and edit alarms to be notified of problems