# AWS IP address ranges<a name="aws-ip-ranges"></a>

AWS publishes its current IP address ranges in JSON format\. With this information, you can identify traffic from AWS\. You can also allow or deny traffic to or from specific types of AWS resources\.

To view the current ranges, download the `.json` file\. To maintain history, save successive versions of the `.json` file on your system\. To determine whether there have been changes since the last time that you saved the file, check the publication time in the current file and compare it to the publication time in the last file that you saved\.

The IP address ranges that you bring to AWS through bring your own IP addresses \(BYOIP\) are not included in the `.json` file\.

Alternatively, some services publish their address ranges using AWS\-managed prefix lists\. For more information, see [Available AWS\-managed prefix lists](working-with-aws-managed-prefix-lists.md#available-aws-managed-prefix-lists)\.

**Topics**
+ [Download](#aws-ip-download)
+ [Syntax](#aws-ip-syntax)
+ [Filtering the JSON file](#filter-json-file)
+ [Implementing egress control](#aws-ip-egress-control)
+ [AWS IP address ranges notifications](#subscribe-notifications)
+ [Release notes](#aws-ip-release-notes)
+ [Learn more](#aws-ip-learn-more)

## Download<a name="aws-ip-download"></a>

Download [ip\-ranges\.json](https://ip-ranges.amazonaws.com/ip-ranges.json)\.

If you access this file programmatically, it is your responsibility to ensure that the application downloads the file only after successfully verifying the TLS certificate presented by the server\.

## Syntax<a name="aws-ip-syntax"></a>

The syntax of `ip-ranges.json` is as follows\.

```
{
  "syncToken": "0123456789",
  "createDate": "yyyy-mm-dd-hh-mm-ss",
  "prefixes": [
    {
      "ip_prefix": "cidr",
      "region": "region",
      "network_border_group": "network_border_group",
      "service": "subset"
    }
  ],
  "ipv6_prefixes": [
    {
      "ipv6_prefix": "cidr",
      "region": "region",
      "network_border_group": "network_border_group",
      "service": "subset"
    }
  ]  
}
```

**syncToken**  
The publication time, in Unix epoch time format\.  
Type: String  
Example: `"syncToken": "1416435608"`

**createDate**  
The publication date and time, in UTC YY\-MM\-DD\-hh\-mm\-ss format\.  
Type: String  
Example: `"createDate": "2014-11-19-23-29-02"`

**prefixes**  
The IP prefixes for the IPv4 address ranges\.  
Type: Array

**ipv6\_prefixes**  
The IP prefixes for the IPv6 address ranges\.  
Type: Array

**ip\_prefix**  
The public IPv4 address range, in CIDR notation\. Note that AWS may advertise a prefix in more specific ranges\. For example, prefix 96\.127\.0\.0/17 in the file may be advertised as 96\.127\.0\.0/21, 96\.127\.8\.0/21, 96\.127\.32\.0/19, and 96\.127\.64\.0/18\.  
Type: String  
Example: `"ip_prefix": "198.51.100.2/24"`

**ipv6\_prefix**  
The public IPv6 address range, in CIDR notation\. Note that AWS may advertise a prefix in more specific ranges\.  
Type: String  
Example: `"ipv6_prefix": "2001:db8:1234::/64"`

**network\_border\_group**  
The name of the network border group, which is a unique set of Availability Zones or Local Zones from where AWS advertises IP addresses\.  
Type: String  
Example: `"network_border_group": "us-west-2-lax-1"`

**region**  
The AWS Region or `GLOBAL` for edge locations\. The `CLOUDFRONT` and `ROUTE53` ranges are `GLOBAL`\.  
Type: String  
Valid values: `af-south-1` \| `ap-east-1` \| `ap-northeast-1` \| `ap-northeast-2` \| `ap-northeast-3` \| `ap-south-1` \| `ap-south-2` \| `ap-southeast-1` \| `ap-southeast-2` \| `ap-southeast-3` \| `ap-southeast-4` \| `ca-central-1` \| `cn-north-1` \| `cn-northwest-1` \| `eu-central-1` \| `eu-central-2` \| `eu-north-1` \| `eu-south-1` \| `eu-south-2` \| `eu-west-1` \| `eu-west-2` \| `eu-west-3` \| `me-central-1` \| `me-south-1` \| `sa-east-1` \| `us-east-1` \| `us-east-2` \| `us-gov-east-1` \| `us-gov-west-1` \| `us-west-1` \| `us-west-2` \| `GLOBAL`  
Example: `"region": "us-east-1"`

**service**  
The subset of IP address ranges\. The addresses listed for `API_GATEWAY` are egress only\. Specify `AMAZON` to get all IP address ranges \(meaning that every subset is also in the `AMAZON` subset\)\. However, some IP address ranges are only in the `AMAZON` subset \(meaning that they are not also available in another subset\)\.  
Type: String  
Valid values: `AMAZON` \| `AMAZON_APPFLOW` \| `AMAZON_CONNECT` \| `API_GATEWAY` \| `CHIME_MEETINGS` \| `CHIME_VOICECONNECTOR` \| `CLOUD9` \| `CLOUDFRONT` \| `CLOUDFRONT_ORIGIN_FACING` \| `CODEBUILD` \| `DYNAMODB` \| `EBS` \| `EC2` \| `EC2_INSTANCE_CONNECT` \| `GLOBALACCELERATOR` \| `KINESIS_VIDEO_STREAMS` \| `ROUTE53` \| `ROUTE53_HEALTHCHECKS` \| `ROUTE53_HEALTHCHECKS_PUBLISHING` \| `ROUTE53_RESOLVER` \| `S3` \| `WORKSPACES_GATEWAYS`  
Example: `"service": "AMAZON"`

## Filtering the JSON file<a name="filter-json-file"></a>

You can download a command line tool to help you filter the information to just what you are looking for\.

### Windows<a name="Get-AWSPublicIpAddressRange-examples"></a>

The [AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/) includes a cmdlet, `Get-AWSPublicIpAddressRange`, to parse this JSON file\. The following examples demonstrate its use\. For more information, see [Querying the Public IP Address Ranges for AWS](https://aws.amazon.com/blogs/developer/querying-the-public-ip-address-ranges-for-aws/) and [Get\-AWSPublicIpAddressRange](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-AWSPublicIpAddressRange.html)\.

**Example 1\. Get the creation date**  

```
PS C:\> Get-AWSPublicIpAddressRange -OutputPublicationDate

Wednesday, August 22, 2018 9:22:35 PM
```

**Example 2\. Get the information for a specific Region**  

```
PS C:\> Get-AWSPublicIpAddressRange -Region us-east-1

IpPrefix        Region      NetworkBorderGroup         Service
--------        ------       -------                   -------
23.20.0.0/14    us-east-1    us-east-1                 AMAZON
50.16.0.0/15    us-east-1    us-east-1                 AMAZON
50.19.0.0/16    us-east-1    us-east-1                 AMAZON
...
```

**Example 3\. Get all IP addresses**  

```
PS C:\> (Get-AWSPublicIpAddressRange).IpPrefix
23.20.0.0/14
27.0.0.0/22
43.250.192.0/24
...
2406:da00:ff00::/64
2600:1fff:6000::/40
2a01:578:3::/64
2600:9000::/28
```

**Example 4\. Get all IPv4 addresses**  

```
PS C:\> Get-AWSPublicIpAddressRange | where {$_.IpAddressFormat -eq "Ipv4"} | select IpPrefix

IpPrefix
--------
23.20.0.0/14
27.0.0.0/22
43.250.192.0/24
...
```

**Example 5\. Get all IPv6 addresses**  

```
PS C:\> Get-AWSPublicIpAddressRange | where {$_.IpAddressFormat -eq "Ipv6"} | select IpPrefix

IpPrefix
--------
2a05:d07c:2000::/40
2a05:d000:8000::/40
2406:dafe:2000::/40
...
```

**Example 6\. Get all IP addresses for a specific service**  

```
PS C:\> Get-AWSPublicIpAddressRange -ServiceKey CODEBUILD | select IpPrefix

IpPrefix
--------
52.47.73.72/29
13.55.255.216/29
52.15.247.208/29
...
```

### Linux<a name="jq-examples"></a>

The following example commands use [the jq tool](https://stedolan.github.io/jq/) to parse a local copy of the JSON file\.

**Example 1\. Get the creation date**  

```
$ jq .createDate < ip-ranges.json

"2016-02-18-17-22-15"
```

**Example 2\. Get the information for a specific Region**  

```
$ jq '.prefixes[] | select(.region=="us-east-1")' < ip-ranges.json

{
  "ip_prefix": "23.20.0.0/14",
  "region": "us-east-1",
  "network_border_group": "us-east-1",
  "service": "AMAZON"
},
{
  "ip_prefix": "50.16.0.0/15",
  "region": "us-east-1",
  "network_border_group": "us-east-1",
  "service": "AMAZON"
},
{
  "ip_prefix": "50.19.0.0/16",
  "region": "us-east-1",
  "network_border_group": "us-east-1",
  "service": "AMAZON"
},
...
```

**Example 3\. Get all IPv4 addresses**  

```
$ jq -r '.prefixes | .[].ip_prefix' < ip-ranges.json

23.20.0.0/14
27.0.0.0/22
43.250.192.0/24
...
```

**Example 4\. Get all IPv6 addresses**  

```
$ jq -r '.ipv6_prefixes | .[].ipv6_prefix' < ip-ranges.json

2a05:d07c:2000::/40
2a05:d000:8000::/40
2406:dafe:2000::/40
...
```

**Example 5\. Get all IPv4 addresses for a specific service**  

```
$ jq -r '.prefixes[] | select(.service=="CODEBUILD") | .ip_prefix' < ip-ranges.json

52.47.73.72/29
13.55.255.216/29
52.15.247.208/29
...
```

**Example 6\. Get all IPv4 addresses for a specific service in a specific Region**  

```
$ jq -r '.prefixes[] | select(.region=="us-east-1") | select(.service=="CODEBUILD") | .ip_prefix' < ip-ranges.json

34.228.4.208/28
```

**Example 7\. Get information for a certain network border group**  

```
$ jq -r '.prefixes[] | select(.region=="us-west-2") | select(.network_border_group=="us-west-2-lax-1") | .ip_prefix' < ip-ranges.json
70.224.192.0/18
52.95.230.0/24
15.253.0.0/16
...
```

## Implementing egress control<a name="aws-ip-egress-control"></a>

To allow an instance to access only AWS services, create security groups with rules that allow outbound traffic to the CIDR blocks in the `AMAZON` list, minus the CIDR blocks that are also in the `EC2` list\. IP addresses in the `EC2` list can be assigned to EC2 instances\.

There are [quotas for security groups](amazon-vpc-limits.md#vpc-limits-security-groups)\. Depending on the number of IP address ranges in each Region, you might need multiple security groups per Region\.

### Windows PowerShell<a name="filter-addresses-powershell"></a>

The following PowerShell example shows you how to get the IP addresses that are in the `AMAZON` list but not the `EC2` list\. Copy the script and save it in a file named `Select_address.ps1`\.

```
$amazon_addresses = Get-AWSPublicIpAddressRange -ServiceKey amazon
$ec2_addresses = Get-AWSPublicIpAddressRange -ServiceKey ec2

ForEach ($address in $amazon_addresses)
{
    if( $ec2_addresses.IpPrefix -notcontains $address.IpPrefix)
    {
       ($address).IpPrefix 
    }
}
```

You can run this script as follows:

```
PS C:\> .\Select_address.ps1
13.32.0.0/15
13.35.0.0/16
13.248.0.0/20
13.248.16.0/21
13.248.24.0/22
13.248.28.0/22
27.0.0.0/22
43.250.192.0/24
43.250.193.0/24
...
```

### jq<a name="filter-addresses-bash-jq"></a>

The following example shows you how to get the IP addresses that are in the `AMAZON` list but not the `EC2` list, for all Regions:

```
jq -r '[.prefixes[] | select(.service=="AMAZON").ip_prefix] - [.prefixes[] | select(.service=="EC2").ip_prefix] | .[]' < ip-ranges.json

52.94.22.0/24
52.94.17.0/24
52.95.154.0/23
52.95.212.0/22
54.239.0.240/28
54.239.54.0/23
52.119.224.0/21
...
```

The following example shows you how to filter the results to one Region:

```
jq -r '[.prefixes[] | select(.region=="us-east-1" and .service=="AMAZON").ip_prefix] - [.prefixes[] | select(.region=="us-east-1" and .service=="EC2").ip_prefix] | .[]' < ip-ranges.json
```

### Python<a name="filter-addresses-python"></a>

The following python script shows you how to get the IP addresses that are in the `AMAZON` list but not the `EC2` list\. Copy the script and save it in a file named `get_ips.py`\.

```
#!/usr/bin/env python
import requests

ip_ranges = requests.get('https://ip-ranges.amazonaws.com/ip-ranges.json').json()['prefixes']
amazon_ips = [item['ip_prefix'] for item in ip_ranges if item["service"] == "AMAZON"]
ec2_ips = [item['ip_prefix'] for item in ip_ranges if item["service"] == "EC2"]

amazon_ips_less_ec2=[]
     
for ip in amazon_ips:
    if ip not in ec2_ips:
        amazon_ips_less_ec2.append(ip)

for ip in amazon_ips_less_ec2: print(str(ip))
```

You can run this script as follows:

```
$ python ./get_ips.py
13.32.0.0/15
13.35.0.0/16
13.248.0.0/20
13.248.16.0/21
13.248.24.0/22
13.248.28.0/22
27.0.0.0/22
43.250.192.0/24
43.250.193.0/24
...
```

## AWS IP address ranges notifications<a name="subscribe-notifications"></a>

Whenever there is a change to the AWS IP address ranges, we send notifications to subscribers of the `AmazonIpSpaceChanged` topic\. The payload contains information in the following format:

```
{
  "create-time":"yyyy-mm-ddThh:mm:ss+00:00",
  "synctoken":"0123456789",
  "md5":"6a45316e8bc9463c9e926d5d37836d33",
  "url":"https://ip-ranges.amazonaws.com/ip-ranges.json"
}
```

**create\-time**  
The creation date and time\.  
Notifications could be delivered out of order\. Therefore, we recommend that you check the timestamps to ensure the correct order\.

**synctoken**  
The publication time, in Unix epoch time format\.

**md5**  
The cryptographic hash value of the `ip-ranges.json` file\. You can use this value to check whether the downloaded file is corrupted\.

**url**  
The location of the `ip-ranges.json` file\.

If you want to be notified whenever there is a change to the AWS IP address ranges, you can subscribe as follows to receive notifications using Amazon SNS\.

**To subscribe to AWS IP address range notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation bar, change the Region to **US East \(N\. Virginia\)**, if necessary\. You must select this Region because the SNS notifications that you are subscribing to were created in this Region\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. In the **Create subscription** dialog box, do the following:

   1. For **Topic ARN**, copy the following Amazon Resource Name \(ARN\):

      ```
      arn:aws:sns:us-east-1:806199016981:AmazonIpSpaceChanged
      ```

   1. For **Protocol**, choose the protocol to use \(for example, `Email`\)\.

   1. For **Endpoint**, type the endpoint to receive the notification \(for example, your email address\)\.

   1. Choose **Create subscription**\.

1. You'll be contacted on the endpoint that you specified and asked to confirm your subscription\. For example, if you specified an email address, you'll receive an email message with the subject line `AWS Notification - Subscription Confirmation`\. Follow the directions to confirm your subscription\.

Notifications are subject to the availability of the endpoint\. Therefore, you might want to check the JSON file periodically to ensure that you've got the latest ranges\. For more information about Amazon SNS reliability, see [https://aws.amazon.com/sns/faqs/#Reliability](https://aws.amazon.com/sns/faqs/#Reliability)\.

If you no longer want to receive these notifications, use the following procedure to unsubscribe\.

**To unsubscribe from AWS IP address ranges notifications**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation pane, choose **Subscriptions**\.

1. Select the check box for the subscription\.

1. Choose **Actions**, **Delete subscriptions**\.

1. When prompted for confirmation, choose **Delete**\.

For more information about Amazon SNS, see the *[Amazon Simple Notification Service Developer Guide](https://docs.aws.amazon.com/sns/latest/dg/)*\.

## Release notes<a name="aws-ip-release-notes"></a>

The following table describes updates to the syntax of `ip-ranges.json`\. We also add new Region codes with each Region launch\.


| Description | Release date | 
| --- | --- | 
| Added the CLOUDFRONT\_ORIGIN\_FACING service code\. | October 12, 2021 | 
| Added the ROUTE53\_RESOLVER service code\. | June 24, 2021 | 
| Added the EBS service code\. | May 12, 2021 | 
| Added the KINESIS\_VIDEO\_STREAMS service code\. | November 19, 2020 | 
| Added the CHIME\_MEETINGS and CHIME\_VOICECONNECTOR service codes\. | June 19, 2020 | 
| Added the AMAZON\_APPFLOW service code\. | June 9, 2020 | 
| Add support for the network border group\. | April 7, 2020 | 
| Added the WORKSPACES\_GATEWAYS service code\. | March 30, 2020 | 
| Added the ROUTE53\_HEALTHCHECK\_PUBLISHING service code\. | January 30, 2020 | 
| Added the API\_GATEWAY service code\. | September 26, 2019 | 
| Added the EC2\_INSTANCE\_CONNECT service code\. | June 26, 2019 | 
| Added the DYNAMODB service code\. | April 25, 2019 | 
| Added the GLOBALACCELERATOR service code\. | December 20, 2018 | 
| Added the AMAZON\_CONNECT service code\. | June 20, 2018 | 
| Added the CLOUD9 service code\. | June 20, 2018 | 
| Added the CODEBUILD service code\. | April 19, 2018 | 
| Added the S3 service code\. | February 28, 2017 | 
| Added support for IPv6 address ranges\. | August 22, 2016 | 
| Initial release | November 19, 2014 | 

## Learn more<a name="aws-ip-learn-more"></a>
+ `AMAZON_APPFLOW` – [IP address ranges](https://docs.aws.amazon.com/appflow/latest/userguide/requirements.html#general)
+ `AMAZON_CONNECT` – [Set up your network](https://docs.aws.amazon.com/connect/latest/adminguide/ccp-networking.html)
+ `CLOUDFRONT` – [Locations and IP address ranges of CloudFront edge servers](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/LocationsOfEdgeServers.html)
+ `DYNAMODB` – [IP address ranges](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Using.IPRanges.html)
+ `EC2` – [Public IPV4 addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html#concepts-public-addresses)
+ `EC2_INSTANCE_CONNECT` – [Set up EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-set-up.html)
+ `GLOBALACCELERATOR` – [Location and IP address ranges of Global Accelerator edge servers](https://docs.aws.amazon.com/global-accelerator/latest/dg/introduction-ip-ranges.html)
+ `ROUTE53` – [IP address ranges of Amazon Route 53 servers](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-ip-addresses.html)
+ `ROUTE53_HEALTHCHECKS` – [IP address ranges of Amazon Route 53 servers](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-ip-addresses.html)
+ `ROUTE53_HEALTHCHECKS_PUBLISHING` – [IP address ranges of Amazon Route 53 servers](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/route-53-ip-addresses.html)
+ `WORKSPACES_GATEWAYS` – [PCoIP gateway servers](https://docs.aws.amazon.com/workspaces/latest/adminguide/workspaces-port-requirements.html#gateway_IP)