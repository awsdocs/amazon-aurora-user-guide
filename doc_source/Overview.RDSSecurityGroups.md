# Controlling Access with Security Groups<a name="Overview.RDSSecurityGroups"></a>

Security groups control the access that traffic has in and out of a DB instance\. Two types of security groups are used with Aurora: VPC security groups and Amazon EC2 security groups\. In simple terms, these work as follows:
+ A VPC security group controls access to DB instances and EC2 instances inside a VPC\.
+ An EC2 security group controls access to an EC2 instance\.

By default, network access is turned off to a DB instance\. You can specify rules in a security group that allows access from an IP address range, port, or EC2 security group\. Once ingress rules are configured, the same rules apply to all DB instances that are associated with that security group\. You can specify up to 20 rules in a security group\.

## VPC Security Groups<a name="Overview.RDSSecurityGroups.VPCSec"></a>

Each VPC security group rule enables a specific source to access a DB instance in a VPC that is associated with that VPC security group\. The source can be a range of addresses \(for example, 203\.0\.113\.0/24\), or another VPC security group\. By specifying a VPC security group as the source, you allow incoming traffic from all instances \(typically application servers\) that use the source VPC security group\. VPC security groups can have rules that govern both inbound and outbound traffic, though the outbound traffic rules typically do not apply to DB instances\. Outbound traffic rules only apply if the DB instance acts as a client\. You must use the [Amazon EC2 API](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html) or the **Security Group** option on the VPC Console to create VPC security groups\. 

When you create rules for your VPC security group that allow access to the instances in your VPC, you must specify a port for each range of addresses that the rule allows access for\. For example, if you want to enable SSH access to instances in the VPC, then you create a rule allowing access to TCP port 22 for the specified range of addresses\.

You can configure multiple VPC security groups that allow access to different ports for different instances in your VPC\. For example, you can create a VPC security group that allows access to TCP port 80 for web servers in your VPC\. You can then create another VPC security group that allows access to TCP port 3306 for RDS MySQL DB instances in your VPC\.

**Note**  
In an Aurora DB cluster, the VPC security group associated with the DB cluster is also associated with all of the DB instances in the DB cluster\. If you change the VPC security group for the DB cluster or for a DB instance, the change is applied automatically to all of the DB instances in the DB cluster\.

