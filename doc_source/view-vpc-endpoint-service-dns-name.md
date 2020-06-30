# Viewing endpoint service private DNS name configuration<a name="view-vpc-endpoint-service-dns-name"></a>

You can view the endpoint service private DNS name for an endpoint service\.

------
#### [ Console ]

**To view an endpoint service private DNS name configuration using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**, and then select the endpoint service\.

1. The **Details** tab displays the following information for the private DNS domain ownership check:
   + **Domain verification status**: The verification status\.
   + **Domain verification type**: The verification type\.
   + **Domain verification value**: The DNS value\.
   + **Domain verification name**: The name of the record subdomain\.

------
#### [ AWS CLI ]

Use [describe\-vpc\-endpoint\-service\-configurations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-service-configurations.html)\.

------
#### [ API ]

Use [DescribeVpcEndpointServiceConfigurations](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcEndpointServiceConfigurations.html)\.

------