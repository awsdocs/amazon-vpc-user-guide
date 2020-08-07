# Amazon VPC policy examples<a name="vpc-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify VPC resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS API\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

To learn how to create an IAM identity\-based policy using these example JSON policy documents, see [Creating Policies on the JSON Tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy best practices](#security_iam_service-with-iam-policy-best-practices)
+ [Viewing the Amazon VPC console](#security_iam_id-based-policy-examples-console)
+ [Allow users to view their own permissions](#security_iam_id-based-policy-examples-view-own-permissions)
+ [Create a VPC with a public subnet](#vpc-public-subnet-iam)
+ [Modify and delete VPC resources](#modify-vpc-resources-iam)
+ [Managing security groups](#vpc-security-groups-iam)
+ [Launching instances into a specific subnet](#subnet-sg-example-iam)
+ [Launching instances into a specific VPC](#subnet-ami-example-iam)
+ [Additional Amazon VPC policy examples](#security-iam-additional-examples)

## Policy best practices<a name="security_iam_service-with-iam-policy-best-practices"></a>

Identity\-based policies are very powerful\. They determine whether someone can create, access, or delete Amazon VPC resources in your account\. These actions can incur costs for your AWS account\. When you create or edit identity\-based policies, follow these guidelines and recommendations:
+ **Get Started Using AWS Managed Policies** – To start using Amazon VPC quickly, use AWS managed policies to give your employees the permissions they need\. These policies are already available in your account and are maintained and updated by AWS\. For more information, see [Get Started Using Permissions With AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-use-aws-defined-policies) in the *IAM User Guide*\.
+ **Grant Least Privilege** – When you create custom policies, grant only the permissions required to perform a task\. Start with a minimum set of permissions and grant additional permissions as necessary\. Doing so is more secure than starting with permissions that are too lenient and then trying to tighten them later\. For more information, see [Grant Least Privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege) in the *IAM User Guide*\.
+ **Enable MFA for Sensitive Operations** – For extra security, require IAM users to use multi\-factor authentication \(MFA\) to access sensitive resources or API operations\. For more information, see [Using Multi\-Factor Authentication \(MFA\) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) in the *IAM User Guide*\.
+ **Use Policy Conditions for Extra Security** – To the extent that it's practical, define the conditions under which your identity\-based policies allow access to a resource\. For example, you can write conditions to specify a range of allowable IP addresses that a request must come from\. You can also write conditions to allow requests only within a specified date or time range, or to require the use of SSL or MFA\. For more information, see [IAM JSON Policy Elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

## Viewing the Amazon VPC console<a name="security_iam_id-based-policy-examples-console"></a>

To access the Amazon VPC console, you must have a minimum set of permissions\. These permissions must allow you to list and view details about the Amazon VPC resources in your AWS account\. If you create an identity\-based policy that is more restrictive than the minimum required permissions, the console won't function as intended for entities \(IAM users or roles\) with that policy\.

The following policy grants users permission to list resources in the VPC console, but not to create, update, or delete them\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeAccountAttributes",
                "ec2:DescribeAddresses",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeClassicLinkInstances",
                "ec2:DescribeClientVpnEndpoints",
                "ec2:DescribeCustomerGateways",
                "ec2:DescribeDhcpOptions",
                "ec2:DescribeEgressOnlyInternetGateways",
                "ec2:DescribeFlowLogs",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeManagedPrefixLists",
                "ec2:DescribeMovingAddresses",
                "ec2:DescribeNatGateways",
                "ec2:DescribeNetworkAcls",
                "ec2:DescribeNetworkInterfaceAttribute",
                "ec2:DescribeNetworkInterfacePermissions",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DescribePrefixLists",
                "ec2:DescribeRouteTables",
                "ec2:DescribeSecurityGroupReferences",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeStaleSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeTags",
                "ec2:DescribeTrafficMirrorFilters",
                "ec2:DescribeTrafficMirrorSessions",
                "ec2:DescribeTrafficMirrorTargets",
                "ec2:DescribeTransitGateways",
                "ec2:DescribeTransitGatewayVpcAttachments",
                "ec2:DescribeTransitGatewayRouteTables",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeVpcClassicLink",
                "ec2:DescribeVpcClassicLinkDnsSupport",
                "ec2:DescribeVpcEndpoints",
                "ec2:DescribeVpcEndpointConnectionNotifications",
                "ec2:DescribeVpcEndpointConnections",
                "ec2:DescribeVpcEndpointServiceConfigurations",
                "ec2:DescribeVpcEndpointServicePermissions",
                "ec2:DescribeVpcEndpointServices",
                "ec2:DescribeVpcPeeringConnections",
                "ec2:DescribeVpcs",
                "ec2:DescribeVpnConnections",
                "ec2:DescribeVpnGateways",
                "ec2:GetManagedPrefixListAssociations",
                "ec2:GetManagedPrefixListEntries"
            ],
            "Resource": "*"
        }
    ]
}
```

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the AWS API\. Instead, for those users, allow access only to actions that match the API operation that they need to perform\.

## Allow users to view their own permissions<a name="security_iam_id-based-policy-examples-view-own-permissions"></a>

This example shows how you might create a policy that allows IAM users to view the inline and managed policies that are attached to their user identity\. This policy includes permissions to complete this action on the console or programmatically using the AWS CLI or AWS API\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ViewOwnUserInfo",
            "Effect": "Allow",
            "Action": [
                "iam:GetUserPolicy",
                "iam:ListGroupsForUser",
                  "iam:ListAttachedUserPolicies",
                "iam:ListUserPolicies",
                "iam:GetUser"
            ],
            "Resource": ["arn:aws:iam::*:user/${aws:username}"]
        },
        {
            "Sid": "NavigateInConsole",
            "Effect": "Allow",
            "Action": [
                "iam:GetGroupPolicy",
                "iam:GetPolicyVersion",
                "iam:GetPolicy",
                "iam:ListAttachedGroupPolicies",
                "iam:ListGroupPolicies",
                "iam:ListPolicyVersions",
                "iam:ListPolicies",
                "iam:ListUsers"
            ],
            "Resource": "*"
        }
    ]
}
```

## Create a VPC with a public subnet<a name="vpc-public-subnet-iam"></a>

The following example enables users to create VPCs, subnets, route tables, and internet gateways\. Users can also attach an internet gateway to a VPC and create routes in route tables\. The `ec2:ModifyVpcAttribute` action enables users to enable DNS hostnames for the VPC, so that each instance launched into a VPC receives a DNS hostname\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:CreateVpc", "ec2:CreateSubnet", "ec2:DescribeAvailabilityZones",
        "ec2:CreateRouteTable", "ec2:CreateRoute", "ec2:CreateInternetGateway", 
        "ec2:AttachInternetGateway", "ec2:AssociateRouteTable", "ec2:ModifyVpcAttribute"
      ],
      "Resource": "*"
    }
   ]
}
```

The preceding policy also enables users to create a VPC using the first VPC wizard configuration option in the Amazon VPC console\. To view the VPC wizard, users must also have permission to use the `ec2:DescribeVpcEndpointServices`\. This ensures that the VPC endpoints section of the VPC wizard loads correctly\.

## Modify and delete VPC resources<a name="modify-vpc-resources-iam"></a>

You might want to control which VPC resources users can modify or delete\. For example, the following policy allows users to work with and delete route tables that have the tag `Purpose=Test`\. The policy also specifies that users can only delete internet gateways that have the tag `Purpose=Test`\. Users cannot work with route tables or internet gateways that do not have this tag\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2:DeleteInternetGateway",
            "Resource": "arn:aws:ec2:*:*:internet-gateway/*",
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/Purpose": "Test"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DeleteRouteTable",
                "ec2:CreateRoute",
                "ec2:ReplaceRoute",
                "ec2:DeleteRoute"
            ],
            "Resource": "arn:aws:ec2:*:*:route-table/*",
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/Purpose": "Test"
                }
            }
        }
    ]
}
```

## Managing security groups<a name="vpc-security-groups-iam"></a>

The following policy grants users permission to create and delete inbound and outbound rules for any security group within a specific VPC\. The policy does this by applying a condition key \(`ec2:Vpc`\) to the security group resource for the `Authorize` and `Revoke` actions\. 

The second statement grants users permission to describe all security groups\. This enables users to view security group rules in order to modify them\.

```
{
"Version": "2012-10-17",
  "Statement":[{
    "Effect":"Allow",
    "Action": [
       "ec2:AuthorizeSecurityGroupIngress",
       "ec2:AuthorizeSecurityGroupEgress",
       "ec2:RevokeSecurityGroupIngress",
       "ec2:RevokeSecurityGroupEgress"],
     "Resource": "arn:aws:ec2:region:account:security-group/*",
      "Condition": {
        "StringEquals": {
          "ec2:Vpc": "arn:aws:ec2:region:account:vpc/vpc-11223344556677889"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": "ec2:DescribeSecurityGroups",
      "Resource": "*"
    }
  ]
}
```

To view security groups on the **Security Groups** page in the Amazon VPC console, users must have permission to use the `ec2:DescribeSecurityGroups` action\. To use the **Create security group** page, users must have permission to use the `ec2:DescribeVpcs` and `ec2:CreateSecurityGroup` actions\.

The following policy allows users to view and create security groups\. It also allows them to add and remove inbound and outbound rules to any security group that's associated with `vpc-11223344556677889`\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
         "ec2:DescribeSecurityGroups", "ec2:DescribeVpcs", "ec2:CreateSecurityGroup"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DeleteSecurityGroup", "ec2:AuthorizeSecurityGroupIngress", "ec2:AuthorizeSecurityGroupEgress",
        "ec2:RevokeSecurityGroupIngress", "ec2:RevokeSecurityGroupEgress"
      ],
      "Resource": "arn:aws:ec2:*:*:security-group/*",
      "Condition":{
         "ArnEquals": {
            "ec2:Vpc": "arn:aws:ec2:*:*:vpc/vpc-11223344556677889"
         }
      }
    }
   ]
}
```

To allow users to change the security group that's associated with an instance, add the `ec2:ModifyInstanceAttribute` action to your policy\. Alternatively, to enable users to change security groups for a network interface, add the `ec2:ModifyNetworkInterfaceAttribute` action to your policy\. 

## Launching instances into a specific subnet<a name="subnet-sg-example-iam"></a>

The following policy grants users permission to launch instances into a specific subnet, and to use a specific security group in the request\. The policy does this by specifying the ARN for `subnet-11223344556677889`, and the ARN for `sg-11223344551122334`\. If users attempt to launch an instance into a different subnet or using a different security group, the request will fail \(unless another policy or statement grants users permission to do so\)\.

The policy also grants permission to use the network interface resource\. When launching into a subnet, the `RunInstances` request creates a primary network interface by default, so the user needs permission to create this resource when launching the instance\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
        "arn:aws:ec2:region::image/ami-*",
        "arn:aws:ec2:region:account:instance/*",
        "arn:aws:ec2:region:account:subnet/subnet-11223344556677889",
        "arn:aws:ec2:region:account:network-interface/*",
        "arn:aws:ec2:region:account:volume/*",
        "arn:aws:ec2:region:account:key-pair/*",
        "arn:aws:ec2:region:account:security-group/sg-11223344551122334"
      ]
    }
   ]
}
```

