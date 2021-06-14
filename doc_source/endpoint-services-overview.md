# AWS PrivateLink and VPC endpoints<a name="endpoint-services-overview"></a>

AWS PrivateLink establishes private connectivity between virtual private clouds \(VPC\) and services hosted on AWS or on\-premises, without exposing traffic between your VPC and the service to the internet\.

To use AWS PrivateLink, create a VPC endpoint for a service in your VPC\. You create the type of VPC endpoint required by the supported service\. This creates an elastic network interface in your subnet with a private IP address that serves as an entry point for traffic destined to the service\. The following diagram shows the basic architecture to securely connect your VPC to an AWS service that supports AWS PrivateLink\.

![\[Using an interface endpoint to access an AWS service\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/vpc-endpoint-privatelink-diagram.png)

You can create your own VPC endpoint service, powered by AWS PrivateLink and enable other AWS customers to access your service\.

For more information, see the [User Guide for AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/privatelink/)\.