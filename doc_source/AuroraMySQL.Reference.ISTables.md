# Aurora MySQL–specific information\_schema tables<a name="AuroraMySQL.Reference.ISTables"></a>

Aurora MySQL has certain `information_schema` tables that are specific to Aurora\.

## information\_schema\.replica\_host\_status<a name="AuroraMySQL.Reference.ISTables.replica_host_status"></a>

The `information_schema.replica_host_status` table contains replication information\. The columns that you can use are shown in the following table\. The remaining columns are for Aurora internal use only\.


| Column | Data type | Description | 
| --- | --- | --- | 
| CPU | double | The CPU percentage usage of the replica host\. | 
| IS\_CURRENT | tinyint | Whether the replica is current\. | 
| LAST\_UPDATE\_TIMESTAMP | datetime\(6\) | The time the last update occurred\. Used to determine whether a record is stale\. | 
| REPLICA\_LAG\_IN\_MILLISECONDS | double | The replica lag in milliseconds\. | 
| SERVER\_ID | varchar\(100\) | The ID of the database server\. | 
| SESSION\_ID | varchar\(100\) | The ID of the database session\. Used to determine whether a DB instance is a writer or reader instance\. | 

**Note**  
When a replica instance falls behind, the information queried from its `information_schema.replica_host_status` table might be outdated\. In this situation, we recommend that you query from the writer instance instead\.  
While the `mysql.ro_replica_status` table has similar information, we don't recommend that you use it\.