## Launching instances into a specific VPC<a name="subnet-ami-example-iam"></a>

The following policy grants users permission to launch instances into any subnet within a specific VPC\. The policy does this by applying a condition key \(`ec2:Vpc`\) to the subnet resource\. 

The policy also grants users permission to launch instances using only AMIs that have the tag "`department=dev`"\. 

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": "arn:aws:ec2:region:account:subnet/*",
        "Condition": {
         "StringEquals": {
            "ec2:Vpc": "arn:aws:ec2:region:account:vpc/vpc-11223344556677889"
            }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": "arn:aws:ec2:region::image/ami-*",
      "Condition": {
         "StringEquals": {
            "ec2:ResourceTag/department": "dev"
            }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [ 
         "arn:aws:ec2:region:account:instance/*",
         "arn:aws:ec2:region:account:volume/*",
         "arn:aws:ec2:region:account:network-interface/*",
         "arn:aws:ec2:region:account:key-pair/*",
         "arn:aws:ec2:region:account:security-group/*"
         ]
      }
   ]
}
```

## Additional Amazon VPC policy examples<a name="security-iam-additional-examples"></a>

You can find additional example IAM policies related to Amazon VPC in the following topics:
+ [ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html#iam-example-classiclink)
+ [Managed prefix lists](managed-prefix-lists.md#managed-prefix-lists-iam)
+ [Traffic mirroring](https://docs.aws.amazon.com/vpc/latest/mirroring/traffic-mirroring-security.html)
+ [Transit gateways](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-authentication-access-control.html#tgw-example-iam-policies)
+ [VPC endpoints and VPC endpoint services](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-iam.html)
+ [VPC endpoint policies](vpc-endpoints-access.md)
+ [VPC Peering](https://docs.aws.amazon.com/vpc/latest/peering/security-iam.html)
+ [AWS Wavelength](https://docs.aws.amazon.com/wavelength/latest/developerguide/wavelength-policy-examples.html)