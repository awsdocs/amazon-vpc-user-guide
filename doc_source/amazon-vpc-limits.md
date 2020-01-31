# Amazon VPC Quotas<a name="amazon-vpc-limits"></a>

The following tables list the quotas, formerly referred to as limits, for Amazon VPC resources per Region for your AWS account\. Unless indicated otherwise, you can [request an increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=vpc) for these quotas\. For some of these quotas, you can view your current quota using the **Limits** page of the Amazon EC2 console\.

If you request a quota increase that applies per resource, we increase the quota for all resources in the Region\. For example, the quota for security groups per VPC applies to all VPCs in the Region\.

## VPC and Subnets<a name="vpc-limits-vpcs-subnets"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
|  VPCs per Region  |  5  |  The quota for internet gateways per Region is directly correlated to this one\. Increasing this quota increases the quota on internet gateways per Region by the same amount\. You can have 100s of VPCs per Region for your needs even though the default quota is 5 VPCs per Region\.  | 
|  Subnets per VPC  |  200  |  \-  | 
| IPv4 CIDR blocks per VPC | 5 | This primary CIDR block and all secondary CIDR blocks count toward this quota\. This quota can be increased up to a maximum of 50\. | 
|  IPv6 CIDR blocks per VPC  |  1  |  This quota cannot be increased\.  | 

## DNS<a name="vpc-limits-dns"></a>

