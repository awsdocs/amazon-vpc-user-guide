# Adding and removing permissions for your endpoint service<a name="add-endpoint-service-permissions"></a>

After you create your endpoint service configuration, you can control which service consumers can create an interface endpoint to connect to your service\. Service consumers are [IAM principals](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html)—IAM users, IAM roles, and AWS accounts\. To add or remove permissions for a principal, you need its Amazon Resource Name \(ARN\)\.
+ For an AWS account \(and therefore all principals in the account\), the ARN is in the form `arn:aws:iam::aws-account-id:root`\.
+ For a specific IAM user, the ARN is in the form `arn:aws:iam::aws-account-id:user/user-name`\.
+ For a specific IAM role, the ARN is in the form `arn:aws:iam::aws-account-id:role/role-name`\.

**Note**  
If you set permission to "anyone can access" and you set the acceptance model to "accept all requests," then you've just made your NLB public\. Because it's easy to obtain an AWS account, there is no practical limitation on who can access your NLB even though it has no public IP address\. 

------
#### [ Console ]

**To add or remove permissions using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Actions**, **Add principals to whitelist**\.

1. Specify the ARN for the principal for which to add permissions\. To add more principals, choose **Add principal**\. To remove a principal, choose the cross icon next to the entry\.
**Note**  
Specify `*` to add permissions for all principals\. This enables all principals in all AWS accounts to create an interface endpoint to your endpoint service\.

1. Choose **Add to Whitelisted principals**\.

1. To remove a principal, select it in the list and choose **Delete**\.

------
#### [ AWS CLI ]

To add permissions for your endpoint service, use the [modify\-vpc\-endpoint\-service\-permissions](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html) command and use the `--add-allowed-principals` parameter to add one or more ARNs for the principals\.

```
aws ec2 modify-vpc-endpoint-service-permissions --service-id vpce-svc-03d5ebb7d9579a2b3 --add-allowed-principals '["arn:aws:iam::123456789012:root"]'
```

To view the permissions you added for your endpoint service, use the [describe\-vpc\-endpoint\-service\-permissions](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-service-permissions.html) command\.

```
aws ec2 describe-vpc-endpoint-service-permissions --service-id vpce-svc-03d5ebb7d9579a2b3
```

```
{
    "AllowedPrincipals": [
        {
            "PrincipalType": "Account", 
            "Principal": "arn:aws:iam::123456789012:root"
        }
    ]
}
```

To remove permissions for your endpoint service, use the [modify\-vpc\-endpoint\-service\-permissions](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html) command and use the `--remove-allowed-principals` parameter to remove one or more ARNs for the principals\.

```
aws ec2 modify-vpc-endpoint-service-permissions --service-id vpce-svc-03d5ebb7d9579a2b3 --remove-allowed-principals '["arn:aws:iam::123456789012:root"]'
```

------
#### [  AWS Tools for Windows PowerShell ]

Use [Edit\-EC2EndpointServicePermission](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2EndpointServicePermission.html)\.

------
#### [ API ]

Use [ModifyVpcEndpointServicePermissions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcEndpointServicePermissions.html)\.

------