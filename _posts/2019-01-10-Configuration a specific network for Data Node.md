---
layout: post
title: NDBCluster - Configuration a private network for Data Nodes
modified: 2019-01-10
categories: [MySQL, NDBCluster]
tags: 
  - MySQL.NDBCluster
comments: true
---
It is recommended to split the Data Nodes from the SQL Nodes and, use a specific network for the intercommunication of Data Nodes.

The way to archive this is to use [TCP] in the cluster configuration.

For example, in my test env, two data node:
  ndb02 Public: 83.16.16.52 Private: 192.168.123.52
  ndb03 Public: 83.16.16.53 Private: 192.168.123.53

In the config.ini:

<pre class="prettyprint lang-sql linenums=1 ">[ndbd]
HostName=ndb02
NodeId=2

[ndbd]
HostName=ndb03
NodeId=3
</pre>

In order to use the private network for the intercommunication in among data nodes, we need to add the following entities in the config.ini. 
<pre class="prettyprint lang-sql linenums=1 ">[TCP]
NodeId1=2
NodeId2=3
HostName1=192.168.123.52
HostName2=192.168.123.53
</pre>

Due to I only have two data nodes, so there is just one pair. If you have more than 2, there should be one [TCP] for each pair.

For example, if there are three data nodes, we need 1 for (1,2), 1 for (1,3) and 1 for (2,3)

The document said that this change only needs Node Restart, but that is Yes and No.
One pair of nodes needs to be shutdown at the same time and then, start.
So, if replics equal to 2, and the cluster only has two Data Nodes, so, there is only 1 pair, so all Data Nodes have to be shutdown at the same time.
If there are four Data Nodes, we can do it for 2 nodes each time. But each time, we can only add one pair in the config.ini, and do Node Restart.

After restart, we can make sure which interface is used for intercommunication:

<pre class="prettyprint lang-sql linenums=1 ">mysql> select * from ndbinfo.transporters where (node_id = 2 and remote_node_id = 3) or (node_id=3 and remote_node_id=2) \G
*************************** 1. row ***************************
node_id: 2
remote_node_id: 3
status: CONNECTED
remote_address: 192.168.123.53
bytes_sent: 414924
bytes_received: 228560
connect_count: 1
overloaded: 0
overload_count: 0
slowdown: 0
slowdown_count: 0
*************************** 2. row ***************************
node_id: 3
remote_node_id: 2
status: CONNECTED
remote_address: 192.168.123.52
bytes_sent: 228560
bytes_received: 414924
connect_count: 1
overloaded: 0
overload_count: 0
slowdown: 0
slowdown_count: 0
2 rows in set (0.01 sec)
</pre> 