For more information, see [DNS Quotas](vpc-dns.md#vpc-dns-limits)\.

## Elastic IP Addresses \(IPv4\)<a name="vpc-limits-eips"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
|  Elastic IP addresses per Region  |  5  |  This is the quota for the number of Elastic IP addresses for use in EC2\-VPC\. For Elastic IP addresses for use in EC2\-Classic, see [Amazon Elastic Compute Cloud Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html) in the *Amazon Web Services General Reference*\.  | 

## Gateways<a name="vpc-limits-gateways"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
|  Customer gateways per Region  |  \-  |  For more information, see [Site\-to\-Site VPN Quotas](https://docs.aws.amazon.com/vpn/latest/s2svpn/vpn-limits.html) in the *AWS Site\-to\-Site VPN User Guide*\.  | 
| Egress\-only internet gateways per Region | 5 | This quota is directly correlated with the quota on VPCs per Region\. To increase this quota, increase the quota on VPCs per Region\. You can attach only one egress\-only internet gateway to a VPC at a time\. | 
|  Internet gateways per Region  |  5  |  This quota is directly correlated with the quota on VPCs per Region\. To increase this quota, increase the quota on VPCs per Region\. Only one internet gateway can be attached to a VPC at a time\.  | 
| NAT gateways per Availability Zone | 5 | A NAT gateway in the pending, active, or deleting state counts against your quota\. | 
|  Virtual private gateways per Region  |  \-  |  For more information, see [Site\-to\-Site VPN Quotas](https://docs.aws.amazon.com/vpn/latest/s2svpn/vpn-limits.html) in the *AWS Site\-to\-Site VPN User Guide*\.   | 

## Network ACLs<a name="vpc-limits-nacls"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
|  Network ACLs per VPC  |  200  |  You can associate one network ACL to one or more subnets in a VPC\. This quota is not the same as the number of rules per network ACL\.  | 
|  Rules per network ACL  | 20 |  This is the one\-way quota for a single network ACL, where the quota for ingress rules is 20, and the quota for egress rules is 20\. This quota includes both IPv4 and IPv6 rules, and includes the default deny rules \(rule number 32767 for IPv4 and 32768 for IPv6, or an asterisk \* in the Amazon VPC console\)\.  This quota can be increased up to a maximum of 40; however, network performance might be impacted due to the increased workload to process the additional rules\.  | 

## Network Interfaces<a name="vpc-limits-enis"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
| Network interfaces per instance | \- | This quota varies by instance type\. For more information, see [IP Addresses Per ENI Per Instance Type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI)\. | 
| Network interfaces per Region | 5000 | \- | 

## Route Tables<a name="vpc-limits-route-tables"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
|  Route tables per VPC  |  200  |  The main route table counts toward this quota\.  | 
|  Routes per route table \(non\-propagated routes\)  |  50  |  You can increase this quota up to a maximum of 1000; however, network performance might be impacted\. This quota is enforced separately for IPv4 routes and IPv6 routes\.  If you have more than 125 routes, we recommend that you paginate calls to describe your route tables for better performance\.   | 
|  BGP advertised routes per route table \(propagated routes\)  |  100  |  This quota cannot be increased\. If you require more than 100 prefixes, advertise a default route\.  | 

## Security Groups<a name="vpc-limits-security-groups"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
|  VPC security groups per Region  |  2500  |  If you have more than 5000 security groups in a Region, we recommend that you paginate calls to describe your security groups for better performance\.  | 
|  Inbound or outbound rules per security group   |  60  |  You can have 60 inbound and 60 outbound rules per security group \(making a total of 120 rules\)\. This quota is enforced separately for IPv4 rules and IPv6 rules; for example, a security group can have 60 inbound rules for IPv4 traffic and 60 inbound rules for IPv6 traffic\. A rule that references a security group or prefix list ID counts as one rule for IPv4 and one rule for IPv6\. A quota change applies to both inbound and outbound rules\. This quota multiplied by the quota for security groups per network interface cannot exceed 1000\. For example, if you increase this quota to 100, we decrease the quota for your number of security groups per network interface to 10\.  | 
|  Security groups per network interface  |  5  |  The maximum is 15\. This quota is enforced separately for IPv4 rules and IPv6 rules\. The quota for security groups per network interface multiplied by the quota for rules per security group cannot exceed 1000\. For example, if you increase this quota to 10, we decrease the quota for your number of rules per security group to 100\.  | 

## VPC Peering Connections<a name="vpc-limits-peering"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
| Active VPC peering connections per VPC |  50  |  The maximum quota is 125 peering connections per VPC\. The number of entries per route table should be increased accordingly; however, network performance might be impacted\.  | 
|  Outstanding VPC peering connection requests  | 25 |  This is the quota for the number of outstanding VPC peering connection requests that you've requested from your account\.  | 
|  Expiry time for an unaccepted VPC peering connection request  |  1 week \(168 hours\)  |  This quota cannot be increased\.  | 

## VPC Endpoints<a name="vpc-limits-endpoints"></a>


| Resource | Default | Comments | 
| --- | --- | --- | 
| Gateway VPC endpoints per Region | 20 | You cannot have more than 255 gateway endpoints per VPC\. | 
| Interface VPC endpoints per VPC | 50 | This is the quota for the maximum number of endpoints in a VPC\. To increase this quota, contact AWS Support\.  | 

## AWS Site\-to\-Site VPN Connections<a name="vpc-limits-vpn"></a>

For more information, see [Site\-to\-Site VPN Quotas](https://docs.aws.amazon.com/vpn/latest/s2svpn/vpn-limits.html) in the *AWS Site\-to\-Site VPN User Guide*\.

## VPC Sharing<a name="vpc-share-limits"></a>

All standard VPC quotas apply to a shared VPC\.


| Resource | Default | Comments | 
| --- | --- | --- | 
|  Number of distinct accounts that a VPC can be shared with  |   100  | This is the quota for the number of distinct participant accounts that subnets in a VPC can be shared with\. This is a per VPC quota and applies across all the subnets shared in a VPC\. AWS recommends that you paginate your DescribeSecurityGroups and DescribeNetworkInterfaces API calls before requesting an increase for this quota\. To increase this quota, contact AWS Support\.  | 
| Number of subnets that can be shared with an account |  100 |  This is the quota for maximum number of subnets that can be shared with an AWS account\. AWS recommends that you paginate your `DescribeSecurityGroups` and `DescribeSubnets` API calls before requesting an increase for this quota\. To increase this quota contact AWS Support\.  | 