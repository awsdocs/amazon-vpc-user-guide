# Example: Create an IPv6 VPC and subnets using the AWS CLI<a name="vpc-subnets-commands-example-ipv6"></a>

The following example uses AWS CLI commands to create a nondefault VPC with an IPv6 CIDR block, a public subnet, and a private subnet with outbound Internet access only\. After you've created the VPC and subnets, you can launch an instance in the public subnet and connect to it\. You can launch an instance in your private subnet and verify that it can connect to the Internet\. To begin, you must first install and configure the AWS CLI\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html)\.

**Topics**
+ [Step 1: Create a VPC and subnets](#vpc-subnets-commands-example-create-ipv6-vpc)
+ [Step 2: Configure a public subnet](#vpc-subnets-commands-example-public-subnet-ipv6)
+ [Step 3: Configure an egress\-only private subnet](#vpc-subnets-commands-example-private-subnet-ipv6)
+ [Step 4: Modify the IPv6 addressing behavior of the subnets](#vpc-subnets-commands-example-subnet-addressing)
+ [Step 5: Launch an instance into your public subnet](#vpc-subnets-commands-example-launch-instance-ipv6)
+ [Step 6: Launch an instance into your private subnet](#vpc-subnets-commands-example-launch-instance-ipv6-private)
+ [Step 7: Clean up](#vpc-subnets-commands-example-clean-up-ipv6)

## Step 1: Create a VPC and subnets<a name="vpc-subnets-commands-example-create-ipv6-vpc"></a>

The first step is to create a VPC and two subnets\. This example uses the IPv4 CIDR block `10.0.0.0/16` for the VPC, but you can choose a different CIDR block\. For more information, see [VPC and subnet sizing](VPC_Subnets.md#VPC_Sizing)\.

**To create a VPC and subnets using the AWS CLI**

1. Create a VPC with a `10.0.0.0/16` CIDR block and associate an IPv6 CIDR block with the VPC\. 

   ```
   aws ec2 create-vpc --cidr-block 10.0.0.0/16 --amazon-provided-ipv6-cidr-block
   ```

   In the output that's returned, take note of the VPC ID\. 

   ```
   {
       "Vpc": {
           "VpcId": "vpc-2f09a348", 
           ...
   }
   ```

1. Describe your VPC to get the IPv6 CIDR block that's associated with the VPC\. 

   ```
   aws ec2 describe-vpcs --vpc-id vpc-2f09a348
   ```

   ```
   {
       "Vpcs": [
           {
               ...
               "Ipv6CidrBlockAssociationSet": [
                   {
                       "Ipv6CidrBlock": "2001:db8:1234:1a00::/56", 
                       "AssociationId": "vpc-cidr-assoc-17a5407e", 
                       "Ipv6CidrBlockState": {
                           "State": "ASSOCIATED"
                       }
                   }
               ], 
               ...
   }
   ```

1. Create a subnet with a `10.0.0.0/24` IPv4 CIDR block and a `2001:db8:1234:1a00::/64` IPv6 CIDR block \(from the ranges that were returned in the previous step\)\.

   ```
   aws ec2 create-subnet --vpc-id vpc-2f09a348 --cidr-block 10.0.0.0/24 --ipv6-cidr-block 2001:db8:1234:1a00::/64
   ```

1. Create a second subnet in your VPC with a `10.0.1.0/24` IPv4 CIDR block and a `2001:db8:1234:1a01::/64` IPv6 CIDR block\.

   ```
   aws ec2 create-subnet --vpc-id vpc-2f09a348 --cidr-block 10.0.1.0/24 --ipv6-cidr-block 2001:db8:1234:1a01::/64
   ```

## Step 2: Configure a public subnet<a name="vpc-subnets-commands-example-public-subnet-ipv6"></a>

After you've created the VPC and subnets, you can make one of the subnets a public subnet by attaching an Internet gateway to your VPC, creating a custom route table, and configuring routing for the subnet to the Internet gateway\. In this example, a route table is created that routes all IPv4 traffic and IPv6 traffic to an Internet gateway\. 

**To make your subnet a public subnet**

1. Create an Internet gateway\.

   ```
   aws ec2 create-internet-gateway
   ```

   In the output that's returned, take note of the Internet gateway ID\.

   ```
   {
       "InternetGateway": {
           ...
           "InternetGatewayId": "igw-1ff7a07b", 
           ...
       }
   }
   ```

1. Using the ID from the previous step, attach the Internet gateway to your VPC\.

   ```
   aws ec2 attach-internet-gateway --vpc-id vpc-2f09a348 --internet-gateway-id igw-1ff7a07b
   ```

1. Create a custom route table for your VPC\.

   ```
   aws ec2 create-route-table --vpc-id vpc-2f09a348
   ```

   In the output that's returned, take note of the route table ID\.

   ```
   {
       "RouteTable": {
           ... 
           "RouteTableId": "rtb-c1c8faa6", 
           ...
       }
   }
   ```

1. Create a route in the route table that points all IPv6 traffic \(`::/0`\) to the Internet gateway\.

   ```
   aws ec2 create-route --route-table-id rtb-c1c8faa6 --destination-ipv6-cidr-block ::/0 --gateway-id igw-1ff7a07b
   ```
**Note**  
If you intend to use your public subnet for IPv4 traffic too, you need to add another route for `0.0.0.0/0` traffic that points to the Internet gateway\.

1. To confirm that your route has been created and is active, you can describe the route table and view the results\.

   ```
   aws ec2 describe-route-tables --route-table-id rtb-c1c8faa6
   ```

   ```
   {
       "RouteTables": [
           {
               "Associations": [], 
               "RouteTableId": "rtb-c1c8faa6", 
               "VpcId": "vpc-2f09a348", 
               "PropagatingVgws": [], 
               "Tags": [], 
               "Routes": [
                   {
                       "GatewayId": "local", 
                       "DestinationCidrBlock": "10.0.0.0/16", 
                       "State": "active", 
                       "Origin": "CreateRouteTable"
                   }, 
                   {
                       "GatewayId": "local", 
                       "Origin": "CreateRouteTable", 
                       "State": "active", 
                       "DestinationIpv6CidrBlock": "2001:db8:1234:1a00::/56"
                   }, 
                   {
                       "GatewayId": "igw-1ff7a07b", 
                       "Origin": "CreateRoute", 
                       "State": "active", 
                       "DestinationIpv6CidrBlock": "::/0"
                   }
               ]
           }
       ]
   }
   ```

1. The route table is not currently associated with any subnet\. Associate it with a subnet in your VPC so that traffic from that subnet is routed to the Internet gateway\. First, describe your subnets to get their IDs\. You can use the `--filter` option to return the subnets for your new VPC only, and the `--query` option to return only the subnet IDs and their IPv4 and IPv6 CIDR blocks\.

   ```
   aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-2f09a348" --query 'Subnets[*].{ID:SubnetId,IPv4CIDR:CidrBlock,IPv6CIDR:Ipv6CidrBlockAssociationSet[*].Ipv6CidrBlock}'
   ```

   ```
   [
       {
           "IPv6CIDR": [
               "2001:db8:1234:1a00::/64"
           ], 
           "ID": "subnet-b46032ec", 
           "IPv4CIDR": "10.0.0.0/24"
       }, 
       {
           "IPv6CIDR": [
               "2001:db8:1234:1a01::/64"
           ], 
           "ID": "subnet-a46032fc", 
           "IPv4CIDR": "10.0.1.0/24"
       }
   ]
   ```

1. You can choose which subnet to associate with the custom route table, for example, `subnet-b46032ec`\. This subnet will be your public subnet\.

   ```
   aws ec2 associate-route-table  --subnet-id subnet-b46032ec --route-table-id rtb-c1c8faa6
   ```

## Step 3: Configure an egress\-only private subnet<a name="vpc-subnets-commands-example-private-subnet-ipv6"></a>

You can configure the second subnet in your VPC to be an IPv6 egress\-only private subnet\. Instances that are launched in this subnet are able to access the Internet over IPv6 \(for example, to get software updates\) through an egress\-only Internet gateway, but hosts on the Internet cannot reach your instances\. 

**To make your subnet an egress\-only private subnet**

1. Create an egress\-only Internet gateway for your VPC\. In the output that's returned, take note of the gateway ID\.

   ```
   aws ec2 create-egress-only-internet-gateway --vpc-id vpc-2f09a348
   ```

   ```
   {
       "EgressOnlyInternetGateway": {
           "EgressOnlyInternetGatewayId": "eigw-015e0e244e24dfe8a", 
           "Attachments": [
               {
                   "State": "attached", 
                   "VpcId": "vpc-2f09a348"
               }
           ]
       }
   }
   ```

1. Create a custom route table for your VPC\. In the output that's returned, take note of the route table ID\.

   ```
   aws ec2 create-route-table --vpc-id vpc-2f09a348
   ```

1. Create a route in the route table that points all IPv6 traffic \(`::/0`\) to the egress\-only Internet gateway\.

   ```
   aws ec2 create-route --route-table-id rtb-abc123ab --destination-ipv6-cidr-block ::/0 --egress-only-internet-gateway-id eigw-015e0e244e24dfe8a
   ```

1. Associate the route table with the second subnet in your VPC \(you described the subnets in the previous section\)\. This subnet will be your private subnet with egress\-only IPv6 Internet access\.

   ```
   aws ec2 associate-route-table --subnet-id subnet-a46032fc --route-table-id rtb-abc123ab
   ```

## Step 4: Modify the IPv6 addressing behavior of the subnets<a name="vpc-subnets-commands-example-subnet-addressing"></a>

You can modify the IP addressing behavior of your subnets so that instances launched into the subnets automatically receive IPv6 addresses\. When you launch an instance into the subnet, a single IPv6 address is assigned from the range of the subnet to the primary network interface \(eth0\) of the instance\.

```
aws ec2 modify-subnet-attribute --subnet-id subnet-b46032ec --assign-ipv6-address-on-creation
```

```
aws ec2 modify-subnet-attribute --subnet-id subnet-a46032fc --assign-ipv6-address-on-creation
```

## Step 5: Launch an instance into your public subnet<a name="vpc-subnets-commands-example-launch-instance-ipv6"></a>

To test that your public subnet is public and that instances in the subnet are accessible from the Internet, launch an instance into your public subnet and connect to it\. First, you must create a security group to associate with your instance, and a key pair with which you'll connect to your instance\. For more information about security groups, see [Security groups for your VPC](VPC_SecurityGroups.md)\. For more information about key pairs, see [Amazon EC2 key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To launch and connect to an instance in your public subnet**

1. Create a key pair and use the `--query` option and the `--output` text option to pipe your private key directly into a file with the `.pem` extension\. 

   ```
   aws ec2 create-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem
   ```

   In this example, launch an Amazon Linux instance\. If you use an SSH client on a Linux or OS X operating system to connect to your instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 MyKeyPair.pem
   ```

1. Create a security group for your VPC, and add a rule that allows SSH access from any IPv6 address\. 

   ```
   aws ec2 create-security-group --group-name SSHAccess --description "Security group for SSH access" --vpc-id vpc-2f09a348
   ```

   ```
   {
       "GroupId": "sg-e1fb8c9a"
   }
   ```

   ```
   aws ec2 authorize-security-group-ingress --group-id sg-e1fb8c9a --ip-permissions '[{"IpProtocol": "tcp", "FromPort": 22, "ToPort": 22, "Ipv6Ranges": [{"CidrIpv6": "::/0"}]}]'
   ```
**Note**  
If you use `::/0`, you enable all IPv6 addresses to access your instance using SSH\. This is acceptable for this short exercise, but in production, authorize only a specific IP address or range of addresses to access your instance\.

1. Launch an instance into your public subnet, using the security group and key pair that you've created\. In the output, take note of the instance ID for your instance\.

   ```
   aws ec2 run-instances --image-id ami-0de53d8956e8dcf80 --count 1 --instance-type t2.micro --key-name MyKeyPair --security-group-ids sg-e1fb8c9a --subnet-id subnet-b46032ec
   ```
**Note**  
In this example, the AMI is an Amazon Linux AMI in the US East \(N\. Virginia\) region\. If you're in a different region, you need the AMI ID for a suitable AMI in your region\. For more information, see [Finding a Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Your instance must be in the `running` state in order to connect to it\. Describe your instance and confirm its state, and take note of its IPv6 address\.

   ```
   aws ec2 describe-instances --instance-id i-0146854b7443af453
   ```

   ```
   {
       "Reservations": [
           {
               ... 
               "Instances": [
                   {
                       ...
                       "State": {
                           "Code": 16, 
                           "Name": "running"
                       }, 
                       ...
                       "NetworkInterfaces": {
                           "Ipv6Addresses": {
                               "Ipv6Address": "2001:db8:1234:1a00::123"
                           } 
                       ...
                   }
               ]
           }
       ]
   }
   ```

1. When your instance is in the running state, you can connect to it using an SSH client on a Linux or OS X computer by using the following command\. Your local computer must have an IPv6 address configured\.

   ```
   ssh -i "MyKeyPair.pem" ec2-user@2001:db8:1234:1a00::123
   ```

   If you're connecting from a Windows computer, use the following instructions: [Connecting to your Linux instance from Windows using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html)\.

## Step 6: Launch an instance into your private subnet<a name="vpc-subnets-commands-example-launch-instance-ipv6-private"></a>

To test that instances in your egress\-only private subnet can access the Internet, launch an instance in your private subnet and connect to it using a bastion instance in your public subnet \(you can use the instance you launched in the previous section\)\. First, you must create a security group for the instance\. The security group must have a rule that allows your bastion instance to connect using SSH, and a rule that allows the `ping6` command \(ICMPv6 traffic\) to verify that the instance is not accessible from the Internet\.

1. Create a security group in your VPC, and add a rule that allows inbound SSH access from the IPv6 address of the instance in your public subnet, and a rule that allows all ICMPv6 traffic:

   ```
   aws ec2 create-security-group --group-name SSHAccessRestricted --description "Security group for SSH access from bastion" --vpc-id vpc-2f09a348
   ```

   ```
   {
       "GroupId": "sg-aabb1122"
   }
   ```

   ```
   aws ec2 authorize-security-group-ingress --group-id sg-aabb1122 --ip-permissions '[{"IpProtocol": "tcp", "FromPort": 22, "ToPort": 22, "Ipv6Ranges": [{"CidrIpv6": "2001:db8:1234:1a00::123/128"}]}]'
   ```

   ```
   aws ec2 authorize-security-group-ingress --group-id sg-aabb1122 --ip-permissions '[{"IpProtocol": "58", "FromPort": -1, "ToPort": -1, "Ipv6Ranges": [{"CidrIpv6": "::/0"}]}]'
   ```

1. Launch an instance into your private subnet, using the security group you've created and the same key pair you used to launch the instance in the public subnet\.

   ```
   aws ec2 run-instances --image-id ami-a4827dc9 --count 1 --instance-type t2.micro --key-name MyKeyPair --security-group-ids sg-aabb1122 --subnet-id subnet-a46032fc
   ```

   Use the `describe-instances` command to verify that your instance is running, and to get its IPv6 address\.

1. Configure SSH agent forwarding on your local machine, and then connect to your instance in the public subnet\. For Linux, use the following commands:

   ```
   ssh-add MyKeyPair.pem
   ```

   ```
   ssh -A ec2-user@2001:db8:1234:1a00::123
   ```

   For OS X, use the following commands: 

   ```
   ssh-add -K MyKeyPair.pem
   ```

   ```
   ssh -A ec2-user@2001:db8:1234:1a00::123
   ```

   For Windows, use the following instructions: [To configure SSH agent forwarding for Windows \(PuTTY\)](vpc-nat-gateway.md#ssh-forwarding-windows)\. Connect to the instance in the public subnet by using its IPv6 address\.

1. From your instance in the public subnet \(the bastion instance\), connect to your instance in the private subnet by using its IPv6 address:

   ```
   ssh ec2-user@2001:db8:1234:1a01::456
   ```

1. From your private instance, test that you can connect to the Internet by running the `ping6` command for a website that has ICMP enabled, for example:

   ```
   ping6 -n ietf.org
   ```

   ```
   PING ietf.org(2001:1900:3001:11::2c) 56 data bytes
   64 bytes from 2001:1900:3001:11::2c: icmp_seq=1 ttl=46 time=73.9 ms
   64 bytes from 2001:1900:3001:11::2c: icmp_seq=2 ttl=46 time=73.8 ms
   64 bytes from 2001:1900:3001:11::2c: icmp_seq=3 ttl=46 time=73.9 ms
   ...
   ```

1. To test that hosts on the Internet cannot reach your instance in the private subnet, use the `ping6` command from a computer that's enabled for IPv6\. You should get a timeout response\. If you get a valid response, then your instance is accessible from the Internet—check the route table that's associated with your private subnet and verify that it does not have a route for IPv6 traffic to an Internet gateway\.

   ```
   ping6 2001:db8:1234:1a01::456
   ```

## Step 7: Clean up<a name="vpc-subnets-commands-example-clean-up-ipv6"></a>

After you've verified that you can connect to your instance in the public subnet and that your instance in the private subnet can access the Internet, you can terminate the instances if you no longer need them\. To do this, use the [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) command\. To delete the other resources you've created in this example, use the following commands in their listed order:

1. Delete your security groups:

   ```
   aws ec2 delete-security-group --group-id sg-e1fb8c9a
   ```

   ```
   aws ec2 delete-security-group --group-id sg-aabb1122
   ```

1. Delete your subnets: 

   ```
   aws ec2 delete-subnet --subnet-id subnet-b46032ec
   ```

   ```
   aws ec2 delete-subnet --subnet-id subnet-a46032fc
   ```

1. Delete your custom route tables:

   ```
   aws ec2 delete-route-table --route-table-id rtb-c1c8faa6
   ```

   ```
   aws ec2 delete-route-table --route-table-id rtb-abc123ab
   ```

1. Detach your Internet gateway from your VPC: 

   ```
   aws ec2 detach-internet-gateway --internet-gateway-id igw-1ff7a07b --vpc-id vpc-2f09a348
   ```

1. Delete your Internet gateway: 

   ```
   aws ec2 delete-internet-gateway --internet-gateway-id igw-1ff7a07b
   ```

1. Delete your egress\-only Internet gateway:

   ```
   aws ec2 delete-egress-only-internet-gateway  --egress-only-internet-gateway-id eigw-015e0e244e24dfe8a
   ```

1. Delete your VPC: 

   ```
   aws ec2 delete-vpc --vpc-id vpc-2f09a348
   ```