# Gateway VPC endpoints<a name="vpce-gateway"></a>

To create and set up a gateway endpoint, follow these general steps:

1. Specify the VPC in which to create the endpoint, and the service to which you're connecting\. A service is identified by an AWS\-managed *prefix list*—the name and ID of a service for a Region\. An AWS prefix list ID uses the form `pl-xxxxxxx` and an AWS prefix list name uses the form "com\.amazonaws\.*region*\.*service*"\. Use the AWS prefix list name \(service name\) to create an endpoint\.

1. Attach an *endpoint policy* to your endpoint that allows access to some or all of the service to which you're connecting\. For more information, see [Using VPC endpoint policies](vpc-endpoints-access.md#vpc-endpoint-policies)\. 

1. Specify one or more route tables in which to create routes to the service\. Route tables control the routing of traffic between your VPC and the other service\. Each subnet that's associated with one of these route tables has access to the endpoint, and traffic from instances in these subnets to the service is then routed through the endpoint\.

In the following diagram, instances in subnet 2 can access Amazon S3 through the gateway endpoint\.

![\[Using a gateway endpoint to access Amazon S3\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-s3-diagram.png)

You can create multiple endpoints in a single VPC, for example, to multiple services\. You can also create multiple endpoints for a single service, and use different route tables to enforce different access policies from different subnets to the same service\.

After you've created an endpoint, you can modify the endpoint policy that's attached to your endpoint, and add or remove the route tables that are used by the endpoint\.

**Topics**
+ [Pricing for gateway endpoints](#gateway-endpoint-pricing)
+ [Routing for gateway endpoints](#vpc-endpoints-routing)
+ [Gateway endpoint limitations](#vpc-endpoints-limitations)
+ [Endpoints for Amazon S3](vpc-endpoints-s3.md)
+ [Endpoints for Amazon DynamoDB](vpc-endpoints-ddb.md)
+ [Creating a gateway endpoint](#create-gateway-endpoint)
+ [Modifying your security group](#vpc-endpoints-security)
+ [Modifying a gateway endpoint](#modify-gateway-endpoint)
+ [Adding or removing gateway endpoint tags](#modify-tags-vpc-gateway-endpoint-tags)

## Pricing for gateway endpoints<a name="gateway-endpoint-pricing"></a>

There is no additional charge for using gateway endpoints\. Standard charges for data transfer and resource usage apply\. For more information about pricing, see [Amazon EC2 Pricing](http://aws.amazon.com/ec2/pricing/)\.

## Routing for gateway endpoints<a name="vpc-endpoints-routing"></a>

When you create or modify an endpoint, you specify the VPC route tables that are used to access the service via the endpoint\. A route is automatically added to each of the route tables with a destination that specifies the AWS prefix list ID of the service \(`pl-xxxxxxxx`\), and a target with the endpoint ID \(`vpce-xxxxxxxx`\); for example:


****  

| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| pl\-1a2b3c4d | vpce\-11bb22cc | 

The AWS prefix list ID logically represents the range of public IP addresses used by the service\. All instances in subnets associated with the specified route tables automatically use the endpoint to access the service\. Subnets that are not associated with the specified route tables do not use the endpoint\. This enables you to keep resources in other subnets separate from your endpoint\. 

To view the current public IP address range for a service, you can use the [describe\-prefix\-lists](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-prefix-lists.html) command\.

**Note**  
The range of public IP addresses for a service may change from time to time\. Consider the implications before you make routing or other decisions based on the current IP address range for a service\.

The following rules apply:
+ You can have multiple endpoint routes to different services in a route table, and you can have multiple endpoint routes to the same service in different route tables\. But you cannot have multiple endpoint routes to the same service in a single route table\. For example, if you create two endpoints to Amazon S3 in your VPC, you cannot create endpoint routes for both endpoints in the same route table\. 
+ You cannot explicitly add, modify, or delete an endpoint route in your route table by using the route table APIs, or by using the Route Tables page in the Amazon VPC console\. You can only add an endpoint route by associating a route table with an endpoint\. To change the route tables that are associated with your endpoint, you can [modify the endpoint](#modify-gateway-endpoint)\. 
+ An endpoint route is automatically deleted when you remove the route table association from the endpoint \(by modifying the endpoint\), or when you delete your endpoint\. 

We use the most specific route that matches the traffic to determine how to route the traffic \(longest prefix match\)\. If you have an existing route in your route table for all internet traffic \(`0.0.0.0/0`\) that points to an internet gateway, the endpoint route takes precedence for all traffic destined for the service, because the IP address range for the service is more specific than `0.0.0.0/0`\. All other internet traffic goes to your internet gateway, including traffic that's destined for the service in other Regions\.

However, if you have existing, more specific routes to IP address ranges that point to an internet gateway or a NAT device, those routes take precedence\. If you have existing routes destined for an IP address range that is identical to the IP address range used by the service, then your routes take precedence\. 

**Example: An endpoint route in a route table**

In this scenario, you have an existing route in your route table for all internet traffic \(`0.0.0.0/0`\) that points to an internet gateway\. Any traffic from the subnet that's destined for another AWS service uses the internet gateway\.


****  

| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 0\.0\.0\.0/0 | igw\-1a2b3c4d | 

You create an endpoint to a supported AWS service, and associate your route table with the endpoint\. An endpoint route is automatically added to the route table, with a destination of `pl-1a2b3c4d` \(assume this represents the service to which you've created the endpoint\)\. Now, any traffic from the subnet that's destined for that AWS service in the same Region goes to the endpoint, and does not go to the internet gateway\. All other internet traffic goes to your internet gateway, including traffic that's destined for other services, and destined for the AWS service in other Regions\.


****  

| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 0\.0\.0\.0/0 | igw\-1a2b3c4d | 
| pl\-1a2b3c4d | vpce\-11bb22cc | 

**Example: Adjusting your route tables for endpoints**

In this scenario, `54.123.165.0/24` is in the Amazon S3 IP address range and you configured your route table to enable instances in your subnet to communicate with Amazon S3 buckets through an internet gateway\. You've added a route with `54.123.165.0/24` as a destination, and the internet gateway as the target\. You then create an endpoint, and associate this route table with the endpoint\. An endpoint route is automatically added to the route table\. You then use the [describe\-prefix\-lists](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-prefix-lists.html) command to view the IP address range for Amazon S3\. The range is `54.123.160.0/19`, which is less specific than the range that's pointing to your internet gateway\. This means that any traffic destined for the `54.123.165.0/24` IP address range continues to use the internet gateway, and does not use the endpoint \(for as long as this remains the public IP address range for Amazon S3\)\.


****  

| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| 54\.123\.165\.0/24  | igw\-1a2b3c4d | 
| pl\-1a2b3c4d | vpce\-11bb22cc | 

To ensure that all traffic destined for Amazon S3 in the same Region is routed via the endpoint, you must adjust the routes in your route table\. To do this, you can delete the route to the internet gateway\. Now, all traffic to Amazon S3 in the same Region uses the endpoint, and the subnet that's associated with your route table is a private subnet\.


****  

| Destination | Target | 
| --- | --- | 
| 10\.0\.0\.0/16 | Local | 
| pl\-1a2b3c4d | vpce\-11bb22cc | 

## Gateway endpoint limitations<a name="vpc-endpoints-limitations"></a>

To use gateway endpoints, you need to be aware of the current limitations:
+ You cannot use an AWS prefix list ID in an outbound rule in a network ACL to allow or deny outbound traffic to the service specified in an endpoint\. If your network ACL rules restrict traffic, you must specify the CIDR block \(IP address range\) for the service instead\. You can, however, use an AWS prefix list ID in an outbound security group rule\. For more information, see [Security groups](vpc-endpoints-access.md#vpc-endpoints-security-groups)\. 
+ Endpoints are supported within the same Region only\. You cannot create an endpoint between a VPC and a service in a different Region\.
+ Endpoints support IPv4 traffic only\.
+ You cannot transfer an endpoint from one VPC to another, or from one service to another\.
+ You have a quota on the number of endpoints you can create per VPC\. For more information, see [VPC endpoints](amazon-vpc-limits.md#vpc-limits-endpoints)\.
+ Endpoint connections cannot be extended out of a VPC\. Resources on the other side of a VPN connection, VPC peering connection, transit gateway, AWS Direct Connect connection, or ClassicLink connection in your VPC cannot use the endpoint to communicate with resources in the endpoint service\. 
+ You must enable DNS resolution in your VPC, or if you're using your own DNS server, ensure that DNS requests to the required service \(such as Amazon S3\) are resolved correctly to the IP addresses maintained by AWS\. For more information, see [Using DNS with your VPC](vpc-dns.md) and [AWS IP Address Ranges](https://docs.aws.amazon.com/general/latest/gr/aws-ip-ranges.html) in the *Amazon Web Services General Reference*\.
+ Review the service\-specific limits for your endpoint service\.

For more information about rules and limitations that are specific to Amazon S3, see [Endpoints for Amazon S3](vpc-endpoints-s3.md)\.

For more information about rules and limitations that are specific to DynamoDB, see [Endpoints for Amazon DynamoDB](vpc-endpoints-ddb.md)\.

## Creating a gateway endpoint<a name="create-gateway-endpoint"></a>

To create an endpoint, you must specify the VPC in which you want to create the endpoint, and the service to which you want to establish the connection\. 

**To create a gateway endpoint using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**, **Create Endpoint**\.

1. For **Service Name**, choose the service to which to connect\. To create a gateway endpoint to DynamoDB or Amazon S3, ensure that the **Type** column indicates **Gateway**\.

1. Complete the following information, and choose **Create endpoint**\.
   + For **VPC**, select a VPC in which to create the endpoint\.
   + For **Configure route tables**, select the route tables to be used by the endpoint\. We automatically add a route that points traffic destined for the service to the endpoint to the selected route tables\.
   + For **Policy**, choose the type of policy\. You can leave the default option, **Full Access**, to allow full access to the service\. Alternatively, you can select **Custom**, and then use the AWS Policy Generator to create a custom policy, or enter your own policy in the policy window\. 
   + \(Optional\) Add or remove a tag\.

     \[Add a tag\] Choose **Add tag** and do the following:
     + For **Key**, enter the key name\.
     + For **Value**, enter the key value\.

     \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

After you've created an endpoint, you can view information about it\. 

**To view information about a gateway endpoint using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your endpoint\.

1. To view information about the endpoint, choose **Summary**\. You can get the AWS prefix list name for the service in the **Service** box\.

1. To view information about the route tables that are used by the endpoint, choose **Route Tables**\. 

1. To view the IAM policy that's attached to your endpoint, choose **Policy**\.
**Note**  
The **Policy** tab only displays the endpoint policy\. It does not display any information about IAM policies for IAM users that have permission to work with endpoints\. It also does not display service\-specific policies; for example, S3 bucket policies\. 

**To create and view an endpoint using the AWS CLI**

1. Use the [describe\-vpc\-endpoint\-services](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-services.html) command to get a list of available services\. In the output that's returned, take note of the name of the service to which you want to connect\. The `serviceType` field indicates whether you connect to the service via an interface endpoint or a gateway endpoint\. 

   ```
   aws ec2 describe-vpc-endpoint-services
   ```

   ```
   {
       "serviceDetailSet": [
           {
               "serviceType": [
                   {
                       "serviceType": "Gateway"
                   }
               ...
   ```

1. To create a gateway endpoint \(for example, to Amazon S3\), use the [create\-vpc\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint.html) command and specify the VPC ID, service name, and route tables that will use the endpoint\. You can optionally use the `--policy-document` parameter to specify a custom policy to control access to the service\. If the parameter is not used, we attach a default policy that allows full access to the service\.

   ```
   aws ec2 create-vpc-endpoint --vpc-id vpc-1a2b3c4d --service-name com.amazonaws.us-east-1.s3 --route-table-ids rtb-11aa22bb
   ```

1. Describe your endpoint using the [describe\-vpc\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoints.html) command\.

   ```
   aws ec2 describe-vpc-endpoints
   ```

**To describe available services using the AWS Tools for Windows PowerShell or API**
+ [Get\-EC2VpcEndpointService](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcEndpointService.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeVpcEndpointServices](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpointServices.html) \(Amazon EC2 Query API\)

**To create a VPC endpoint using the AWS Tools for Windows PowerShell or API**
+ [New\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2VpcEndpoint.html) \(AWS Tools for Windows PowerShell\)
+ [CreateVpcEndpoint](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateVpcEndpoint.html) \(Amazon EC2 Query API\)

**To describe your VPC endpoints using the AWS Tools for Windows PowerShell or API**
+ [Get\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcEndpoint.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeVpcEndpoints](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcEndpoints.html) \(Amazon EC2 Query API\)

## Modifying your security group<a name="vpc-endpoints-security"></a>

If the VPC security group associated with your instance restricts outbound traffic, you must add a rule to allow traffic destined for the AWS service to leave your instance\. 

**To add an outbound rule for a gateway endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select your VPC security group, choose the **Outbound Rules** tab, and then choose **Edit**\.

1. Select the type of traffic from the **Type** list, and enter the port range, if required\. For example, if you use your instance to retrieve objects from Amazon S3, choose **HTTPS** from the **Type** list\. 

1. For **Destination**, start entering `pl-` to display a list of prefix list IDs and names for the available AWS services\. Choose the prefix list ID for the AWS service, or enter it\.

1. Choose **Save**\.

 For more information about security groups, see [Security groups for your VPC](VPC_SecurityGroups.md)\.

**To get the AWS prefix list name, ID, and IP address range for an AWS service using the command line or API**
+ [describe\-prefix\-lists](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-prefix-lists.html) \(AWS CLI\)
+ [Get\-EC2PrefixList](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2PrefixList.html) \(AWS Tools for Windows PowerShell\)
+ [DescribePrefixLists](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribePrefixLists.html) \(Amazon EC2 Query API\)

## Modifying a gateway endpoint<a name="modify-gateway-endpoint"></a>

You can modify a gateway endpoint by changing or removing its policy, and adding or removing the route tables that are used by the endpoint\. 

**To change the policy associated with a gateway endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your endpoint\.

1. Choose **Actions**, **Edit policy**\.

1. You can choose **Full Access** to allow full access\. Alternatively, choose **Custom**, and then use the AWS Policy Generator to create a custom policy, or enter your own policy in the policy window\. When you're done, choose **Save**\.
**Note**  
It can take a few minutes for policy changes to take effect\.

**To add or remove route tables used by a gateway endpoint**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints** and select your endpoint\.

1. Choose **Actions**, **Manage route tables**\.

1. Select or deselect the required route tables, and choose **Modify Route Tables**\.

**To modify a gateway endpoint using the AWS CLI**

1. Use the [describe\-vpc\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoints.html) command to get the ID of your gateway endpoint\.

   ```
   aws ec2 describe-vpc-endpoints
   ```

1. The following example uses the [modify\-vpc\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint.html) command to associate route table `rtb-aaa222bb` with the gateway endpoint, and reset the policy document\.

   ```
   aws ec2 modify-vpc-endpoint --vpc-endpoint-id vpce-1a2b3c4d --add-route-table-ids rtb-aaa222bb --reset-policy
   ```

**To modify a VPC endpoint using the AWS Tools for Windows PowerShell or an API**
+ [Edit\-EC2VpcEndpoint](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcEndpoint.html) \(AWS Tools for Windows PowerShell\)
+ [ModifyVpcEndpoint](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcEndpoint.html) \(Amazon EC2 Query API\)

## Adding or removing gateway endpoint tags<a name="modify-tags-vpc-gateway-endpoint-tags"></a>

Tags provide a way to identify the gateway endpoint\. You can add or remove a tag\.

**To add or remove a gateway endpoint tag**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**\.

1. Select the gateway endpoint and choose **Actions**, **Add/Edit Tags**\.

1. Add or remove a tag\.

   \[Add a tag\] Choose **Create tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose the delete button \(“x”\) to the right of the tag’s Key and Value\.

**To add or remove a tag using the AWS Tools for Windows PowerShell or an API**
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\) 
+ [CreateTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateTags.html) \(AWS Tools for Windows PowerShell\)
+ [delete\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html) \(AWS CLI\) 
+ [DeleteTags](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteTags.html) \(AWS Tools for Windows PowerShell\)