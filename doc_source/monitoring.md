# Monitoring your VPC<a name="monitoring"></a>

You can use the following tools to monitor traffic or network access in your virtual private cloud \(VPC\)\.

**VPC Flow Logs**  
You can use VPC Flow Logs to capture detailed information about the traffic going to and from network interfaces in your VPCs\.

**Amazon VPC IP Address Manager \(IPAM\)**  
You can use IPAM to plan, track, and monitor IP addresses for your workloads\. For more information, see [IP Address Manager](https://docs.aws.amazon.com/vpc/latest/ipam/)\.

**Traffic Mirroring**  
You can use this feature to copy network traffic from a network interface of an Amazon EC2 instance and send it to out\-of\-band security and monitoring appliances for deep packet inspection\. You can detect network and security anomalies, gain operational insights, implement compliance and security controls, and troubleshoot issues\. For more information, see [Traffic Mirroring](https://docs.aws.amazon.com/vpc/latest/mirroring/)\.

**Reachability Analyzer**  
You can use this tool to analyze and debug network reachability between two resources in your VPC\. After you specify the source and destination resources, Reachability Analyzer produces hop\-by\-hop details of the virtual path between them when they are reachable, and identifies the blocking component when they are unreachable\. For more information, see [Reachability Analyzer](https://docs.aws.amazon.com/vpc/latest/reachability/)\.

**Network Access Analyzer**  
You can use Network Access Analyzer to understand network access to your resources\. This helps you identify improvements to your network security posture and demonstrate that your network meets specific compliance requirements\. For more information, see [Network Access Analyzer](https://docs.aws.amazon.com/vpc/latest/network-access-analyzer/)\.

**CloudTrail logs**  
You can use AWS CloudTrail to capture detailed information about the calls made to the Amazon VPC API\. You can use the generated CloudTrail logs to determine which calls were made, the source IP address where the call came from, who made the call, when the call was made, and so on\. For more information, see [Logging Amazon EC2 Amazon EBS, and Amazon VPC API calls using AWS CloudTrail](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/using-cloudtrail.html) in the *Amazon EC2 API Reference*\.