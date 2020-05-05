# VPC endpoints and VPC endpoint services \(AWS PrivateLink\)<a name="endpoint-services-overview"></a>

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by AWS PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\. 

Endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components\. They allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic\.

## VPC endpoints concepts<a name="concepts"></a>

The following are the key concepts for VPC endpoints:
+ **Endpoint service** — Your own application in your VPC\. Other AWS principals can create a connection from their VPC to your endpoint service
+ **Gateway endpoint** — A [gateway endpoint](vpce-gateway.md) is a gateway that you specify as a target for a route in your route table for traffic destined to a supported AWS service\. 
+ **Interface endpoint** — An [interface endpoint](vpce-interface.md) is an elastic network interface with a private IP address from the IP address range of your subnet that serves as an entry point for traffic destined to a supported service\. 

## Working with VPC endpoints<a name="working-with"></a>

You can create, access, and manage VPC endpoints using any of the following:
+ **AWS Management Console** — Provides a web interface that you can use to access your VPC endpoints\.
+ **AWS Command Line Interface \(AWS CLI\)** — Provides commands for a broad set of AWS services, including Amazon VPC\. The AWS CLI is supported on Windows, macOS, and Linux\. For more information, see [AWS Command Line Interface](https://aws.amazon.com/cli/)\.
+ **AWS SDKs** — Provide language\-specific APIs\. The AWS SDKs take care of many of the connection details, such as calculating signatures, handling request retries, and handling errors\. For more information, see [AWS SDKs](https://aws.amazon.com/tools/#SDKs)\.
+ **Query API** — Provides low\-level API actions that you call using HTTPS requests\. Using the Query API is the most direct way to access Amazon VPC\. However, it requires that your application handle low\-level details such as generating the hash to sign the request and handling errors\. For more information, see the [Amazon EC2 API Reference](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/)\.