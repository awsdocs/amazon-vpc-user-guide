# Reference prefix lists in your AWS resources<a name="managed-prefix-lists-referencing"></a>

You can reference a prefix list in the following AWS resources\.

**Topics**
+ [VPC security groups](#prefix-list-vpc-security-group)
+ [Subnet route tables](#prefix-list-subnet-route-table)
+ [Transit gateway route tables](#prefix-list-tgw-route-table)
+ [AWS Network Firewall rule groups](#prefix-list-nfw-rule-groups)
+ [Amazon Managed Grafana network access control](#prefix-list-grafana)

## VPC security groups<a name="prefix-list-vpc-security-group"></a>

You can specify a prefix list as the source for an inbound rule, or as the destination for an outbound rule\. For more information, see [Security groups](vpc-security-groups.md)\.

**To reference a prefix list in a security group rule using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update\. 

1. Choose **Actions**, **Edit inbound rules** or **Actions**, **Edit outbound rules**\.

1. Choose **Add rule**\. For **Type**, select the traffic type\. For **Source** \(inbound rules\) or **Destination** \(outbound rules\), choose the ID of the prefix list\. 

1. Choose **Save rules**\.

**To reference a prefix list in a security group rule using the AWS CLI**  
Use the [authorize\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) and [authorize\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-egress.html) commands\. For the `--ip-permissions` parameter, specify the ID of the prefix list using `PrefixListIds`\.

## Subnet route tables<a name="prefix-list-subnet-route-table"></a>

You can specify a prefix list as the destination for route table entry\. You cannot reference a prefix list in a gateway route table\. For more information about route tables, see [Configure route tables](VPC_Route_Tables.md)\.

**To reference a prefix list in a route table using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Route Tables**, and select the route table\.

1. Choose **Actions**, **Edit routes**\.

1. To add a route, choose **Add route**\.

1. For **Destination** enter the ID of a prefix list\. 

1. For **Target**, choose a target\.

1. Choose **Save changes**\.

**To reference a prefix list in a route table using the AWS CLI**  
Use the [create\-route](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route.html) \(AWS CLI\) command\. Use the `--destination-prefix-list-id` parameter to specify the ID of a prefix list\.

## Transit gateway route tables<a name="prefix-list-tgw-route-table"></a>

You can specify a prefix list as the destination for a route\. For more information, see [Prefix list references](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-prefix-lists.html) in *Amazon VPC Transit Gateways*\.

## AWS Network Firewall rule groups<a name="prefix-list-nfw-rule-groups"></a>

An AWS Network Firewall rule group is a reusable set of criteria for inspecting and handling network traffic\. If you create Suricata\-compatible stateful rule groups in AWS Network Firewall, you can reference a prefix list from the rule group\. For more information, see [Referencing Amazon VPC prefix lists](https://docs.aws.amazon.com/network-firewall/latest/developerguide/rule-groups-ip-set-references.html#rule-groups-referencing-prefix-lists) and [Creating a stateful rule group](https://docs.aws.amazon.com/network-firewall/latest/developerguide/rule-group-stateful-creating.html) in the *AWS Network Firewall Developer Guide*\.

## Amazon Managed Grafana network access control<a name="prefix-list-grafana"></a>

You can specify one or more prefix lists as an inbound rule for requests to Amazon Managed Grafana workspaces\. For more information about Grafana workspace network access control, including how to reference prefix lists, see [Managing network access](https://docs.aws.amazon.com/grafana/latest/userguide/AMG-configure-nac.html) in the *Amazon Managed Grafana User Guide*\.