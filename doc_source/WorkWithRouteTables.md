# Working with route tables<a name="WorkWithRouteTables"></a>

The following tasks show you how to work with route tables\.

**Note**  
When you use the VPC wizard in the console to create a VPC with a gateway, the wizard automatically updates the route tables to use the gateway\. If you're using the command line tools or API to set up your VPC, you must update the route tables yourself\.

**Topics**
+ [Determining which route table a subnet is associated with](#SubnetRouteTables)
+ [Determining which subnets and or gateways are explicitly associated with a table](#Route_Which_Associations)
+ [Creating a custom route table](#CustomRouteTable)
+ [Adding and removing routes from a route table](#AddRemoveRoutes)
+ [Enabling and disabling route propagation](#EnableDisableRouteProp)
+ [Associating a subnet with a route table](#AssociateSubnet)
+ [Changing a subnet route table](#ChangeRouteTable)
+ [Disassociating a subnet from a route table](#DisassociateSubnetRouteTable)
+ [Replacing the main route table](#Route_Replacing_Main_Table)
+ [Associating a gateway with a route table](#associate-route-table-gateway)
+ [Disassociating a gateway from a route table](#disassociate-route-table-gateway)
+ [Replacing and restoring the target for a local route](#replace-local-route-target)
+ [Deleting a route table](#DeleteRouteTable)

## Determining which route table a subnet is associated with<a name="SubnetRouteTables"></a>

You can determine which route table a subnet is associated with by looking at the subnet details in the Amazon VPC console\.

**To determine which route table a subnet is associated with**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Choose the **Route Table** tab to view the route table ID and its routes\. If it's the main route table, the console doesn't indicate whether the association is implicit or explicit\. To determine if the association to the main route table is explicit, see [Determining which subnets and or gateways are explicitly associated with a table](#Route_Which_Associations)\.

## Determining which subnets and or gateways are explicitly associated with a table<a name="Route_Which_Associations"></a>

You can determine how many and which subnets or gateways are explicitly associated with a route table\. 

The main route table can have explicit and implicit subnet associations\. Custom route tables have only explicit associations\.

Subnets that aren't explicitly associated with any route table have an implicit association with the main route table\. You can explicitly associate a subnet with the main route table\. For an example of why you might do that, see [Replacing the main route table](#Route_Replacing_Main_Table)\.

**To determine which subnets are explicitly associated using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. View the **Explicit subnet association** column to determine the explicitly associated subnets\.

1. Select the required route table\.

1. Choose the **Subnet Associations** tab in the details pane\. The subnets explicitly associated with the table are listed on the tab\. Any subnets not associated with any route table \(and thus implicitly associated with the main route table\) are also listed\.

**To determine which gateways are explicitly associated using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. View the **Edge associations** column to determine the associated gateways\.

1. Select the required route table\.

1. Choose the **Edge Associations** tab in the details pane\. The gateways that are associated with the route table are listed\.

**To describe one or more route tables and view its associations using the command line**
+ [describe\-route\-tables](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-route-tables.html) \(AWS CLI\)
+ [Get\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Creating a custom route table<a name="CustomRouteTable"></a>

You can create a custom route table for your VPC using the Amazon VPC console\.

**To create a custom route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Choose **Create route table**\.

1. \(Optional\) For **Name tag**, enter a name for your route table\. 

1. For **VPC**, choose your VPC\. 

1. \(Optional\) Add or remove a tag\.

   \[Add a tag\] Choose **Add tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose the Delete button \("X"\) to the right of the tag’s Key and Value\.

1. Choose **Create**\.

**To create a custom route table using the command line**
+ [create\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route-table.html) \(AWS CLI\)
+ [New\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Adding and removing routes from a route table<a name="AddRemoveRoutes"></a>

You can add, delete, and modify routes in your route tables\. You can only modify routes that you've added\.

For more information about working with static routes for a Site\-to\-Site VPN connection, see [Editing Static Routes for a Site\-to\-Site VPN Connection](https://docs.aws.amazon.com/vpn/latest/s2svpn/SetUpVPNConnections.html#vpn-edit-static-routes) in the *AWS Site\-to\-Site VPN User Guide*\.

**To modify or add a route to a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. To add a route, choose **Add route**\. For **Destination** enter the destination CIDR block, a single IP address, or the ID of a prefix list\. 

1. To modify an existing route, for **Destination**, replace the destination CIDR block or single IP address\. For **Target**, choose a target\.

1. Choose **Save routes**\.

**To add a route to a route table using the command line**
+ [create\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) \(AWS CLI\)
+ [New\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**Note**  
If you add a route using a command line tool or the API, the destination CIDR block is automatically modified to its canonical form\. For example, if you specify `100.68.0.18/18` for the CIDR block, we create a route with a destination CIDR block of `100.68.0.0/18`\.

**To replace an existing route in a route table using the command line**
+ [replace\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route.html) \(AWS CLI\)
+ [Set\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**To delete a route from a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. Choose the delete button \(x\) to the right of the route that you want to delete\.

1. Choose **Save routes** when you are done\.

**To delete a route from a route table using the command line**
+ [delete\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route.html) \(AWS CLI\)
+ [Remove\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Route.html) \(AWS Tools for Windows PowerShell\)

## Enabling and disabling route propagation<a name="EnableDisableRouteProp"></a>

Route propagation allows a virtual private gateway to automatically propagate routes to the route tables\. This means that you don't need to manually enter VPN routes to your route tables\. You can enable or disable route propagation\.

For more information about VPN routing options, see [Site\-to\-Site VPN routing options](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html) in the *Site\-to\-Site VPN User Guide*\.

**To enable route propagation using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit route propagation**\.

1. Select the **Propagate** check box next to the virtual private gateway, and then choose **Save**\.

**To enable route propagation using the command line**
+ [enable\-vgw\-route\-propagation](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-vgw-route-propagation.html) \(AWS CLI\)
+ [Enable\-EC2VgwRoutePropagation](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VgwRoutePropagation.html) \(AWS Tools for Windows PowerShell\)

**To disable route propagation using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit route propagation**\. 

1. Clear the **Propagate** check box, and then choose **Save**\.

**To disable route propagation using the command line**
+ [disable\-vgw\-route\-propagation](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-vgw-route-propagation.html) \(AWS CLI\)
+ [Disable\-EC2VgwRoutePropagation](https://docs.aws.amazon.com/powershell/latest/reference/items/Disable-EC2VgwRoutePropagation.html) \(AWS Tools for Windows PowerShell\)

## Associating a subnet with a route table<a name="AssociateSubnet"></a>

To apply route table routes to a particular subnet, you must associate the route table with the subnet\. A route table can be associated with multiple subnets\. However, a subnet can only be associated with one route table at a time\. Any subnet not explicitly associated with a table is implicitly associated with the main route table by default\.

**To associate a route table with a subnet using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. On the **Subnet Associations** tab, choose **Edit subnet associations**\.

1. Select the **Associate** check box for the subnet to associate with the route table, and then choose **Save**\.

**To associate a subnet with a route table using the command line**
+ [associate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) \(AWS CLI\)
+ [Register\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Changing a subnet route table<a name="ChangeRouteTable"></a>

You can change which route table a subnet is associated with\. 

**To change a subnet route table association using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**, and then select the subnet\.

1. In the **Route Table** tab, choose **Edit route table association**\.

1. From the **Route Table ID** list, select the new route table with which to associate the subnet, and then choose **Save**\.

**To change the route table associated with a subnet using the command line**
+ [replace\-route\-table\-association](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route-table-association.html) \(AWS CLI\)
+ [Set\-EC2RouteTableAssociation](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2RouteTableAssociation.html) \(AWS Tools for Windows PowerShell\)

## Disassociating a subnet from a route table<a name="DisassociateSubnetRouteTable"></a>

You can disassociate a subnet from a route table\. Until you associate the subnet with another route table, it's implicitly associated with the main route table\.

**To disassociate a subnet from a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. In the **Subnet Associations** tab, choose **Edit subnet associations**\.

1. Clear the **Associate** check box for the subnet, and then choose **Save**\.

**To disassociate a subnet from a route table using the command line**
+ [disassociate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-route-table.html) \(AWS CLI\)
+ [Unregister\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Replacing the main route table<a name="Route_Replacing_Main_Table"></a>

You can change which route table is the main route table in your VPC\. 

**To replace the main route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the subnet route table that should be the new main route table, and then choose **Actions**, **Set Main Route Table**\.

1. In the confirmation dialog box, choose **Ok**\.

**To replace the main route table using the command line**
+ [replace\-route\-table\-association](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route-table-association.html) \(AWS CLI\)
+ [Set\-EC2RouteTableAssociation](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2RouteTableAssociation.html) \(AWS Tools for Windows PowerShell\)

The following procedure describes how to remove an explicit association between a subnet and the main route table\. The result is an implicit association between the subnet and the main route table\. The process is the same as disassociating any subnet from any route table\.

**To remove an explicit association with the main route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. In the **Subnet Associations** tab, choose **Edit subnet associations**\.

1. Clear the check box for the subnet, and then choose **Save**\.

## Associating a gateway with a route table<a name="associate-route-table-gateway"></a>

You can associate an internet gateway or a virtual private gateway with a route table\. For more information, see [Gateway route tables](VPC_Route_Tables.md#gateway-route-table)\.

**To associate a gateway with a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit edge associations**\.

1. Choose **Internet gateways** or **Virtual private gateways** to display the list of gateways\. 

1. Choose the gateway, and then choose **Save**\.

**To associate a gateway with a route table using the AWS CLI**  
Use the [associate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) command\. The following example associates internet gateway `igw-11aa22bb33cc44dd1` with route table `rtb-01234567890123456`\.

```
aws ec2 associate-route-table --route-table-id rtb-01234567890123456 --gateway-id igw-11aa22bb33cc44dd1
```

## Disassociating a gateway from a route table<a name="disassociate-route-table-gateway"></a>

You can disassociate an internet gateway or a virtual private gateway from a route table\.

**To associate a gateway with a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit edge associations**\.

1. For **Associated gateways**, choose the delete button \(x\) for the gateway you want to disassociate\. 

1. Choose **Save**\.

**To disassociate a gateway from a route table using the command line**
+ [disassociate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-route-table.html) \(AWS CLI\)
+ [Unregister\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

## Replacing and restoring the target for a local route<a name="replace-local-route-target"></a>

You can change the target of the default local route in a [gateway route table](VPC_Route_Tables.md#gateway-route-table) and specify a network interface or instance in the same VPC as the target instead\. If you replace the target of a local route, you can later restore it to the default `local` target\. If your VPC has [multiple CIDR blocks](VPC_Subnets.md#vpc-resize), your route tables have multiple local routes—one per CIDR block\. You can replace or restore the target of each of the local routes as needed\.

You cannot replace the target for a local route in a subnet route table\.

**To replace the target for a local route using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. For **Target**, choose **Network Interface** to display the list of network interfaces, and choose the network interface\.

   Alternatively, choose **Instance** to display the list of instances, and choose the instance\.

1. Choose **Save routes**\.

**To restore the target for a local route using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. For **Target**, choose **local**\.

1. Choose **Save routes**\.

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

## Deleting a route table<a name="DeleteRouteTable"></a>

You can delete a route table only if there are no subnets associated with it\. You can't delete the main route table\.

**To delete a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the route table, and then choose **Actions**, **Delete Route Table**\.

1. In the confirmation dialog box, choose **Delete Route Table**\.

**To delete a route table using the command line**
+ [delete\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route-table.html) \(AWS CLI\)
+ [Remove\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)