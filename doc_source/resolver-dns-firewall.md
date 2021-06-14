# Route 53 Resolver DNS Firewall<a name="resolver-dns-firewall"></a>

With DNS Firewall, you define domain name filtering rules in rule groups that you associate with your VPCs\. You can specify lists of domain names to allow or block, and you can customize the responses for the DNS queries that you block\. For more information, see the [Route 53 Resolver DNS Firewall Documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall.html)\.

You implement DNS Firewall with the following AWS resources\. 


| DNS Firewall resource | Description | 
| --- | --- | 
| DNS Firewall rule group | A DNS Firewall rule group is a named, reusable collection of DNS Firewall rules for filtering DNS queries\. You populate the rule group with the filtering rules, then associate the rule group with one or more VPCs from Amazon VPC\. When you associate a rule group with a VPC, you enable DNS Firewall filtering for the VPC\. Then, when Resolver receives a DNS query for a VPC that has a rule group associated with it, Resolver passes the query to DNS Firewall for filtering\.Each rule within the rule group specifies one domain list and an action to take on DNS queries whose domains match the domain specifications in the list\. You can allow, block, or alert on matching queries\. You can also define custom responses for blocked queries\. For more information, see [Rule groups and rules in Route 53 Resolver DNS Firewall](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-rule-groups.html)\. | 
| Domain list | A domain list is a reusable set of domain specifications that you use in a DNS Firewall rule, inside a rule group\.For more information, see [Domain lists in Route 53 Resolver DNS Firewall](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-domain-lists.html)\. | 

You can also use AWS Firewall Manager to centrally configure and manage DNS Firewall resources across your accounts and organizations in AWS Organizations\. You can manage firewalls for multiple accounts using a single account in Firewall Manager\. For more information, see [AWS Firewall Manager](https://docs.aws.amazon.com/waf/latest/developerguide/fms-chapter.html) in the *AWS WAF, AWS Firewall Manager, and AWS Shield Advanced Developer Guide*\.