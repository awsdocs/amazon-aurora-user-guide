# How to Create a VPC for Use with Amazon Aurora<a name="Aurora.CreateVPC"></a>

The following sections discuss how to create a VPC for use with Amazon Aurora\.

**Note**  
For a helpful and detailed guide on connecting to an Amazon Aurora DB cluster, you can see [Aurora MySQL Database Administrator's Handbook – Connection Management](https://d1.awsstatic.com/whitepapers/RDS/amazon-aurora-mysql-database-administrator-handbook.pdf)\.

## Create a VPC and Subnets<a name="CHAP_Aurora.CreateVPC"></a>

You can only create an Amazon Aurora DB cluster in a Virtual Private Cloud \(VPC\) that spans two Availability Zones, and each zone must contain at least one subnet\. You can create an Aurora DB cluster in the default VPC for your AWS account, or you can create a user\-defined VPC\. For information, see [Amazon Virtual Private Cloud VPCs and Amazon Aurora](USER_VPC.md)\.

Amazon Aurora optionally can create a VPC and subnet group for you to use with your DB cluster\. Doing this can be helpful if you have never created a VPC, or if you would like to create a new VPC that is separate from your other VPCs\. If you want Amazon Aurora to create a VPC and subnet group for you, then skip this procedure and see [Create an Aurora MySQL DB Cluster](CHAP_GettingStartedAurora.CreatingConnecting.Aurora.md#CHAP_GettingStarted.Aurora.CreateDBCluster)\.

**Note**  
All VPC and EC2 resources that you use with your Aurora DB cluster must be in one of the regions listed in [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

**To create a VPC for use with an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region to create your VPC in\. This example uses the US East \(Ohio\) Region\. 

1. In the upper\-left corner, choose **VPC Dashboard**\. Choose **Start VPC Wizard** to begin creating a VPC\.

1. In the Create VPC wizard, choose **VPC with a Single Public Subnet**\. Choose **Select**\.  
![\[Begin the Create a VPC Wizard\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCreateVPC01.png)

1. Set the following values in the **Create VPC** panel:
   + **IP CIDR block:** `10.0.0.0/16`
   + **VPC name:** `gs-cluster-vpc`
   + **Public subnet:** `10.0.0.0/24`
   + **Availability Zone:** `us-east-1a`
   + **Subnet name:** `gs-subnet1`
   + **Enable DNS hostnames:** `Yes`
   + **Hardware tenancy:** `Default`  
![\[Create a VPC with a Single Public Subnet\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCreateVPC02.png)

1. Choose **Create VPC**\.

1. When your VPC has been created, choose **Close** on the notification page\.

**To create additional subnets**

1. To add the second subnet to your VPC, in the VPC Dashboard choose **Subnets**, and then choose **Create Subnet**\. An Amazon Aurora DB cluster requires at least two VPC subnets\.

1. Set the following values in the **Create Subnet** panel:
   + **Name tag:** `gs-subnet2`
   + **VPC:** Choose the VPC that you created in the previous step, for example: `vpc-a464d1c1 (10.0.0.0/16) | gs-cluster-vpc`\.
   + **Availability Zone:** `us-east-1c`
   + **CIDR block:** `10.0.1.0/24`  
![\[Create a Subnet\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCreateVPC03.png)

1. Choose **Yes Create**\.

1. To ensure that the second subnet that you created uses the same route table as the first subnet, in the VPC Dashboard, choose **Subnets**, and then choose the first subnet that was created for the VPC, `gs-subnet1`\. Choose the **Route Table** tab, and note the **Current Route Table**, for example: `rtb-c16ce5bc`\. 

1. In the list of subnets, clear the first subnet and choose the second subnet, `gs-subnet2`\. Choose the **Route Table** tab, and then choose **Edit**\. In the **Change to** list, choose the route table from the previous step, for example: `rtb-c16ce5bc`\. Choose **Save** to save your choice\.  
![\[Edit the route table for a subnet\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCreateVPC04.png)

## Create a Security Group and Add Inbound Rules<a name="CHAP_GettingStarted.Aurora.CreateSecurityGroup"></a>

After you've created your VPC and subnets, the next step is to create a security group and add inbound rules\.

**To create a security group**

The last step in creating a VPC for use with your Amazon Aurora DB cluster is to create a VPC security group, which identifies which network addresses and protocols are allowed to access DB instances in your VPC\.

1. In the VPC Dashboard, choose **Security Groups**, and then choose **Create Security Group**\.

1. Set the following values in the **Create Security Group** panel:
   + **Name tag:** `gs-securitygroup1`
   + **Group name:** `gs-securitygroup1`
   + **Description:** `Getting Started Security Group`
   + **VPC:** Choose the VPC that you created earlier, for example: `vpc-b5754bcd | gs-cluster-vpc`\.  
![\[Create a Security Group\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCreateVPC05.png)

1. Choose **Yes, Create** to create the security group\.

**To add inbound rules to the security group**

To connect to your Aurora DB cluster, you need to add an inbound rule to your VPC security group that allows inbound traffic to connect\.

1. Determine the IP address to use to connect to the Aurora cluster\. You can use the service at [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com) to determine your public IP address\. If you are connecting through an ISP or from behind your firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.
**Warning**  
If you use `0.0.0.0/0`, you enable all IP addresses to access your DB cluster\. This is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, you'll authorize only a specific IP address or range of addresses to access your DB cluster\.

1. In the VPC Dashboard, choose **Security Groups**, and then choose the `gs-securitygroup1` security group that you created in the previous procedure\.

1. Choose the **Inbound** tab, and then choose the **Edit** button\.

1. Set the following values for your new inbound rule:
   + **Type:** `All Traffic`
   + **Source:** The IP address or range from the previous step, for example `203.0.113.25/32`\.  
![\[Add Ingress Rules\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCreateVPC06.png)

1. Choose **Save** to save your settings\.

## Create a DB Subnet Group<a name="CHAP_GettingStarted.Aurora.CreateSubnetGroup"></a>

The last thing that you need before you can create an Aurora DB cluster is a DB subnet group\. Your DB subnet group identifies the subnets that your DB cluster uses from the VPC that you created in the previous steps\. Your DB subnet group must include at least one subnet in at least two of the Availability Zones in the AWS Region where you want to deploy your DB cluster\.

**To create a DB subnet group for use with your Aurora DB cluster**

1. Open the Amazon Aurora console at [https://console\.aws\.amazon\.com/rds](https://console.aws.amazon.com/rds)\.

1. Choose **Subnet Groups**, and then choose **Create DB Subnet Group**\.

1. Set the following values for your new DB subnet group:
   + **Name:** `gs-subnetgroup1`
   + **Description:** `Getting Started Subnet Group`
   + **VPC ID:** Choose the VPC that you created in the previous procedure, for example, `gs-cluster-vpc (vpc-b5754bcd)`\.

1. In the **Add subnets** section, choose the Availability Zones that include the subnets from **Availability Zones**, and then choose the subnets from **Subnets**\.  
![\[Create Subnet Group\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCreateSubnetGroup01.png)

1. Choose **Create** to create the subnet group\.