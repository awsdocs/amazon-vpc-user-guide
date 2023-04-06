# Work with route tables<a name="WorkWithRouteTables"></a>

This section explains how to work with route tables\.

**Topics**
+ [Determine the route table for a subnet](#SubnetRouteTables)
+ [Determine which subnets and or gateways are explicitly associated](#Route_Which_Associations)
+ [Create a custom route table](#CustomRouteTable)
+ [Add and remove routes from a route table](#AddRemoveRoutes)
+ [Enable or disable route propagation](#EnableDisableRouteProp)
+ [Associate a subnet with a route table](#AssociateSubnet)
+ [Change the route table for a subnet](#ChangeRouteTable)
+ [Disassociate a subnet from a route table](#DisassociateSubnetRouteTable)
+ [Replace the main route table](#Route_Replacing_Main_Table)
+ [Associate a gateway with a route table](#associate-route-table-gateway)
+ [Disassociate a gateway from a route table](#disassociate-route-table-gateway)
+ [Replace or restore the target for a local route](#replace-local-route-target)
+ [Delete a route table](#DeleteRouteTable)

## Determine the route table for a subnet<a name="SubnetRouteTables"></a>

You can determine which route table a subnet is associated with by looking at the subnet details in the Amazon VPC console\.

**To determine the route table for a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the subnet\.

1. Choose the **Route table** tab to view information about the route table and its routes\. To determine whether the association is to the main route table, and if that association is explicit, see [Determine which subnets and or gateways are explicitly associated](#Route_Which_Associations)\.

## Determine which subnets and or gateways are explicitly associated<a name="Route_Which_Associations"></a>

You can determine how many and which subnets or gateways are explicitly associated with a route table\. 

The main route table can have explicit and implicit subnet associations\. Custom route tables have only explicit associations\.

Subnets that aren't explicitly associated with any route table have an implicit association with the main route table\. You can explicitly associate a subnet with the main route table\. For an example of why you might do that, see [Replace the main route table](#Route_Replacing_Main_Table)\.

**To determine which subnets are explicitly associated using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**\.

1. Check the **Explicit subnet association** column to determine the explicitly associated subnets and the **Main** column to determine whether this is the main route table\.

1. Select the route table and choose the **Subnet associations** tab\.

1. The subnets under **Explicit subnet associations** are explicitly associated with the route table\. The subnets under **Subnets without explicit associations** belong to the same VPC as the route table, but are not associated with any route table, so they are implicitly associated with the main route table for the VPC\.

**To determine which gateways are explicitly associated using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**\.

1. Select the route table and choose the **Edge associations** tab\.

**To describe one or more route tables and view its associations using the command line**
+ [describe\-route\-tables](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-route-tables.html) \(AWS CLI\)
+ [Get\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Create a custom route table<a name="CustomRouteTable"></a>

You can create a custom route table for your VPC using the Amazon VPC console\.

**To create a custom route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**\.

1. Choose **Create route table**\.

1. \(Optional\) For **Name**, enter a name for your route table\. 

1. For **VPC**, choose your VPC\. 

1. \(Optional\) To add a tag, choose **Add new tag** and enter the tag key and tag value\.

1. Choose **Create route table**\.

**To create a custom route table using the command line**
+ [create\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route-table.html) \(AWS CLI\)
+ [New\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Add and remove routes from a route table<a name="AddRemoveRoutes"></a>

You can add, delete, and modify routes in your route tables\. You can only modify routes that you've added\.

For more information about working with static routes for a Site\-to\-Site VPN connection, see [Editing Static Routes for a Site\-to\-Site VPN Connection](https://docs.aws.amazon.com/vpn/latest/s2svpn/SetUpVPNConnections.html#vpn-edit-static-routes) in the *AWS Site\-to\-Site VPN User Guide*\.

**To update the routes for a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. To add a route, choose **Add route**\. For **Destination** enter the destination CIDR block, a single IP address, or the ID of a prefix list\.

1. To modify a route, for **Destination**, replace the destination CIDR block or single IP address\. For **Target**, choose a target\.

1. To delete a route, choose **Remove**\.

1. Choose **Save changes**\.

**To update the routes for a route table using the command line**
+ [create\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) \(AWS CLI\)
+ [replace\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route.html) \(AWS CLI\)
+ [delete\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route.html) \(AWS CLI\)
+ [New\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Route.html) \(AWS Tools for Windows PowerShell\)
+ [Set\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2Route.html) \(AWS Tools for Windows PowerShell\)
+ [Remove\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**Note**  
If you add a route using a command line tool or the API, the destination CIDR block is automatically modified to its canonical form\. For example, if you specify `100.68.0.18/18` for the CIDR block, we create a route with a destination CIDR block of `100.68.0.0/18`\.

## Enable or disable route propagation<a name="EnableDisableRouteProp"></a>

Route propagation allows a virtual private gateway to automatically propagate routes to your route tables\. This means that you don't need to manually add or remove VPN routes\.

To complete this process, you must have a virtual private gateway\.

For more information, see [Site\-to\-Site VPN routing options](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html) in the *Site\-to\-Site VPN User Guide*\.

**To enable route propagation using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. Choose **Actions**, **Edit route propagation**\.

1. Select the **Enable** check box next to the virtual private gateway, and then choose **Save**\.

**To enable route propagation using the command line**
+ [enable\-vgw\-route\-propagation](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-vgw-route-propagation.html) \(AWS CLI\)
+ [Enable\-EC2VgwRoutePropagation](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VgwRoutePropagation.html) \(AWS Tools for Windows PowerShell\)

**To disable route propagation using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. Choose **Actions**, **Edit route propagation**\. 

1. Clear the **Enable** check box next to the virtual private gateway, and then choose **Save**\.

**To disable route propagation using the command line**
+ [disable\-vgw\-route\-propagation](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-vgw-route-propagation.html) \(AWS CLI\)
+ [Disable\-EC2VgwRoutePropagation](https://docs.aws.amazon.com/powershell/latest/reference/items/Disable-EC2VgwRoutePropagation.html) \(AWS Tools for Windows PowerShell\)

## Associate a subnet with a route table<a name="AssociateSubnet"></a>

To apply route table routes to a particular subnet, you must associate the route table with the subnet\. A route table can be associated with multiple subnets\. However, a subnet can only be associated with one route table at a time\. Any subnet not explicitly associated with a table is implicitly associated with the main route table by default\.

**To associate a route table with a subnet using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. On the **Subnet associations** tab, choose **Edit subnet associations**\.

1. Select the check box for the subnet to associate with the route table\.

1. Choose **Save associations**\.

**To associate a subnet with a route table using the command line**
+ [associate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) \(AWS CLI\)
+ [Register\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Change the route table for a subnet<a name="ChangeRouteTable"></a>

You can change the route table association for a subnet\.

When you change the route table, your existing connections in the subnet are dropped unless the new route table contains a route for the same traffic to the same target\.

**To change a subnet route table association using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**, and then select the subnet\.

1. From the **Route table** tab, choose **Edit route table association**\.

1. For **Route table ID**, select the new route table\.

1. Choose **Save**\.

**To change the route table associated with a subnet using the command line**
+ [replace\-route\-table\-association](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route-table-association.html) \(AWS CLI\)
+ [Set\-EC2RouteTableAssociation](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2RouteTableAssociation.html) \(AWS Tools for Windows PowerShell\)

## Disassociate a subnet from a route table<a name="DisassociateSubnetRouteTable"></a>

You can disassociate a subnet from a route table\. Until you associate the subnet with another route table, it's implicitly associated with the main route table\.

**To disassociate a subnet from a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. From the **Subnet associations** tab, choose **Edit subnet associations**\.

1. Clear the check box for the subnet\.

1. Choose **Save associations**\.

**To disassociate a subnet from a route table using the command line**
+ [disassociate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-route-table.html) \(AWS CLI\)
+ [Unregister\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Replace the main route table<a name="Route_Replacing_Main_Table"></a>

You can change which route table is the main route table in your VPC\. 

**To replace the main route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the new main route table\.

1. Choose **Actions**, **Set main route table**\.

1. When prompted for confirmation, enter **set**, and then choose **OK**\.

**To replace the main route table using the command line**
+ [replace\-route\-table\-association](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route-table-association.html) \(AWS CLI\)
+ [Set\-EC2RouteTableAssociation](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2RouteTableAssociation.html) \(AWS Tools for Windows PowerShell\)

The following procedure describes how to remove an explicit association between a subnet and the main route table\. The result is an implicit association between the subnet and the main route table\. The process is the same as disassociating any subnet from any route table\.

**To remove an explicit association with the main route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. From the **Subnet associations** tab, choose **Edit subnet associations**\.

1. Clear the checkbox for the subnet\.

1. Choose **Save associations**\.

## Associate a gateway with a route table<a name="associate-route-table-gateway"></a>

You can associate an internet gateway or a virtual private gateway with a route table\. For more information, see [Gateway route tables](VPC_Route_Tables.md#gateway-route-tables)\.

**To associate a gateway with a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. From the **Edge associations** tab, choose **Edit edge associations**\.

1. Select the checkbox for the gateway\.

1. Choose **Save changes**\.

**To associate a gateway with a route table using the AWS CLI**  
Use the [associate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) command\. The following example associates internet gateway `igw-11aa22bb33cc44dd1` with route table `rtb-01234567890123456`\.

```
aws ec2 associate-route-table --route-table-id rtb-01234567890123456 --gateway-id igw-11aa22bb33cc44dd1
```

## Disassociate a gateway from a route table<a name="disassociate-route-table-gateway"></a>

You can disassociate an internet gateway or a virtual private gateway from a route table\.

**To associate a gateway with a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. From the **Edge associations** tab, choose **Edit edge associations**\.

1. Clear the checkbox for the gateway\. 

1. Choose **Save changes**\.

**To disassociate a gateway from a route table using the command line**
+ [disassociate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-route-table.html) \(AWS CLI\)
+ [Unregister\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Replace or restore the target for a local route<a name="replace-local-route-target"></a>

You can change the target of the default local route\. If you replace the target of a local route, you can later restore it to the default `local` target\. If your VPC has [multiple CIDR blocks](vpc-cidr-blocks.md#vpc-resize), your route tables have multiple local routes—one per CIDR block\. You can replace or restore the target of each of the local routes as needed\.

**To update the local route using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. From the **Routes** tab, choose **Edit routes**\.

1. For the local route, clear **Target** and then choose a new target\.

1. Choose **Save changes**\.

**To restore the target for a local route using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. For the route, clear **Target**, and then choose **local**\.

1. Choose **Save changes**\.

**To replace the target for a local route using the AWS CLI**  
Use the [replace\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route.html) command\. The following example replaces the target of the local route with `eni-11223344556677889`\.

```
aws ec2 replace-route --route-table-id rtb-01234567890123456 --destination-cidr-block 10.0.0.0/16 --network-interface-id eni-11223344556677889
```

**To restore the target for a local route using the AWS CLI**  
The following example restores the local target for route table `rtb-01234567890123456`\.

```
aws ec2 replace-route --route-table-id rtb-01234567890123456 --destination-cidr-block 10.0.0.0/16 --local-target
```

## Delete a route table<a name="DeleteRouteTable"></a>

You can delete a route table only if there are no subnets associated with it\. You can't delete the main route table\.

**To delete a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route tables**, and then select the route table\.

1. Choose **Actions**, **Delete route table**\.

1. When prompted for confirmation, enter **delete**, and then choose **Delete**\.

**To delete a route table using the command line**
+ [delete\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route-table.html) \(AWS CLI\)
+ [Remove\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)