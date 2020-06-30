# Private DNS names for endpoint services<a name="verify-domains"></a>

When you create a VPC endpoint service, AWS generates endpoint\-specific DNS hostnames that you can use to communicate with the service\. These names include the VPC endpoint ID, the Availability Zone name and Region Name, for example, vpce\-1234\-abcdev\-us\-east\-1\.vpce\-svc\-123345\.us\-east\-1\.vpce\.amazonaws\.com\. By default, your consumers access the service with that DNS name and usually need to modify the application configuration\.

If the endpoint service is for an AWS service, or a service available in the AWS Marketplace, there is a default DNS name\. For other services, the service provider can configure a private DNS name so consumers can access the service using an existing DNS name without making changes to their applications\. For more information, see [VPC endpoint services \(AWS PrivateLink\)](endpoint-service.md)\.

Service providers can specify a private DNS name for a new endpoint service, or an existing endpoint service\. To use a private DNS name, enable the feature, and then specify a private DNS name\. Before consumers can use the private DNS name, you must verify that you have control of the domain/subdomain\. You can initiate domain ownership verification using the Amazon VPC Console or API\. After the domain ownership verification completes, consumers access the endpoint by using the private DNS name\.

**Note**  
In order to verify the domain, you need to have a public hosted name, or a public DNS provider\.

The high\-level procedure is as follows:

1. Add a private DNS name\. For more information, see [Creating a VPC endpoint service configuration](create-endpoint-service.md) or [Modifying an existing endpoint service private DNS name](modify-vpc-endpoint-service-dns-name.md)\.

1. Note the **Domain verification value** and **Domain verification name** that you need for the DNS server records\. For more information, see [Viewing endpoint service private DNS name configuration](view-vpc-endpoint-service-dns-name.md)\.

1. Add a record to the DNS server\. For more information, see [VPC endpoint service private DNS name verification](endpoint-services-dns-validation.md)\.

1. Verify the private DNS name\. For more information, see [Manually initiating the endpoint service private DNS name domain verification](verify-vpc-endpoint-service-dns-name.md)\.

You can manage the verification process by using the Amazon VPC console or the Amazon VPC API\.
+ [VPC endpoint service private DNS name verification](endpoint-services-dns-validation.md)
+ [Modifying an existing endpoint service private DNS name](modify-vpc-endpoint-service-dns-name.md)
+ [Removing an endpoint service private DNS name](remove-vpc-endpoint-service-dns-name.md)
+ [Viewing endpoint service private DNS name configuration](view-vpc-endpoint-service-dns-name.md)
+ [Amazon VPC private DNS name domain verification TXT records](dns-txt-records.md)

## Domain name verification considerations<a name="considerations"></a>

Make note of the following important points about domain ownership verification:
+ A consumer can only use the private DNS name to access the endpoint service when the verification status is **verified**\.
+ If the verification status changes from **verified** to **pendingVerification**, or **failed**, existing consumer connections remain, but any new connection requests are denied\.
**Important**  
For service providers who are concerned about connections to endpoint services that are no longer in the **verified** state, we recommend that you use [DescribeVpcEndpoints](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcEndpoints.html) to periodically check the verification state\. We recommend that you perform this check at least one time per day\.
+ An endpoint service can only have one private DNS name\.
+ You can specify a private DNS name for a new endpoint service, or an existing endpoint service\.
+ You can only use public domain name servers\.
+ You can use wildcards in domain names, for example, "\*\.myexampleservice\.com"\.
+ You must perform a separate domain ownership verification check for each endpoint service\. 
+ You can verify the domain of a subdomain\. For example, you can verify *example\.com*, instead of *a\.example\.com*\. As specified in [RFC 1034](https://tools.ietf.org/html/rfc1034#section-3.6), each DNS label can have up to 63 characters and the whole domain name must not exceed a total length of 255 characters\. 

  If you add an additional subdomain, you must verify the subdomain, or the domain\. For example, let's say you had *a\.example\.com*, and verified *example\.com*\. You now add *b\.example\.com* as a private DNS name\. You must verify *example\.com* or *b\.example\.com* before your consumers can use the name\.
+ Domain names must be lower\-cased\.