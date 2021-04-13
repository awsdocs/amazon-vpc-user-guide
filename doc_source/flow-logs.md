# VPC Flow Logs<a name="flow-logs"></a>

VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC\. Flow log data can be published to Amazon CloudWatch Logs or Amazon S3\. After you've created a flow log, you can retrieve and view its data in the chosen destination\.

Flow logs can help you with a number of tasks, such as:
+ Diagnosing overly restrictive security group rules
+ Monitoring the traffic that is reaching your instance
+ Determining the direction of the traffic to and from the network interfaces

Flow log data is collected outside of the path of your network traffic, and therefore does not affect network throughput or latency\. You can create or delete flow logs without any risk of impact to network performance\.

**Topics**
+ [Flow logs basics](#flow-logs-basics)
+ [Flow log records](#flow-log-records)
+ [Flow log record examples](flow-logs-records-examples.md)
+ [Flow log limitations](#flow-logs-limitations)
+ [Flow logs pricing](#flow-logs-pricing)
+ [Publishing flow logs to CloudWatch Logs](flow-logs-cwl.md)
+ [Publishing flow logs to Amazon S3](flow-logs-s3.md)
+ [Working with flow logs](working-with-flow-logs.md)
+ [Querying flow logs using Amazon Athena](flow-logs-athena.md)
+ [Troubleshooting VPC Flow Logs](flow-logs-troubleshooting.md)

## Flow logs basics<a name="flow-logs-basics"></a>

You can create a flow log for a VPC, a subnet, or a network interface\. If you create a flow log for a subnet or VPC, each network interface in that subnet or VPC is monitored\. 

Flow log data for a monitored network interface is recorded as *flow log records*, which are log events consisting of fields that describe the traffic flow\. For more information, see [Flow log records](#flow-log-records)\.

To create a flow log, you specify:
+ The resource for which to create the flow log
+ The type of traffic to capture \(accepted traffic, rejected traffic, or all traffic\)
+ The destinations to which you want to publish the flow log data

In the following example, you create a flow log \(`fl-aaa`\) that captures accepted traffic for the network interface for instance A1 and publishes the flow log records to an Amazon S3 bucket\. You create a second flow log that captures all traffic for subnet B and publishes the flow log records to Amazon CloudWatch Logs\. The flow log \(`fl-bbb`\) captures traffic for all network interfaces in subnet B\. There are no flow logs that capture traffic for instance A2's network interface\.

![\[Flow logs for a subnet and an instance\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/flow-logs-diagram.png)

After you've created a flow log, it can take several minutes to begin collecting and publishing data to the chosen destinations\. Flow logs do not capture real\-time log streams for your network interfaces\. For more information, see [Creating a flow log](working-with-flow-logs.md#create-flow-log)\. 

If you launch more instances into your subnet after you've created a flow log for your subnet or VPC, a new log stream \(for CloudWatch Logs\) or log file object \(for Amazon S3\) is created for each new network interface\. This occurs as soon as any network traffic is recorded for that network interface\.

You can create flow logs for network interfaces that are created by other AWS services, such as:
+ Elastic Load Balancing
+ Amazon RDS
+ Amazon ElastiCache
+ Amazon Redshift
+ Amazon WorkSpaces
+ NAT gateways
+ Transit gateways

Regardless of the type of network interface, you must use the Amazon EC2 console or the Amazon EC2 API to create a flow log for a network interface\.

You can apply tags to your flow logs\. Each tag consists of a key and an optional value, both of which you define\. Tags can help you organize your flow logs, for example by purpose or owner\.

If you no longer require a flow log, you can delete it\. Deleting a flow log disables the flow log service for the resource, and no new flow log records are created or published to CloudWatch Logs or Amazon S3\. Deleting the flow log does not delete any existing flow log records or log streams \(for CloudWatch Logs\) or log file objects \(for Amazon S3\) for a network interface\. To delete an existing log stream, use the CloudWatch Logs console\. To delete existing log file objects, use the Amazon S3 console\. After you've deleted a flow log, it can take several minutes to stop collecting data\. For more information, see [Deleting a flow log](working-with-flow-logs.md#delete-flow-log)\.

## Flow log records<a name="flow-log-records"></a>

A flow log record represents a network flow in your VPC\. By default, each record captures a network internet protocol \(IP\) traffic flow \(characterized by a 5\-tuple on a per network interface basis\) that occurs within an *aggregation interval*, also referred to as a *capture window*\.

Each record is a string with fields separated by spaces\. A record includes values for the different components of the IP flow, for example, the source, destination, and protocol\.

When you create a flow log, you can use the default format for the flow log record, or you can specify a custom format\.

**Topics**
+ [Aggregation interval](#flow-logs-aggregration-interval)
+ [Default format](#flow-logs-default)
+ [Custom format](#flow-logs-custom)
+ [Available fields](#flow-logs-fields)

### Aggregation interval<a name="flow-logs-aggregration-interval"></a>

The aggregation interval is the period of time during which a particular flow is captured and aggregated into a flow log record\. By default, the maximum aggregation interval is 10 minutes\. When you create a flow log, you can optionally specify a maximum aggregation interval of 1 minute\. Flow logs with a maximum aggregation interval of 1 minute produce a higher volume of flow log records than flow logs with a maximum aggregation interval of 10 minutes\.

When a network interface is attached to a [Nitro\-based instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances), the aggregation interval is always 1 minute or less, regardless of the specified maximum aggregation interval\.

After data is captured within an aggregation interval, it takes additional time to process and publish the data to CloudWatch Logs or Amazon S3\. This additional time could be around 5 minutes to publish to CloudWatch Logs, and around 10 minutes to publish to Amazon S3\. The flow logs service delivers within this additional time in a best effort manner\. In some cases, your logs might be delayed beyond the 5 to 10 minutes additional time mentioned previously\.

### Default format<a name="flow-logs-default"></a>

With the default format, the flow log records include the version 2 fields, in the order shown in the [available fields](#flow-logs-fields) table\. You cannot customize or change the default format\. To capture additional fields or a different subset of fields, specify a custom format instead\.

### Custom format<a name="flow-logs-custom"></a>

With a custom format, you specify which fields are included in the flow log records and in which order\. This enables you to create flow logs that are specific to your needs and to omit fields that are not relevant\. Using a custom format can reduce the need for separate processes to extract specific information from the published flow logs\. You can specify any number of the available flow log fields, but you must specify at least one\.

### Available fields<a name="flow-logs-fields"></a>

The following table describes all of the available fields for a flow log record\. The **Version** column indicates the VPC Flow Logs version in which the field was introduced\. The default format includes all version 2 fields, in same the order that they appear in the table\.

If a field is not applicable or could not be computed for a specific record, the record displays a '\-' symbol for that entry\. Metadata fields that do not come directly from the packet header are best effort approximations, and their values might be missing or inaccurate\.


| Field | Description | Version | 
| --- | --- | --- | 
|  version  |  The VPC Flow Logs version\. If you use the default format, the version is 2\. If you use a custom format, the version is the highest version among the specified fields\. For example, if you specify only fields from version 2, the version is 2\. If you specify a mixture of fields from versions 2, 3, and 4, the version is 4\.  | 2 | 
|  account\-id  |  The AWS account ID of the owner of the source network interface for which traffic is recorded\. If the network interface is created by an AWS service, for example when creating a VPC endpoint or Network Load Balancer, the record may display unknown for this field\.  | 2 | 
|  interface\-id  |  The ID of the network interface for which the traffic is recorded\.  | 2 | 
|  srcaddr  |  The source address for incoming traffic, or the IPv4 or IPv6 address of the network interface for outgoing traffic on the network interface\. The IPv4 address of the network interface is always its private IPv4 address\. See also pkt\-srcaddr\.  | 2 | 
|  dstaddr  |  The destination address for outgoing traffic, or the IPv4 or IPv6 address of the network interface for incoming traffic on the network interface\. The IPv4 address of the network interface is always its private IPv4 address\. See also pkt\-dstaddr\.  | 2 | 
|  srcport  |  The source port of the traffic\.  | 2 | 
|  dstport  |  The destination port of the traffic\.  | 2 | 
|  protocol  |  The IANA protocol number of the traffic\. For more information, see [ Assigned Internet Protocol Numbers](http://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)\.  | 2 | 
|  packets  |  The number of packets transferred during the flow\.  | 2 | 
|  bytes  |  The number of bytes transferred during the flow\.  | 2 | 
|  start  |  The time, in Unix seconds, when the first packet of the flow was received within the aggregation interval\. This might be up to 60 seconds after the packet was transmitted or received on the network interface\.  | 2 | 
|  end  |  The time, in Unix seconds, when the last packet of the flow was received within the aggregation interval\. This might be up to 60 seconds after the packet was transmitted or received on the network interface\.  | 2 | 
|  action  |  The action that is associated with the traffic: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)  | 2 | 
|  log\-status  |  The logging status of the flow log: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)  | 2 | 
|  vpc\-id  |  The ID of the VPC that contains the network interface for which the traffic is recorded\.  | 3 | 
|  subnet\-id  |  The ID of the subnet that contains the network interface for which the traffic is recorded\.  | 3 | 
|  instance\-id  |  The ID of the instance that's associated with network interface for which the traffic is recorded, if the instance is owned by you\. Returns a '\-' symbol for a [requester\-managed network interface](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/requester-managed-eni.html); for example, the network interface for a NAT gateway\.  | 3 | 
|  tcp\-flags  |  The bitmask value for the following TCP flags: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) ACK is reported only when it's accompanied with SYN\. TCP flags can be OR\-ed during the aggregation interval\. For short connections, the flags might be set on the same line in the flow log record, for example, 19 for SYN\-ACK and FIN, and 3 for SYN and FIN\. For an example, see [TCP flag sequence](flow-logs-records-examples.md#flow-log-example-tcp-flag)\.  | 3 | 
|  type  |  The type of traffic\. The possible values are: IPv4, IPv6, and EFA\. For more information about the Elastic Fabric Adapter \(EFA\), see [Elastic Fabric Adapter](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html)\.  | 3 | 
|  pkt\-srcaddr  |  The packet\-level \(original\) source IP address of the traffic\. Use this field with the srcaddr field to distinguish between the IP address of an intermediate layer through which traffic flows, and the original source IP address of the traffic\. For example, when traffic flows through [a network interface for a NAT gateway](flow-logs-records-examples.md#flow-log-example-nat), or where the IP address of a pod in Amazon EKS is different from the IP address of the network interface of the instance node on which the pod is running \(for communication within a VPC\)\.  | 3 | 
|  pkt\-dstaddr  |  The packet\-level \(original\) destination IP address for the traffic\. Use this field with the dstaddr field to distinguish between the IP address of an intermediate layer through which traffic flows, and the final destination IP address of the traffic\. For example, when traffic flows through [a network interface for a NAT gateway](flow-logs-records-examples.md#flow-log-example-nat), or where the IP address of a pod in Amazon EKS is different from the IP address of the network interface of the instance node on which the pod is running \(for communication within a VPC\)\.  | 3 | 
|  region  |  The Region that contains the network interface for which traffic is recorded\.  |  4  | 
|  az\-id  |  The ID of the Availability Zone that contains the network interface for which traffic is recorded\. If the traffic is from a sublocation, the record displays a '\-' symbol for this field\.  |  4  | 
| sublocation\-type |  The type of sublocation that's returned in the sublocation\-id field\. The possible values are: [wavelength](https://aws.amazon.com/wavelength/) \| [outpost](https://docs.aws.amazon.com/outposts/latest/userguide/) \| [localzone](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-local-zones)\. If the traffic is not from a sublocation, the record displays a '\-' symbol for this field\.  |  4  | 
|  sublocation\-id  |  The ID of the sublocation that contains the network interface for which traffic is recorded\. If the traffic is not from a sublocation, the record displays a '\-' symbol for this field\.  |  4  | 
|  pkt\-src\-aws\-service  |  The name of the subset of [IP address ranges](https://docs.aws.amazon.com/general/latest/gr/aws-ip-ranges.html) for the pkt\-srcaddr field, if the source IP address is for an AWS service\. The possible values are: AMAZON \| AMAZON\_APPFLOW \| AMAZON\_CONNECT \| API\_GATEWAY \| CHIME\_MEETINGS \| CHIME\_VOICECONNECTOR \| CLOUD9 \| CLOUDFRONT \| CODEBUILD \| DYNAMODB \| EC2 \| EC2\_INSTANCE\_CONNECT \| GLOBALACCELERATOR \| KINESIS\_VIDEO\_STREAMS \| ROUTE53 \| ROUTE53\_HEALTHCHECKS \| S3 \| WORKSPACES\_GATEWAYS\.  |  5  | 
|  pkt\-dst\-aws\-service  |  The name of the subset of IP address ranges for the pkt\-dstaddr field, if the destination IP address is for an AWS service\. For a list of possible values, see the pkt\-src\-aws\-service field\.  |  5  | 
|  flow\-direction  |  The direction of the flow with respect to the interface where traffic is captured\. The possible values are: ingress \| egress\.  |  5  | 
|  traffic\-path  |  The path that egress traffic takes to the destination\. To determine whether the traffic is egress traffic, check the flow\-direction field\. The possible values are as follows\. If none of the values apply, the field is set to \-\. If the network interface is attached to an instance based on the Nitro System, the possible values include 7 and 8 but not 2\. With instances not based on the Nitro System \(for example, T2 and M4\), the possible values include 2 but not 7 or 8\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)  |  5  | 

## Flow log limitations<a name="flow-logs-limitations"></a>

To use flow logs, you need to be aware of the following limitations:
+ You cannot enable flow logs for network interfaces that are in the EC2\-Classic platform\. This includes EC2\-Classic instances that have been linked to a VPC through ClassicLink\. 
+ You can't enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account\.
+ After you've created a flow log, you cannot change its configuration or the flow log record format\. For example, you can't associate a different IAM role with the flow log, or add or remove fields in the flow log record\. Instead, you can delete the flow log and create a new one with the required configuration\. 
+ If your network interface has multiple IPv4 addresses and traffic is sent to a secondary private IPv4 address, the flow log displays the primary private IPv4 address in the `dstaddr` field\. To capture the original destination IP address, create a flow log with the `pkt-dstaddr` field\.
+ If traffic is sent to a network interface and the destination is not any of the network interface's IP addresses, the flow log displays the primary private IPv4 address in the `dstaddr` field\. To capture the original destination IP address, create a flow log with the `pkt-dstaddr` field\.
+ If traffic is sent from a network interface and the source is not any of the network interface's IP addresses, the flow log displays the primary private IPv4 address in the `srcaddr` field\. To capture the original source IP address, create a flow log with the `pkt-srcaddr` field\.
+ If traffic is sent to or sent by a network interface, the `srcaddr` and `dstaddr` fields in the flow log always display the primary private IPv4 address, regardless of the packet source or destination\. To capture the packet source or destination, create a flow log with the `pkt-srcaddr` and `pkt-dstaddr` fields\.
+ When your network interface is attached to a [Nitro\-based instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances), the aggregation interval is always 1 minute or less, regardless of the specified maximum aggregation interval\.

Flow logs do not capture all IP traffic\. The following types of traffic are not logged:
+ Traffic generated by instances when they contact the Amazon DNS server\. If you use your own DNS server, then all traffic to that DNS server is logged\. 
+ Traffic generated by a Windows instance for Amazon Windows license activation\.
+ Traffic to and from `169.254.169.254` for instance metadata\.
+ Traffic to and from `169.254.169.123` for the Amazon Time Sync Service\.
+ DHCP traffic\.
+ Traffic to the reserved IP address for the default VPC router\.
+ Traffic between an endpoint network interface and a Network Load Balancer network interface\.

## Flow logs pricing<a name="flow-logs-pricing"></a>

Data ingestion and archival charges for vended logs apply when you publish flow logs to CloudWatch Logs or to Amazon S3\. For more information and examples, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing)\.

To track charges from publishing flow logs to your Amazon S3 buckets, you can apply cost allocation tags to your flow log subscriptions\. To track charges from publishing flow logs to CloudWatch Logs, you can apply cost allocation tags to your destination CloudWatch Logs log group\. Thereafter, your AWS cost allocation report will include usage and costs aggregated by these tags\. You can apply tags that represent business categories \(such as cost centers, application names, or owners\) to organize your costs\. For more information, see [Using Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\.