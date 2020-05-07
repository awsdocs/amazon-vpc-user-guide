# Removing an endpoint service private DNS name<a name="remove-vpc-endpoint-service-dns-name"></a>

You can remove the endpoint service private DNS name only after there are no connections to the service\.

------
#### [ Console ]

**To remove an endpoint service private DNS name using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**\.

1. Select the endpoint service, and then choose **Actions**, **Modify private DNS name**\. 

1. Clear **Enable private DNS name**, and then clear the **Private DNS name**\.

1. Choose **Modify**\.

------
#### [ AWS CLI ]

Use [modify\-vpc\-endpoint\-service\-configuration](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-configuration.html)\.

------
#### [ API ]

Use [ModifyVpcEndpointServiceConfiguration](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyVpcEndpointServiceConfiguration.html)\.

------