# Controlling Access with Security Groups<a name="Overview.RDSSecurityGroups"></a>

Security groups control the access that traffic has in and out of a DB instance\. Aurora supports VPC security groups\.

## VPC Security Groups<a name="Overview.RDSSecurityGroups.VPCSec"></a>

Each VPC security group rule enables a specific source to access a DB instance in a VPC that is associated with that VPC security group\. The source can be a range of addresses \(for example, 203\.0\.113\.0/24\), or another VPC security group\. By specifying a VPC security group as the source, you allow incoming traffic from all instances \(typically application servers\) that use the source VPC security group\. VPC security groups can have rules that govern both inbound and outbound traffic, though the outbound traffic rules typically do not apply to DB instances\. Outbound traffic rules only apply if the DB instance acts as a client\. You must use the [Amazon EC2 API](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html) or the **Security Group** option on the VPC Console to create VPC security groups\. 

When you create rules for your VPC security group that allow access to the instances in your VPC, you must specify a port for each range of addresses that the rule allows access for\. For example, if you want to enable SSH access to instances in the VPC, then you create a rule allowing access to TCP port 22 for the specified range of addresses\.

You can configure multiple VPC security groups that allow access to different ports for different instances in your VPC\. For example, you can create a VPC security group that allows access to TCP port 80 for web servers in your VPC\. You can then create another VPC security group that allows access to TCP port 3306 for Aurora MySQL DB instances in your VPC\.

**Note**  
In an Aurora DB cluster, the VPC security group associated with the DB cluster is also associated with all of the DB instances in the DB cluster\. If you change the VPC security group for the DB cluster or for a DB instance, the change is applied automatically to all of the DB instances in the DB cluster\.

For more information on VPC security groups, see [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

## Security Group Scenario<a name="Overview.RDSSecurityGroups.Scenarios"></a>

A common use of a DB instance in a VPC is to share data with an application server running in an Amazon EC2 instance in the same VPC, which is accessed by a client application outside the VPC\. For this scenario, you use the RDS and VPC pages on the AWS Management Console or the RDS and EC2 API operations to create the necessary instances and security groups: 

1. Create a VPC security group \(for example, `sg-appsrv1`\) and define inbound rules that use the IP addresses of the client application as the source\. This security group allows your client application to connect to EC2 instances in a VPC that uses this security group\.

1. Create an EC2 instance for the application and add the EC2 instance to the VPC security group \(`sg-appsrv1`\) that you created in the previous step\. The EC2 instance in the VPC shares the VPC security group with the DB instance\.

1. Create a second VPC security group \(for example, `sg-dbsrv1`\) and create a new rule by specifying the VPC security group that you created in step 1 \(`sg-appsrv1`\) as the source\.

1. Create a new DB instance and add the DB instance to the VPC security group \(`sg-dbsrv1`\) that you created in the previous step\. When you create the DB instance, use the same port number as the one specified for the VPC security group \(`sg-dbsrv1`\) rule that you created in step 3\.

The following diagram shows this scenario\.

![\[DB instance and EC2 instance in a VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/con-VPC-sec-grp.png)

For more information about using a VPC, see [Amazon Virtual Private Cloud VPCs and Amazon Aurora](USER_VPC.md)\.

## Creating a VPC Security Group<a name="Overview.RDSSecurityGroups.Create"></a>

You can create a VPC security group for a DB instance by using the VPC console\. For information about creating a security group, see [Provide Access to the DB Cluster in the VPC by Creating a Security Group](CHAP_SettingUp_Aurora.md#CHAP_SettingUp_Aurora.SecurityGroup) and [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\.

## Associating a Security Group with a DB Instance<a name="Overview.RDSSecurityGroups.Associate"></a>

You can associate a security group with a DB instance by using **Modify** on the RDS console, the `ModifyDBInstance` Amazon RDS API, or the `modify-db-instance` AWS CLI command\.

 For information about modifying a DB instance in a DB cluster, see [Modify a DB Instance in a DB Cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\. For security group considerations when you restore a DB instance from a DB snapshot, see [Security Group Considerations](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Security)\.

## Associating a Security Group with a DB Cluster<a name="Overview.RDSSecurityGroups.AssociateWithCluster"></a>

You can associate a security group with a DB cluster by using **Modify cluster** on the RDS console, the `ModifyDBCluster` Amazon RDS API, or the `modify-db-cluster` AWS CLI command\.

For information about modifying a DB cluster, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.