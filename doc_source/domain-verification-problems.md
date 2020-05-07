# Troubleshooting common private DNS domain verification problems<a name="domain-verification-problems"></a>

To verify an endpoint service private DNS domain name with Amazon VPC, you initiate the process using either the Amazon VPC console or the API\. This section contains information that can help you resolve issues with the verification process\.

## Common domain verification problems<a name="domain-verification-common-problems"></a>

If you attempt to verify a domain and you encounter problems, review the possible causes and solutions below\.
+ You're attempting to verify a domain that you don't own\. You can't verify a domain unless you own it\. 
+ Your DNS provider doesn't allow underscores in TXT record names\. Some DNS providers don't allow you to include the underscore character in the DNS record names for your domain\. If this is true for your provider, you can omit *\_amazovpc* from the name of the TXT record\.
+ Your DNS provider appended the domain name to the end of the TXT record\. Some DNS providers automatically append the name of your domain to the attribute name of the TXT record\. For example, if you create a record where the attribute name is *\_amazonvpc\.example\.com*, the provider might append the domain name, resulting in *\_amazonvpc\.example\.com\.example\.com*\)\. To avoid duplication of the domain name, add a period to the end of the domain name when you create the TXT record\. This step tells your DNS provider that it isn't necessary to append the domain name to the TXT record\.
+ Your DNS provider modified the DNS record value\. Some providers automatically modify DNS record values to use only lowercase letters\. We only verify your domain when it detects a verification record for which the attribute value exactly matches the value that we provided when you started the domain ownership verification process\. If the DNS provider for your domain changes your TXT record values to use only lowercase letters, contact the DNS provider for additional assistance\.
+ You want to verify the same domain multiple times\. You might need to verify your domain more than once because you're sending in different AWS Regions, or because you're using the same domain to send from multiple AWS accounts\. If your DNS provider doesn't allow you to have more than one TXT record with the same attribute name, you might still be able to verify two domains\. If your DNS provider allows it, you can assign multiple attribute values to the same TXT record\. For example, if your DNS is managed by Amazon Route 53, you can set up multiple values for the same TXT record by completing the following steps: 

  1. In the Route 53 console, choose the TXT record that you created when you verified your domain in the first Region\.

  1. In the **Value** box, go to the end of the existing attribute value, and then press Enter\.

  1. Add the attribute value for the additional Region, and then save the record set\.

  If your DNS provider doesn't allow you to assign multiple values to the same TXT record, you can verify the domain once with the value in the attribute name of the TXT record, and another time with the value removed from the attribute name\. For example, you verify with “\_asnbcdasd”, and then with "asnbcdasd”\. The downside of this solution is that you can only verify the same domain two times\.

## How to check domain verification settings<a name="domain-verification-check-dns"></a>

You can verify that your private DNS name domain ownership verification TXT record is published correctly to your DNS server by using the following procedure\. This procedure uses the [nslookup](http://en.wikipedia.org/wiki/Nslookup) tool, which is available for Windows and Linux\. On Linux, you can also use [dig](http://en.wikipedia.org/wiki/Dig_(command))\.

The commands in these instructions are executed on Windows 7, and the example domain we use is *example\.com*\.

In this procedure, you first find the DNS servers that serve your domain, and then query those servers to view the TXT records\. You query the DNS servers that serve your domain because those servers contain the most up\-to\-date information for your domain, which can take time to propagate to other DNS servers\.

**To verify that your domain ownership verification TXT record is published to your DNS server**

1. Find the name servers for your domain by taking the following steps\.

   1. Go to the command line\. To get to the command line on Windows 7, choose **Start** and then enter **cmd**\. On Linux\-based operating systems, open a terminal window\.

   1. At the command prompt, enter the following, where *<domain>* is your domain\.

      ```
      1. nslookup -type=NS <domain>
      ```

      For example, if your domain was *example\.com*, the command would look like the following\.

      ```
      1. nslookup -type=NS example.com
      ```

      The command's output will list the name servers that serve your domain\. You will query one of these servers in the next step\.

1. Verify that the TXT record is correctly published by taking the following steps\. 

   1. At the command prompt, enter the following, where *<domain>* is your domain, and *<name server>* is one of the name servers you found in step 1\.

      ```
      1. nslookup -type=TXT  _aksldja21i1.<domain> <name server>
      ```

      In our *\_aksldja21i1\.example\.com* example, if a name server that we found in step 1 was called *ns1\.name\-server\.net*, we would enter the following\.

      ```
      1. nslookup -type=TXT  _aksldja21i1.example.com ns1.name-server.net
      ```

   1. In the output of the command, verify that the string that follows `text = ` matches the TXT value you see when you choose the domain in the Identities list of the Amazon VPC console\. 

      In our example, we are looking for a TXT record under *\_aksldja21i1\.example\.com* with a value of `asjdakjshd78126eu21`\. If the record is correctly published, we would expect the command to have the following output\.

      ```
      1. _aksldja21i1.example.com text = "asjdakjshd78126eu21"
      ```