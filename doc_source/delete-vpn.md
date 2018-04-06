# Deleting a VPN Connection<a name="delete-vpn"></a>

If you no longer need a VPN connection, you can delete it\.

**Important**  
If you delete your VPN connection and then create a new one, you have to download new configuration information and have your network administrator reconfigure the customer gateway\.

**To delete a VPN connection using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **VPN Connections**\.

1. Select the VPN connection and choose **Actions**, **Delete**\.

1. Choose **Delete**\.

If you no longer require a customer gateway, you can delete it\. You can't delete a customer gateway that's being used in a VPN connection\.

**To delete a customer gateway using the console**

1. In the navigation pane, choose **Customer Gateways**\.

1. Select the customer gateway to delete and choose **Actions**, **Delete Customer Gateway**\.

1. Choose **Yes, Delete**\.

If you no longer require a virtual private gateway for your VPC, you can detach it\.

**To detach a virtual private gateway using the console**

1. In the navigation pane, choose **Virtual Private Gateways**\.

1. Select the virtual private gateway and choose **Actions**, **Detach from VPC**\.

1. Choose **Yes, Detach**\.

If you no longer require a detached virtual private gateway, you can delete it\. You can't delete a virtual private gateway that's still attached to a VPC\.

**To delete a virtual private gateway using the console**

1. In the navigation pane, choose **Virtual Private Gateways**\.

1. Select the virtual private gateway to delete and choose **Actions**, **Delete Virtual Private Gateway**\.

1. Choose **Yes, Delete**\.

**To delete a VPN connection using the command line or API**
+ [DeleteVpnConnection](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteVpnConnection.html) \(Amazon EC2 Query API\)
+ [delete\-vpn\-connection](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpn-connection.html) \(AWS CLI\)
+ [Remove\-EC2VpnConnection](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2VpnConnection.html) \(AWS Tools for Windows PowerShell\)

**To delete a customer gateway using the command line or API**
+ [DeleteCustomerGateway](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteCustomerGateway.html) \(Amazon EC2 Query API\)
+ [delete\-customer\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-customer-gateway.html) \(AWS CLI\)
+ [Remove\-EC2CustomerGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2CustomerGateway.html) \(AWS Tools for Windows PowerShell\)

**To detach a virtual private gateway using the command line or API**
+ [DetachVpnGateway](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DetachVpnGateway.html) \(Amazon EC2 Query API\)
+ [detach\-vpn\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/detach-vpn-gateway.html) \(AWS CLI\)
+ [Dismount\-EC2VpnGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2VpnGateway.html) \(AWS Tools for Windows PowerShell\)

**To delete a virtual private gateway using the command line or API**
+ [DeleteVpnGateway](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DeleteVpnGateway.html) \(Amazon EC2 Query API\)
+ [delete\-vpn\-gateway](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpn-gateway.html) \(AWS CLI\)
+ [Remove\-EC2VpnGateway](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2VpnGateway.html) \(AWS Tools for Windows PowerShell\)