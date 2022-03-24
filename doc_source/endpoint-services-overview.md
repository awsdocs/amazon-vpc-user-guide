# Connect your VPC to services using AWS PrivateLink<a name="endpoint-services-overview"></a>

AWS PrivateLink establishes private connectivity between virtual private clouds \(VPC\) and supported AWS services, services hosted by other AWS accounts, and supported AWS Marketplace services\. You do not need to use an internet gateway, NAT device, AWS Direct Connect connection, or AWS Site\-to\-Site VPN connection to communicate with the service\.

To use AWS PrivateLink, create a VPC endpoint in your VPC, specifying the name of the service and a subnet\. This creates an elastic network interface in the subnet that serves as an entry point for traffic destined to the service\.

You can create your own VPC endpoint service, powered by AWS PrivateLink and enable other AWS customers to access your service\.

The following diagram shows the common use cases for AWS PrivateLink\. The VPC on the left has several EC2 instances in a private subnet and three interface VPC endpoints\. The top\-most VPC endpoint connects to an AWS service\. The middle VPC endpoint connects to a service hosted by another AWS account \(a VPC endpoint service\)\. The bottom VPC endpoint connects to an AWS Marketplace partner service\.

![\[Using interface VPC endpoints to access an AWS service, an endpoint service hosted by another AWS account, and a partner service from AWS Marketplace.\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/use-cases.png)

For more information, see [AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/privatelink/)\.