# VPC Endpoints<a name="vpc-endpoints"></a>

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\. 

There are two types of VPC endpoints: 

+ *Interface*

+ *Gateway*


****  

| Endpoint type | Description | Supported services | 
| --- | --- | --- | 
|  Interface \(powered by AWS PrivateLink\)  |  An elastic network interface with a private IP address that serves as an entry point for traffic destined to a supported service\.   |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-endpoints.html)  | 
|  Gateway   |  A gateway that is a target for a specified route in your route table, used for traffic destined to a supported AWS service\.  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-endpoints.html)  | 

Endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components that allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic\.


+ [Interface VPC Endpoints \(AWS PrivateLink\)](vpce-interface.md)
+ [Gateway VPC Endpoints](vpce-gateway.md)
+ [Controlling Access to Services with VPC Endpoints](vpc-endpoints-access.md)
+ [Deleting a VPC Endpoint](delete-vpc-endpoint.md)

**Controlling the Use of VPC Endpoints**  
By default, IAM users do not have permission to work with endpoints\. You can create an IAM user policy that grants users the permissions to create, modify, describe, and delete endpoints\. We currently do not support resource\-level permissions for any of the `ec2:*VpcEndpoint*` API actions, or for the `ec2:DescribePrefixLists` action\. You cannot create an IAM policy that grants users the permissions to use a specific endpoint or prefix list\. For more information, see the following example: [8\. Creating and managing VPC endpoints](VPC_IAM.md#vpc-endpoints-iam)\. 