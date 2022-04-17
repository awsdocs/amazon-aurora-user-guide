# aurora\_version<a name="aurora_version"></a>

Returns the string value of the Amazon Aurora PostgreSQL\-Compatible Edition version number\. 

## Syntax<a name="aurora_version-syntax"></a>

 

```
aurora_version()
```

## Arguments<a name="aurora_version-arguments"></a>

None

## Return type<a name="aurora_version-return-type"></a>

CHAR or VARCHAR string

## Usage notes<a name="aurora_version-usage-notes"></a>

This function displays the version of the Amazon Aurora PostgreSQL\-Compatible Edition database engine\. The version number is returned as a string formatted as *major*\.*minor*\.*patch*\. For more information about Aurora PostgreSQL version numbers, see [Aurora version number](AuroraPostgreSQL.Updates.Versions.md#AuroraPostgreSQL.Updates.Versions.AuroraNumber)\. 

You can choose when to apply minor version upgrades by setting the maintenance window for your Aurora PostgreSQL DB cluster\. To learn how, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

Starting with the release of Aurora PostgreSQL versions 13\.3, 12\.8, 11\.13, 10\.18, and for all other later versions, Aurora version numbers follow PostgreSQL version numbers\. For more information about all Aurora PostgreSQL releases, see [Amazon Aurora PostgreSQL updates](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) in the *Release Notes for Aurora PostgreSQL*\. 

## Examples<a name="aurora_version-examples"></a>

The following example shows the results of calling the `aurora_version` function on an Aurora PostgreSQL DB cluster running [PostgreSQL 12\.7, Aurora PostgreSQL release 4\.2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.42) and then running the same function on a cluster running [Aurora PostgreSQL version 13\.3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.133X)\. 

```
=> SELECT * FROM aurora_version();
aurora_version
----------------
 4.2.2
SELECT * FROM aurora_version();
aurora_version
----------------
 13.3.0
```

This example shows how to use the function with various options to get more detail about the Aurora PostgreSQL version\. This example has an Aurora version number that's distinct from the PostgreSQL version number\.

```
=> SHOW SERVER_VERSION;
 server_version
----------------
 12.7
(1 row)

=> SELECT * FROM aurora_version();
aurora_version
----------------
 4.2.2
(1 row)
    
=> SELECT current_setting('server_version') AS "PostgreSQL Compatiblility";
PostgreSQL Compatiblility
---------------------------
 12.7
(1 row)

=> SELECT version() AS "PostgreSQL Compatiblility Full String";
PostgreSQL Compatiblility Full String
-------------------------------------------------------------------------------------------------------------
 PostgreSQL 12.7 on aarch64-unknown-linux-gnu, compiled by aarch64-unknown-linux-gnu-gcc (GCC) 7.4.0, 64-bit
(1 row) 
    
=> SELECT 'Aurora: '
    || aurora_version()
    || ' Compatible with PostgreSQL: '
    || current_setting('server_version') AS "Instance Version";
Instance Version
------------------------------------------------
 Aurora: 4.2.2 Compatible with PostgreSQL: 12.7
(1 row)
```

This next example uses the function with the same options in the previous example\. This example doesn't have an Aurora version number that's distinct from the PostgreSQL version number\.

```
=> SHOW SERVER_VERSION;
server_version
----------------
 13.3

=> SELECT * FROM aurora_version();
aurora_version
----------------
 13.3.0
=> SELECT current_setting('server_version') AS "PostgreSQL Compatiblility";
PostgreSQL Compatiblility
---------------------------
 13.3

=> SELECT version() AS "PostgreSQL Compatiblility Full String";
PostgreSQL Compatiblility Full String
-------------------------------------------------------------------------------------------------
 PostgreSQL 13.3 on x86_64-pc-linux-gnu, compiled by x86_64-pc-linux-gnu-gcc (GCC) 7.4.0, 64-bit
=> SELECT 'Aurora: ' 
    || aurora_version() 
    || ' Compatible with PostgreSQL: ' 
    || current_setting('server_version') AS "Instance Version";
Instance Version
-------------------------------------------------------
 Aurora: 13.3.0 Compatible with PostgreSQL: 13.3
```