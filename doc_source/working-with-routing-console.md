# Manage middlebox routes<a name="working-with-routing-console"></a>

The middlebox routing wizard is available in the Amazon Virtual Private Cloud Console\.

**Topics**
+ [Create routes using the middlebox routing wizard](#creating-routing-console)
+ [Modify middlebox routes](#modify-route)
+ [View the middlebox routing wizard route tables](#viewing-routing-console-routes)
+ [Delete the middlebox routing wizard configuration](#deleting-routing-console)

## Create routes using the middlebox routing wizard<a name="creating-routing-console"></a>

**To create routes using the middlebox routing wizard**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, and then choose **Actions**, **Manage middlebox routes**\.

1. Choose **Create routes**\.

1. On the **Specify routes** page, do the following:
   + For **Source**, choose the source for your traffic\. If you choose a virtual private gateway, for **Destination IPv4 CIDR**, enter the CIDR for the on\-premises traffic entering the VPC from the virtual private gateway\.
   + For **Middlebox**, choose the network interface ID that is associated with your middlebox appliance, or when you use a Gateway Load Balancer endpoint, choose the VPC endpoint ID\.
   + For **Destination subnet**, choose the destination subnet\.

1. \(Optional\) To add another destination subnet, choose **Add additional subnet**, and then do the following:
   + For **Middlebox**, choose the network interface ID that is associated with your middlebox appliance, or when you use a Gateway Load Balancer endpoint, choose the VPC endpoint ID\.

     You must use the same middlebox appliance for multiple subnets\.
   + For **Destination subnet**, choose the destination subnet\.

1. \(Optional\) To add another source, choose **Add source**, and then repeat the previous steps\.

1. Choose **Next**\.

1. On the **Review and create** page, verify the routes and then choose **Create routes**\.

## Modify middlebox routes<a name="modify-route"></a>

You can edit your route configuration by changing the gateway, the middlebox, or the destination subnet\.

When you make any modifications, the middlebox routing wizard automatically perform the following operations:
+ Creates new route tables for the gateway, middlebox, and destination subnet\.
+ Adds the necessary routes to the new route tables\.
+ Disassociates the current route tables that the middlebox routing wizard associated with the resources\.
+ Associates the new route tables that the middlebox routing wizard creates with the resources\.

**To modify middlebox routes using the middlebox routing wizard**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, and then choose **Actions**, **Manage middlebox routes**\.

1. Choose **Edit routes**\.

1. To change the gateway, for **Source**, choose the gateway through which traffic enters your VPC\. If you choose a virtual private gateway, for **Destination IPv4 CIDR**, enter the destination subnet CIDR\.

1. To add another destination subnet, choose **Add additional subnet**, and then do the following:
   + For **Middlebox**, choose the network interface ID that is associated with your middlebox appliance, or when you use a Gateway Load Balancer endpoint, choose the VPC endpoint ID\.

     You must use the same middlebox appliance for multiple subnets\.
   + For **Destination subnet**, choose the destination subnet\.

1. Choose **Next**\.

1. On the **Review and update** page, a list of route tables and their routes that will be created by the middlebox routing wizard is displayed\. Verify the routes, and then in the confirmation dialog box, choose **Update routes**\.

## View the middlebox routing wizard route tables<a name="viewing-routing-console-routes"></a>

**To view the middlebox routing wizard route tables**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, and then choose **Actions**, **Manage middlebox routes**\.

1. Under **Middlebox route tables**, the number indicates how many routes the middlebox routing wizard created\. Choose the number to view the routes\.

We display the middlebox routing wizard routes on a separate route table page\.

## Delete the middlebox routing wizard configuration<a name="deleting-routing-console"></a>

If you decide that you no longer want the middlebox routing wizard configuration, you must manually delete the route tables\.

**To delete the middlebox routing wizard configuration**

1. View the middlebox routing wizard route tables\. For more information, see [View the middlebox routing wizard route tables](#viewing-routing-console-routes)\.

   After you perform the operation, the route tables that the middlebox routing wizard created are displayed on a separate route table page\. 

1. Delete each route table that is displayed\. For more information, see [Delete a route table](WorkWithRouteTables.md#DeleteRouteTable)\.