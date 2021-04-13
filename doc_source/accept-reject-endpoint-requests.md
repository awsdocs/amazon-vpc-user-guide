# Accept and reject endpoint connection requests<a name="accept-reject-endpoint-requests"></a>

After you create an endpoint service, service consumers for which you've added permission can create an interface endpoint or Gateway Load Balancer endpoint to connect to your service\. For more information, see [Interface VPC endpoints \(AWS PrivateLink\)](vpce-interface.md) and [Gateway Load Balancer endpoints \(AWS PrivateLink\)](vpce-gateway-load-balancer.md)\. 

If you specified that acceptance is required for connection requests, you must manually accept or reject endpoint connection requests to your endpoint service\. After an endpoint is accepted, it becomes `available`\. Be aware that it can take time for a validation status change to be completed and the state to be `available`\.

You can reject an endpoint connection after it's in the `available` state\.

------
#### [ Console ]

**To accept or reject a connection request using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoint Services** and select your endpoint service\.

1. The **Endpoint Connections** tab lists endpoint connections that are currently pending your approval\. Select the endpoint, choose **Actions**, and choose **Accept endpoint connection request** to accept the connection or **Reject endpoint connection request** to reject it\.

------
#### [ AWS CLI ]

The view the endpoint connections that are pending acceptance, use the [describe\-vpc\-endpoint\-connections](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-endpoint-connections.html) command and filter by the `pendingAcceptance` state\.

```
aws ec2 describe-vpc-endpoint-connections --filters Name=vpc-endpoint-state,Values=pendingAcceptance
```

```
{
    "VpcEndpointConnections": [
        {
            "VpcEndpointId": "vpce-0c1308d7312217abc", 
            "ServiceId": "vpce-svc-03d5ebb7d9579a2b3", 
            "CreationTimestamp": "2017-11-30T10:00:24.350Z", 
            "VpcEndpointState": "pendingAcceptance", 
            "VpcEndpointOwner": "123456789012"
        }
    ]
}
```

To accept an endpoint connection request, use the [accept\-vpc\-endpoint\-connections](https://docs.aws.amazon.com/cli/latest/reference/ec2/accept-vpc-endpoint-connections.html) command and specify the endpoint ID and endpoint service ID\.

```
aws ec2 accept-vpc-endpoint-connections --service-id vpce-svc-03d5ebb7d9579a2b3 --vpc-endpoint-ids vpce-0c1308d7312217abc
```

To reject an endpoint connection request, use the [reject\-vpc\-endpoint\-connections](https://docs.aws.amazon.com/cli/latest/reference/ec2/reject-vpc-endpoint-connections.html) command\.

```
aws ec2 reject-vpc-endpoint-connections --service-id vpce-svc-03d5ebb7d9579a2b3 --vpc-endpoint-ids vpce-0c1308d7312217abc
```

------
#### [  AWS Tools for Windows PowerShell ]

Use [Confirm\-EC2EndpointConnection](https://docs.aws.amazon.com/powershell/latest/reference/items/Confirm-EC2EndpointConnection.html) and [Deny\-EC2EndpointConnection](https://docs.aws.amazon.com/powershell/latest/reference/items/Deny-EC2EndpointConnection.html)\.

------
#### [ API ]

Use [AcceptVpcEndpointConnections](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-AcceptVpcEndpointConnections.html) and [RejectVpcEndpointConnections](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-RejectVpcEndpointConnections.html)\.

------