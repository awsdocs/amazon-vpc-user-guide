# AWS PrivateLink quotas<a name="vpc-limits-endpoints"></a>

The following tables list the quotas, formerly referred to as limits, for AWS PrivateLink resources per Region for your account\. Unless indicated otherwise, you can [request an increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=vpc) for these quotas\.

If you request a quota increase that applies per resource, we increase the quota for all resources in the Region\.


| Resource | Default | Comments | 
| --- | --- | --- | 
| Gateway VPC endpoints per Region | 20 | You cannot have more than 255 gateway endpoints per VPC\. | 
| Interface and Gateway Load Balancer endpoints per VPC | 50 | This is the combined quota for the maximum number of interface endpoints and Gateway Load Balancer endpoints in a VPC\. To increase this quota, contact AWS Support\. | 
|  VPC endpoint policy size  | 20,480 characters \(including white space\) | This quota cannot be increased\. | 

The following maximum transmission unit \(MTU\) rules apply to traffic that passes through a VPC endpoint\.
+ The maximum transmission unit \(MTU\) of a network connection is the size, in bytes, of the largest permissible packet that can be passed through the VPC endpoint\. The larger the MTU, the more data that can be passed in a single packet\. A VPC endpoint supports an MTU of 8500 bytes\.
+ Packets with a size larger than 8500 bytes that arrive at the VPC endpoint are dropped\.
+ The VPC endpoint does not generate the FRAG\_NEEDEDICMP packet, so Path MTU Discovery \(PMTUD\) is not supported\.
+ The VPC endpoint enforces Maximum Segment Size \(MSS\) clamping for all packets\. For more information, see [RFC879](https://tools.ietf.org/html/rfc879)\.