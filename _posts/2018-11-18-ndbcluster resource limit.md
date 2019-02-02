---
layout: post
title: NDBCluster - Resource Limit
modified: 2018-11-18
categories: [MySQL, NDBCluster]
tags: 
  - MySQL.NDBCluster
comments: true
---

MySQL doesn’t offer a documented and easy way to check the usage of resource, like Tables, Indexes, Scan, and etc.

Fortunately, there is an internal table in NDBINFO database, named <span style="color:#ff0000;"><strong>ndb$pools</strong></span> can be used to check the current and historical high usage of most of resources.

I list the description and the name of each resource.

Use the following statement to check:
<pre class="prettyprint lang-sql linenums=1 ">
ndb_mgm> 2 DUMP 2514 0
select count(*) from ndbinfo.cluster_operations;
select count(distinct fq_name) from ndbinfo.memory_per_fragment where type = 'Ordered index';
select * from ndbinfo.ndb$pools where pool_name in(
'Attribute Record',
'Index Op',
'TC Scan Record',
'Scan Fragment',
'SubOp',
'Fired Trigger',
'Subscriber',
'Subscription',
'Table Record',
'Trigger Record',
'Index') and block_instance=0;
show engine ndbcluster status;
</pre>

<b>MaxNoOfAttributes</b>
<ul>
	<li>Default: 1000</li>
	<li>Describe: Table, Secondary Unique Hash Index, Ordered Index and BLOB will consume attributes.</li>
	<li>Usage: Attribute Record</li>
</ul>
<b>MaxNoOfConcurrentIndexOperations</b>
<ul>
	<li>Default: 8192</li>
	<li>Describe: For queries using a unique hash index at the same time This record is allocated only while executing a part of a query. As soon as this part has been executed, the record is released.</li>
	<li>Usage: Index Op</li>
</ul>
<b>MaxNoOfConcurrentOperations </b>
<ul>
	<li>Default: 32768</li>
	<li>Describe: In a transaction, the DML on each row needs a entry on each replica. So, update 4 rows on data node 1 and with 2 replica, needs 8 entities.</li>
	<li>Usage: ndbinfo.cluster_operations for current</li>
</ul>
<b>MaxNoOfConcurrentScans</b>
<ul>
	<li>Default: 256</li>
	<li>Describe: All operations but primary or unique key lookups are scan operations. In addition, a scan record is also required for full table scans. It is recommended to set to 500 which is the upper limit</li>
	<li>Usage<b>: </b>TC Scan Record</li>
</ul>
<b>MaxNoOfLocalScans</b>
<ul>
	<li>Default: 0 = MaxNoOfCuncurrentScans * number_of_data_nodes * 4 + 2</li>
	<li>Describe: In contrast to scan record, which is used for taking care of scan accessing the entire cluster, local scan record is responsible for accessing data in its own data node.</li>
	<li>Usage: Scan Fragment</li>
</ul>
<b>MaxNoOfConcurrentSubOperations</b>
<ul>
	<li>Default: 256</li>
	<li>Describe: Max no of concurrent subscriber operations</li>
	<li>Usage: SubOp</li>
<pre class="prettyprint lang-sql linenums=1 ">
mysql> show engine ndbcluster status;
| ndbcluster | NdbOperation          | created=332, free=332, sizeof=944 
</pre>
</ul>
<b>MaxNoOfConcurrentTransactions</b>
<ul>
	<li>Default: 4096</li>
	<li>Describe:
<ul>
	<li>It doesn’t represent the number of transactions. It represents the number of transaction records instead. A transaction record is a memory buffer used by Transaction Coordinator (TC). One TC on a certain data node takes care of each transaction throughout its lifecycle. Every data node has TC on it. Transaction records is used for coordinating transactions between the data nodes.</li>
	<li>Each active transaction needs a record. And each table the thread access in the transaction needs a record.</li>
	<li>The data nodes do cache the transaction objects. So if you had a (mysqld) connection that has used more transaction objects than is usually required for the connections, you can reconnect to release the cached transaction objects.</li>
	<li>It is recommended to set it as max_connections * (average_number_of_tables_accessed_per_transaction + 1) * number_of_sql_nodes / number_of_node_groups</li>
</ul>
</li>
	<li>Usage:</li>
</ul>
<pre class="prettyprint lang-sql linenums=1 ">
mysql> show engine ndbcluster status;
| ndbcluster | NdbTransaction        | created=5, free=0, sizeof=368   

ndb_mgm> 2 DUMP 2514 0
-- log on the data node.
Start of ApiConnectRec summary (4096 total allocated)
Api node 4 connect records seized : 6 stateless : 0 stateful : 1 scan : 0
Api node 5 connect records seized : 0 stateless : 0 stateful : 0 scan : 0
</pre>

<b>MaxNoOfFiredTriggers</b>
<ul>
	<li>Default: 4000</li>
	<li>Describe: A record is created when an operation is performed that affects a unique hash index. Inserting or deleting a record in a table with unique hash indexes or updating a column that is part of a unique hash index fires an insert or a delete in the index table.</li>
	<li>Usage: Fired Trigger</li>
</ul>
<b>MaxNoOfOrderedIndexes</b>
<ul>
	<li>Default: 128</li>
	<li>Describe: Total number of ordered indexes that can be defined in the system</li>
	<li>Usage:</li>
</ul>
select count(distinct fq_name) from ndbinfo.memory_per_fragment where type = 'Ordered index'

<b>MaxNoOfSubscribers</b>
<ul>
	<li>Default: 0 == 2 * MaxNoOfTables</li>
	<li>Describe: For NDB replication only. There is a replication subscription for each table each direction. So, the default value is enough for two nodes with bidirectional replication. But, for a circular replication setup using three Nodes, we need to change this value to 3 * MaxNoOfTables</li>
	<li>Usage: Subscriber</li>
</ul>
<b>MaxNoOfSubscriptions</b>
<ul>
	<li>Default: 0 = MaxNoOfTables</li>
	<li>Describe: Each NDB table in an NDB Cluster requires a subscription in the NDB kernel.</li>
	<li>Usage: Subscription</li>
</ul>
<b>MaxNoOfTables</b>
<ul>
	<li>Default: 128, but it is not the exact number you can have, the actually number of objects is calculated based on this value.</li>
	<li>Describe: Every objects, including:Table, Secondary Unique Hash Index, Ordered Index and BLOB, will consume one entry of this resource.</li>
	<li>Usage: Table Record. The exact limit will also be here.</li>
</ul>
<b>MaxNoOfTriggers</b>
<ul>
	<li>Default: 768</li>
	<li>Describe: Total number of triggers that can be defined in the system. The number of required triggers is adjusted internally using the following options: MaxNoOfTables, MaxNoOfOrderedIndexes, and MaxNoOfUniqueHashIndexes.</li>
	<li>Usage: Trigger Record</li>
</ul>
<b>MaxNoOfUniqueHashIndexes</b>
<ul>
	<li>Default: 64</li>
	<li>Describe: Total number of unique hash indexes that can be defined in the system. But this parameter is not working, it doesn’t restrict the number of Unique Hash Indexes in the system.</li>
	<li>Usage: Index</li>
</ul>