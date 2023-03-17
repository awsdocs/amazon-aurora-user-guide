# Aurora global databases<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase"></a>

An *Aurora global database* is a single database that spans multiple AWS Regions, enabling low\-latency global reads and disaster recovery from any Region\-wide outage\. It provides built\-in fault tolerance for your deployment because the DB instance relies not on a single AWS Region, but upon multiple Regions and different Availability Zones\.  For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.

**Topics**
+ [Aurora global databases with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.amy)
+ [Aurora global databases with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.apg)

## Aurora global databases with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.amy"></a>

The following table shows the available Regions and engine versions for Aurora global databases with Aurora MySQL\.


| Region | Aurora MySQL version 3 | Aurora MySQL version 2 | 
| --- | --- | --- | 
| US East \(Ohio\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| US East \(N\. Virginia\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| US West \(N\. California\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| US West \(Oregon\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Africa \(Cape Town\) | Version 3\.01\.0 and higher | Version 2\.07\.1 and higher | 
| Asia Pacific \(Hong Kong\) | Version 3\.01\.0 and higher | Version 2\.07\.1 and higher | 
| Asia Pacific \(Hyderabad\) | Version 3\.02\.0 and higher | – | 
| Asia Pacific \(Jakarta\) | Version 3\.01\.0 and higher | Version 2\.07\.6 and higher | 
| Asia Pacific \(Melbourne\) | – | – | 
| Asia Pacific \(Mumbai\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Asia Pacific \(Osaka\) | Version 3\.01\.0 and higher | Version 2\.07\.3 and higher | 
| Asia Pacific \(Seoul\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Asia Pacific \(Singapore\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Asia Pacific \(Sydney\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Asia Pacific \(Tokyo\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Canada \(Central\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| China \(Beijing\) | Version 3\.01\.0 and higher | Version 2\.07\.2 and higher | 
| China \(Ningxia\) | Version 3\.01\.0 and higher | Version 2\.07\.2 and higher | 
| Europe \(Frankfurt\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Europe \(Ireland\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Europe \(London\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Europe \(Milan\) | Version 3\.01\.0 and higher | Version 2\.07\.1 and higher | 
| Europe \(Paris\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Europe \(Spain\) | Version 3\.02\.0 and higher | – | 
| Europe \(Stockholm\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| Europe \(Zurich\) | Version 3\.02\.0 and higher | – | 
| Middle East \(Bahrain\) | Version 3\.01\.0 and higher | Version 2\.07\.1 and higher | 
| Middle East \(UAE\) | Version 3\.02\.0 and higher | – | 
| South America \(São Paulo\) | Version 3\.01\.0 and higher | Version 2\.07\.1 and higher | 
| AWS GovCloud \(US\-East\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 
| AWS GovCloud \(US\-West\) | Version 3\.01\.0 and higher | Version 2\.07\.0 and higher | 

## Aurora global databases with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.apg"></a>

The following table shows the available Regions and engine versions for Aurora global databases with Aurora PostgreSQL\.


| Region | Aurora PostgreSQL 14 | Aurora PostgreSQL 13 | Aurora PostgreSQL 12 | Aurora PostgreSQL 11 | 
| --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| US East \(N\. Virginia\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| US West \(N\. California\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| US West \(Oregon\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Africa \(Cape Town\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Hong Kong\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Hyderabad\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Jakarta\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Melbourne\) | – | – | – | – | 
| Asia Pacific \(Mumbai\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Osaka\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Seoul\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Singapore\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Sydney\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Asia Pacific \(Tokyo\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Canada \(Central\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| China \(Beijing\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| China \(Ningxia\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Europe \(Frankfurt\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Europe \(Ireland\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Europe \(London\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Europe \(Milan\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Europe \(Paris\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Europe \(Spain\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Europe \(Stockholm\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Europe \(Zurich\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Middle East \(Bahrain\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| Middle East \(UAE\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| South America \(São Paulo\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| AWS GovCloud \(US\-East\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 
| AWS GovCloud \(US\-West\) | Version 14\.3 and higher | Version 13\.3 and higher | Version 12\.7 and higher | Version 11\.9 and version 11\.12 and higher | 