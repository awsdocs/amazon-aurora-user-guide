# Choosing the Regions and Availability Zones<a name="Concepts.RegionsAndAvailabilityZones"></a>

Amazon cloud computing resources are hosted in multiple locations world\-wide\. These locations are composed of AWS Regions and Availability Zones\. Each *AWS Region* is a separate geographic area\. Each AWS Region has multiple, isolated locations known as *Availability Zones*\. Amazon RDS provides you the ability to place resources, such as instances, and data in multiple locations\. Resources aren't replicated across AWS Regions unless you do so specifically\.

Amazon operates state\-of\-the\-art, highly\-available data centers\. Although rare, failures can occur that affect the availability of instances that are in the same location\. If you host all your instances in a single location that is affected by such a failure, none of your instances would be available\.

![\[Single AZ Scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Con-AZ.png)

It is important to remember that each AWS Region is completely independent\. Any Amazon RDS activity you initiate \(for example, creating database instances or listing available database instances\) runs only in your current default AWS Region\. The default AWS Region can be changed in the console, by setting the EC2\_REGION environment variable, or it can be overridden by using the `--region` parameter with the AWS Command Line Interface\. See [Configuring the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html), specifically, the sections on Environment Variables and Command Line Options for more information\. 

Amazon RDS supports a special AWS Region called AWS GovCloud \(US\) that is designed to allow US government agencies and customers to move more sensitive workloads into the cloud\. AWS GovCloud \(US\) addresses the US government's specific regulatory and compliance requirements\. For more information about AWS GovCloud \(US\), see [What Is AWS GovCloud \(US\)?](http://docs.aws.amazon.com/govcloud-us/latest/UserGuide/whatis.html) 

To create or work with an Amazon RDS DB instance in a specific AWS Region, use the corresponding regional service endpoint\. 

## Availability<a name="Aurora.Overview.Availability"></a>

Availability in AWS Regions for Amazon Aurora varies by database engine compatibility\.

### Availability for Amazon Aurora MySQL<a name="Aurora.AuroraMySQL.Availability"></a>

The following table shows the regions where Aurora MySQL is currently available\.


| Region | Console Link | 
| --- | --- | 
| US East \(N\. Virginia\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-east\-1](https://console.aws.amazon.com/rds/home?region=us-east-1) | 
| US East \(Ohio\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-east\-2](https://console.aws.amazon.com/rds/home?region=us-east-2) | 
| US West \(N\. California\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-west\-1](https://console.aws.amazon.com/rds/home?region=us-west-1) | 
| US West \(Oregon\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-west\-2](https://console.aws.amazon.com/rds/home?region=us-west-2) | 
| Canada \(Central\) | [https://console\.aws\.amazon\.com/rds/home?region=ca\-central\-1](https://console.aws.amazon.com/rds/home?region=ca-central-1) | 
| China \(Ningxia\) | [https://console\.aws\.amazon\.com/rds/home?region=cn\-northwest\-1](https://console.aws.amazon.com/rds/home?region=cn-northwest-1) | 
| Asia Pacific \(Mumbai\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-south\-1](https://console.aws.amazon.com/rds/home?region=ap-south-1) | 
| Asia Pacific \(Tokyo\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-northeast\-1](https://console.aws.amazon.com/rds/home?region=ap-northeast-1) | 
| Asia Pacific \(Seoul\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-northeast\-2](https://console.aws.amazon.com/rds/home?region=ap-northeast-2) | 
| Asia Pacific \(Singapore\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-southeast\-1](https://console.aws.amazon.com/rds/home?region=ap-southeast-1) | 
| Asia Pacific \(Sydney\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-southeast\-2](https://console.aws.amazon.com/rds/home?region=ap-southeast-2) | 
| EU \(Frankfurt\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-central\-1](https://console.aws.amazon.com/rds/home?region=eu-central-1) | 
| EU \(Ireland\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-1](https://console.aws.amazon.com/rds/home?region=eu-west-1) | 
| EU \(London\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-2](https://console.aws.amazon.com/rds/home?region=eu-west-2) | 
| EU \(Paris\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-3](https://console.aws.amazon.com/rds/home?region=eu-west-3) | 

### Availability for Amazon Aurora PostgreSQL<a name="Aurora.AuroraPostgreSQL.Availability"></a>

The following table shows the regions where Aurora PostgreSQL is currently available\.


| Region | Console Link | 
| --- | --- | 
| US East \(Ohio\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-east\-2](https://console.aws.amazon.com/rds/home?region=us-east-2) | 
| US East \(N\. Virginia\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-east\-1](https://console.aws.amazon.com/rds/home?region=us-east-1) | 
| US West \(N\. California\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-west\-1](https://console.aws.amazon.com/rds/home?region=us-west-1) | 
| US West \(Oregon\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-west\-2](https://console.aws.amazon.com/rds/home?region=us-west-2) | 
| Asia Pacific \(Mumbai\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-south\-1](https://console.aws.amazon.com/rds/home?region=ap-south-1) | 
| Asia Pacific \(Singapore\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-southeast\-1](https://console.aws.amazon.com/rds/home?region=ap-southeast-1) | 
| Asia Pacific \(Sydney\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-southeast\-2](https://console.aws.amazon.com/rds/home?region=ap-southeast-2) | 
| Asia Pacific \(Seoul\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-northeast\-2](https://console.aws.amazon.com/rds/home?region=ap-northeast-2) | 
| Asia Pacific \(Tokyo\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-northeast\-1](https://console.aws.amazon.com/rds/home?region=ap-northeast-1) | 
| Canada \(Central\) | [https://console\.aws\.amazon\.com/rds/home?region=ca\-central\-1](https://console.aws.amazon.com/rds/home?region=ca-central-1) | 
| China \(Ningxia\) | [https://console\.aws\.amazon\.com/rds/home?region=cn\-northwest\-1](https://console.aws.amazon.com/rds/home?region=cn-northwest-1) | 
| EU \(Frankfurt\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-central\-1](https://console.aws.amazon.com/rds/home?region=eu-central-1) | 
| EU \(Ireland\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-1](https://console.aws.amazon.com/rds/home?region=eu-west-1) | 
| EU \(London\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-2](https://console.aws.amazon.com/rds/home?region=eu-west-2) | 
| EU \(Paris\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-3](https://console.aws.amazon.com/rds/home?region=eu-west-3) | 

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
|  `America/Fortaleza`  |   | 
|  `America/Guatemala`  |   | 
|  `America/Halifax`  |  This time zone setting can return incorrect values from 27 Oct 1918 05:00:00 GMT to 31 Oct 1918 05:00:00 GMT\.  | 
|  `America/Manaus`  |   | 
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