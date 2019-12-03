# Working with Route Tables<a name="WorkWithRouteTables"></a>

The following tasks show you how to work with route tables\.

**Note**  
When you use the VPC wizard in the console to create a VPC with a gateway, the wizard automatically updates the route tables to use the gateway\. If you're using the command line tools or API to set up your VPC, you must update the route tables yourself\.

**Topics**
+ [Determining Which Route Table a Subnet Is Associated With](#SubnetRouteTables)
+ [Determining Which Subnets Are Explicitly Associated with a Table](#Route_Which_Associations)
+ [Creating a Custom Route Table](#CustomRouteTable)
+ [Adding and Removing Routes from a Route Table](#AddRemoveRoutes)
+ [Enabling and Disabling Route Propagation](#EnableDisableRouteProp)
+ [Associating a Subnet with a Route Table](#AssociateSubnet)
+ [Changing a Subnet Route Table](#ChangeRouteTable)
+ [Disassociating a Subnet from a Route Table](#DisassociateSubnetRouteTable)
+ [Replacing the Main Route Table](#Route_Replacing_Main_Table)
+ [Associating a Gateway with a Route Table](#associate-route-table-gateway)
+ [Replacing and Restoring the Target for a Local Route](#replace-local-route-target)
+ [Deleting a Route Table](#DeleteRouteTable)
+ [API and Command Overview](#route-tables-api-cli)

## Determining Which Route Table a Subnet Is Associated With<a name="SubnetRouteTables"></a>

You can determine which route table a subnet is associated with by looking at the subnet details in the Amazon VPC console\.

**To determine which route table a subnet is associated with**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Choose the **Route Table** tab to view the route table ID and its routes\. If it's the main route table, the console doesn't indicate whether the association is implicit or explicit\. To determine if the association to the main route table is explicit, see [Determining Which Subnets Are Explicitly Associated with a Table](#Route_Which_Associations)\.

## Determining Which Subnets Are Explicitly Associated with a Table<a name="Route_Which_Associations"></a>

You can determine how many and which subnets are explicitly associated with a route table\. 

The main route table can have explicit and implicit associations\. Custom route tables have only explicit associations\.

Subnets that aren't explicitly associated with any route table have an implicit association with the main route table\. You can explicitly associate a subnet with the main route table\. For an example of why you might do that, see [Replacing the Main Route Table](#Route_Replacing_Main_Table)\.

**To determine which subnets are explicitly associated**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. View the **Explicit subnet association** column to determine the explicitly associated subnets\.

1. Select the required route table\.

1. Choose the **Subnet Associations** tab in the details pane\. The subnets explicitly associated with the table are listed on the tab\. Any subnets not associated with any route table \(and thus implicitly associated with the main route table\) are also listed\.

## Creating a Custom Route Table<a name="CustomRouteTable"></a>

You can create a custom route table for your VPC using the Amazon VPC console\.

**To create a custom route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Choose **Create route table**\.

1. For **Name tag**, you can optionally provide a name for your route table\. Doing so creates a tag with a key of `Name` and a value that you specify\. For **VPC**, choose your VPC, and then choose **Create**\.

## Adding and Removing Routes from a Route Table<a name="AddRemoveRoutes"></a>

You can add, delete, and modify routes in your route tables\. You can only modify routes that you've added\.

**To modify or add a route to a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. To add a route, choose **Add route**\. For **Destination** enter the destination CIDR block or a single IP address\. 

1. To modify an existing route, for **Destination**, replace the destination CIDR block or single IP address\. For **Target**, choose a target\.

1. Choose **Save routes**\.

**To delete a route from a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. Choose the delete button \(x\) to the right of the route that you want to delete\.

1. Choose **Save routes** when you are done\.

## Enabling and Disabling Route Propagation<a name="EnableDisableRouteProp"></a>

Route propagation allows a virtual private gateway to automatically propagate routes to the route tables\. This means that you don't need to manually enter VPN routes to your route tables\. You can enable or disable route propagation\.

For more information about VPN routing options, see [Site\-to\-Site VPN Routing Options](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPNRoutingTypes.html) in the *Site\-to\-Site VPN User Guide*\.

**To enable route propagation**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit route propagation**\.

1. Select the **Propagate** check box next to the virtual private gateway, and then choose **Save**\.

**To disable route propagation**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit route propagation**\. 

1. Clear the **Propagate** check box, and then choose **Save**\.

## Associating a Subnet with a Route Table<a name="AssociateSubnet"></a>

To apply route table routes to a particular subnet, you must associate the route table with the subnet\. A route table can be associated with multiple subnets\. However, a subnet can only be associated with one route table at a time\. Any subnet not explicitly associated with a table is implicitly associated with the main route table by default\.

**To associate a route table with a subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. On the **Subnet Associations** tab, choose **Edit subnet associations**\.

1. Select the **Associate** check box for the subnet to associate with the route table, and then choose **Save**\.

## Changing a Subnet Route Table<a name="ChangeRouteTable"></a>

You can change which route table a subnet is associated with\. 

**To change a subnet route table association**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**, and then select the subnet\.

1. In the **Route Table** tab, choose **Edit route table association**\.

1. From the **Route Table ID** list, select the new route table with which to associate the subnet, and then choose **Save**\.

## Disassociating a Subnet from a Route Table<a name="DisassociateSubnetRouteTable"></a>

You can disassociate a subnet from a route table\. Until you associate the subnet with another route table, it's implicitly associated with the main route table\.

**To disassociate a subnet from a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. In the **Subnet Associations** tab, choose **Edit subnet associations**\.

1. Clear the **Associate** check box for the subnet, and then choose **Save**\.

## Replacing the Main Route Table<a name="Route_Replacing_Main_Table"></a>

You can change which route table is the main route table in your VPC\. 

**To replace the main route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the subnet route table that should be the new main route table, and then choose **Actions**, **Set Main Route Table**\.

1. In the confirmation dialog box, choose **Ok**\.

The following procedure describes how to remove an explicit association between a subnet and the main route table\. The result is an implicit association between the subnet and the main route table\. The process is the same as disassociating any subnet from any route table\.

**To remove an explicit association with the main route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. In the **Subnet Associations** tab, choose **Edit subnet associations**\.

1. Clear the check box for the subnet, and then choose **Save**\.

## Associating a Gateway with a Route Table<a name="associate-route-table-gateway"></a>

You can associate an internet gateway or a virtual private gateway with a route table\. For more information, see [Gateway Route Tables](VPC_Route_Tables.md#gateway-route-table)\.

**To associate a gateway with a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and then select the route table\.

1. Choose **Actions**, **Edit edge associations**\.

1. Choose **Internet gateways** or **Virtual private gateways** to display the list of gateways\. 

1. Choose the gateway, and then choose **Save**\.

**To associate an internet gateway with a route table using the AWS CLI**  
The following command associates internet gateway `igw-11aa22bb33cc44dd1` with route table `rtb-01234567890123456`\.

```
aws ec2 associate-route-table --route-table-id rtb-01234567890123456 --gateway-id igw-11aa22bb33cc44dd1
```

## Replacing and Restoring the Target for a Local Route<a name="replace-local-route-target"></a>

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
The following command replaces the target of the local route with `eni-11223344556677889`\.

```
aws ec2 replace-route --route-table-id rtb-01234567890123456 --destination-cidr-block 10.0.0.0/16 --network-interface-id eni-11223344556677889
```

**To restore the target for a local route using the AWS CLI**  
The following command restores the local target for route table `rtb-01234567890123456`\.

```
aws ec2 replace-route --route-table-id rtb-01234567890123456 --destination-cidr-block 10.0.0.0/16 --local-target
```

## Deleting a Route Table<a name="DeleteRouteTable"></a>

You can delete a route table only if there are no subnets associated with it\. You can't delete the main route table\.

**To delete a route table**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**\.

1. Select the route table, and then choose **Actions**, **Delete Route Table**\.

1. In the confirmation dialog box, choose **Delete Route Table**\.

## API and Command Overview<a name="route-tables-api-cli"></a>

You can perform the tasks described on this page using the command line or API\. For more information about the command line interface and a list of available API operations, see [Accessing Amazon VPC](what-is-amazon-vpc.md#VPCInterfaces)\.

**Create a custom route table**
+ [create\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route-table.html) \(AWS CLI\)
+ [New\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

**Add a route to a route table**
+ [create\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) \(AWS CLI\)
+ [New\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**Associate a subnet or gateway with a route table**
+ [associate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-route-table.html) \(AWS CLI\)
+ [Register\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

**Describe one or more route tables**
+ [describe\-route\-tables](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-route-tables.html) \(AWS CLI\)
+ [Get\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

**Delete a route from a route table**
+ [delete\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route.html) \(AWS CLI\)
+ [Remove\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**Replace an existing route in a route table**
+ [replace\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route.html) \(AWS CLI\)
+ [Set\-EC2Route](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2Route.html) \(AWS Tools for Windows PowerShell\)

**Disassociate a subnet or gateway from a route table**
+ [disassociate\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-route-table.html) \(AWS CLI\)
+ [Unregister\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)

**Change the route table associated with a subnet or gateway**
+ [replace\-route\-table\-association](https://docs.aws.amazon.com/cli/latest/reference/ec2/replace-route-table-association.html) \(AWS CLI\)
+ [Set\-EC2RouteTableAssociation](https://docs.aws.amazon.com/powershell/latest/reference/items/Set-EC2RouteTableAssociation.html) \(AWS Tools for Windows PowerShell\)

**Create a static route associated with a Site\-to\-Site VPN connection**
+ [create\-vpn\-connection\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpn-connection-route.html) \(AWS CLI\)
+ [New\-EC2VpnConnectionRoute](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpnConnectionRoute.html) \(AWS Tools for Windows PowerShell\)

**Delete a static route associated with a Site\-to\-Site VPN connection**
+ [delete\-vpn\-connection\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-vpn-connection-route.html) \(AWS CLI\)
+ [Remove\-EC2VpnConnectionRoute](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2VpnConnectionRoute.html) \(AWS Tools for Windows PowerShell\)

**Enable a virtual private gateway to propagate routes to a route table**
+ [enable\-vgw\-route\-propagation](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-vgw-route-propagation.html) \(AWS CLI\)
+ [Enable\-EC2VgwRoutePropagation](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VgwRoutePropagation.html) \(AWS Tools for Windows PowerShell\)

**Disable a virtual private gateway from propagating routes to a route table**
+ [disable\-vgw\-route\-propagation](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-vgw-route-propagation.html) \(AWS CLI\)
+ [Disable\-EC2VgwRoutePropagation](https://docs.aws.amazon.com/powershell/latest/reference/items/Disable-EC2VgwRoutePropagation.html) \(AWS Tools for Windows PowerShell\)

**Delete a route table**
+ [delete\-route\-table](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-route-table.html) \(AWS CLI\)
+ [Remove\-EC2RouteTable](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2RouteTable.html) \(AWS Tools for Windows PowerShell\)