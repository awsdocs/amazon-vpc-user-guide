# Changing the Network Load Balancers and acceptance settings<a name="modify-endpoint-service"></a>

You can modify your endpoint service configuration by changing the Network Load Balancers that are associated with the endpoint service, and by changing whether acceptance is required for requests to connect to your endpoint service\.

You cannot disassociate a load balancer if there are interface endpoints attached to your endpoint service\.

------
#### [ Console ]

**To change the network load balancers for your endpoint service using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Actions**, **Associate/Disassociate Network Load Balancers**\.

1. Select or deselect the load balancers as required, and choose **Save**\.

**To modify the acceptance setting using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. Choose **Actions**, **Modify endpoint acceptance setting**\.

1. Select or deselect **Require acceptance for endpoint**, and choose **Modify**\.

------
#### [ AWS CLI ]

To change the load balancers for your endpoint service, use the [modify\-vpc\-endpoint\-service\-configuration](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-configuration.html) command and use the `--add-network-load-balancer-arn` or `--remove-network-load-balancer-arn` parameter\. 

```
aws ec2 modify-vpc-endpoint-service-configuration --service-id vpce-svc-09222513e6e77dc86 --remove-network-load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/net/nlb-vpce/e94221227f1ba532
```

To change whether acceptance is required, use the [modify\-vpc\-endpoint\-service\-configuration](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-endpoint-service-configuration.html) command and specify `--acceptance-required` or `--no-acceptance-required`\.

```
aws ec2 modify-vpc-endpoint-service-configuration --service-id vpce-svc-09222513e6e77dc86 --no-acceptance-required
```

------
#### [  AWS Tools for Windows PowerShell ]

Use [Edit\-EC2VpcEndpointServiceConfiguration](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcEndpointServiceConfiguration.html)\.

------
#### [ API ]

Use [ModifyVpcEndpointServiceConfiguration](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcEndpointServiceConfiguration.html)\.

------