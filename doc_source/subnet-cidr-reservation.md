# Subnet CIDR reservations<a name="subnet-cidr-reservation"></a>

A *subnet CIDR reservation* is a range of IPv4 or IPv6 addresses that you set aside so that AWS can't assign them to your network interfaces\. This enables you to reserve IPv4 or IPv6 CIDRs \(also called "prefixes"\) for use with your network interfaces\.

When you create a subnet CIDR reservation, you specify how you will use the reserved IP addresses\. The following options are available:
+ **Prefix** — AWS assigns addresses from the reserved IP address range to network interfaces\. For more information, see [Assigning prefixes to Amazon EC2 network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-prefix-eni.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ **Explicit** — You manually assign IP addresses to network interfaces\.

The following rules apply to subnet CIDR reservations:
+ When you create a subnet CIDR reservation, the IP address range can include addresses that are already in use\. Creating a subnet reservation does not unassign any IP addresses that are already in use\.
+ You can reserve multiple CIDR ranges per subnet\. When you reserve multiple CIDR ranges within the same VPC, the CIDR ranges cannot overlap\.
+ When you reserve more than one range in a subnet for Prefix Delegation, and Prefix Delegation is configured for automatic assignment, we choose the IP addresses to assign to network interfaces at random\.
+ When you delete a subnet reservation, the unused IP addresses are available for AWS to assign to your network interfaces\. Deleting a subnet reservation does not unassign any IP addresses that are in use\.

## Work with subnet CIDR reservations using the console<a name="edit-subnet-cidr-reservations"></a>

You can create and manage subnet CIDR reservations as follows\.

**To edit subnet CIDR reservations**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select the subnet\.

1. Choose **Actions**, **Edit CIDR reservations** and do the following:
   + To add an IPv4 CIDR reservation, choose **IPv4**, **Add IPv4 CIDR reservation**\. Choose the reservation type, enter the CIDR range, and choose **Add**\.
   + To add an IPv6 CIDR reservation, choose **IPv6**, **Add IPv6 CIDR reservation**\. Choose the reservation type, enter the CIDR range, and choose **Add**\.
   + To remove a CIDR reservation, choose **Remove** at the end of the entry\.

## Work with subnet CIDR reservations using the AWS CLI<a name="work-with-subnet-cidr-reservations"></a>

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