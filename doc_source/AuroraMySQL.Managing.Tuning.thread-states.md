# Tuning Aurora MySQL with thread states<a name="AuroraMySQL.Managing.Tuning.thread-states"></a>

The following table summarizes the most common general thread states for Aurora MySQL\.


| General thread state | Description | 
| --- | --- | 
|  [creating sort index](ams-states.sort-index.md)  |  This thread state indicates that a thread is processing a `SELECT` statement that requires the use of an internal temporary table to sort the data\.  | 
|  [sending data](ams-states.sending-data.md)  |  This thread state indicates that a thread is reading and filtering rows for a query to determine the correct result set\.  | 