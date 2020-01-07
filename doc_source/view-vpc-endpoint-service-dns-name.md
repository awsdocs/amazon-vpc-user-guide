# Viewing Endpoint Service Private DNS Name Configuration<a name="view-vpc-endpoint-service-dns-name"></a>

You can modify the endpoint service private DNS name for a new or existing endpoint service\.

------
#### [ Console ]

**To view an endpoint service private DNS name configuration using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**\.

1. Select the endpoint service and then choose **Actions**, **Modify private DNS name**\. 

1. The **Details** tab displays the following information for the private DNS domain ownership check:
   + **Domain verification status**: The verification status\.
   + **Domain verification type**: The verification type\.
   + **Domain verification value**: The DNS value\.
   + **Domain verification name**: The name of the record subdomain\.

------
#### [ AWS CLI ]

Use [describe\-vpc\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoints.html)\.

------
#### [ API ]

Use [DescribeVpcEndpoints](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcEndpoints.html)\.

------