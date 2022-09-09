# Aurora global databases<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase"></a>

An *Aurora global database* is a single database that spans multiple AWS Regions, enabling low\-latency global reads and disaster recovery from any Region\-wide outage\. It provides built\-in fault tolerance for your deployment because the DB instance relies not on a single AWS Region, but upon multiple Regions and different Availability Zones\.  For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.

**Topics**
+ [Aurora global databases with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.ams)
+ [Aurora global databases with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.apg)

## Aurora global databases with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.ams"></a>

Following are the supported engines and Region availability for Aurora global databases with Aurora MySQL\.


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US East \(N\. Virginia\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(N\. California\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(Oregon\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Africa \(Cape Town\) | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | – | – | 
| Asia Pacific \(Jakarta\) | – | – | – | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Osaka\) | Version 1\.22\.3 and higher | Version 2\.07\.3 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Canada \(Central\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| China \(Beijing\) | Version 1\.22\.2 and higher | Version 2\.07\.2 and higher | Version 3\.01\.0 and higher | 
| China \(Ningxia\) | Version 1\.22\.2 and higher | Version 2\.07\.2 and higher | Version 3\.01\.0 and higher | 
| Europe \(Frankfurt\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Ireland\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(London\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Milan\) | – | – | – | 
| Europe \(Paris\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Stockholm\) | Version 1\.22\.2 and higher | Version 2\.07\.0 and higher | Version 3\.01\.0 and higher | 
| Middle East \(Bahrain\) | – | – | – | 
| Middle East \(UAE\) | – | – | – | 
| South America \(São Paulo\) | Version 1\.22\.2 and higher | Version 2\.07\.1 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-East\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-West\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 

## Aurora global databases with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.apg"></a>

Following are the supported engines and Region availability for Aurora global databases with Aurora PostgreSQL\.


| Region | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | Aurora PostgreSQL 12 | Aurora PostgreSQL 13 | Aurora PostgreSQL 14 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US East \(N\. Virginia\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(N\. California\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(Oregon\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Africa \(Cape Town\) | – | – | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | – | – | – | – | 
| Asia Pacific \(Jakarta\) | – | – | – | – | – | 
| Asia Pacific \(Mumbai\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Osaka\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Seoul\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Singapore\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Sydney\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Tokyo\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Canada \(Central\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| China \(Beijing\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| China \(Ningxia\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Frankfurt\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Ireland\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(London\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Milan\) | – | – | – | – | – | 
| Europe \(Paris\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Stockholm\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Middle East \(Bahrain\) | – | – | – | – | – | 
| Middle East \(UAE\) | – | – | – | – | – | 
| South America \(São Paulo\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-East\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-West\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 