For more information on VPC security groups, see [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

## Security Group Scenario<a name="Overview.RDSSecurityGroups.Scenarios"></a>

A common use of an RDS instance in a VPC is to share data with an application server running in an Amazon EC2 instance in the same VPC, which is accessed by a client application outside the VPC\. For this scenario, you use the RDS and VPC pages on the AWS Management Console or the RDS and EC2 API actions to create the necessary instances and security groups: 

1. Create a VPC security group \(for example, `sg-appsrv1`\) and define inbound rules that use the IP addresses of the client application as the source\. This security group allows your client application to connect to EC2 instances in a VPC that uses this security group\.

1. Create an EC2 instance for the application and add the EC2 instance to the VPC security group \(`sg-appsrv1`\) that you created in the previous step\. The EC2 instance in the VPC shares the VPC security group with the DB instance\.

1. Create a second VPC security group \(for example, `sg-dbsrv1`\) and create a new rule by specifying the VPC security group that you created in step 1 \(`sg-appsrv1`\) as the source\.

1. Create a new DB instance and add the DB instance to the VPC security group \(`sg-dbsrv1`\) that you created in the previous step\. When you create the instance, use the same port number as the one specified for the VPC security group \(`sg-dbsrv1`\) rule that you created in step 3\.

The following diagram shows this scenario\.

![\[VPC and EC2 security group scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/con-VPC-sec-grp.png)

For more information about using a VPC, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.

## Creating a VPC Security Group<a name="Overview.RDSSecurityGroups.Create"></a>

You can create a VPC security group for a DB instance by using the VPC console\. For information about creating a security group, see [Provide Access to the DB Cluster in the VPC by Creating a Security Group](CHAP_SettingUp_Aurora.md#CHAP_SettingUp_Aurora.SecurityGroup) and [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\.

## Associating a Security Group with a DB Instance<a name="Overview.RDSSecurityGroups.Associate"></a>

You can associate a security group with a DB instance by using **Modify** on the RDS console, the `ModifyDBInstance` Amazon RDS API, or the `modify-db-instance` AWS CLI command\.

 For information about modifying a DB instance in a DB cluster, see [Modify a DB Instance in a DB Cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\. For security group considerations when you restore a DB instance from a DB snapshot, see [Security Group Considerations](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Security)\.

## Associating a Security Group with a DB Cluster<a name="Overview.RDSSecurityGroups.AssociateWithCluster"></a>

You can associate a security group with a DB cluster by using **Modify cluster** on the RDS console, the `ModifyDBCluster` Amazon RDS API, or the `modify-db-cluster` AWS CLI command\.

For information about modifying a DB cluster, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.

## Deleting DB VPC Security Groups<a name="Overview.RDSSecurityGroups.DeleteDBVPCGroups"></a>

DB VPC security groups are an RDS mechanism to synchronize security information with a VPC security group\. However, this synchronization is no longer required, because RDS has been updated to use VPC security group information directly\.

**Note**  
DB VPC security groups are deprecated, and they are different from DB security groups, VPC security groups, and EC2 security groups\.

We strongly recommend that you delete any DB VPC security groups that you currently use\. If you don't delete your DB VPC security groups, you might encounter unintended behaviors with your RDS DB instances, which can be as severe as losing access to a DB instance\. The unintended behaviors are a result of an action such as an update to a DB instance, an option group, or similar\. Such updates cause RDS to resynchronize the DB VPC security group with the VPC security group\. This resynchronization can result in your security information being overwritten with incorrect and outdated security information\. This result can have a severe impact on your access to your RDS DB instances\.

### How Can I Determine If I Have a DB VPC Security Group?<a name="w4aac16c21c35b8"></a>

Because DB VPC security groups have been deprecated, they don't appear in the RDS console\. However, you can call the [describe\-db\-security\-groups](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-security-groups.html) AWS CLI command or the [DescribeDBSecurityGroups](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBSecurityGroups.html) API action to determine if you have any DB VPC security groups\.

In this case, you can call the `describe-db-security-groups` AWS CLI command with JSON specified as the output format\. If you do, you can identify DB VPC security groups by the VPC identifier on the second line of the output for the security group as shown in the following example\.

```
{
    "DBSecurityGroups": [
        {
            "VpcId": "vpc-abcd1234",
            "DBSecurityGroupDescription": "default:vpc-abcd1234",
            "IPRanges": [
                {
                    "Status": "authorized",
                    "CIDRIP": "xxx.xxx.xxx.xxx/n"
                },
                {
                    "Status": "authorized",
                    "CIDRIP": "xxx.xxx.xxx.xxx/n "
                }
            ],
            "OwnerId": "123456789012",
            "EC2SecurityGroups": [],
            "DBSecurityGroupName": "default:vpc-abcd1234"
        }
    ]
}
```

If you run the `DescribeDBSecurityGroups` API action, then you can identify DB VPC security groups using the <VpcId> response element as shown in the following example\.

```
<DBSecurityGroup>
  <EC2SecurityGroups/>
  <DBSecurityGroupDescription>default:vpc-abcd1234</DBSecurityGroupDescription>
  <IPRanges>
    <IPRange>
      <CIDRIP>xxx.xxx.xxx.xxx/n</CIDRIP>
      <Status>authorized</Status>
    </IPRange>
    <IPRange>
      <CIDRIP>xxx.xxx.xxx.xxx/n</CIDRIP>
      <Status>authorized</Status>
    </IPRange>
  </IPRanges>
  <VpcId>vpc-abcd1234</VpcId>
  <OwnerId>123456789012</OwnerId>
  <DBSecurityGroupName>default:vpc-abcd1234</DBSecurityGroupName>
</DBSecurityGroup>
```

### How Do I Delete a DB VPC Security Group?<a name="w4aac16c21c35c10"></a>

Because DB VPC security groups don't appear in the RDS console, you must call the [delete\-db\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-security-group.html) AWS CLI command or the [DeleteDBSecurityGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBSecurityGroup.html) API action to delete a DB VPC security group\.

After you delete a DB VPC security group, your DB instances in your VPC continue to be secured by the VPC security group for that VPC\. The DB VPC security group that was deleted was merely a copy of the VPC security group information\.

### Review Your AWS CloudFormation Templates<a name="w4aac16c21c35c12"></a>

Older versions of AWS CloudFormation templates can contain instructions to create a DB VPC security group\. Because DB VPC security groups are not yet fully deprecated, they can still be created\. Make sure that any AWS CloudFormation templates that you use to provision a DB instance with security settings don't also create a DB VPC security group\. Don't use AWS CloudFormation templates that create an RDS `DBSecurityGroup` with an `EC2VpcId` as shown in the following example\.

```
{
  "DbSecurityByEC2SecurityGroup" : {
    "Type" : "AWS::RDS::DBSecurityGroup",
    "Properties" : {
       "GroupDescription" : "Ingress for Amazon EC2 security group",
       "EC2VpcId" : "MyVPC",
       "DBSecurityGroupIngress" : [ {
          "EC2SecurityGroupId" : "sg-b0ff1111",
          "EC2SecurityGroupOwnerId" : "111122223333"
       }, {
          "EC2SecurityGroupId" : "sg-ffd722222",
          "EC2SecurityGroupOwnerId" : "111122223333"
       } ]
    }
  }
}
```

Instead, add security information for your RDS DB instances in a VPC using VPC security groups, as shown in the following example\.

```
{
  "DBInstance" : {
    "Type": "AWS::RDS::DBInstance",
    "Properties": {
      "DBName"            : { "Ref" : "DBName" },
      "Engine"            : "MySQL",
      "MultiAZ"           : { "Ref": "MultiAZDatabase" },
      "MasterUsername"    : { "Ref" : "<master_username>" },
      "DBInstanceClass"   : { "Ref" : "DBClass" },
      "AllocatedStorage"  : { "Ref" : "DBAllocatedStorage" },
      "MasterUserPassword": { "Ref" : "<master_password>" },
      "VPCSecurityGroups" : [ { "Fn::GetAtt": [ "VPCSecurityGroup", "GroupId" ] } ]
    }
  }
}
```