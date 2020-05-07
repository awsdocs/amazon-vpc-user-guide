# ClassicLink<a name="vpc-classiclink"></a>

ClassicLink allows you to link an EC2\-Classic instance to a VPC in your account, within the same region\. This allows you to associate the VPC security groups with the EC2\-Classic instance, enabling communication between your EC2\-Classic instance and instances in your VPC using private IPv4 addresses\. ClassicLink removes the need to make use of public IPv4 addresses or Elastic IP addresses to enable communication between instances in these platforms\. For more information about private and public IPv4 addresses, see [IP Addressing in your VPC](vpc-ip-addressing.md)\.

ClassicLink is available to all users with accounts that support the EC2\-Classic platform, and can be used with any EC2\-Classic instance\. 

There is no additional charge for using ClassicLink\. Standard charges for data transfer and instance hour usage apply\.

For more information about ClassicLink and how to use it, see the following topics in the *Amazon EC2 User Guide*:
+ [ClassicLink basics](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html#classiclink-basics)
+ [ClassicLink limitations](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html#classiclink-limitations)
+ [Working with ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html#working-with-classiclink)
+ [ClassicLink API and CLI overview](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html#classiclink-api-cli)