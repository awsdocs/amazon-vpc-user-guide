# Flow log record examples<a name="flow-logs-records-examples"></a>

The following are examples of flow log records that capture specific traffic flows\.

For information about flow log record format, see [Flow log records](flow-logs.md#flow-log-records)\.

**Topics**
+ [Accepted and rejected Traffic](#flow-log-example-accepted-rejected)
+ [No data and skipped records](#flow-log-example-no-data)
+ [Security group and network ACL rules](#flow-log-example-security-groups)
+ [IPv6 traffic](#flow-log-example-ipv6)
+ [TCP flag sequence](#flow-log-example-tcp-flag)
+ [Traffic through a NAT gateway](#flow-log-example-nat)
+ [Traffic through a transit gateway](#flow-log-example-tgw)

## Accepted and rejected Traffic<a name="flow-log-example-accepted-rejected"></a>

The following are examples of default flow log records\.

In this example, SSH traffic \(destination port 22, TCP protocol\) to network interface `eni-1235b8ca123456789` in account `123456789010` was allowed\.

```
2 123456789010 eni-1235b8ca123456789 172.31.16.139 172.31.16.21 20641 22 6 20 4249 1418530010 1418530070 ACCEPT OK
```

In this example, RDP traffic \(destination port 3389, TCP protocol\) to network interface `eni-1235b8ca123456789` in account `123456789010` was rejected\.

```
2 123456789010 eni-1235b8ca123456789 172.31.9.69 172.31.9.12 49761 3389 6 20 4249 1418530010 1418530070 REJECT OK
```

## No data and skipped records<a name="flow-log-example-no-data"></a>

The following are examples of default flow log records\.

In this example, no data was recorded during the aggregation interval\.

```
2 123456789010 eni-1235b8ca123456789 - - - - - - - 1431280876 1431280934 - NODATA
```

In this example, records were skipped during the aggregation interval\.

```
2 123456789010 eni-11111111aaaaaaaaa - - - - - - - 1431280876 1431280934 - SKIPDATA
```

## Security group and network ACL rules<a name="flow-log-example-security-groups"></a>

If you're using flow logs to diagnose overly restrictive or permissive security group rules or network ACL rules, be aware of the statefulness of these resources\. Security groups are stateful — this means that responses to allowed traffic are also allowed, even if the rules in your security group do not permit it\. Conversely, network ACLs are stateless, therefore responses to allowed traffic are subject to network ACL rules\.

For example, you use the `ping` command from your home computer \(IP address is `203.0.113.12`\) to your instance \(the network interface's private IP address is `172.31.16.139`\)\. Your security group's inbound rules allow ICMP traffic but the outbound rules do not allow ICMP traffic\. Because security groups are stateful, the response ping from your instance is allowed\. Your network ACL permits inbound ICMP traffic but does not permit outbound ICMP traffic\. Because network ACLs are stateless, the response ping is dropped and does not reach your home computer\. In a default flow log, this is displayed as two flow log records: 
+ An `ACCEPT` record for the originating ping that was allowed by both the network ACL and the security group, and therefore was allowed to reach your instance\. 
+ A `REJECT` record for the response ping that the network ACL denied\.

```
2 123456789010 eni-1235b8ca123456789 203.0.113.12 172.31.16.139 0 0 1 4 336 1432917027 1432917142 ACCEPT OK
```

```
2 123456789010 eni-1235b8ca123456789 172.31.16.139 203.0.113.12 0 0 1 4 336 1432917094 1432917142 REJECT OK
```

If your network ACL permits outbound ICMP traffic, the flow log displays two `ACCEPT` records \(one for the originating ping and one for the response ping\)\. If your security group denies inbound ICMP traffic, the flow log displays a single `REJECT` record, because the traffic was not permitted to reach your instance\.

## IPv6 traffic<a name="flow-log-example-ipv6"></a>

The following is an example of a default flow log record\. In the example, SSH traffic \(port 22\) from IPv6 address `2001:db8:1234:a100:8d6e:3477:df66:f105` to network interface `eni-1235b8ca123456789` in account `123456789010` was allowed\.

```
2 123456789010 eni-1235b8ca123456789 2001:db8:1234:a100:8d6e:3477:df66:f105 2001:db8:1234:a102:3304:8879:34cf:4071 34892 22 6 54 8855 1477913708 1477913820 ACCEPT OK
```

## TCP flag sequence<a name="flow-log-example-tcp-flag"></a>

The following is an example of a custom flow log that captures the following fields in the following order\.

```
version vpc-id subnet-id instance-id interface-id account-id type srcaddr dstaddr srcport dstport pkt-srcaddr pkt-dstaddr protocol bytes packets start end action tcp-flags log-status
```

The `tcp-flags` field can help you identify the direction of the traffic, for example, which server initiated the connection\. In the following records \(starting at 7:47:55 PM and ending at 7:48:53 PM\), two connections were started by a client to a server running on port 5001\. Two SYN flags \(`2`\) were received by server from the client from different source ports on the client \(`43416` and `43418`\)\. For each SYN, a SYN\-ACK was sent from the server to the client \(`18`\) on the corresponding port\.

```
3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 52.213.180.42 10.0.0.62 43416 5001 52.213.180.42 10.0.0.62 6 568 8 1566848875 1566848933 ACCEPT 2 OK
3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 10.0.0.62 52.213.180.42 5001 43416 10.0.0.62 52.213.180.42 6 376 7 1566848875 1566848933 ACCEPT 18 OK
3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 52.213.180.42 10.0.0.62 43418 5001 52.213.180.42 10.0.0.62 6 100701 70 1566848875 1566848933 ACCEPT 2 OK
3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 10.0.0.62 52.213.180.42 5001 43418 10.0.0.62 52.213.180.42 6 632 12 1566848875 1566848933 ACCEPT 18 OK
```

In the second aggregation interval, one of the connections that was established during the previous flow is now closed\. The client sent a FIN flag \(`1`\) to the server for the connection on port `43418`\. The server sent a FIN to the client on port `43418`\.

```
3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 10.0.0.62 52.213.180.42 5001 43418 10.0.0.62 52.213.180.42 6 63388 1219 1566848933 1566849113 ACCEPT 1 OK
3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 52.213.180.42 10.0.0.62 43418 5001 52.213.180.42 10.0.0.62 6 23294588 15774 1566848933 1566849113 ACCEPT 1 OK
```

For short connections \(for example, a few seconds\) that are opened and closed within a single aggregation interval, the flags might be set on the same line in the flow log record for traffic flow in the same direction\. In the following example, the connection is established and finished within the same aggregation interval\. In the first line, the TCP flag value is `3`, which indicates that there was a SYN and a FIN message sent from the client to the server\. In the second line, the TCP flag value is `19`, which indicates that there was SYN\-ACK and a FIN message sent from the server to the client\.

```
3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 52.213.180.42 10.0.0.62 43638 5001 52.213.180.42 10.0.0.62 6 1260 17 1566933133 1566933193 ACCEPT 3 OK
3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 10.0.0.62 52.213.180.42 5001 43638  10.0.0.62 52.213.180.42 6 967 14 1566933133 1566933193 ACCEPT 19 OK
```

## Traffic through a NAT gateway<a name="flow-log-example-nat"></a>

In this example, an instance in a private subnet accesses the internet through a NAT gateway that's in a public subnet\.

![\[Accessing the internet through a NAT gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/flow-log-nat-gateway.png)

The following custom flow log for the NAT gateway network interface captures the following fields in the following order\.

```
instance-id interface-id srcaddr dstaddr pkt-srcaddr pkt-dstaddr
```

The flow log shows the flow of traffic from the instance IP address \(10\.0\.1\.5\) through the NAT gateway network interface to a host on the internet \(203\.0\.113\.5\)\. The NAT gateway network interface is a requester\-managed network interface, therefore the flow log record displays a '\-' symbol for the `instance-id` field\. The following line shows traffic from the source instance to the NAT gateway network interface\. The values for the `dstaddr` and `pkt-dstaddr` fields are different\. The `dstaddr` field displays the private IP address of the NAT gateway network interface, and the `pkt-dstaddr` field displays the final destination IP address of the host on the internet\. 

```
- eni-1235b8ca123456789 10.0.1.5 10.0.0.220 10.0.1.5 203.0.113.5
```

The next two lines show the traffic from the NAT gateway network interface to the target host on the internet, and the response traffic from the host to the NAT gateway network interface\.

```
- eni-1235b8ca123456789 10.0.0.220 203.0.113.5 10.0.0.220 203.0.113.5
- eni-1235b8ca123456789 203.0.113.5 10.0.0.220 203.0.113.5 10.0.0.220
```

The following line shows the response traffic from the NAT gateway network interface to the source instance\. The values for the `srcaddr` and `pkt-srcaddr` fields are different\. The `srcaddr` field displays the private IP address of the NAT gateway network interface, and the `pkt-srcaddr` field displays the IP address of the host on the internet\.

```
- eni-1235b8ca123456789 10.0.0.220 10.0.1.5 203.0.113.5 10.0.1.5
```

You create another custom flow log using the same set of fields as above\. You create the flow log for the network interface for the instance in the private subnet\. In this case, the `instance-id` field returns the ID of the instance that's associated with the network interface, and there is no difference between the `dstaddr` and `pkt-dstaddr` fields and the `srcaddr` and `pkt-srcaddr` fields\. Unlike the network interface for the NAT gateway, this network interface is not an intermediate network interface for traffic\.

```
i-01234567890123456 eni-1111aaaa2222bbbb3 10.0.1.5 203.0.113.5 10.0.1.5 203.0.113.5 #Traffic from the source instance to host on the internet
i-01234567890123456 eni-1111aaaa2222bbbb3 203.0.113.5 10.0.1.5 203.0.113.5 10.0.1.5 #Response traffic from host on the internet to the source instance
```

## Traffic through a transit gateway<a name="flow-log-example-tgw"></a>

In this example, a client in VPC A connects to a web server in VPC B through a transit gateway\. The client and server are in different Availability Zones\. Therefore, traffic arrives at the server in VPC B using `eni-11111111111111111` and leaves VPC B using `eni-22222222222222222`\.

![\[Traffic through a transit gateway\]](http://docs.aws.amazon.com/vpc/latest/userguide/images/flow-log-tgw.png)

You create a custom flow log for VPC B with the following format\.

```
version interface-id account-id vpc-id subnet-id instance-id srcaddr dstaddr srcport dstport protocol tcp-flags type pkt-srcaddr pkt-dstaddr action log-status
```

The following lines from the flow log records demonstrate the flow of traffic on the network interface for the web server\. The first line is the request traffic from the client, and the last line is the response traffic from the web server\.

```
3 eni-33333333333333333 123456789010 vpc-abcdefab012345678 subnet-22222222bbbbbbbbb i-01234567890123456 10.20.33.164 10.40.2.236 39812 80 6 3 IPv4 10.20.33.164 10.40.2.236 ACCEPT OK
...
3 eni-33333333333333333 123456789010 vpc-abcdefab012345678 subnet-22222222bbbbbbbbb i-01234567890123456 10.40.2.236 10.20.33.164 80 39812 6 19 IPv4 10.40.2.236 10.20.33.164 ACCEPT OK
```

The following line is the request traffic on `eni-11111111111111111`, a requester\-managed network interface for the transit gateway in subnet `subnet-11111111aaaaaaaaa`\. The flow log record therefore displays a '\-' symbol for the `instance-id` field\. The `srcaddr` field displays the private IP address of the transit gateway network interface, and the `pkt-srcaddr` field displays the source IP address of the client in VPC A\.

```
3 eni-11111111111111111 123456789010 vpc-abcdefab012345678 subnet-11111111aaaaaaaaa - 10.40.1.175 10.40.2.236 39812 80 6 3 IPv4 10.20.33.164 10.40.2.236 ACCEPT OK
```

The following line is the response traffic on `eni-22222222222222222`, a requester\-managed network interface for the transit gateway in subnet `subnet-22222222bbbbbbbbb`\. The `dstaddr` field displays the private IP address of the transit gateway network interface, and the `pkt-dstaddr` field displays the IP address of the client in VPC A\.

```
3 eni-22222222222222222 123456789010 vpc-abcdefab012345678 subnet-22222222bbbbbbbbb - 10.40.2.236 10.40.2.31 80 39812 6 19 IPv4 10.40.2.236 10.20.33.164 ACCEPT OK
```