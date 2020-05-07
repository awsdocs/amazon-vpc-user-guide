# Adding or removing VPC endpoint service tags<a name="modify-tags-vpc-endpoint-service-tags"></a>

Tags provide a way to identify the VPC endpoint service\. You can add or remove a tag\.

------
#### [ Console ]

**To add or remove a VPC endpoint service tag**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services**\.

1. Select the VPC endpoint service and choose **Actions**, **Add/Edit Tags**\.

1. Add or remove a tag\.

   \[Add a tag\] Choose **Create tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

------
#### [  AWS Tools for Windows PowerShell ]

Use [CreateTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateTags.html) and [DeleteTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteTags.html)\.

To accept an endpoint connection request, use the [accept\-vpc\-endpoint\-connections](https://docs.aws.amazon.com/cli/latest/reference/ec2/accept-vpc-endpoint-connections.html) command and specify the endpoint ID and endpoint service ID\.

------
#### [ API ]

Use [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) and [delete\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html)\.

------