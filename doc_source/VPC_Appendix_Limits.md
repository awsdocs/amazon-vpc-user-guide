# Amazon VPC Limits<a name="VPC_Appendix_Limits"></a>

The following tables list the limits for Amazon VPC resources per region for your AWS account\. Unless indicated otherwise, you can request an increase for these limits using the AWS Support Center\.

**To request a limit increase**

1. Open the [AWS Support Center](https://console.aws.amazon.com/support/home#/) page, sign in if necessary, and choose **Create Case**\.

1. For **Regarding**, choose **Service Limit Increase**\.

1. For **Limit Type**, choose **VPC**\. Choose the region and the applicable limit\. 

1. Complete the rest of the form and choose **Submit**\.

If you want to increase a limit that applies per resource, we increase the limit for all resources in the region; for example, the limit for security groups per VPC applies to all VPCs in the region\.


+ [VPC and Subnets](#vpc-limits-vpcs-subnets)
+ [DNS](#vpc-limits-dns)
+ [Elastic IP Addresses \(IPv4\)](#vpc-limits-eips)
+ [Flow Logs](#vpc-limits-flow-logs)
+ [Gateways](#vpc-limits-gateways)
+ [Network ACLs](#vpc-limits-nacls)
+ [Network Interfaces](#vpc-limits-enis)
+ [Route Tables](#vpc-limits-route-tables)
+ [Security Groups](#vpc-limits-security-groups)
+ [VPC Peering Connections](#vpc-limits-peering)
+ [VPC Endpoints](#vpc-limits-endpoints)
+ [VPN Connections](#vpc-limits-vpn)

## VPC and Subnets<a name="vpc-limits-vpcs-subnets"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  VPCs per region  |  5  |  The limit for internet gateways per region is directly correlated to this one\. Increasing this limit increases the limit on internet gateways per region by the same amount\. The multiple of the number of VPCs in the region and the number of security groups per VPC cannot exceed 5000\.  | 
|  Subnets per VPC  |  200  |  \-  | 
| IPv4 CIDR blocks per VPC | 5 | This limit is made up of your primary CIDR block plus 4 secondary CIDR blocks\. | 
|  IPv6 CIDR blocks per VPC  |  1  |  This limit cannot be increased\.  | 

## DNS<a name="vpc-limits-dns"></a>

For more information, see [DNS Limits](vpc-dns.md#vpc-dns-limits)\.

## Elastic IP Addresses \(IPv4\)<a name="vpc-limits-eips"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Elastic IP addresses per region  |  5  |  This is the limit for the number of Elastic IP addresses for use in EC2\-VPC\. For Elastic IP addresses for use in EC2\-Classic, see [Amazon EC2 Limits](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_ec2) in the *Amazon Web Services General Reference*\.  | 

## Flow Logs<a name="vpc-limits-flow-logs"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Flow logs per single network interface, single subnet, or single VPC in a region  | 2 | This limit cannot be increased\. You can effectively have 6 flow logs per network interface if you create 2 flow logs for the subnet, and 2 flow logs for the VPC in which your network interface resides\. | 

## Gateways<a name="vpc-limits-gateways"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Customer gateways per region  |  50  |  To increase this limit, contact AWS Support\.  | 
| Egress\-only internet gateways per region | 5 | This limit is directly correlated with the limit on VPCs per region\. To increase this limit, increase the limit on VPCs per region\. Only one egress\-only internet gateway can be attached to a VPC at a time\. | 
|  Internet gateways per region  |  5  |  This limit is directly correlated with the limit on VPCs per region\. To increase this limit, increase the limit on VPCs per region\. Only one internet gateway can be attached to a VPC at a time\.  | 
| NAT gateways per Availability Zone | 5 | A NAT gateway in the pending, active, or deleting state counts against your limit\. | 
|  Virtual private gateways per region  |  5  |  Only one virtual private gateway can be attached to a VPC at a time\.   | 

## Network ACLs<a name="vpc-limits-nacls"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Network ACLs per VPC  |  200  |  You can associate one network ACL to one or more subnets in a VPC\. This limit is not the same as the number of rules per network ACL\.  | 
|  Rules per network ACL  | 20 |  This is the one\-way limit for a single network ACL, where the limit for ingress rules is 20, and the limit for egress rules is 20\. This limit includes both IPv4 and IPv6 rules, and includes the default deny rules \(rule number 32767 for IPv4 and 32768 for IPv6, or an asterisk \* in the Amazon VPC console\)\.  This limit can be increased up to a maximum of 40; however, network performance may be impacted due to the increased workload to process the additional rules\.  | 

## Network Interfaces<a name="vpc-limits-enis"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
| Network interfaces per instance | \- | This limit varies by instance type\. For more information, see [IP Addresses Per ENI Per Instance Type](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI)\. | 
| Network interfaces per region | 350 | This limit is the greater of either the default limit \(350\) or your On\-Demand Instance limit multiplied by 5\. The default limit for On\-Demand Instances is 20\. If your On\-Demand Instance limit is below 70, the default limit of 350 applies\. To increase this limit, submit a request or increase your On\-Demand Instance limit\. | 

## Route Tables<a name="vpc-limits-route-tables"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Route tables per VPC  |  200  |  This limit includes the main route table\.  | 
|  Routes per route table \(non\-propagated routes\)  |  50  |  You can increase this limit up to a maximum of 100; however, network performance may be impacted\. This limit is enforced separately for IPv4 routes and IPv6 routes \(you can have 50 each, and a maximum of 100 each\)\.  | 
|  BGP advertised routes per route table \(propagated routes\)  |  100  |  This limit cannot be increased\. If you require more than 100 prefixes, advertise a default route\.  | 

## Security Groups<a name="vpc-limits-security-groups"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Security groups per VPC \(per region\)  |  500  |  The multiple of the number of VPCs in the region and the number of security groups per VPC cannot exceed 5000\.  | 
|  Inbound or outbound rules per security group   |  50  |  You can have 50 inbound and 50 outbound rules per security group \(giving a total of 100 combined inbound and outbound rules\)\. To increase or decrease this limit, contact AWS Support — a limit change applies to both inbound and outbound rules\. However, the multiple of the limit for inbound or outbound rules per security group and the limit for security groups per network interface cannot exceed 250\. For example, if you want to increase the limit to 100, we decrease your number of security groups per network interface to 2\. This limit is enforced separately for IPv4 rules and IPv6 rules; for example, your security group can have 50 inbound rules for IPv4 traffic and 50 inbound rules for IPv6 traffic\. A rule that references a security group counts as one rule for IPv4 and one rule for IPv6\.  | 
|  Security groups per network interface  |  5  |  To increase or decrease this limit, you can contact AWS Support\. The maximum is 16\. The multiple of the limit for security groups per network interface and the limit for rules per security group cannot exceed 250\. For example, if you increase the limit to 10, we decrease your number of rules per security group to 25\.  | 

## VPC Peering Connections<a name="vpc-limits-peering"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
| Active VPC peering connections per VPC |  50  |  The maximum limit is 125 peering connections per VPC\. The number of entries per route table should be increased accordingly; however, network performance may be impacted\.   | 
|  Outstanding VPC peering connection requests  | 25 |  This is the limit for the number of outstanding VPC peering connection requests that you've requested from your account\. To increase this limit, contact AWS Support\.  | 
|  Expiry time for an unaccepted VPC peering connection request  |  1 week \(168 hours\)  |  To increase this limit, contact AWS Support\.  | 

## VPC Endpoints<a name="vpc-limits-endpoints"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
| Gateway VPC endpoints per region | 20 | To increase this limit, contact AWS Support\. The maximum limit is 255 endpoints per VPC, regardless of your endpoint limit per region\. | 
| Interface VPC endpoints per VPC | 20 | \- | 

## VPN Connections<a name="vpc-limits-vpn"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  VPN connections per region  |  50  |  \-  | 
|  VPN connections per VPC \(per virtual private gateway\)  | 10 |  \-  | 