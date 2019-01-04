# Amazon VPC Limits<a name="amazon-vpc-limits"></a>

The following tables list the limits for Amazon VPC resources per Region for your AWS account\. Unless indicated otherwise, you can request an increase for these limits using the [Amazon VPC limits form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=vpc)\. For some of these limits, you can view your current limit using the **Limits** page of the Amazon EC2 console\.

If you request a limit increase that applies per resource, we increase the limit for all resources in the Region\. For example, the limit for security groups per VPC applies to all VPCs in the Region\.

## VPC and Subnets<a name="vpc-limits-vpcs-subnets"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  VPCs per Region  |  5  |  The limit for internet gateways per Region is directly correlated to this one\. Increasing this limit increases the limit on internet gateways per Region by the same amount\.  | 
|  Subnets per VPC  |  200  |  \-  | 
| IPv4 CIDR blocks per VPC | 5 | This limit is made up of your primary CIDR block plus 4 secondary CIDR blocks\. | 
|  IPv6 CIDR blocks per VPC  |  1  |  This limit cannot be increased\.  | 

## DNS<a name="vpc-limits-dns"></a>

For more information, see [DNS Limits](vpc-dns.md#vpc-dns-limits)\.

## Elastic IP Addresses \(IPv4\)<a name="vpc-limits-eips"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Elastic IP addresses per Region  |  5  |  This is the limit for the number of Elastic IP addresses for use in EC2\-VPC\. For Elastic IP addresses for use in EC2\-Classic, see [Amazon EC2 Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_ec2) in the *Amazon Web Services General Reference*\.  | 

## Flow Logs<a name="vpc-limits-flow-logs"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Flow logs per single network interface, single subnet, or single VPC in a Region  | 2 | This limit cannot be increased\. You can effectively have 6 flow logs per network interface if you create 2 flow logs for the subnet, and 2 flow logs for the VPC in which your network interface resides\. | 

## Gateways<a name="vpc-limits-gateways"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Customer gateways per Region  |  50  |  \-  | 
| Egress\-only internet gateways per Region | 5 | This limit is directly correlated with the limit on VPCs per Region\. To increase this limit, increase the limit on VPCs per Region\. You can attach only one egress\-only internet gateway to a VPC at a time\. | 
|  Internet gateways per Region  |  5  |  This limit is directly correlated with the limit on VPCs per Region\. To increase this limit, increase the limit on VPCs per Region\. Only one internet gateway can be attached to a VPC at a time\.  | 
| NAT gateways per Availability Zone | 5 | A NAT gateway in the pending, active, or deleting state counts against your limit\. | 
|  Virtual private gateways per Region  |  5  |  You can attach only one virtual private gateway to a VPC at a time\.   | 

## Network ACLs<a name="vpc-limits-nacls"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Network ACLs per VPC  |  200  |  You can associate one network ACL to one or more subnets in a VPC\. This limit is not the same as the number of rules per network ACL\.  | 
|  Rules per network ACL  | 20 |  This is the one\-way limit for a single network ACL, where the limit for ingress rules is 20, and the limit for egress rules is 20\. This limit includes both IPv4 and IPv6 rules, and includes the default deny rules \(rule number 32767 for IPv4 and 32768 for IPv6, or an asterisk \* in the Amazon VPC console\)\.  This limit can be increased up to a maximum of 40; however, network performance might be impacted due to the increased workload to process the additional rules\.  | 

## Network Interfaces<a name="vpc-limits-enis"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
| Network interfaces per instance | \- | This limit varies by instance type\. For more information, see [IP Addresses Per ENI Per Instance Type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI)\. | 
| Network interfaces per Region | 350 | This limit is the greater of either the default limit \(350\) or your On\-Demand Instance limit multiplied by 5\. The default limit for On\-Demand Instances is 20\. If your On\-Demand Instance limit is below 70, the default limit of 350 applies\. To increase this limit, submit a request or increase your On\-Demand Instance limit\. | 

## Route Tables<a name="vpc-limits-route-tables"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Route tables per VPC  |  200  |  This limit includes the main route table\.  | 
|  Routes per route table \(non\-propagated routes\)  |  50  |  You can increase this limit up to a maximum of 100; however, network performance might be impacted\. This limit is enforced separately for IPv4 routes and IPv6 routes \(you can have 50 each, and a maximum of 100 each\)\.  | 
|  BGP advertised routes per route table \(propagated routes\)  |  100  |  This limit cannot be increased\. If you require more than 100 prefixes, advertise a default route\.  | 

## Security Groups<a name="vpc-limits-security-groups"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  VPC security groups per Region  |  2500  |  The maximum is 10000\. If you have more than 5000 security groups in a Region, we recommend that you paginate calls to describe your security groups for better performance\.  | 
|  Inbound or outbound rules per security group   |  60  |  You can have 60 inbound and 60 outbound rules per security group \(making a total of 120 rules\)\. This limit is enforced separately for IPv4 rules and IPv6 rules; for example, a security group can have 60 inbound rules for IPv4 traffic and 60 inbound rules for IPv6 traffic\. A rule that references a security group or preflix list ID counts as one rule for IPv4 and one rule for IPv6\. A limit change applies to both inbound and outbound rules\. This limit multiplied by the limit for security groups per network interface cannot exceed 300\. For example, if you increase this limit to 100, we decrease the limit for your number of security groups per network interface to 3\.  | 
|  Security groups per network interface  |  5  |  The maximum is 16\. The limit for security groups per network interface multiplied by the limit for rules per security group cannot exceed 300\. For example, if you increase this limit to 10, we decrease the limit for your number of rules per security group to 30\.  | 

## VPC Peering Connections<a name="vpc-limits-peering"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
| Active VPC peering connections per VPC |  50  |  The maximum limit is 125 peering connections per VPC\. The number of entries per route table should be increased accordingly; however, network performance might be impacted\.  | 
|  Outstanding VPC peering connection requests  | 25 |  This is the limit for the number of outstanding VPC peering connection requests that you've requested from your account\.  | 
|  Expiry time for an unaccepted VPC peering connection request  |  1 week \(168 hours\)  |  \-  | 

## VPC Endpoints<a name="vpc-limits-endpoints"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
| Gateway VPC endpoints per Region | 20 | You cannot have more than 255 gateway endpoints per VPC\. | 
| Interface VPC endpoints per VPC | 20 | The maximum limit for interface endpoints per Region is this limit multiplied by the number of VPCs in the Region\. | 

## AWS Site\-to\-Site VPN Connections<a name="vpc-limits-vpn"></a>


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Site\-to\-Site VPN connections per Region  |  50  |  \-  | 
|  Site\-to\-Site VPN connections per VPC \(per virtual private gateway\)  | 10 |  \-  | 

## VPC Sharing<a name="vpc-share-limits"></a>

All standard VPC limits apply to a shared VPC\.


| Resource | Default limit | Comments | 
| --- | --- | --- | 
|  Number of distinct accounts that a VPC can be shared with  |   30  |  \-  | 
| Number of subnets that can be shared with an account |  100 |  \-  | 