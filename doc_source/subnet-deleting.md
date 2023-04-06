# Delete a subnet<a name="subnet-deleting"></a>

If you no longer need a subnet, you can delete it\. You cannot delete a subnet if it contains any network interfaces\. For example, you must terminate any instances in a subnet before you can delete it\.

**To delete a subnet using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Terminate all instances in the subnet\. For more information, see [Terminate your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide*\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the subnet and choose **Actions**, **Delete subnet**\.

1. When prompted for confirmation, type **delete** and then choose **Delete**\.

**To delete a subnet using the AWS CLI**  
Use the [delete\-subnet](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-subnet.html) command\.