# Deleting a VPC endpoint<a name="delete-vpc-endpoint"></a>

If you no longer require an endpoint, you can delete it\. Deleting a gateway endpoint also deletes the endpoint routes in the route tables that were used by the endpoint, but doesn't affect any security groups associated with the VPC in which the endpoint resides\. Deleting an interface endpoint also deletes the endpoint network interfaces\.

**To delete an endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your endpoint\.

1. Choose **Actions**, **Delete Endpoint**\. 

1. In the confirmation screen, choose **Yes, Delete**\.

**To delete a VPC endpoint**
+ [delete\-vpc\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc-endpoints.html) \(AWS CLI\)
+ [Remove\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2VpcEndpoint.html) \(AWS Tools for Windows PowerShell\)
+ [DeleteVpcEndpoints](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteVpcEndpoints.html) \(Amazon EC2 Query API\)