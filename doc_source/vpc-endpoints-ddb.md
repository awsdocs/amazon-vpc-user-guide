# Endpoints for Amazon DynamoDB<a name="vpc-endpoints-ddb"></a>

If you've already set up access to your DynamoDB tables from your VPC, you can continue to access the tables as you normally would after you set up a gateway endpoint\. However, take note of the following:
+ Your endpoint has a policy that controls the use of the endpoint to access DynamoDB resources\. The default policy allows access by any user or service within the VPC, using credentials from any AWS account, to any DynamoDB resource\. For more information, see [Controlling access to services with VPC endpoints](vpc-endpoints-access.md)\.
+ DynamoDB does not support resource\-based policies \(for example, on tables\)\. Access to DynamoDB is controlled through the endpoint policy and IAM policies for individual IAM users and roles\.
+ You cannot access Amazon DynamoDB Streams through a VPC endpoint\.
+ Endpoints currently do not support cross\-region requests—ensure that you create your endpoint in the same Region as your DynamoDB tables\. 
+ If you use AWS CloudTrail to log DynamoDB operations, the log files contain the private IP address of the EC2 instance in the VPC and the endpoint ID for any actions performed through the endpoint\.
+ The source IPv4 addresses from instances in your affected subnets change from public IPv4 addresses to the private IPv4 addresses from your VPC\. An endpoint switches network routes and disconnects open TCP connections\. The previous connections that used public IPv4 addresses are not resumed\. We recommend that you do not have any critical tasks running when you create or modify an endpoint; or that you test to ensure that your software can automatically reconnect to DynamoDB after the connection break\.

Before you use endpoints with DynamoDB, ensure that you have also read the following general limitations: [Gateway endpoint limitations](vpce-gateway.md#vpc-endpoints-limitations)\.

For more information about creating a gateway VPC endpoint, see [Gateway VPC endpoints](vpce-gateway.md)\.

## Using endpoint policies for DynamoDB<a name="vpc-endpoints-policies-ddb"></a>

An endpoint policy is an IAM policy that you attach to an endpoint that allows access to some or all of the service to which you're connecting\. The following are example endpoint policies for accessing DynamoDB\.

**Important**  
All types of policies — IAM user policies and endpoint policies — must grant the necessary permissions for access to DynamoDB to succeed\. 

**Example Example: Read\-only access**  
You can create a policy that restricts actions to only listing and describing DynamoDB tables through the VPC endpoint\.   

```
{
  "Statement": [
    {
      "Sid": "ReadOnly",
      "Principal": "*",
      "Action": [
        "dynamodb:DescribeTable",
        "dynamodb:ListTables"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

**Example Example: Restrict access to a specific table**  
You can create a policy that restricts access to a specific DynamoDB table\. In this example, the endpoint policy allows access to `StockTable` only\.  

```
{
  "Statement": [
    {
      "Sid": "AccessToSpecificTable",
      "Principal": "*",
      "Action": [
        "dynamodb:Batch*",
        "dynamodb:Delete*",
        "dynamodb:DescribeTable",
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:Update*"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/StockTable"
    }
  ]
}
```

## Using IAM policies to control access to DynamoDB<a name="vpc-endpoints-policies-ddb-iam-user"></a>

You can create an IAM policy for your IAM users, groups, or roles to restrict access to DynamoDB tables from a specific VPC endpoint only\. To do this, you can use the `aws:sourceVpce` condition key for the table resource in your IAM policy\.

For more information about managing access to DynamoDB, see [Authentication and Access Control for Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/authentication-and-access-control.html) in the *Amazon DynamoDB Developer Guide*\.

**Example Example: Restrict access from a specific endpoint**  
In this example, users are denied permission to work with DynamoDB tables, except if accessed through endpoint `vpce-11aa22bb`\.  

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Sid": "AccessFromSpecificEndpoint",
         "Action": "dynamodb:*",
         "Effect": "Deny",
         "Resource": "arn:aws:dynamodb:region:account-id:table/*",
         "Condition": { "StringNotEquals" : { "aws:sourceVpce": "vpce-11aa22bb" } }
      }
   ]
}
```