# Extension versions for Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Extensions"></a>

**Topics**
+ [Extensions supported for Aurora PostgreSQL 12\.x](#AuroraPostgreSQL.Extensions.12)
+ [Extensions supported for Aurora PostgreSQL 11\.x](#AuroraPostgreSQL.Extensions.11)
+ [Extensions supported for Aurora PostgreSQL 10\.x](#AuroraPostgreSQL.Extensions.10)
+ [Extensions supported for Aurora PostgreSQL 9\.6\.x](#AuroraPostgreSQL.Extensions.96)
+ [Aurora PostgreSQL apg\_plan\_mgmt extension versions](#AuroraPostgreSQL.Extensions.apg_plan_mgmt)

To upgrade a PostgreSQL extension, see [Upgrading PostgreSQL extensions](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.Upgrading.ExtensionUpgrades)\.

## Extensions supported for Aurora PostgreSQL 12\.x<a name="AuroraPostgreSQL.Extensions.12"></a>

The following table shows the PostgreSQL extension versions that are currently supported on Aurora PostgreSQL versions 12\.x\. "NA" indicates that the extension isn't available for that PostgreSQL version\. For more information about PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/12/extend-extensions.html) in the PostgreSQL documentation\. 


| Extension | 12\.4 | 12\.6 | 
| --- | --- | --- | 
| [address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 3\.0\.2 | 3\.0\.2 | 
| [address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 3\.0\.2 | 3\.0\.2 | 
|  [amcheck](https://www.postgresql.org/docs/current/amcheck.html)  | 1\.2 | 1\.2 | 
|  [apg\_plan\_mgmt](#AuroraPostgreSQL.Extensions.apg_plan_mgmt)  | 2\.0 | 2\.0 | 
| aurora\_stat\_utils | 1\.0 | 1\.0 | 
| aws\_commons | 1\.2 | 1\.2 | 
| aws\_lambda | 1\.0 | 1\.0 | 
|  [aws\_ml](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/postgresql-ml.html#postgresql-ml-aws_ml-install)   | 1\.0 | 1\.0 | 
| aws\_s3 | 1\.1 | 1\.1 | 
| [bloom](https://www.postgresql.org/docs/12/bloom.html) | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/12/btree-gin.html) | 1\.3 | 1\.3 | 
| [btree\_gist](http://www.postgresql.org/docs/12/btree-gist.html) | 1\.5 | 1\.5 | 
| [citext](http://www.postgresql.org/docs/12/citext.html) | 1\.6 | 1\.6 | 
| [cube](http://www.postgresql.org/docs/12/cube.html) | 1\.4 | 1\.4 | 
| [dblink](http://www.postgresql.org/docs/12/dblink.html) | 1\.2 | 1\.2 | 
| [dict\_int](http://www.postgresql.org/docs/12/dict-int.html) | 1\.0 | 1\.0 | 
| [dict\_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html) | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/12/earthdistance.html) | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/12/fuzzystrmatch.html) | 1\.1 | 1\.1 | 
| hll | 2\.14 | 2\.14 | 
| [hstore](http://www.postgresql.org/docs/12/hstore.html) | 1\.6 | 1\.6 | 
| [hstore\_plperl](https://www.postgresql.org/docs/12/hstore.html) | 1\.0 | 1\.0 | 
| [intagg](http://www.postgresql.org/docs/12/intagg.html) | 1\.1 | 1\.1 | 
| [intarray](http://www.postgresql.org/docs/12/intarray.html) | 1\.2 | 1\.2 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.4 | 2\.4 | 
| [isn](http://www.postgresql.org/docs/12/isn.html) | 1\.2 | 1\.2 | 
| jsonb\_plperl | 1\.0 | 1\.0 | 
| log\_fdw | 1\.1 | 1\.1 | 
| [ltree](http://www.postgresql.org/docs/12/ltree.html) | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) | 3\.8 | 3\.8 | 
| [pg\_bigm](https://pgbigm.osdn.jp/pg_bigm_en-1-2.html) | NA | 1\.2 | 
| [pg\_buffercache](http://www.postgresql.org/docs/12/pgbuffercache.html) | 1\.3 | 1\.3 | 
| pg\_cron | NA | 1\.3 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html) | 1\.2 | 1\.2 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) | 1\.3\.5 | 1\.3\.5 | 
| pg\_partman | NA | 4\.4\.0 | 
| [pg\_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html) | 1\.2 | 1\.2 | 
| pg\_proctab | NA | 0\.0\.9 | 
| [pg\_repack](http://reorg.github.io/pg_repack/) | 1\.4\.5 | 1\.4\.5 | 
| pg\_similarity | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/12/pgstatstatements.html) | 1\.7 | 1\.7 | 
| [pg\_trgm](http://www.postgresql.org/docs/12/pgtrgm.html) | 1\.4 | 1\.4 | 
| [pg\_visibility](https://www.postgresql.org/docs/12/pgvisibility.html) | 1\.2 | 1\.2 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.4 | 1\.4 | 
| [pgcrypto](http://www.postgresql.org/docs/12/pgcrypto.html) | 1\.3 | 1\.3 | 
| [pglogical](https://github.com/2ndQuadrant/pglogical) | 2\.3\.2 | 2\.3\.2 | 
|  pglogical\_origin  | 1\.0\.0 | 1\.0\.0 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | 3\.0\.3 | 3\.0\.3 | 
| [pgrowlocks](http://www.postgresql.org/docs/12/pgrowlocks.html) | 1\.2 | 1\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/12/pgstattuple.html) | 1\.5 | 1\.5 | 
|  [pgTAP](https://pgtap.org/)  | 1\.1\.0 | 1\.1\.0 | 
| plcoffee | 2\.3\.14 | 2\.3\.14 | 
| plls | 2\.3\.14 | 2\.3\.14 | 
| [plperl](https://www.postgresql.org/docs/12/plperl.html) | 1\.0 | 1\.0 | 
| [plpgsql](https://www.postgresql.org/docs/12/plpgsql.html) | 1\.0 | 1\.0 | 
| [plprofiler](https://github.com/bigsql/plprofiler) | 4\.1 | 4\.1 | 
| [pltcl](https://www.postgresql.org/docs/12/pltcl-overview.html) | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 2\.3\.14 | 2\.3\.14 | 
| [PostGIS](http://www.postgis.net/) | 3\.0\.2 | 3\.0\.2 | 
| postgis\_raster | 3\.0\.2 | 3\.0\.2 | 
| [postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 3\.0\.2 | 3\.0\.2 | 
| [postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 3\.0\.2 | 3\.0\.2 | 
| [postgres\_fdw](http://www.postgresql.org/docs/12/postgres-fdw.html) | 1\.0 | 1\.0 | 
|  [ prefix](https://github.com/dimitri/prefix) | 1\.2\.0 | 1\.2\.0 | 
| [RDKit](https://www.rdkit.org/) | 3\.8 | 3\.8 | 
| rds\_activity\_stream | 1\.3 | 1\.3 | 
| [sslinfo](http://www.postgresql.org/docs/12/sslinfo.html) | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/12/tablefunc.html) | 1\.0 | 1\.0 | 
| [test\_parser](https://www.postgresql.org/docs/9.4/test-parser.html) | 1\.0 | 1\.0 | 
| [tsm\_system\_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html) | 1\.0 | 1\.0 | 
| [tsm\_system\_time](https://www.postgresql.org/docs/12/tsm-system-time.html) | 1\.0 | 1\.0 | 
|  [unaccent](http://www.postgresql.org/docs/12/unaccent.html) | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/12/uuid-ossp.html) | 1\.1 | 1\.1 | 

## Extensions supported for Aurora PostgreSQL 11\.x<a name="AuroraPostgreSQL.Extensions.11"></a>

The following table shows PostgreSQL extension versions currently supported on Aurora PostgreSQL versions 11\.x\. "NA" indicates that the extension isn't available for that PostgreSQL version\. For more information about PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/11/extend-extensions.html)\. 


| Extension | 11\.4 | 11\.6 | 11\.7 | 11\.8 | 11\.9 | 11\.11 | 
| --- | --- | --- | --- | --- | --- | --- | 
| [address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.5\.1 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.5\.1 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
|  [amcheck](https://www.postgresql.org/docs/current/amcheck.html)  | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [apg\_plan\_mgmt](#AuroraPostgreSQL.Extensions.apg_plan_mgmt) | 1\.0\.1 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 
| aurora\_stat\_utils | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| aws\_commons | 1\.0 | 1\.1 | 1\.1 | 1\.1 | 1\.2 | 1\.2 | 
| aws\_lambda | NA | NA | NA | NA | 1\.0 | 1\.0 | 
|  [aws\_ml](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/postgresql-ml.html#postgresql-ml-aws_ml-install)   | NA | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| aws\_s3 | 1\.0 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [bloom](https://www.postgresql.org/docs/11/bloom.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/11/btree-gin.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [btree\_gist](http://www.postgresql.org/docs/11/btree-gist.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [citext](http://www.postgresql.org/docs/11/citext.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [cube](http://www.postgresql.org/docs/11/cube.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [dblink](http://www.postgresql.org/docs/11/dblink.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ dict\_int](http://www.postgresql.org/docs/11/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [dict\_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/11/earthdistance.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/11/fuzzystrmatch.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| hll | 2\.11 | 2\.11 | 2\.11 | 2\.11 | 2\.11 | 2\.11 | 
| [hstore](http://www.postgresql.org/docs/11/hstore.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [hstore\_plperl](https://www.postgresql.org/docs/11/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [intagg](http://www.postgresql.org/docs/11/intagg.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [intarray](http://www.postgresql.org/docs/11/intarray.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.2 | 2\.2 | 2\.2 | 2\.2 | 2\.2 | 2\.2 | 
| [isn](http://www.postgresql.org/docs/11/isn.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| jsonb\_plperl | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| log\_fdw | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ltree](http://www.postgresql.org/docs/11/ltree.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) | 3\.7 | 3\.7 | 3\.8 | 3\.8 | 3\.8 | 3\.8 | 
| [pg\_bigm](https://pgbigm.osdn.jp/pg_bigm_en-1-2.html) | NA | NA | NA | NA | NA | 1\.2 | 
| [pg\_buffercache](http://www.postgresql.org/docs/11/pgbuffercache.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) | 1\.3\.4 | 1\.3\.4 | 1\.3\.4 | 1\.3\.5 | 1\.3\.5 | 1\.3\.5 | 
| [pg\_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| pg\_proctab | NA | NA | NA | NA | NA | 0\.0\.9 | 
| [pg\_repack](http://reorg.github.io/pg_repack/) | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 
| pg\_similarity | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/11/pgstatstatements.html) | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 
| [pg\_trgm](http://www.postgresql.org/docs/11/pgtrgm.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [pg\_visibility](https://www.postgresql.org/docs/11/pgvisibility.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3\.1 | 1\.3\.1 | 
| [pgcrypto](http://www.postgresql.org/docs/11/pgcrypto.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pglogical](https://github.com/2ndQuadrant/pglogical) | NA | NA | NA | 2\.2\.2 | 2\.2\.2 | 2\.2\.2 | 
|  pglogical\_origin  | NA | NA | NA | 1\.0\.0 | 1\.0\.0 | 1\.0\.0 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 
| [pgrowlocks](http://www.postgresql.org/docs/11/pgrowlocks.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/11/pgstattuple.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [pgTAP](https://pgtap.org/) | 1\.0\.0 | 1\.0\.0 | 1\.1\.0 | 1\.1\.0 | 1\.1\.0 | 1\.1\.0 | 
| plcoffee | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| plls | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| [plperl](https://www.postgresql.org/docs/11/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plpgsql](https://www.postgresql.org/docs/11/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plprofiler](https://github.com/bigsql/plprofiler) | NA | 4\.1 | 4\.1 | 4\.1 | 4\.1 | 4\.1 | 
| [pltcl](https://www.postgresql.org/docs/11/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| [PostGIS](http://www.postgis.net/) | 2\.5\.1 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.5\.1 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.5\.1 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [postgres\_fdw](http://www.postgresql.org/docs/11/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ prefix](https://github.com/dimitri/prefix) | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 
| [RDKit](https://www.rdkit.org/) | NA | NA | NA | 3\.8 | 3\.8 | 3\.8 | 
| rds\_activity\_stream | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [sslinfo](http://www.postgresql.org/docs/11/sslinfo.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/11/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [test\_parser](https://www.postgresql.org/docs/9.4/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsm\_system\_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsm\_system\_time](https://www.postgresql.org/docs/11/tsm-system-time.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [unaccent](http://www.postgresql.org/docs/11/unaccent.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/11/uuid-ossp.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 

## Extensions supported for Aurora PostgreSQL 10\.x<a name="AuroraPostgreSQL.Extensions.10"></a>

The following table shows PostgreSQL extension versions currently supported on Aurora PostgreSQL versions 10\.x\. "NA" indicates that the extension isn't available for that PostgreSQL version\. For more information about PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/11/extend-extensions.html)\. 

**Note**  
The `adminpack` extension is no longer supported because it accesses the file system\.
The `plperlu` extension is no longer supported because it is an untrusted language extension\.
The `pltclu` extension is no longer supported because it is an untrusted language extension\.


| Extension | 10\.4 | 10\.5 | 10\.6 | 10\.7 | 10\.11 | 10\.12 | 10\.13 | 10\.14 | 10\.16 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| [address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 
| [address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 
|  [adminpack](https://www.postgresql.org/docs/current/adminpack.html)  | 1\.1 | 1\.1 | 1\.1 | NA | NA | NA | NA | NA | NA | 
|  [amcheck](https://www.postgresql.org/docs/current/amcheck.html)  | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [apg\_plan\_mgmt](#AuroraPostgreSQL.Extensions.apg_plan_mgmt) | 0\.1 | 1\.0\.1 | 1\.0\.1 | 1\.0\.1 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 
|  aurora\_stat\_utils  | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  aws\_commons  | NA | NA | 1\.0 | 1\.0 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [aws\_ml](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/postgresql-ml.html#postgresql-ml-aws_ml-install)  | NA | NA | NA | NA | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  aws\_s3  | NA | NA | 1\.0 | 1\.0 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [bloom](https://www.postgresql.org/docs/10/bloom.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/10/btree-gin.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [btree\_gist](http://www.postgresql.org/docs/10/btree-gist.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [chkpass](http://www.postgresql.org/docs/10/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext](http://www.postgresql.org/docs/10/citext.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [cube](http://www.postgresql.org/docs/10/cube.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [dblink](http://www.postgresql.org/docs/10/dblink.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [dict\_int](http://www.postgresql.org/docs/10/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [dict\_xsyn](https://www.postgresql.org/docs/10/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/10/earthdistance.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/10/fuzzystrmatch.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| hll | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 
| [hstore](http://www.postgresql.org/docs/10/hstore.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [hstore\_plperl](https://www.postgresql.org/docs/10/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| hstore\_plperlu | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [intagg](http://www.postgresql.org/docs/10/intagg.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [intarray](http://www.postgresql.org/docs/10/intarray.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 
| [isn](http://www.postgresql.org/docs/10/isn.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| log\_fdw | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ltree](http://www.postgresql.org/docs/10/ltree.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) | 3\.6 | 3\.6 | 3\.6 | 3\.6 | 3\.6 | 3\.8 | 3\.8 | 3\.8 | 3\.8 | 
| [pg\_buffercache](http://www.postgresql.org/docs/10/pgbuffercache.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/10/pgfreespacemap.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) | 1\.3\.0 | 1\.3\.1 | 1\.3\.1 | 1\.3\.1 | 1\.3\.3 | 1\.3\.3 | 1\.3\.5 | 1\.3\.5 | 1\.3\.5 | 
| [pg\_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_repack ](http://reorg.github.io/pg_repack/) | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 
| pg\_similarity | NA | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/10/pgstatstatements.html) | 1\.5 | 1\.5 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 
| [pg\_trgm](http://www.postgresql.org/docs/10/pgtrgm.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_visibility](https://www.postgresql.org/docs/10/pgvisibility.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2\.1 | 1\.2\.1 | 1\.2\.1 | 
| [pgcrypto](http://www.postgresql.org/docs/10/pgcrypto.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pglogical](https://github.com/2ndQuadrant/pglogical) | NA | NA | NA | NA | NA | NA | 2\.2\.2 | 2\.2\.2 | 2\.2\.2 | 
| pglogical\_origin  | NA | NA | NA | NA | NA | NA | 1\.0\.0 | 1\.0\.0 | 1\.0\.0 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [pgrowlocks](http://www.postgresql.org/docs/10/pgrowlocks.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/10/pgstattuple.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| plcoffee | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| plls | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| [plperl](https://www.postgresql.org/docs/10/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| plperlu | 1\.0 | 1\.0 | 1\.0 | NA | NA | NA | NA | NA | NA | 
| [plpgsql](https://www.postgresql.org/docs/10/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plprofiler](https://github.com/bigsql/plprofiler) | NA | NA | NA | NA | 4\.0 | 4\.1 | 4\.1 | 4\.1 | 4\.1 | 
| [pltcl](https://www.postgresql.org/docs/10/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| pltclu | 1\.0 | 1\.0 | 1\.0 | NA | NA | NA | NA | NA | NA | 
| [plv8](https://github.com/plv8) | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| [PostGIS](http://www.postgis.net/) | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 
| [postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 
| [postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 
| [postgres\_fdw](http://www.postgresql.org/docs/10/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ prefix](https://github.com/dimitri/prefix) | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 
| [RDKit](https://www.rdkit.org/) | NA | NA | NA | NA | NA | NA | 3\.8 | 3\.8 | 3\.8 | 
| rds\_activity\_stream  | NA | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [sslinfo](http://www.postgresql.org/docs/10/sslinfo.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/10/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [test\_parser](https://www.postgresql.org/docs/9.4/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/tsearch2.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsm\_system\_rows](https://www.postgresql.org/docs/10/tsm-system-rows.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsm\_system\_time](https://www.postgresql.org/docs/10/tsm-system-time.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [unaccent](http://www.postgresql.org/docs/10/unaccent.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/10/uuid-ossp.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 

## Extensions supported for Aurora PostgreSQL 9\.6\.x<a name="AuroraPostgreSQL.Extensions.96"></a>

The following table shows PostgreSQL extension versions currently supported on Aurora PostgreSQL versions 9\.6\.x\. "NA" indicates that the extension isn't available for that PostgreSQL version\. For more information about PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/11/extend-extensions.html)\. 

**Note**  
The `apgcc` RDS for PostgreSQL internal extension is no longer supported\. 
The `apgunit` RDS for PostgreSQL internal extension is no longer supported\.
The `pageinspect` extension is no longer publicly supported by RDS for PostgreSQL\.
The `xml2` extension is no longer supported by PostgreSQL\.


| Extension | 9\.6\.3  | 9\.6\.6  | 9\.6\.8 | 9\.6\.9 | 9\.6\.11 | 9\.6\.12 | 9\.6\.16 | 9\.6\.17 | 9\.6\.18 | 9\.6\.19 | 9\.6\.21 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| [address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 
| [address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 
| [apg\_plan\_mgmt](#AuroraPostgreSQL.Extensions.apg_plan_mgmt) | NA | NA | NA | 0\.1 | 1\.0\.1 | 1\.0\.1 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 
| apgcc | 1\.0 | 1\.0 | NA | NA | NA | NA | NA | NA | NA | NA | NA | 
| apgunit | 1\.0 | 1\.0 | NA | NA | NA | NA | NA | NA | NA | NA | NA | 
| aurora\_stat\_utils | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [bloom](https://www.postgresql.org/docs/9.6/bloom.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/9.6/btree-gin.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gist](http://www.postgresql.org/docs/9.6/btree-gist.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [chkpass](http://www.postgresql.org/docs/9.6/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext](http://www.postgresql.org/docs/9.6/citext.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [cube](http://www.postgresql.org/docs/9.6/cube.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [dblink](http://www.postgresql.org/docs/9.6/dblink.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [dict\_int ](http://www.postgresql.org/docs/9.6/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [dict\_xsyn](https://www.postgresql.org/docs/9.6/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/9.6/earthdistance.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/9.6/fuzzystrmatch.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| hll | NA | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 2\.10 | 
| [hstore](http://www.postgresql.org/docs/9.6/hstore.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [hstore\_plperl](https://www.postgresql.org/docs/9.6/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [intagg](http://www.postgresql.org/docs/9.6/intagg.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [intarray](http://www.postgresql.org/docs/9.6/intarray.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.0 | 2\.0 | 2\.0 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 2\.1 | 
| [isn](http://www.postgresql.org/docs/9.6/isn.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| log\_fdw | 1\.0 | 1\.0 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ltree](http://www.postgresql.org/docs/9.6/ltree.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) | NA | 3\.6 | 3\.6 | 3\.6 | 3\.6 | 3\.6 | 3\.6 | 3\.8 | 3\.8 | 3\.8 | 3\.8 | 
| [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | 1\.5 | 1\.5 | NA | NA | NA | NA | NA | NA | NA | NA | NA | 
| [pg\_buffercache](http://www.postgresql.org/docs/9.6/pgbuffercache.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) | 1\.1\.3 | 1\.1\.3 | 1\.2\.2 | 1\.2\.2 | 1\.2\.3 | 1\.2\.3 | 1\.2\.5 | 1\.2\.5 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_repack](http://reorg.github.io/pg_repack/) | 1\.4\.0 | 1\.4\.2 | 1\.4\.2 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 
| pg\_similarity | NA | NA | NA | NA | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/9.6/pgstatstatements.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [pg\_trgm](http://www.postgresql.org/docs/9.6/pgtrgm.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_visibility](https://www.postgresql.org/docs/9.6/pgvisibility.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.0 | 1\.0 | 1\.0 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.2 | 1\.1\.2 | 
| [pgcrypto](http://www.postgresql.org/docs/9.6/pgcrypto.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 
| [pgrowlocks](http://www.postgresql.org/docs/9.6/pgrowlocks.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/9.6/pgstattuple.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| plcoffee | 1\.5\.3 | 1\.5\.3 | 2\.1\.0 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| plls | 1\.5\.3 | 1\.5\.3 | 2\.1\.0 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| [plperl](https://www.postgresql.org/docs/current/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plpgsql](https://www.postgresql.org/docs/current/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pltcl](https://www.postgresql.org/docs/current/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 1\.5\.3 | 1\.5\.3 | 2\.1\.0 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.3\.14 | 2\.3\.14 | 2\.3\.14 | 
| [PostGIS](http://www.postgis.net/) | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 
| [postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 
| [postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 
| [postgres\_fdw](http://www.postgresql.org/docs/9.6/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  [ prefix](https://github.com/dimitri/prefix) | NA | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 
| [sslinfo](http://www.postgresql.org/docs/9.6/sslinfo.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/9.6/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [test\_parser](https://www.postgresql.org/docs/9.4/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/tsearch2.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  [unaccent](http://www.postgresql.org/docs/9.6/unaccent.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/9.6/uuid-ossp.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [xml2](https://www.postgresql.org/docs/current/xml2.html) | 1\.1 | 1\.1 | NA | NA | NA | NA | NA | NA | NA | NA | NA | 

## Aurora PostgreSQL apg\_plan\_mgmt extension versions<a name="AuroraPostgreSQL.Extensions.apg_plan_mgmt"></a>

**Topics**
+ [Version 2\.0 of the Aurora PostgreSQL apg\_plan\_mgmt extension](#AuroraPostgreSQL.Extensions.apg_plan_mgmt.20)
+ [Version 1\.0\.1 of the Aurora PostgreSQL apg\_plan\_mgmt extension](#AuroraPostgreSQL.Extensions.apg_plan_mgmt.101)

### Version 2\.0 of the Aurora PostgreSQL apg\_plan\_mgmt extension<a name="AuroraPostgreSQL.Extensions.apg_plan_mgmt.20"></a>

You use the `apg_plan_mgmt` extension with query plan management\. For more about how to install, upgrade, and use the `apg_plan_mgmt` extension, see [Managing query execution plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

The `apg_plan_mgmt` extension changes for version 2\.0 include the following:

**New extension features**

1. You can now manage all queries inside SQL functions, whether they have parameters or not\.

1. You can now manage all queries inside PL/pgSQL functions, whether they have parameters or not\.

1. You can now manage queries in generic plans, whether they have parameters or not\. To learn more about generic plans versus custom plans, see the `PREPARE` statement in the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/sql-prepare.html)\. 

1. You can now use query plan management to enforce the use of specific types of aggregate methods in query plans\.

**Extension improvements**

1. You can now save plans with a size up to 8KB times the setting of the `max_worker_processes` parameter\. Previously the maximum plan size was 8KB\.

1. Fixed bugs for unnamed prepared statements such as those from JDBC\.

1. Previously, when you tried to do `CREATE EXTENSION apg_plan_mgmt` when it is not loaded in the `shared_preload_libraries`, the PostgreSQL backend connection was dropped\. Now, an error message prints and the connection is not dropped\.

1. The default value of the `cardinality_error` in the `apg_plan_mgmt.plans table` is NULL, but it can be set to \-1 during the `apg_plan_mgmt.evolve_plan_baselines` function\. NULL is now used consistently\.

1. Plans are now saved for queries that refer to temporary tables\.

1. The default maximum number of plans is increased from 1000 to 10000\.

1. The following pgss parameters are deprecated because the automatic plan capture mode should be used instead of those parameters\.
   + `apg_plan_mgmt.pgss_min_calls`
   + `apg_plan_mgmt.pgss_min_mean_time_ms`
   + `apg_plan_mgmt.pgss_min_stddev_time_ms`
   + `apg_plan_mgmt.pgss_min_total_time_ms`

### Version 1\.0\.1 of the Aurora PostgreSQL apg\_plan\_mgmt extension<a name="AuroraPostgreSQL.Extensions.apg_plan_mgmt.101"></a>

The `apg_plan_mgmt` extension changes for version 1\.0\.1 include the following:

**New extension features**

1. The `validate_plans` function has a new `action` value called `update_plan_hash`\. This action updates the `plan_hash` ID for plans that can't be reproduced exactly\. The `update_plan_hash` value also allows you to fix a plan by rewriting the SQL\. You can then register the good plan as an `Approved` plan for the original SQL\. Following is an example of using the `update_plan_hash` action\.

   ```
   UPDATE apg_plan_mgmt.plans SET plan_hash = new _plan_hash, plan_outline = good_plan_outline 
      WHERE sql_hash = bad_plan_sql_hash AND plan_hash = bad_plan_plan_hash;
   SELECT apg_plan_mgmt.validate_plans(bad_plan_sql_hash, bad_plan_plan_hash, 'update_plan_hash');
   SELECT apg_plan_mgmt.reload();
   ```

1. A new `get_explain_stmt` function is available that generates the text of an `EXPLAIN` statement for the specified SQL statement\. It includes the parameters `sql_hash`, `plan_hash` and `explain_options`\. 

   The parameter `explain_options` can be any comma\-separated list of valid `EXPLAIN` options, as shown following\.

   ```
   analyze,verbose,buffers,hashes,format json
   ```

   If the parameter `explain_options` is NULL or an empty string, the `get_explain_stmt` function generates a simple `EXPLAIN` statement\. 

   To create an `EXPLAIN` script for your workload or a portion of it, use the `\a` , `\t`, and `\o` options to redirect the output to a file\. For example, you can create an `EXPLAIN` script for the top\-ranked \(top\-K\) statements by using the PostgreSQL `pg_stat_statements` view sorted by `total_time` in `DESC` order\.

1. The precise location of the Gather parallel query operator is determined by costing, and may change slightly over time\. To prevent these differences from invalidating the entire plan, query plan management now computes the same `plan_hash` even if the Gather operators move to different places in the plan tree\.

1. Support is added for nonparameterized statements inside pl/pgsql functions\.

1. Overhead is reduced when the `apg_plan_mgmt` extension is installed on multiple databases in the same cluster while two or more databases are being accessed concurrently\. Also, this release fixed a bug in this area that caused plans to not be stored in shared memory\.

**Extension improvements**

1. Improvements to the `evolve_plan_baselines` function\.

   1. The `evolve_plan_baselines` function now computes a `cardinality_error` metric over all nodes in the plan\. Using this metric, you can identify any plan where the cardinality estimation error is large, and the plan quality is more doubtful\. Long\-running statements with high `cardinality_error` values are high\-priority candidates for query tuning\.

   1. Reports generated by `evolve_plan_baselines` now include `sql_hash`, `plan_hash`, and the plan `status`\.

   1. You can now allow `evolve_plan_baselines` to approve previously `Rejected` plans\.

   1. The meaning of `speedup_factor` for `evolve_plan_baselines` is now always relative to the baseline plan\. For example, a value of 1\.1 now means 10 percent faster than the baseline plan\. A value of 0\.9 means 10 percent slower than the baseline plan\. The comparison is made using running time alone instead of total time\.

   1. The `evolve_plan_baselines` function now warms the cache in a new way\. It does this by running the baseline plan, then running the baseline plan one more time, and then running the candidate plan once\. Previously, `evolve_plan_baselines` ran the candidate plan twice\. This approach added significantly to running time, especially for slow candidate plans\. However, running the candidate plan twice is more reliable when the candidate plan uses an index that isn't used in the baseline plan\.

1. Query plan management no longer saves plans that refer to system tables or views, temporary tables, or the query plan management's own tables\.

1. Bug fixes include caching a plan immediately when saved and fixing a bug that caused the back end to terminate\.