# Logging and Monitoring for Amazon VPC<a name="logging-monitoring"></a>

You can use the following automated monitoring tools to watch VPC and report when something is wrong: 
+ **Flow logs**: Flow logs capture information about the IP traffic going to and from network interfaces in your VPC\. You can create a flow log for a VPC, subnet, or individual network interface\. Flow log data is published to CloudWatch Logs or Amazon S3, and can help you diagnose overly restrictive or overly permissive security group and network ACL rules\. For more information, see [VPC Flow Logs](flow-logs.md)\.