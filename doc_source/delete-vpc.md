# Delete your VPC<a name="delete-vpc"></a>

When you are finished with a VPC, you can delete it\.

**Requirement**  
Before you can delete a VPC, you must first terminate or delete any resources that created a [requester\-managed network interface](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/requester-managed-eni.html) in the VPC\. For example, you must terminate your EC2 instances and delete your load balancers, NAT gateways, transit gateways, and interface VPC endpoints\.

**Topics**
+ [Delete a VPC using the console](#delete-vpc-console)
+ [Delete a VPC using the command line](#delete-vpc-cli)

## Delete a VPC using the console<a name="delete-vpc-console"></a>

If you delete a VPC using the Amazon VPC console, we also delete the following VPC components for you:
+ DHCP options
+ Egress\-only internet gateways
+ Gateway endpoints
+ Internet gateways
+ Network ACLs
+ Route tables
+ Security groups
+ Subnets

**To delete your VPC using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Terminate all instances in the VPC\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC to delete and choose **Actions**, **Delete VPC**\.

1. If there are resources that you must delete or terminate before we can delete the VPC, we display them\. Delete or terminate these resources and then try again\. Otherwise, we display the resources that we will delete in addition to the VPC\. Review the list and then proceed to the next step\.

1. \(Optional\) If you have a Site\-to\-Site VPN connection, you can select the option to delete it\. If you plan to use the customer gateway with another VPC, we recommend that you keep the Site\-to\-Site VPN connection and the gateways\. Otherwise, you must configure your customer gateway device again after you create a new Site\-to\-Site VPN connection\.

1. When prompted for confirmation, enter **delete** and then choose **Delete**\.

## Delete a VPC using the command line<a name="delete-vpc-cli"></a>

Before you can delete a VPC using the command line, you must terminate or delete any resources that created a requester\-managed network interface in the VPC, plus you must delete or detach all VPC resources that you created, such as subnets, security groups, network ACLs, route tables, internet gateways, and egress\-only internet gateways\. You do not need to delete the default security group, default route table, and default network ACL\.

The following procedure demonstrates the commands that you need to delete common VPC resources, and then delete your VPC\. You must use these commands in this order\. If you created additional VPC resources, you'll also need to use its corresponding delete command before you can delete the VPC\.

**To delete a VPC using the AWS CLI**

1. Delete your security group using the [delete\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-security-group.html) command:

   ```
   aws ec2 delete-security-group --group-id sg-id
   ```

1. Delete each network ACL using the [delete\-network\-acl](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-network-acl.html) command:

   ```
   aws ec2 delete-network-acl --network-acl-id acl-id
   ```

1. Delete each subnet using the [delete\-subnet](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-subnet.html) command:

   ```
   aws ec2 delete-subnet --subnet-id subnet-id
   ```

1. Delete each custom route table using the [delete\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route-table.html) command:

   ```
   aws ec2 delete-route-table --route-table-id rtb-id
   ```

1. Detach your internet gateway from your VPC using the [detach\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/detach-internet-gateway.html) command:

   ```
   aws ec2 detach-internet-gateway --internet-gateway-id igw-id --vpc-id vpc-id
   ```

1. Delete your internet gateway using the [delete\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-internet-gateway.html) command:

   ```
   aws ec2 delete-internet-gateway --internet-gateway-id igw-id
   ```

1. \[Dual stack VPC\] Delete your egress\-only internet gateway using the [delete\-egress\-only\-internet\-gateway](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-egress-only-internet-gateway.html) command:

   ```
   aws ec2 delete-egress-only-internet-gateway --egress-only-internet-gateway-id eigw-id
   ```

1. Delete your VPC using the [delete\-vpc](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpc.html) command:

   ```
   aws ec2 delete-vpc --vpc-id vpc-id
   ```