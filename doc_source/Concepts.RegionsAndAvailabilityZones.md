# Regions and Availability Zones<a name="Concepts.RegionsAndAvailabilityZones"></a>

Amazon cloud computing resources are hosted in multiple locations world\-wide\. These locations are composed of AWS Regions and Availability Zones\. Each *AWS Region* is a separate geographic area\. Each AWS Region has multiple, isolated locations known as *Availability Zones*\.

**Note**  
For information about finding the Availability Zones for an AWS Region, see [Describing Your Regions, Availability Zones, and Local Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#using-regions-availability-zones-describe) in the Amazon EC2 documentation\.

Amazon operates state\-of\-the\-art, highly\-available data centers\. Although rare, failures can occur that affect the availability of instances that are in the same location\. If you host all your instances in a single location that is affected by such a failure, none of your instances is available\.

![\[AWS Region\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Con-AZ.png)

It is important to remember that each AWS Region is completely independent\. Any Amazon RDS activity you initiate \(for example, creating database instances or listing available database instances\) runs only in your current default AWS Region\. The default AWS Region can be changed in the console, by setting the EC2\_REGION environment variable, or it can be overridden by using the `--region` parameter with the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Configuring the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html), specifically the sections about environment variables and command line options\. 

Amazon RDS supports a special AWS Region called AWS GovCloud \(US\-West\) that is designed to allow US government agencies and customers to move more sensitive workloads into the cloud\. AWS GovCloud \(US\-West\) addresses the US government's specific regulatory and compliance requirements\. For more information about AWS GovCloud \(US\-West\), see [What Is AWS GovCloud \(US\-West\)?](https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/whatis.html) 

To create or work with an Amazon RDS DB instance in a specific AWS Region, use the corresponding regional service endpoint\. 

**Note**  
Aurora doesn't support Local Zones\.

## AWS Regions<a name="Concepts.RegionsAndAvailabilityZones.Regions"></a>

Each AWS Region is designed to be isolated from the other AWS Regions\. This design achieves the greatest possible fault tolerance and stability\.

When you view your resources, you see only the resources that are tied to the AWS Region that you specified\. This is because AWS Regions are isolated from each other, and we don't automatically replicate resources across AWS Regions\.

## Availability Zones<a name="Concepts.RegionsAndAvailabilityZones.AvailabilityZones"></a>

When you create a DB instance, you can choose an Availability Zone or have AWS choose one for you\. An Availability Zone is represented by an AWS Region code followed by a letter identifier \(for example, `us-east-1a`\)\.

## Region Availability<a name="Aurora.Overview.Availability"></a>

**Note**  
Aurora MySQL version 5\.7 and Aurora PostgreSQL version 10\.7 don't have time zone data that reflect recent changes to Daylight Saving Time \(DST\) in Brazil\. For a workaround until updated versions are available, reset the DB cluster's time zone parameter if the expected time doesn't show correctly for the recently changed Brazil time zone\. Do the following:  
South America \(Sao Paulo\) Region – set the time zone to `America/Fortaleza`\.
South America \(Cuiaba\) Region – set the time zone to `America/Manaus`\.
To change the time zone, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

When you work with an Aurora DB cluster using the command line interface or API operations, make sure that you specify its regional endpoint\.

**Topics**
+ [Aurora MySQL Region Availability](#Aurora.Overview.Availability.MySQL)
+ [Aurora PostgreSQL Region Availability](#Aurora.Overview.Availability.PostgreSQL)

### Aurora MySQL Region Availability<a name="Aurora.Overview.Availability.MySQL"></a>

The following table shows the AWS Regions where Aurora MySQL is currently available and the endpoint for each Region\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.RegionsAndAvailabilityZones.html)

### Aurora PostgreSQL Region Availability<a name="Aurora.Overview.Availability.PostgreSQL"></a>

The following table shows the AWS Regions where Aurora PostgreSQL is currently available and the endpoint for each Region\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.RegionsAndAvailabilityZones.html)

## Local Time Zone for Amazon Aurora DB Clusters<a name="Aurora.Overview.LocalTimeZone"></a>

By default, the time zone for an Amazon Aurora DB cluster is Universal Time Coordinated \(UTC\)\. You can set the time zone for instances in your DB cluster to the local time zone for your application instead\.

To set the local time zone for a DB cluster, set the `time_zone` parameter in the cluster parameter group for your DB cluster to one of the supported values listed later in this section\. When you set the `time_zone` parameter for a DB cluster, all instances in the DB cluster change to use the new local time zone\. If other Aurora DB clusters are using the same cluster parameter group, then all instances in those DB clusters change to use the new local time zone also\. For information on cluster\-level parameters, see [Amazon Aurora DB Cluster and DB Instance Parameters](USER_WorkingWithParamGroups.md#Aurora.Managing.ParameterGroups)\.

After you set the local time zone, all new connections to the database reflect the change\. If you have any open connections to your database when you change the local time zone, you won't see the local time zone update until after you close the connection and open a new connection\.

If you are replicating across AWS Regions, then the replication master DB cluster and the replica use different parameter groups \(parameter groups are unique to an AWS Region\)\. To use the same local time zone for each instance, you must set the `time_zone` parameter in the parameter groups for both the replication master and the replica\.

When you restore a DB cluster from a DB cluster snapshot, the local time zone is set to UTC\. You can update the time zone to your local time zone after the restore is complete\. If you restore a DB cluster to a point in time, then the local time zone for the restored DB cluster is the time zone setting from the parameter group of the restored DB cluster\.

You can set your local time zone to one of the values listed in the following table\. For some time zones, time values for certain date ranges can be reported incorrectly as noted in the table\. For Australia time zones, the time zone abbreviation returned is an outdated value as noted in the table\. 


|  Time Zone  |  Notes  | 
| --- | --- | 
|  `Africa/Harare`  |  This time zone setting can return incorrect values from 28 Feb 1903 21:49:40 GMT to 28 Feb 1903 21:55:48 GMT\.  | 
|  `Africa/Monrovia`  |   | 
|  `Africa/Nairobi`  |  This time zone setting can return incorrect values from 31 Dec 1939 21:30:00 GMT to 31 Dec 1959 21:15:15 GMT\.  | 
|  `Africa/Windhoek`  |   | 
|  `America/Bogota `  |  This time zone setting can return incorrect values from 23 Nov 1914 04:56:16 GMT to 23 Nov 1914 04:56:20 GMT\.  | 
|  `America/Caracas`  |   | 
|  `America/Chihuahua`  |   | 
|  `America/Cuiaba`  |   | 
|  `America/Denver`  |   | 
|  `America/Fortaleza`  |  If your DB cluster is in the South America \(Sao Paulo\) Region and the expected time doesn't show correctly for the recently changed Brazil time zone, reset the DB cluster's time zone parameter to `America/Fortaleza`\.  | 
|  `America/Guatemala`  |   | 
|  `America/Halifax`  |  This time zone setting can return incorrect values from 27 Oct 1918 05:00:00 GMT to 31 Oct 1918 05:00:00 GMT\.  | 
|  `America/Manaus`  |  If your DB cluster is in the South America \(Cuiaba\) Region and the expected time doesn't show correctly for the recently changed Brazil time zone, reset the DB cluster's time zone parameter to `America/Manaus`\.  | 
|  `America/Matamoros`  |   | 
|  `America/Monterrey`  |   | 
|  `America/Montevideo`  |   | 
|  `America/Phoenix`  |   | 
|  `America/Tijuana`  |   | 
|  `Asia/Ashgabat`  |   | 
|  `Asia/Baghdad`  |   | 
|  `Asia/Baku`  |   | 
|  `Asia/Bangkok`  |   | 
|  `Asia/Beirut`  |   | 
|  `Asia/Calcutta`  |   | 
|  `Asia/Kabul`  |   | 
|  `Asia/Karachi`  |   | 
|  `Asia/Kathmandu`  |   | 
|  `Asia/Muscat `  |  This time zone setting can return incorrect values from 31 Dec 1919 20:05:36 GMT to 31 Dec 1919 20:05:40 GMT\.  | 
|  `Asia/Riyadh `  |  This time zone setting can return incorrect values from 13 Mar 1947 20:53:08 GMT to 31 Dec 1949 20:53:08 GMT\.  | 
|  `Asia/Seoul`  |  This time zone setting can return incorrect values from 30 Nov 1904 15:30:00 GMT to 07 Sep 1945 15:00:00 GMT\.  | 
|  `Asia/Shanghai`  |  This time zone setting can return incorrect values from 31 Dec 1927 15:54:08 GMT to 02 Jun 1940 16:00:00 GMT\.  | 
|  `Asia/Singapore`  |   | 
|  `Asia/Taipei`  |  This time zone setting can return incorrect values from 30 Sep 1937 16:00:00 GMT to 29 Sep 1979 15:00:00 GMT\.  | 
|  `Asia/Tehran`  |   | 
|  `Asia/Tokyo`  |  This time zone setting can return incorrect values from 30 Sep 1937 15:00:00 GMT to 31 Dec 1937 15:00:00 GMT\.  | 
|  `Asia/Ulaanbaatar`  |   | 
|  `Atlantic/Azores`  |  This time zone setting can return incorrect values from 24 May 1911 01:54:32 GMT to 01 Jan 1912 01:54:32 GMT\.  | 
|  `Australia/Adelaide`  |  The abbreviation for this time zone is returned as CST instead of ACDT/ACST\.  | 
|  `Australia/Brisbane`  |  The abbreviation for this time zone is returned as EST instead of AEDT/AEST\.  | 
|  `Australia/Darwin `  |  The abbreviation for this time zone is returned as CST instead of ACDT/ACST\.  | 
|  `Australia/Hobart`  |  The abbreviation for this time zone is returned as EST instead of AEDT/AEST\.  | 
|  `Australia/Perth`  |  The abbreviation for this time zone is returned as WST instead of AWDT/AWST\.  | 
|  `Australia/Sydney `  |  The abbreviation for this time zone is returned as EST instead of AEDT/AEST\.  | 
|  `Brazil/East`  |   | 
|  `Canada/Saskatchewan`  |  This time zone setting can return incorrect values from 27 Oct 1918 08:00:00 GMT to 31 Oct 1918 08:00:00 GMT\.  | 
|  `Europe/Amsterdam`  |   | 
|  `Europe/Athens`  |   | 
|  `Europe/Dublin`  |   | 
|  `Europe/Helsinki`  |  This time zone setting can return incorrect values from 30 Apr 1921 22:20:08 GMT to 30 Apr 1921 22:20:11 GMT\.  | 
|  `Europe/Paris`  |   | 
|  `Europe/Prague`  |   | 
|  `Europe/Sarajevo`  |   | 
|  `Pacific/Auckland`  |   | 
|  `Pacific/Guam`  |   | 
|  `Pacific/Honolulu`  |  This time zone setting can return incorrect values from 21 May 1933 11:30:00 GMT to 30 Sep 1945 11:30:00 GMT\.  | 
|  `Pacific/Samoa`  |  This time zone setting can return incorrect values from 01 Jan 1911 11:22:48 GMT to 01 Jan 1950 11:30:00 GMT\.  | 
|  `US/Alaska`  |   | 
|  `US/Central`  |   | 
|  `US/Eastern`  |   | 
|  `US/East-Indiana`  |   | 
|  `US/Pacific`  |   | 
|  `UTC`  |   | 