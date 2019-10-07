# Controlling Access to Amazon VPC Resources<a name="VPC_IAM"></a>

To allow access to Amazon VPC resources without sharing your security credentials, you must create and attach an IAM policy to the IAM user or the group to which the IAM user belongs\. The IAM user must be given permission to use the specific Amazon VPC resources and Amazon EC2 API actions they need\. When you attach a policy to a user or group of users, it allows or denies permission to perform the specified tasks on the specified resources\. Some API actions support resource\-level permissions, which allow you to control the specific resources that users can create or modify\.

**Important**  
Currently, not all Amazon EC2 API actions support resource\-level permissions\. If an Amazon EC2 API action does not support resource\-level permissions, you can grant users permission to use the action, but you have to specify a \* for the resource element of your policy statement\. For an example of how to do this, see the following example policy: [1\. Managing a VPC](#managingvpciam) We'll add support for additional API actions and ARNs for additional Amazon EC2 resources later\. For information about which ARNs you can use with which Amazon EC2 API actions, as well as supported condition keys for each ARN, see [Supported Resources and Conditions for Amazon EC2 API Actions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-iam-actions-resources.html) in the *Amazon EC2 User Guide for Linux Instances*\. 

For more information about creating IAM policies for Amazon EC2, supported resources for EC2 API actions, as well as example policies for Amazon EC2, see [IAM Policies for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances*\. 

**Topics**
+ [Example Policies for the AWS CLI or SDK](#ExamplePolicies_VPC)
+ [Example Policies for the Console](#example-policies-vpc-console)

## Example Policies for the AWS CLI or SDK<a name="ExamplePolicies_VPC"></a>

The following examples show policy statements that you can use to control the permissions that IAM users have to Amazon VPC\. These examples are designed for users that use the AWS CLI or an AWS SDK\.

**Topics**
+ [1\. Managing a VPC](#managingvpciam)
+ [2\. Read\-Only Policy for Amazon VPC](#readonlyvpciam)
+ [3\. Custom Policy for Amazon VPC](#customepolicyvpciam)
+ [4\. Launching instances into a specific subnet](#subnet-sg-example-iam)
+ [5\. Launching instances into a specific VPC](#subnet-ami-example-iam)
+ [6\. Managing security groups in a VPC](#securitygroupvpciam)
+ [7\. Creating and managing VPC peering connections](#vpcpeeringiam)
+ [8\. Creating and managing VPC endpoints](#vpc-endpoints-iam)

For example policies for working with ClassicLink, see [Example Policies for CLI or SDK](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExamplePolicies_EC2.html) in the *Amazon EC2 User Guide for Linux Instances*\.

### 1\. Managing a VPC<a name="managingvpciam"></a>

The following policy grants users permission to create and manage your VPC\. You might attach this policy to a group of network administrators\. The `Action` element specifies the API actions related to VPCs, subnets, Internet gateways, customer gateways, virtual private gateways, Site\-to\-Site VPN connections, route tables, Elastic IP addresses, security groups, network ACLs, and DHCP options sets\. The policy also allows the group to run, stop, start, and terminate instances\. It also allows the group to list Amazon EC2 resources\.

The policy uses wildcards to specify all actions for each type of object \(for example, `*SecurityGroup*`\)\. Alternatively, you could list each action explicitly\. If you use the wildcards, be aware that if we add new actions whose names include any of the wildcarded strings in the policy, the policy would automatically grant the group access to those new actions\.

The `Resource` element uses a wildcard to indicate that users can specify all resources with these API actions\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions\.

```
{
    "Version": "2012-10-17",
    "Statement":[{
    "Effect":"Allow",
    "Action":["ec2:*Vpc*",
              "ec2:*Subnet*",
              "ec2:*Gateway*",
              "ec2:*Vpn*",
              "ec2:*Route*",
              "ec2:*Address*",
              "ec2:*SecurityGroup*",
              "ec2:*NetworkAcl*",
              "ec2:*DhcpOptions*",
              "ec2:RunInstances",
              "ec2:StopInstances",
              "ec2:StartInstances",
              "ec2:TerminateInstances",
              "ec2:Describe*"],
    "Resource":"*"
    }
  ]
  }
```

### 2\. Read\-Only Policy for Amazon VPC<a name="readonlyvpciam"></a>

The following policy grants users permission to list your VPCs and their components\. They can't create, update, or delete them\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeAccountAttributes",
                "ec2:DescribeAddresses",
                "ec2:DescribeClassicLinkInstances",
                "ec2:DescribeCustomerGateways",
                "ec2:DescribeDhcpOptions",
                "ec2:DescribeEgressOnlyInternetGateways",
                "ec2:DescribeFlowLogs",
                "ec2:DescribeInternetGateways",
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
                "ec2:DescribeVpnGateways"
            ],
            "Resource": "*"
        }
    ]
}
```

### 3\. Custom Policy for Amazon VPC<a name="customepolicyvpciam"></a>

The following policy grants users permission to launch instances, stop instances, start instances, terminate instances, and describe the available resources for Amazon EC2 and Amazon VPC\.

The second statement in the policy protects against any other policy that might grant the user access to a wider range of API actions by explicitly denying permissions\.

```
{
  "Version": "2012-10-17",
  "Statement":[{
    "Effect":"Allow",
    "Action":["ec2:RunInstances",
              "ec2:StopInstances",
              "ec2:StartInstances",
              "ec2:TerminateInstances",
              "ec2:Describe*"],
     "Resource":"*"
     },
     {
     "Effect":"Deny",
     "NotAction":["ec2:RunInstances",
                  "ec2:StopInstances",
                  "ec2:StartInstances",
                  "ec2:TerminateInstances",
                  "ec2:Describe*"],
     "Resource":"*"
     }
  ]
}
```

### 4\. Launching instances into a specific subnet<a name="subnet-sg-example-iam"></a>

The following policy grants users permission to launch instances into a specific subnet, and to use a specific security group in the request\. The policy does this by specifying the ARN for `subnet-1a2b3c4d`, and the ARN for `sg-123abc123`\. If users attempt to launch an instance into a different subnet or using a different security group, the request will fail \(unless another policy or statement grants users permission to do so\)\.

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
        "arn:aws:ec2:region:account:subnet/subnet-1a2b3c4d",
        "arn:aws:ec2:region:account:network-interface/*",
        "arn:aws:ec2:region:account:volume/*",
        "arn:aws:ec2:region:account:key-pair/*",
        "arn:aws:ec2:region:account:security-group/sg-123abc123"
      ]
    }
   ]
}
```

### 5\. Launching instances into a specific VPC<a name="subnet-ami-example-iam"></a>

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
            "ec2:Vpc": "arn:aws:ec2:region:account:vpc/vpc-1a2b3c4d"
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

### 6\. Managing security groups in a VPC<a name="securitygroupvpciam"></a>

The following policy grants users permission to create and delete inbound and outbound rules for any security group within a specific VPC\. The policy does this by applying a condition key \(`ec2:Vpc`\) to the security group resource for the `Authorize` and `Revoke` actions\. 

The second statement grants users permission to describe all security groups\. This is necessary in order for users to be able to modify security group rules using the CLI\.

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
          "ec2:Vpc": "arn:aws:ec2:region:account:vpc/vpc-1a2b3c4d"
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

### 7\. Creating and managing VPC peering connections<a name="vpcpeeringiam"></a>

The following are examples of policies you can use to manage the creation and modification of VPC peering connections\. 

**a\. Create a VPC peering connection**

The following policy allows users to create VPC peering connection requests using only VPCs that are tagged with `Purpose=Peering`\. The first statement applies a condition key \(`ec2:ResourceTag`\) to the VPC resource\. Note that the VPC resource for the `CreateVpcPeeringConnection` action is always the requester VPC\. 

The second statement grants users permissions to create the VPC peering connection resource, and therefore uses the \* wildcard in place of a specific resource ID\. 

```
{
"Version": "2012-10-17",
"Statement":[{
 "Effect":"Allow",
 "Action": "ec2:CreateVpcPeeringConnection",
 "Resource": "arn:aws:ec2:region:account:vpc/*",
  "Condition": {
    "StringEquals": {
     "ec2:ResourceTag/Purpose": "Peering"
    }
   }
  },
  {
  "Effect": "Allow",
  "Action": "ec2:CreateVpcPeeringConnection",
  "Resource": "arn:aws:ec2:region:account:vpc-peering-connection/*"
  }
 ]
}
```

The following policy allows users in AWS account 333333333333 to create VPC peering connections using any VPC in the `us-east-1` region, but only if the VPC that will be accepting the peering connection is a specific VPC \(`vpc-aaa111bb`\) in a specific account \(777788889999\)\. 

```
{
"Version": "2012-10-17",
"Statement": [{
 "Effect":"Allow",
 "Action": "ec2:CreateVpcPeeringConnection",
 "Resource": "arn:aws:ec2:us-east-1:333333333333:vpc/*"
 },
 {
  "Effect": "Allow",
  "Action": "ec2:CreateVpcPeeringConnection",
  "Resource": "arn:aws:ec2:region:333333333333:vpc-peering-connection/*",
    "Condition": {
     "ArnEquals": {
       "ec2:AccepterVpc": "arn:aws:ec2:region:777788889999:vpc/vpc-aaa111bb"
     }
    }
   }
 ]
}
```

**b\. Accept a VPC peering connection**

The following policy allows users to accept VPC peering connection requests from AWS account 444455556666 only\. This helps to prevent users from accepting VPC peering connection requests from unknown accounts\. The first statement uses the `ec2:RequesterVpc` condition key to enforce this\. 

The policy also grants users permissions to accept VPC peering requests only when your VPC has the tag `Purpose=Peering`\. 

```
{
"Version": "2012-10-17",
"Statement":[{
 "Effect":"Allow",
 "Action": "ec2:AcceptVpcPeeringConnection",
 "Resource": "arn:aws:ec2:region:account:vpc-peering-connection/*",
  "Condition": {
   "ArnEquals": {
    "ec2:RequesterVpc": "arn:aws:ec2:region:444455556666:vpc/*"
   }
  }
 },
 {
 "Effect": "Allow",
 "Action": "ec2:AcceptVpcPeeringConnection",
 "Resource": "arn:aws:ec2:region:account:vpc/*",
  "Condition": {
   "StringEquals": {
    "ec2:ResourceTag/Purpose": "Peering"
    }
   }
  }
 ]
}
```

**c\. Deleting a VPC peering connection**

The following policy allows users in account 444455556666 to delete any VPC peering connection, except those that use the specified VPC `vpc-1a2b3c4d`, which is in the same account\. The policy specifies both the `ec2:AccepterVpc` and `ec2:RequesterVpc` condition keys, as the VPC may have been the requester VPC or the peer VPC in the original VPC peering connection request\. 

```
{
"Version": "2012-10-17",
"Statement": [{
  "Effect":"Allow",
  "Action": "ec2:DeleteVpcPeeringConnection",
  "Resource": "arn:aws:ec2:region:444455556666:vpc-peering-connection/*",
   "Condition": {
    "ArnNotEquals": {
     "ec2:AccepterVpc": "arn:aws:ec2:region:444455556666:vpc/vpc-1a2b3c4d",
     "ec2:RequesterVpc": "arn:aws:ec2:region:444455556666:vpc/vpc-1a2b3c4d"
    }
   }
  }
 ]
}
```

**d\. Working within a specific account**

The following policy allows users to work with VPC peering connections entirely within a specific account\. Users can view, create, accept, reject, and delete VPC peering connections, provided they are all within AWS account 333333333333\. 

The first statement allows users to view all VPC peering connections\. The `Resource` element requires a \* wildcard in this case, as this API action \(`DescribeVpcPeeringConnections`\) currently does not support resource\-level permissions\.

The second statement allows users to create VPC peering connections, and allows access to all VPCs in account 333333333333 in order to do so\. 

The third statement uses a \* wildcard as part of the `Action` element to allow all VPC peering connection actions\. The condition keys ensure that the actions can only be performed on VPC peering connections with VPCs that are part of account 333333333333\. For example, a user is not allowed to delete a VPC peering connection if either the accepter or requester VPC is in a different account\. A user cannot create a VPC peering connection with a VPC in a different account\.

```
{
"Version": "2012-10-17",
"Statement": [{
 "Effect": "Allow",
 "Action": "ec2:DescribeVpcPeeringConnections",
 "Resource": "*"
  },
  {
  "Effect": "Allow",
  "Action": ["ec2:CreateVpcPeeringConnection","ec2:AcceptVpcPeeringConnection"],
  "Resource": "arn:aws:ec2:*:333333333333:vpc/*"
  },
  {
  "Effect": "Allow",
  "Action": "ec2:*VpcPeeringConnection",
  "Resource": "arn:aws:ec2:*:333333333333:vpc-peering-connection/*",
  "Condition": {
   "ArnEquals": {
    "ec2:AccepterVpc": "arn:aws:ec2:*:333333333333:vpc/*",
    "ec2:RequesterVpc": "arn:aws:ec2:*:333333333333:vpc/*"
    }
   }
  }
 ]
}
```

### 8\. Creating and managing VPC endpoints<a name="vpc-endpoints-iam"></a>

The following policy grants users permission to create, modify, view, and delete VPC endpoints, VPC endpoint services, and VPC endpoint connection notifications\. Users can also accept and reject VPC endpoint connection requests\. None of the `ec2:*VpcEndpoint*` actions support resource\-level permissions, so you have to use the \* wildcard for the `Resource` element to allow users to work with all resources\.

```
{
    "Version": "2012-10-17",
    "Statement":[{
    "Effect":"Allow",
    "Action":"ec2:*VpcEndpoint*",
    "Resource":"*"
    }
  ]
}
```

## Example Policies for the Console<a name="example-policies-vpc-console"></a>

You can use IAM policies to grant users permissions to view and work with specific resources in the Amazon VPC console\. You can use the example policies in the previous section; however, they are designed for requests that are made with the AWS CLI or an AWS SDK\. The console uses additional API actions for its features, so these policies may not work as expected\. 

This section demonstrates policies that enable users to work with specific parts of the VPC console\.

**Topics**
+ [1\. Using the VPC wizard](#vpc-wizard-iam)
+ [2\. Managing a VPC](#manage-vpc-console-iam)
+ [3\. Managing security groups](#vpc-security-groups-console-iam)
+ [4\. Creating a VPC peering connection](#peering-connection-console-iam)

### 1\. Using the VPC wizard<a name="vpc-wizard-iam"></a>

You can use the VPC wizard in the Amazon VPC console to create and set up and configure a VPC for you, so that it's ready for you to use\. The wizard provides different configuration options, depending on your requirements\. For more information about using the VPC wizard to create a VPC, see [Scenarios and Examples](VPC_Scenarios.md)\.

To enable users to use the VPC wizard, you must grant them permission to create and modify the resources that form part of the selected configuration\. The following example policies show the actions that are required for each of the wizard configuration options\. 

**Note**  
 If the VPC wizard fails at any point, it attempts to detach and delete the resources that it's created\. If you do not grant users permissions to use these actions, then those resources remain in your account\.

**Option 1: VPC with a single public subnet**

The first VPC wizard configuration option creates a VPC with a single subnet\. In your IAM policy, you must grant users permission to use the following actions so they can successfully use this wizard option:
+ `ec2:CreateVpc`, `ec2:CreateSubnet`, `ec2:CreateRouteTable`, and `ec2:CreateInternetGateway`: To create a VPC, a subnet, a custom route table, and an Internet gateway\.
+ `ec2:DescribeAvailabilityZones`: To display the section of the wizard with the **Availability Zone** list and the CIDR block field for the subnet\. Even if users intend to leave the default settings, they will not be able to create a VPC unless those options are displayed\.
+ `ec2:DescribeVpcEndpointServices`: To display the VPC endpoint section of the wizard\.
+ `ec2:AttachInternetGateway`: To attach the Internet gateway to the VPC\.
+ `ec2:CreateRoute`: To create a route in the custom route table\. The route points traffic to the Internet gateway\.
+ `ec2:AssociateRouteTable`: To associate the custom route table to the subnet\.
+ `ec2:ModifyVpcAttribute`: To modify the VPC's attribute to enable DNS hostnames, so that each instance launched into this VPC receives a DNS hostname\.

None of the API actions in this policy support resource\-level permissions, so you cannot control which specific resources users can use\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:CreateVpc", "ec2:CreateSubnet", "ec2:DescribeAvailabilityZones", "ec2:DescribeVpcEndpointServices",
        "ec2:CreateRouteTable", "ec2:CreateRoute", "ec2:CreateInternetGateway", 
        "ec2:AttachInternetGateway", "ec2:AssociateRouteTable", "ec2:ModifyVpcAttribute"
      ],
      "Resource": "*"
    }
   ]
}
```

**Option 2: VPC with a public and private subnet**

The second VPC wizard configuration option creates a VPC with a public and private subnet, and provides the option to launch a NAT gateway or a NAT instance\. The following policy has the same actions as the previous example \(option 1\), plus actions that allow users to run and configure either a NAT gateway or a NAT instance\.

The following actions are required regardless if you're launching a NAT instance or a NAT gateway:
+ `ec2:DescribeKeyPairs`: To display a list of existing key pairs and load the NAT section of the wizard\.

The following actions are required to create a NAT gateway \(these actions are not required for launching a NAT instance\):
+ `ec2:CreateNatGateway`: To create the NAT gateway\. 
+ `ec2:DescribeNatGateways`: To check NAT gateway status until it's in the available state\.
+ `ec2:DescribeAddresses`: To list the available Elastic IP addresses in your account to associate with the NAT gateway\.

The following actions are required to launch a NAT instance \(these actions are not required for creating a NAT gateway\):
+ `ec2:DescribeImages`: To locate an AMI that's been configured to run as a NAT instance\.
+ `ec2:RunInstances`: To launch the NAT instance\.
+ `ec2:AllocateAddress` and `ec2:AssociateAddress`: To allocate an Elastic IP address to your account, and then associate it with the NAT instance\. 
+ `ec2:ModifyInstanceAttribute`: To disable source/destination checking for the NAT instance\.
+ `ec2:DescribeInstances`: To check the status of the instance until it's in the running state\.
+ `ec2:DescribeRouteTables`, `ec2:DescribeVpnGateways`, and `ec2:DescribeVpcs`: To gather information about the routes that must be added to the main route table\.

The following policy allows users to create either a NAT instance or a NAT gateway\.

```
{
  "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "ec2:CreateVpc", "ec2:CreateSubnet", "ec2:DescribeAvailabilityZones", "ec2:DescribeVpcEndpointServices",
          "ec2:CreateRouteTable", "ec2:CreateRoute", "ec2:CreateInternetGateway", "ec2:CreateNatGateway",
          "ec2:AttachInternetGateway", "ec2:AssociateRouteTable", "ec2:ModifyVpcAttribute", "ec2:DescribeKeyPairs", 
          "ec2:DescribeImages", "ec2:RunInstances", "ec2:AllocateAddress", "ec2:AssociateAddress",
          "ec2:DescribeAddresses", "ec2:DescribeInstances", "ec2:ModifyInstanceAttribute", "ec2:DescribeRouteTables",
          "ec2:DescribeVpnGateways", "ec2:DescribeVpcs", "ec2:DescribeSubnets", "ec2:DescribeNatGateways"
            ],
            "Resource": "*"
        }
    ]
}
```

You can use resource\-level permissions on the `ec2:RunInstances` action to control users' ability to launch instances\. For example, you can specify the ID of a NAT\-enabled AMI so that users can only launch instances from this AMI\. To find out which AMI the wizard uses to launch a NAT instance, log in to the Amazon VPC console as a user with full permissions, then carry out the second option of the VPC wizard\. Switch to the Amazon EC2 console, select the **Instances** page, select the NAT instance, and note the AMI ID that was used to launch it\. 

The following policy allows users to launch instances using only `ami-1a2b3c4d`\. If users try to launch an instance using any other AMI, the launch fails\. 

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:CreateVpc", "ec2:CreateSubnet", "ec2:DescribeAvailabilityZones", "ec2:DescribeVpcEndpointServices",
        "ec2:CreateRouteTable", "ec2:CreateRoute", "ec2:CreateInternetGateway", 
        "ec2:AttachInternetGateway", "ec2:AssociateRouteTable", "ec2:ModifyVpcAttribute", 
        "ec2:DescribeKeyPairs", "ec2:DescribeImages", "ec2:AllocateAddress", "ec2:AssociateAddress", 
        "ec2:DescribeInstances", "ec2:ModifyInstanceAttribute", "ec2:DescribeRouteTables", 
        "ec2:DescribeVpnGateways", "ec2:DescribeVpcs"     
      ],
      "Resource": "*"
    },	
				{
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
        "arn:aws:ec2:region::image/ami-1a2b3c4d",
        "arn:aws:ec2:region:account:instance/*",
        "arn:aws:ec2:region:account:subnet/*",
        "arn:aws:ec2:region:account:network-interface/*",
        "arn:aws:ec2:region:account:volume/*",
        "arn:aws:ec2:region:account:key-pair/*",
        "arn:aws:ec2:region:account:security-group/*"
      ]
    }
   ]
}
```

**Option 3: VPC with public and private subnets and AWS Site\-to\-Site VPN access**

The third VPC wizard configuration option creates a VPC with a public and private subnet, and creates a AWS Site\-to\-Site VPN connection between your VPC and your own network\. In your IAM policy, you must grant users permission to use the same actions as option 1\. This allows them to create a VPC and two subnets, and to configure the routing for the public subnet\. To create a Site\-to\-Site VPN connection, users must also have permission to use the following actions:
+ `ec2:CreateCustomerGateway`: To create a customer gateway\.
+ `ec2:CreateVpnGateway` and `ec2:AttachVpnGateway`: To create a virtual private gateway, and attach it to the VPC\.
+ `ec2:EnableVgwRoutePropagation`: To enable route propagation so that routes are automatically propagated to your route table\.
+ `ec2:CreateVpnConnection`: To create a Site\-to\-Site VPN connection\.
+ `ec2:DescribeVpnConnections`, `ec2:DescribeVpnGateways`, and `ec2:DescribeCustomerGateways`: To display the options on the second configuration page of the wizard\.
+ `ec2:DescribeVpcs` and `ec2:DescribeRouteTables`: To gather information about the routes that must be added to the main route table\.

None of the API actions in this policy support resource\-level permissions, so you cannot control which specific resources users can use\.

```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "ec2:CreateVpc", "ec2:CreateSubnet", "ec2:DescribeAvailabilityZones", "ec2:DescribeVpcEndpointServices",
      "ec2:CreateRouteTable", "ec2:CreateRoute", "ec2:CreateInternetGateway", 
      "ec2:AttachInternetGateway", "ec2:AssociateRouteTable", "ec2:ModifyVpcAttribute", 
      "ec2:CreateCustomerGateway", "ec2:CreateVpnGateway", "ec2:AttachVpnGateway",
      "ec2:EnableVgwRoutePropagation", "ec2:CreateVpnConnection", "ec2:DescribeVpnGateways", 
      "ec2:DescribeCustomerGateways", "ec2:DescribeVpnConnections", "ec2:DescribeRouteTables", 
      "ec2:DescribeNetworkAcls", "ec2:DescribeInternetGateways", "ec2:DescribeVpcs"
    ],
    "Resource": "*"
  }
  ]
}
```

**Option 4: VPC with a private subnet only and AWS Site\-to\-Site VPN access**

The fourth VPC configuration option creates a VPC with a private subnet, and creates a Site\-to\-Site VPN connection between the VPC and your own network\. Unlike the other three options, users do not need permission to create or attach an Internet gateway to the VPC, and they do not need permission to create a route table and associate it with the subnet\. They will require the same permissions as listed in the previous example \(option 3\) to establish the Site\-to\-Site VPN connection\.

None of the API actions in this policy support resource\-level permissions, so you cannot control which specific resources users can use\.

```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "ec2:CreateVpc", "ec2:CreateSubnet", "ec2:DescribeAvailabilityZones", "ec2:DescribeVpcEndpointServices",
      "ec2:ModifyVpcAttribute", "ec2:CreateCustomerGateway", "ec2:CreateVpnGateway", 
      "ec2:AttachVpnGateway", "ec2:EnableVgwRoutePropagation", "ec2:CreateVpnConnection", 
      "ec2:DescribeVpnGateways", "ec2:DescribeCustomerGateways", "ec2:DescribeVpnConnections", 
      "ec2:DescribeRouteTables", "ec2:DescribeNetworkAcls", "ec2:DescribeInternetGateways", "ec2:DescribeVpcs"
    ],
    "Resource": "*"
  }
  ]
}
```

### 2\. Managing a VPC<a name="manage-vpc-console-iam"></a>

On the **Your VPCs** page in the VPC console, you can create or delete a VPC\. To view VPCs, users must have permission to use the `ec2:DescribeVPCs` action\. To create a VPC using the **Create VPC** dialog box, users must have permission to use the `ec2:CreateVpc` action\. 

**Note**  
By default, the VPC console creates a tag with a key of `Name` and a value that the user specifies\. If users do not have permission to the use the `ec2:CreateTags` action, then they will see an error in the **Create VPC** dialog box when they try to create a VPC\. However, the VPC may have been successfully created\.

When you set up a VPC, you typically create a number of dependent objects, such as subnets and an Internet gateway\. You cannot delete a VPC until you've disassociated and deleted these dependent objects\. When you delete a VPC using the console, it performs these actions for you \(except terminating your instances; you have to do this yourself\)\.

The following example allows users to view and create VPCs on the **Your VPCs** page, and to delete VPCs that have been created with the first option in the VPC wizard \- a VPC with a single public subnet\. This VPC has one subnet that's associated with a custom route table, and an Internet gateway that's attached to it\. To delete the VPC and its components using the console, you must grant users permission to use a number of `ec2:Describe*` actions, so that the console can check if there are any other resources that are dependent on this VPC\. You must also grant users permission to disassociate the route table from the subnet, detach the Internet gateway from the VPC, and permission to delete both these resources\. 

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeVpcs", "ec2:DescribeRouteTables", "ec2:DescribeVpnGateways", "ec2:DescribeInternetGateways", 
        "ec2:DescribeSubnets", "ec2:DescribeDhcpOptions", "ec2:DescribeInstances", "ec2:DescribeVpcAttribute",
        "ec2:DescribeNetworkAcls",  "ec2:DescribeNetworkInterfaces", "ec2:DescribeAddresses", 
        "ec2:DescribeVpcPeeringConnections", "ec2:DescribeSecurityGroups",
        "ec2:CreateVpc", "ec2:DeleteVpc", "ec2:DetachInternetGateway", "ec2:DeleteInternetGateway", 
        "ec2:DisassociateRouteTable", "ec2:DeleteSubnet", "ec2:DeleteRouteTable"
      ],
      "Resource": "*"
    }
   ]
}
```

You can't apply resource\-level permissions to any of the `ec2:Describe*` API actions, but you can apply resource\-level permissions to some of the `ec2:Delete*` actions to control which resources users can delete\. 

For example, the following policy allows users to delete only route tables and Internet gateways that have the tag `Purpose=Test`\. Users cannot delete individual route tables or Internet gateways that do not have this tag, and similarly, users cannot use the VPC console to delete a VPC that's associated with a different route table or Internet gateway\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeVpcs", "ec2:DescribeRouteTables", "ec2:DescribeVpnGateways", "ec2:DescribeInternetGateways", 
        "ec2:DescribeSubnets", "ec2:DescribeDhcpOptions", "ec2:DescribeInstances", "ec2:DescribeVpcAttribute",
        "ec2:DescribeNetworkAcls",  "ec2:DescribeNetworkInterfaces", "ec2:DescribeAddresses", 
        "ec2:DescribeVpcPeeringConnections", "ec2:DescribeSecurityGroups",
        "ec2:CreateVpc", "ec2:DeleteVpc", "ec2:DetachInternetGateway", 
        "ec2:DisassociateRouteTable", "ec2:DeleteSubnet"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action":  "ec2:DeleteInternetGateway",
      "Resource": "arn:aws:ec2:region:account:internet-gateway/*",
	  "Condition": {
         "StringEquals": {
            "ec2:ResourceTag/Purpose": "Test"
         }
       }
      },
      {
      "Effect": "Allow",
      "Action": "ec2:DeleteRouteTable",
      "Resource": "arn:aws:ec2:region:account:route-table/*",
	  "Condition": {
         "StringEquals": {
            "ec2:ResourceTag/Purpose": "Test"
         }
      }
    }
   ]
}
```

### 3\. Managing security groups<a name="vpc-security-groups-console-iam"></a>

To view security groups on the **Security Groups** page in the Amazon VPC console, users must have permission to use the `ec2:DescribeSecurityGroups` action\. To use the **Create Security Group** dialog box to create a security group, users must have permission to use the `ec2:DescribeVpcs` and `ec2:CreateSecurityGroup` actions\. If users do not have permission to use the `ec2:DescribeSecurityGroups` action, they can still create a security group using the dialog box, but they may encounter an error that indicates that the group was not created\.

In the **Create Security Group** dialog box, users must add the security group name and description, but they will not be able to enter a value for the **Name tag** field unless they've been granted permission to use the `ec2:CreateTags` action\. However, they do not need this action to successfully create a security group\.

The following policy allows users to view and create security groups, and add and remove inbound and outbound rules to any security group that's associated with `vpc-1a2b3c4d`\.

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
            "ec2:Vpc": "arn:aws:ec2:*:*:vpc/vpc-1a2b3c4d"
         }
      }
    }
   ]
}
```

### 4\. Creating a VPC peering connection<a name="peering-connection-console-iam"></a>

To view VPC peering connections in the Amazon VPC console, users must have permission to use the `ec2:DescribePeeringConnections` action\. To use the **Create VPC Peering Connection** dialog box, users must have permission to use the `ec2:DescribeVpcs` action\. This allows them to view and select a VPC; without this action, the dialog box cannot load\. You can apply resource\-level permissions to all the `ec2:*PeeringConnection` actions, except `ec2:DescribeVpcPeeringConnections`\. 

The following policy allows users to view VPC peering connections, and to use the **Create VPC Peering Connection** dialog box to create a VPC peering connection using a specific requester VPC \(`vpc-1a2b3c4d`\) only\. If users try to create a VPC peering connection with a different requester VPC, the request fails\.

```
{
  "Version": "2012-10-17",
  "Statement":[{
   "Effect":"Allow",
   "Action": [
    "ec2:DescribeVpcPeeringConnections", "ec2:DescribeVpcs"
   ],
   "Resource": "*"
  },
  {
   "Effect":"Allow",
   "Action": "ec2:CreateVpcPeeringConnection",
   "Resource": [
    "arn:aws:ec2:*:*:vpc/vpc-1a2b3c4d",
    "arn:aws:ec2:*:*:vpc-peering-connection/*"
   ]
  }
  ]
}
```

For more examples of writing IAM policies for working with VPC peering connections, see [7\. Creating and managing VPC peering connections](#vpcpeeringiam)\.