# Deleting an endpoint service configuration<a name="delete-endpoint-service"></a>

You can delete an endpoint service configuration\. Deleting the configuration does not delete the application hosted in your VPC or the associated load balancers\. 

Before you delete the endpoint service configuration, you must reject any `available` or `pending-acceptance` VPC endpoints that are attached to the service\. For more information, see [Accepting and rejecting interface endpoint connection requests](accept-reject-endpoint-requests.md)\.

------
#### [ Console ]

**To delete an endpoint service configuration using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select the service\.

1. Choose **Actions**, **Delete**\.

1. Choose **Yes, Delete**\.

------
#### [ AWS CLI ]

**To delete an endpoint service configuration using the AWS CLI**
+ Use the [delete\-vpc\-endpoint\-service\-configurations](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc-endpoint-service-configurations.html) command and specify the ID of the service\. 

  ```
  aws ec2 delete-vpc-endpoint-service-configurations --service-ids vpce-svc-03d5ebb7d9579a2b3
  ```

------
#### [  AWS Tools for Windows PowerShell ]

Use [Remove\-EC2EndpointServiceConfiguration](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2EndpointServiceConfiguration.html)\.

------
#### [ API ]

Use [DeleteVpcEndpointServiceConfigurations](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteVpcEndpointServiceConfigurations.html)\.

------