# Manually initiating the endpoint service private DNS name domain verification<a name="verify-vpc-endpoint-service-dns-name"></a>

The service provider must prove that they own the private DNS name domain before consumers can use the private DNS name\.

------
#### [ Console ]

**To initiate the verification process of the private DNS name domain using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**\.

1. Select the endpoint service, and then choose **Actions**, **Verify domain owernship for Private DNS Name**\. 

1. Choose **Verify**\.

   If the DNS settings are not correctly updated, the domain will display a status of **failed** on the **Details** tab\. If this happens, complete the steps on the troubleshooting page at [Troubleshooting common private DNS domain verification problems](domain-verification-problems.md)\. 

------
#### [ AWS CLI ]

Use [start\-vpc\-endpoint\-service\-private\-dns\-verification](https://docs.aws.amazon.com/cli/latest/reference/ec2/startVpcEndpointServicePrivateDnsVerification.html)\.

------
#### [ API ]

Use [StartVpcEndpointServicePrivateDnsVerification](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_StartVpcEndpointServicePrivateDnsVerification.html)\.

------