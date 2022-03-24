# DNS64 and NAT64<a name="nat-gateway-nat64-dns64"></a>

A NAT gateway supports network address translation from IPv6 to IPv4, popularly known as NAT64\. NAT64 helps your IPv6 AWS resources communicate with IPv4 resources in the same VPC or a different VPC, in your on\-premises network or over the internet\. You can use NAT64 with DNS64 on Amazon Route 53 Resolver or use your own DNS64 server\.

**Topics**
+ [What is DNS64?](#nat-gateway-dns64-what-is)
+ [What is NAT64?](#nat-gateway-nat64-what-is)
+ [Configure DNS64 and NAT64](#nat-gateway-nat64-dns64-walkthrough)

## What is DNS64?<a name="nat-gateway-dns64-what-is"></a>

Your IPv6\-only workloads running in VPCs can only send and receive IPv6 network packets\. Without DNS64, a DNS query for an IPv4\-only service will yield an IPv4 destination address in response and your IPv6\-only service cannot communicate with it\. To bridge this communication gap, you can enable DNS64 for a subnet and it applies to all the AWS resources within that subnet\. With DNS64, the Amazon Route 53 Resolver looks up the DNS record for the service you queried for and does one of the following:
+ If the record contains an IPv6 address, it returns the original record and the connection is established without any translation over IPv6\.
+ If there is no IPv6 address associated with the destination in the DNS record, the Route 53 Resolver synthesizes one by prepending the well\-known `/96` prefix, defined in RFC6052 \(`64:ff9b::/96`\), to the IPv4 address in the record\. Your IPv6\-only service sends network packets to the synthesized IPv6 address\. You will then need to route this traffic through the NAT gateway, which performs the necessary translation on the traffic to allow IPv6 services in your subnet to access IPv4 services outside that subnet\.

You can enable or disable DNS64 on a subnet using the [modify\-subnet\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-subnet-attribute.html) using the AWS CLI or with the VPC console by selecting a subnet and choosing **Actions** > **Edit subnet settings**\.

## What is NAT64?<a name="nat-gateway-nat64-what-is"></a>

NAT64 enables your IPv6\-only services in Amazon VPCs to communicate with IPv4\-only services within the same VPC \(in different subnets\) or connected VPCs, in your on\-premises networks, or over the internet\.

NAT64 is automatically available on your existing NAT gateways or on any new NAT gateways you create\. It's not a feature you enable or disable\.

Once you have enabled DNS64 and your IPv6\-only service sends network packets to the synthesized IPv6 address through the NAT gateway, the following happens:
+ From the `64:ff9b::/96` prefix, the NAT gateway recognizes that the original destination is IPv4 and translates the IPv6 packets to IPv4 by replacing:
  + Source IPv6 with its own private IP which is translated to Elastic IP address by the internet gateway\.
  + Destination IPv6 to IPv4 by truncating the `64:ff9b::/96` prefix\.
+ The NAT gateway sends the translated IPv4 packets to the destination through the internet gateway, virtual private gateway, or transit gateway and initiates a connection\.
+ The IPv4\-only host sends back IPv4 response packets\. Once a connection is established, NAT gateway accepts the response IPv4 packets from the external hosts\.
+ The response IPv4 packets are destined for NAT gateway, which receives the packets and de\-NATs them by replacing its IP \(destination IP\) with the host’s IPv6 address and prepending back `64:ff9b::/96` to the source IPv4 address\. The packet then flows to the host following the local route\.

In this way, the NAT gateway enables your IPv6\-only workloads in an Amazon VPC subnet to communicate with IPv4\-only services anywhere outside the subnet\.

## Configure DNS64 and NAT64<a name="nat-gateway-nat64-dns64-walkthrough"></a>

Follow the steps in this section to configure DNS64 and NAT64 to enable communication with IPv4\-only services\.

**Topics**
+ [Enable communication with IPv4\-only services on the internet with the AWS CLI](#nat-gateway-nat64-dns64-walkthrough-internet)
+ [Enable communication with IPv4\-only services in your on\-premises environment](#nat-gateway-nat64-dns64-walkthrough-on-prem)

### Enable communication with IPv4\-only services on the internet with the AWS CLI<a name="nat-gateway-nat64-dns64-walkthrough-internet"></a>

If you have a subnet with IPv6\-only workloads that needs to communicate with IPv4\-only services outside the subnet, this example shows you how to enable these IPv6\-only services to communicate with IPv4\-only services on the internet\.

You should first configure a NAT gateway in a public subnet \(separate from the subnet containing the IPv6\-only workloads\)\. For example, the subnet containing the NAT gateway should have a `0.0/0 `route pointing to the internet gateway\.

Complete these steps to enable these IPv6\-only services to connect with IPv4\-only services on the internet:

1. Add the following three routes to the route table of the subnet containing the IPv6\-only workloads:
   + IPv4 route \(if any\) pointing to the NAT gateway\.
   + `64:ff9b::/96` route pointing to the NAT gateway\. This will allow traffic from your IPv6\-only workloads destined for IPv4\-only services to be routed through the NAT gateway\.
   + IPv6 `::/0` route pointing to the egress\-only internet gateway \(or the internet gateway\)\.

   Note that pointing `::/0` to the internet gateway will allow external IPv6 hosts \(outside the VPC\) to initiate connection over IPv6\.

   

   ```
   aws ec2 create-route --route-table-id rtb-34056078 --destination-cidr-block
   0.0.0.0/0 –-nat-gateway-id nat-05dba92075d71c408
   ```

   

   ```
   aws ec2 create-route --route-table-id rtb-34056078 –-destination-ipv6-cidr-block
   64:ff9b::/96 –-nat-gateway-id nat-05dba92075d71c408
   ```

   

   ```
   aws ec2 create-route --route-table-id rtb-34056078 –-destination-ipv6-cidr-block
   ::/0 --egress-only-internet-gateway-id eigw-c0a643a9
   ```

1. Enable DNS64 capability in the subnet containing the IPv6\-only workloads\.

   ```
   aws ec2 modify-subnet-attribute --subnet-id subnet-1a2b3c4d –-enable-dns64
   ```

Now, resources in your private subnet can establish stateful connections with both IPv4 and IPv6 services on the internet\. Configure your security group and NACLs appropriately to allow egress and ingress traffic to `64:ff9b::/96` traffic\.

### Enable communication with IPv4\-only services in your on\-premises environment<a name="nat-gateway-nat64-dns64-walkthrough-on-prem"></a>

Amazon Route 53 Resolver enables you to forward DNS queries from your VPC to an on\-premises network and vice versa\. You can do this by doing the following:
+ You create a Route 53 Resolver outbound endpoint in a VPC and assign it the IPv4 addresses that you want Route 53 Resolver to forward queries from\. For your on\-premises DNS resolver, these are the IP addresses that the DNS queries originate from and, therefore, should be IPv4 addresses\.
+ You create one or more rules which specify the domain names of the DNS queries that you want Route 53 Resolver to forward to your on\-premises resolvers\. You also specify the IPv4 addresses of the on\-premises resolvers\.
+ Now that you have set up a Route 53 Resolver outbound endpoint, you need to enable DNS64 on the subnet containing your IPv6\-only workloads and route any data destined for your on\-premises network through a NAT gateway\.

How DNS64 works for IPv4\-only destinations in on\-premises networks:

1. You assign an IPv4 address to the Route 53 Resolver outbound endpoint in your VPC\.

1. The DNS query from your IPv6 service goes to Route 53 Resolver over IPv6\. Route 53 Resolver matches the query against the forwarding rule and gets an IPv4 address for your on\-premises resolver\.

1. Route 53 Resolver converts the query packet from IPv6 into IPv4 and forwards it to the outbound endpoint\. Each IP address of the endpoint represents one ENI that forwards the request to the on\-premises IPv4 address of your DNS resolver\.

1. The on\-premises resolver sends the response packet over IPv4 back through the outbound endpoint to Route 53 Resolver\.

1. Assuming the query was made from a DNS64\-enabled subnet, Route 53 Resolver does two things:

   1. Checks the content of the response packet\. If there’s an IPv6 address in the record, it keeps the content as is, but if it contains only an IPv4 record\. It synthesizes an IPv6 record as well by prepending `64:ff9b::/96` to the IPv4 address\.

   1. Repackages the content and sends it to the service in your VPC over IPv6\.