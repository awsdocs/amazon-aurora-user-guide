# Working with a DB instance in a VPC<a name="USER_VPC.WorkingWithRDSInstanceinaVPC"></a>

Your DB instance is in a virtual private cloud \(VPC\)\. A VPC is a virtual network that is logically isolated from other virtual networks in the AWS Cloud\. Amazon VPC lets you launch AWS resources, such as an Amazon Aurora DB instance or Amazon EC2 instance, into a VPC\. The VPC can either be a default VPC that comes with your account or one that you create\. All VPCs are associated with your AWS account\. 

Your default VPC has three subnets you can use to isolate resources inside the VPC\. The default VPC also has an internet gateway that can be used to provide access to resources inside the VPC from outside the VPC\. 

For a list of scenarios involving Amazon Aurora DB instances in a VPC , see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\. 

To learn how to work with DB instances inside a VPC, see the following:

**Topics**
+ [Working with a DB instance in a VPC](#Overview.RDSVPC.Create)
+ [Working with DB subnet groups](#USER_VPC.Subnets)
+ [Hiding a DB instance in a VPC from the internet](#USER_VPC.Hiding)
+ [Creating a DB instance in a VPC](#USER_VPC.InstanceInVPC)

For a tutorial that shows you how to create a VPC that you can use with a common Amazon Aurora scenario, see [Tutorial: Create an Amazon VPC for use with a DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. 

**Note**  
Currently, Aurora doesn't support dual\-stack mode\.

## Working with a DB instance in a VPC<a name="Overview.RDSVPC.Create"></a>

Here are some tips on working with a DB instance in a VPC:
+ Your VPC must have at least two subnets\. These subnets must be in two different Availability Zones in the AWS Region where you want to deploy your DB instance\. A subnet is a segment of a VPC's IP address range that you can specify and that lets you group instances based on your security and operational needs\. 
+ If you want your DB instance in the VPC to be publicly accessible, you must enable the VPC attributes *DNS hostnames* and *DNS resolution*\. 
+ Your VPC must have a DB subnet group that you create \(for more information, see the next section\)\. You create a DB subnet group by specifying the subnets you created\. Amazon Aurora chooses a subnet and an IP address within that subnet to associate with your DB instance\. The DB instance uses the Availability Zone that contains the subnet\.
+ Your VPC must have a VPC security group that allows access to the DB instance\. 

  For more information, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md), and [Tutorial: Create an Amazon VPC for use with a DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.
+ The CIDR blocks in each of your subnets must be large enough to accommodate spare IP addresses for Amazon Aurora to use during maintenance activities, including failover and compute scaling\. For example, a range such as 10\.0\.0\.0/24 and 10\.0\.1\.0/24 is typically large enough\.
+  A VPC can have an *instance tenancy* attribute of either *default* or *dedicated*\. All default VPCs have the instance tenancy attribute set to default, and a default VPC can support any DB instance class\. 

  If you choose to have your DB instance in a dedicated VPC where the instance tenancy attribute is set to dedicated, the DB instance class of your DB instance must be one of the approved Amazon EC2 dedicated instance types\. For example, the m3\.medium EC2 dedicated instance corresponds to the db\.m3\.medium DB instance class\. For information about instance tenancy in a VPC, see [Dedicated instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the *Amazon Elastic Compute Cloud User Guide*\. 

  For more information about the instance types that can be in a dedicated instance, see [Amazon EC2 dedicated instances](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/) on the EC2 pricing page\. 
**Note**  
When you set the instance tenancy attribute to dedicated for an Amazon RDS DB instance, it doesn't guarantee that the DB instance will run on a dedicated host\.

## Working with DB subnet groups<a name="USER_VPC.Subnets"></a>

*Subnets* are segments of a VPC's IP address range that you designate to group your resources based on security and operational needs\. A DB subnet group is a collection of subnets \(typically private\) that you create in a VPC and that you then designate for your DB instances\. By using a DB subnet group, you can specify a particular VPC when creating DB instances using the CLI or API\. If you use the console, you can choose the VPC and subnet groups you want to use\. 

Each DB subnet group should have subnets in at least two Availability Zones in a given AWS Region\. When creating a DB instance in a VPC, make sure to choose a DB subnet group\. From the DB subnet group, Amazon Aurora chooses a subnet and an IP address within that subnet to associate with your DB instance\. The DB instance uses the Availability Zone that contains the subnet\. If the primary DB instance of a Multi\-AZ deployment fails, Amazon Aurora can promote the corresponding standby and later create a new standby using an IP address of the subnet in one of the other Availability Zones\.

The subnets in a DB subnet group are either public or private\. The subnets are public or private, depending on the configuration that you set for their network access control lists \(network ACLs\) and routing tables\. For a DB instance to be publicly accessible, all of the subnets in its DB subnet group must be public\. If a subnet that's associated with a publicly accessible DB instance changes from public to private, it can affect DB instance availability\.

When Amazon Aurora creates a DB instance in a VPC, it assigns a network interface to your DB instance by using an IP address from your DB subnet group\. However, we strongly recommend that you use the Domain Name System \(DNS\) name to connect to your DB instance because the underlying IP address changes during failover\. 

**Note**  
For each DB instance that you run in a VPC, make sure to reserve at least one address in each subnet in the DB subnet group for use by Amazon Aurora for recovery actions\. 

## Hiding a DB instance in a VPC from the internet<a name="USER_VPC.Hiding"></a>

One common Amazon Aurora scenario is to have a VPC in which you have an EC2 instance with a public\-facing web application and a DB instance with a database that is not publicly accessible\. For example, you can create a VPC that has a public subnet and a private subnet\. Amazon EC2 instances that function as web servers can be deployed in the public subnet, and the DB instances are deployed in the private subnet\. In such a deployment, only the web servers have access to the DB instances\. For an illustration of this scenario, see [A DB instance in a VPC accessed by an EC2 instance in the same VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario1)\. 

When you launch a DB instance inside a VPC, the DB instance has a private IP address for traffic inside the VPC\. This private IP address isn't publicly accessible\. You can use the *Public access* option to designate whether the DB instance also has a public IP address in addition to the private IP address\. If the DB instance is designated as publicly accessible, its DNS endpoint resolves to the private IP address from within the DB instance's VPC, and to the public IP address from outside of the DB instance's VPC\. Access to the DB instance is ultimately controlled by the security group it uses, and that public access is not permitted if the security group assigned to the DB instance doesn't include inbound rules that permit it\. In addition, for a DB instance to be publicly accessible, the subnets in its DB subnet group must have an internet gateway\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)

You can modify a DB instance to turn on or off public accessibility by modifying the *Public access* option\. For more information, see the modifying section for your DB engine\.

The following illustration shows the **Public access** option in the **Additional connectivity configuration** section\. To set the option, open the **Additional connectivity configuration** section in the **Connectivity** section\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/VPC-example4.png)

For information about modifying a DB instance to set the **Public access** option, see [Modify a DB instance in a DB cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\.

## Creating a DB instance in a VPC<a name="USER_VPC.InstanceInVPC"></a>

The following procedures help you create a DB instance in a VPC\. If your account has a default VPC and you want to use it, you can begin with step 2\. The VPC and DB subnet group have already been created for you in the default VPC\. If you want to create an additional VPC, you can start with step 1 and create a new VPC\. 

**Note**  
If you want your DB instance in the VPC to be publicly accessible, you must update the DNS information for the VPC by enabling the VPC attributes *DNS hostnames* and *DNS resolution*\. For information about updating the DNS information for a VPC instance, see [Updating DNS support for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html)\. 

Follow these steps to create a DB instance in a VPC:
+ [Step 1: Create a VPC](#USER_VPC.CreatingVPC) 
+  [Step 2: Create a DB subnet group](#USER_VPC.CreateDBSubnetGroup)
+  [Step 3: Create a VPC security group](#USER_VPC.CreateVPCSecurityGroup)
+  [Step 4: Create a DB instance in the VPC](#USER_VPC.CreateDBInstanceInVPC) 

### Step 1: Create a VPC<a name="USER_VPC.CreatingVPC"></a>

If your AWS account does not have a default VPC, or if you want to create an additional VPC, follow the instructions for creating a new VPC\. The VPC must have subnets in at least two Availability Zones\. You use these subnets when you create a DB subnet group\. If you have a default VPC, a subnet is automatically created for you in each Availability Zone in the AWS Region\.

For more information, see [Create a VPC with private and public subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets), or see [Create a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#Create-VPC) in the *Amazon VPC User Guide*\. 

### Step 2: Create a DB subnet group<a name="USER_VPC.CreateDBSubnetGroup"></a>

A DB subnet group is a collection of subnets \(typically private\) that you create for a VPC and that you then designate for your DB instances\. A DB subnet group allows you to specify a particular VPC when you create DB instances using the CLI or API\. If you use the console, you can just choose the VPC and subnets you want to use\. Each DB subnet group must have at least one subnet in at least two Availability Zones in the AWS Region\. 

 For a DB instance to be publicly accessible, the subnets in the DB subnet group must have an internet gateway\. For more information about internet gateways for subnets, see [ Connect to the internet using an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\. 

When you create a DB instance in a VPC, make sure to choose a DB subnet group\. Amazon Aurora chooses a subnet and an IP address within that subnet to associate with your DB instance\. If no DB subnet groups exist, Amazon Aurora, creates a default subnet group when you create a DB cluster\. Amazon Aurora creates and associates an Elastic Network Interface to your DB instance with that IP address\. The DB instance uses the Availability Zone that contains the subnet\. For Multi\-AZ deployments, defining a subnet for two or more Availability Zones in an AWS Region allows Amazon Aurora to create a new standby in another Availability Zone should the need arise\. You need to do this even for Single\-AZ deployments, just in case you want to convert them to Multi\-AZ deployments at some point\. 

In this step, you create a DB subnet group and add the subnets that you created for your VPC\. 

**To create a DB subnet group**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Subnet groups**\.

1. Choose **Create DB Subnet Group**\.

1. For **Name**, type the name of your DB subnet group\.

1. For **Description**, type a description for your DB subnet group\. 

1.  For **VPC**, choose the VPC that you created\. 

1. In the **Add subnets** section, choose the Availability Zones that include the subnets from **Availability Zones**, and then choose the subnets from **Subnets**\.  
![\[Create DB Subnet Group button\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/RDSVPC101.png)

1. Choose **Create**\. 

    Your new DB subnet group appears in the DB subnet groups list on the RDS console\. You can choose the DB subnet group to see details, including all of the subnets associated with the group, in the details pane at the bottom of the window\. 

### Step 3: Create a VPC security group<a name="USER_VPC.CreateVPCSecurityGroup"></a>

Before you create your DB instance, you can create a VPC security group to associate with your DB instance\. If you don't create a VPC security group, you can use the default security group when you create a DB cluster\. For instructions on how to create a security group for your DB instance, see [Create a VPC security group for a private DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB), or see [Control traffic to resources using security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon VPC User Guide*\.\. 

### Step 4: Create a DB instance in the VPC<a name="USER_VPC.CreateDBInstanceInVPC"></a>

In this step, you create a DB instance and use the VPC name, the DB subnet group, and the VPC security group you created in the previous steps\. 

**Note**  
If you want your DB instance in the VPC to be publicly accessible, you must enable the VPC attributes *DNS hostnames* and *DNS resolution*\. For information on updating the DNS information for a VPC instance, see [DNS attributes for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\. 

For details on how to create a DB instance, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md) \.

When prompted in the **Connectivity** section, enter the VPC name, the DB subnet group, and the VPC security group you created in the previous steps\.

**Note**  
Updating VPCs is not currently supported for Aurora clusters\.