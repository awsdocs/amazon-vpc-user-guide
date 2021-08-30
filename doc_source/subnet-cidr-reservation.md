# Subnet CIDR reservations<a name="subnet-cidr-reservation"></a>

A *subnet CIDR reservation* is a range of IPv4 or IPv6 addresses in a subnet\. When you create the reservation, you specify how you will use the reserved range\. The following options are available:
+ **Prefix** — You can assign IP addresses to network interfaces that are associated with an instance\. For more information, see [Assigning prefixes to Amazon EC2 network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-prefix-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **Explicit** — AWS does not use the IP addresses\. You manually assign the IP addresses to resources that reside in your subnet\.

The following rules apply to subnet CIDR reservations:
+ You can reserve multiple CIDR ranges per subnet\. The reservation types for each range can both be the same type \(for example **prefix**\), or different \(for example, **prefix** and **explicit**\)\.
+ When you reserve multiple CIDR ranges within the same VPC, the CIDR ranges cannot overlap\.
+ When you reserve more than one range in a subnet for Prefix Delegation, and Prefix Delegation is configured for automatic assignment, we randomly choose an IP address to assign to the network interface\.
+ When you remove a reservation, the IP addresses that are assigned to resources are not changed\. Only the IP addresses that are not in use become available\.

## Work with subnet CIDR reservations<a name="work-with-subnet-cidr-reservations"></a>

You can use the AWS CLI to create and manage subnet CIDR reservations\.

**Topics**
+ [Create a subnet CIDR reservation](#Create-subnet-cidr-reservations)
+ [View subnet CIDR reservations](#view-subnet-cidr-reservations)
+ [Delete a subnet CIDR reservation](#delete-subnet-cidr-reservations)

### Create a subnet CIDR reservation<a name="Create-subnet-cidr-reservations"></a>

You can use [create\-subnet\-cidr\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-subnet-cidr-reservation.html) to create a subnet CIDR reservation\.

```
aws ec2 create-subnet-cidr-reservation --subnet-id subnet-03c51e2eEXAMPLE --reservation-type prefix --cidr 2600:1f13:925:d240:3a1b::/80
```

The following is example output\.

```
{
    "SubnetCidrReservation": {
        "SubnetCidrReservationId": "scr-044f977c4eEXAMPLE",
        "SubnetId": "subnet-03c51e2ef5EXAMPLE",
        "Cidr": "2600:1f13:925:d240:3a1b::/80",
        "ReservationType": "prefix",
        "OwnerId": "123456789012"
    }
}
```

### View subnet CIDR reservations<a name="view-subnet-cidr-reservations"></a>

You can use [get\-subnet\-cidr\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-subnet-cidr-reservations.html) to view the details of a subnet CIDR reservation\.

```
aws ec2 get-subnet-cidr-reservations --subnet-id subnet-05eef9fb78EXAMPLE
```

### Delete a subnet CIDR reservation<a name="delete-subnet-cidr-reservations"></a>

You can use [delete\-subnet\-cidr\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-subnet-cidr-reservation.html) to delete a subnet CIDR reservation\.

```
aws ec2 delete-subnet-cidr-reservation --subnet-cidr-reservation-id scr-044f977c4eEXAMPLE
```