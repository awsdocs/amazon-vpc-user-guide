# Amazon VPC private DNS name domain verification TXT records<a name="dns-txt-records"></a>

Your domain is associated with a set of Domain Name System \(DNS\) records that you manage through your DNS provider\. A TXT record is a type of DNS record that provides additional information about your domain\. Each TXT record consists of a name and a value\.

When you initiate domain ownership verification using the Amazon VPC Console or API, we give you the name and value to use for the TXT record\. For example, if your domain is myexampleservice\.com, the TXT record settings that Amazon VPC generates will look similar to the following example:


**Endpoint private DNS name TXT record**  

| Domain verification name | Type | Domain verification value | 
| --- | --- | --- | 
|  \_vpce:aksldja21i1\.myexampleservice\.com  |  TXT  |  vpce:asjdakjshd78126eu21  | 

Add a TXT record to your domain's DNS server using the specified **Domain verification name** and **Domain verification value**\. Amazon VPC domain ownership verification is complete when Amazon VPC detects the existence of the TXT record in your domain's DNS settings\.

If your DNS provider does not allow DNS record names to contain underscores, you can use the domain name for the **Domain verification name**\. In that case, for the preceding example, the TXT record name would be myexampleservice\.com\. 

You can find troubleshooting information and instructions on how to check your domain ownership verification settings in [Troubleshooting common private DNS domain verification problems](domain-verification-problems.md)\.

------
#### [ Amazon Route 53 ]

The procedure for adding TXT records to your domain's DNS server depends on who provides your DNS service\. Your DNS provider might be Amazon Route 53 or another domain name registrar\. This section provides procedures for adding a TXT record to Route 53, and generic procedures that apply to other DNS providers\.

**To add a TXT record to the DNS record for your Route 53\-managed domain**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Endpoint Services**\.

1. Select the endpoint service\.

1. On the **Details** tab, note the values shown next to **Domain verification value** and **Domain verification name**\.

1. Open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.

1. In the navigation pane, choose **Hosted Zones**\.

1. Select the domain that you want to add a TXT record to, and then choose **Go to Record Sets**\.

1. Choose **Create Record Set**\.

1. In the **Create Record Set** pane, make the following selections:

   1. For **Name**, enter the endpoint service **Domain verification name** from the Amazon VPC console\.

   1. For **Type**, choose **TXT – Text**\.

   1. For **TTL \(Seconds\)**, enter **1800**\.

   1. For **Value**, enter the **Domain verification value** from the Amazon VPC console\.

   1. Choose **Create**\.

1. On the **Details** tab of the **Endpoint Services** page in the Amazon VPC console, check the value in the **Domain verification status** column next for the endpoint\. If the status is "pending verification," wait a few minutes, and then choose **refresh**\. Repeat this process until the value in the status column is "verified"\. You can manually start the verification process\. For more information, see [Manually initiating the endpoint service private DNS name domain verification](verify-vpc-endpoint-service-dns-name.md)\.

------
#### [ Generic procedures for other DNS providers ]

The procedures for adding TXT records to the DNS configurations vary from provider to provider\. For specific steps, consult your DNS provider's documentation\. The procedure in this section gives a basic overview of the steps you take when adding a TXT record to the DNS configuration for your domain\.

**To add a TXT record to your domain's DNS server \(general procedure\)**

1. Go to your DNS provider's website\. If you aren't sure which DNS provider serves your domain, you can look it up by using a free [ Whois service](http://www.google.com/search?aq=f&hl=en&q=whois+free&btnG=Search)\.

1. On the provider's website, sign in to your account\.

1. Find the page for updating your domain's DNS records\. This page often has a name such as DNS Records, DNS Zone File, or Advanced DNS\. If you're unsure, consult the provider's documentation\.

1. Add a TXT record with the name and value provided by AWS\.
**Important**  
Some DNS providers automatically append the domain name to the end of DNS records\. Adding a record that already contains the domain name \(such as *\_pmBGN/7Mjnf\.example\.com*\) might result in the duplication of the domain name \(such as *\_pmBGN/7Mjnfexample\.com\.example\.com*\)\. To avoid duplication of the domain name, add a period to the end of the domain name in the DNS record\. This will indicate to your DNS provider that the record name is fully qualified \(that is, no longer relative to the domain name\), and will prevent the DNS provider from appending an additional domain name\.

1. Save your changes\. DNS record updates can take up to 48 hours to take effect, but they often take effect much sooner\.

------