# Configuring a database for Babelfish<a name="babelfish-configuration"></a>

When you create a Babelfish for Aurora PostgreSQL DB cluster, you can use a parameter group in one of two ways\. You can create a new parameter group that configures a cluster with Babelfish running\. Or you can use a pre\-existing Amazon Aurora parameter group\. 

To use an existing parameter group, edit the group and set the `babelfish_status` parameter to `on`\. Specify any Babelfish options before creating your Aurora PostgreSQL cluster\. For information about modifying your parameter group, see [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)\.

The following parameters control Babelfish preferences\.


| Parameter | Type | Default value | Values allowed | Description | Modifiable? | 
| --- | --- | --- | --- | --- | --- | 
| rds\.babelfish\_status | String | off | on, off, datatypesonly | Sets the state of Babelfish for Aurora PostgreSQL; functionality\. When this parameter is set to `datatypesonly`, Babelfish is turned off but SQL Server data types are still available\. | Yes | 
| babelfishpg\_tds\.default\_server\_name | String | Microsoft SQL Server | null | The default name of the Babelfish server\. | Yes | 
| babelfishpg\_tds\.port | Integer | 1433 | 1\-65535 | Sets the TCP port used for requests in SQL Server syntax\. | Yes | 
| babelfishpg\_tds\.tds\_default\_protocol\_version | Integer | 0 | TDSv7\.0, TDSv7\.1, TDSv7\.1\.1, TDSv7\.2, TDSv7\.3A, TDSv7\.3B, TDSv7\.4, DEFAULT | Sets a default TDS protocol version for connecting clients\. | Yes | 
| babelfishpg\_tds\.tds\_ssl\_encrypt | Boolean | 0 | 0/1 | Turns encryption on \(0\) or off \(1\) for data traversing the TDS listener port\. For detailed information about using SSL for client connections, see [How Babelfish interprets SSL settings](#babelfish-ssl)\. | Yes | 
| babelfishpg\_tds\.tds\_ssl\_max\_protocol\_version | String | 'TLSv1\.2' | 'TLSv1, TLSv1\.1, TLSv1\.2' | Sets the minimum SSL/TLS protocol version to use for the TDS session\. | Yes | 
| babelfishpg\_tds\.tds\_ssl\_min\_protocol\_version | String | 'TLSv1' | 'TLSv1, TLSv1\.1, TLSv1\.2' | Sets the minimum SSL/TLS protocol version to use for the TDS session\. | Yes | 
| babelfishpg\_tds\.tds\_default\_packet\_size | Integer | 4096 | 512\-32767 | Sets the default packet size for connecting SQL Server clients\. | Yes | 
| babelfishpg\_tds\.tds\_default\_numeric\_scale | Integer | 8 | 0\-38 | Sets the default scale of numeric type to be sent in the TDS column metadata if the engine doesn't specify one\. | Yes | 
| babelfishpg\_tds\.tds\_default\_numeric\_precision | Integer | 38 | 1\-38 | Sets the default precision of numeric type to be sent in the TDS column metadata if the engine doesn't specify one\. | Yes | 
| babelfishpg\_tsql\.version | String | null | default |  Sets the output of @@VERSION variable\. Don't modify this value for Aurora PostgreSQL DB clusters\.  | Yes | 
| babelfishpg\_tsql\.default\_locale | String | en\_US | Allowed |  Default locale used for Babelfish collations\. The default locale is only the locale and doesn't include any qualifiers\. Set this parameter when you provision a Babelfish DB cluster\. After the DB cluster is provisioned, modifications to this parameter are ignored\.  | Yes | 
| babelfishpg\_tsql\.migration\_mode | List | single\-db | single\-db, multi\-db,null |  Defines if multiple user databases are supported\. Set this parameter when you provision a Babelfish DB cluster\. After the DB cluster is provisioned, don't modify the value of this parameter\.  | No | 
| babelfishpg\_tsql\.server\_collation\_name | String | bbf\_unicode\_general\_ci\_as | [Babelfish collation support](babelfish-collations.md) |  The name of the collation used for server\-level actions\. Set once at provisioning time\. Set this parameter when you provision a Babelfish DB cluster\. After the DB cluster is provisioned, don't modify the value of this parameter\.  | Yes | 
| babelfishpg\_tds\.listen\_addresses | String | \* | null | Sets the host name or IP address or addresses to listen for TDS on\. | No | 
| babelfishpg\_tds\.enable\_tds\_debug\_log\_level | Integer | '1' | '0, 1, 2, 3' | Sets the logging level in TDS; 0 turns off logging\. | Yes | 
| babelfishpg\_tds\.unix\_socket\_directories | String | /tmp | NULL | TDS server Unix socket directories\. | No | 
| babelfishpg\_tds\.unix\_socket\_group | String | rdsdb | NULL | TDS server Unix socket group\. | No | 
| unix\_socket\_permissions | Integer | 0700 | 0 \- 511 | TDS server Unix socket permissions\. | No | 

## How Babelfish interprets SSL settings<a name="babelfish-ssl"></a>

When a client connects to port `1433`, Babelfish compares the Secure Sockets Layer \(SSL\) setting sent during the client handshake to the Babelfish SSL parameter setting \(`tds_ssl_encrypt`\)\. Babelfish then determines if a connection is allowed\. If a connection is allowed, encryption behavior is either enforced or not, depending on your parameter settings and the support for encryption offered by the client\.

The table following shows how Babelfish behaves for each combination\.


| Client SSL setting | Babelfish SSL setting | Connection allowed? | Value returned to client | 
| --- | --- | --- | --- | 
| ENCRYPT\_OFF | tds\_ssl\_encrypt=false | Allowed, the login packet is encrypted | ENCRYPT\_OFF | 
| ENCRYPT\_OFF | tds\_ssl\_encrypt=true | Allowed, the entire connection is encrypted | ENCRYPT\_REQ | 
| ENCRYPT\_ON | tds\_ssl\_encrypt=false | Allowed, the entire connection is encrypted | ENCRYPT\_ON | 
| ENCRYPT\_ON | tds\_ssl\_encrypt=true | Allowed, the entire connection is encrypted | ENCRYPT\_ON | 
| ENCRYPT\_NOT\_SUP | tds\_ssl\_encrypt=false | Yes | ENCRYPT\_NOT\_SUP | 
| ENCRYPT\_NOT\_SUP | tds\_ssl\_encrypt=true | No, connection closed   | ENCRYPT\_REQ | 
| ENCRYPT\_REQ | tds\_ssl\_encrypt=false | Allowed, the entire connection is encrypted | ENCRYPT\_ON | 
| ENCRYPT\_REQ | tds\_ssl\_encrypt=true | Allowed, the entire connection is encrypted | ENCRYPT\_ON | 
| ENCRYPT\_CLIENT\_CERT | tds\_ssl\_encrypt=false | No, connection closed | Unsupported | 
| ENCRYPT\_CLIENT\_CERT | tds\_ssl\_encrypt=true | No, connection closed | Unsupported | 