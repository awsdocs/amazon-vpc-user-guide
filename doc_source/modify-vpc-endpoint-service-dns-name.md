# Modifying an existing endpoint service private DNS name<a name="modify-vpc-endpoint-service-dns-name"></a>

You can modify the endpoint service private DNS name for a new or existing endpoint service\.

**To modify an endpoint service private DNS name using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**\.

1. Select the endpoint service, and then choose **Actions**, **Modify private DNS name**\. 

1. Select **Enable private DNS name**, and then for **Private DNS name**, enter the private DNS name\.

1. Choose **Modify**\.

After you update the name, update the entry for the domain on your DNS server\. We automatically poll the DNS server to verify that the record exists on the server\. DNS record updates can take up to 48 hours to take effect, but they often take effect much sooner\. For more information, see [Amazon VPC private DNS name domain verification TXT records](dns-txt-records.md) and [VPC endpoint service private DNS name verification](endpoint-services-dns-validation.md)\.

**To modify the endpoint service private DNS name using the AWS CLI or API**
+ [modify\-vpc\-endpoint\-service\-configuration](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-configuration.html)
+ [ModifyVpcEndpointServiceConfiguration](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyVpcEndpointServiceConfiguration.html)