# VPC endpoint service private DNS name verification<a name="endpoint-services-dns-validation"></a>

Your domain is associated with a set of Domain Name System \(DNS\) records that you manage through your DNS provider\. A TXT record is a type of DNS record that provides additional information about your domain\. Each TXT record consists of a name and a value\.

When you initiate domain ownership verification using the Amazon VPC Console or API, we give you the name and value to use for the TXT record\. For example, if your domain is myexampleservice\.com, the TXT record settings that we generate will look similar to the following example:


**Endpoint private DNS name TXT record**  

| Domain verification name | Type | Domain verification value | 
| --- | --- | --- | 
|  \_vpce:aksldja21i1  |  TXT  |  vpce:asjdakjshd78126eu21  | 

Add a TXT record to your domain's DNS server using the specified **Domain verification name** and **Domain verification value**\. The domain ownership verification is complete when we detect the existence of the TXT record in your domain's DNS settings\.

If your DNS provider does not allow DNS record names to contain underscores, you can omit *\_aksldja21i1* from the **Domain verification name**\. In that case, for the preceding example, the TXT record name would be myexampleservice\.com instead of *\_vpce:aksldja21i1\.myexampleservice\.com*\.

## Adding a TXT record to your domain's DNS server<a name="add-dns-txt-record"></a>

The procedure for adding TXT records to your domain's DNS server depends on who provides your DNS service\. Your DNS provider might be Amazon Route 53 or another domain name registrar\. This section provides procedures for adding a TXT record to Route 53, and generic procedures that apply to other DNS providers\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Endpoint Services**\.

1. Select the endpoint service\.

1. On the **Details** tab, note the values shown next to **Domain verification value** and **Domain verification name**\.

1. If Route 53 provides the DNS service for the domain that you're verifying, and you're signed in to the AWS Management Console under the same account that you use for Route 53, we give you the option of updating your DNS server immediately from within the Amazon VPC console\.

   If you use a different DNS provider, the procedures for updating the DNS records vary depending on which DNS or web hosting provider you use\. The following table lists links to the documentation for several common providers\. This list isn't exhaustive and inclusion in this list isn’t an endorsement or recommendation of any company’s products or services\. If your provider isn't listed in the table, you can probably use the domain with endpoints\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/vpc/latest/userguide/endpoint-services-dns-validation.html)

   When verification is complete, the domain's status in the Amazon VPC console changes from **Pending** to **Verified**\.

1. You can now use the private domain name for the VPC endpoint service\.

If the DNS settings are not correctly updated, the domain status displays a status of **failed** on the **Details** tab\. If this happens, complete the steps on the troubleshooting page at [Troubleshooting common private DNS domain verification problems](domain-verification-problems.md)\. After you verify that your TXT record was created correctly, retry the operation\.