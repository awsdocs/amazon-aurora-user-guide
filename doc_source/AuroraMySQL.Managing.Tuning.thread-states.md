# Tuning Aurora MySQL with thread states<a name="AuroraMySQL.Managing.Tuning.thread-states"></a>

The following table summarizes the most common general thread states for Aurora MySQL\.


| General thread state | Description | Possible causes | Possible actions | 
| --- | --- | --- | --- | 
|  [sending data](ams-states.sending-data.md)  |  This thread state indicates that a thread is reading and filtering rows for a query to determine the correct result set\.  |  An inefficient query or suboptimal server configuration  |  Examine memory settings, plans for index usage, and the volume of data returned\.  | 