---
layout: post
title: NDBCluster - a case of brain split
modified: 2018-11-19
categories: [MySQL, NDBCluster]
tags: 
  - MySQL.NDBCluster
comments: true
---

We have a test ndbcluster, the whole cluster failed every night during 11:00 pm to 1:00 am.
And then, the data node restarted automatically, so no one know this until last week, one of two data nodes didn't start successfully.

I checked the cluster log, I found that the manage node 6 disconnected with 1(another management node), 2(data node), 3(data node).
<pre class="prettyprint linenums=1 ">
2018-11-16 23:11:30 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 6: Node 3 Disconnected
2018-11-16 23:11:30 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 6: Node 1 Disconnected
2018-11-16 23:11:30 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 6: Node 2 Disconnected
2018-11-16 23:12:36 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 6: Node 1 Disconnected
2018-11-18 00:23:49 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 6: Node 2 Disconnected
2018-11-18 00:23:49 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 6: Node 1 Disconnected
2018-11-18 00:24:16 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 6: Node 1 Disconnected
</pre>

The data node and management node are on the same machine, Node 1(M) and 2(D) on one box, Node 3(D) and 6(M) on the another box.<br/>
I don't know why the former DBA did this, it is not safe for ndbcluster due to one of the management nodes is an arbitrator.<br/>
If the machine which is as an aribtrator in the cluster is down, the whole cluster would be shut down to prevent data corruption.

So, there is an interesting situation that Node 6 is also missing connection with node 3 which is on the same machine.<br/>
Even though I doubt it is a network-related problem, it is hard to talk with Network engineer about this.

So, I deployed a script to ping each machie.<br/>
I can see the pocket lost, at the same time, the data node will report missing heartbeat.

<pre class="prettyprint lang-sh linenums=1 ">
/usr1/dba/appadm11>>cat xxx.log|grep -E "EST 2018|loss|statistics"|awk '{
> if($0 ~ /EST 2018/){
>   TIME=$2" "$3" "$4
>   getline;
>   IP=$2;
>   getline;
>   LOSS=$6;
>   printf "%s %s %s\n",TIME,IP,LOSS;
> }
> }'|grep -v " 0%"
Nov 16 23:11:31 192.168.92.81 100%
Nov 16 23:11:45 192.168.92.81 100%
Nov 16 23:11:59 192.168.92.81 100%
Nov 16 23:12:57 192.168.92.81 33%
Nov 17 00:27:17 192.168.92.81 33%
Nov 17 00:45:32 192.168.92.81 33%
Nov 17 00:45:37 192.168.92.81 100%
Nov 17 00:45:51 192.168.92.81 100%
Nov 17 00:46:05 192.168.92.81 100%
Nov 18 00:21:41 192.168.92.81 33%
Nov 18 00:23:10 192.168.92.81 100%
Nov 18 00:23:24 192.168.92.81 100%
Nov 18 00:23:38 192.168.92.81 100%
Nov 19 00:23:18 192.168.92.81 33%
Nov 19 00:23:23 192.168.92.81 100%
Nov 19 00:23:37 192.168.92.81 100%
Nov 19 00:23:51 192.168.92.81 100%
</pre>

<pre class="prettyprint lang-sh linenums=1 ">
-- cluster log
2018-11-16 23:12:05 [/l01/mysql/cluster_data/6.log] WARNING  -- Node 3: Node 1 missed heartbeat 2
2018-11-16 23:12:06 [/l01/mysql/cluster_data/6.log] WARNING  -- Node 3: Node 1 missed heartbeat 3
2018-11-16 23:12:08 [/l01/mysql/cluster_data/6.log] WARNING  -- Node 3: Node 1 missed heartbeat 4
2018-11-16 23:12:08 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 3: Node 1 declared dead due to missed heartbeat
2018-11-18 00:23:59 [/l01/mysql/cluster_data/6.log] WARNING  -- Node 3: Node 1 missed heartbeat 2
2018-11-18 00:24:00 [/l01/mysql/cluster_data/6.log] WARNING  -- Node 3: Node 1 missed heartbeat 3
2018-11-18 00:24:02 [/l01/mysql/cluster_data/6.log] WARNING  -- Node 3: Node 1 missed heartbeat 4
2018-11-18 00:24:02 [/l01/mysql/cluster_data/6.log] ALERT    -- Node 3: Node 1 declared dead due to missed heartbeat
2018-11-19 00:24:02 [/l01/mysql/cluster_data/6.log] WARNING  -- Node 3: Node 4 missed heartbeat 2 
</pre>

So, with evidence in hand, we talked with the network guy, and he found there is a snapshot job at night, the whole machine may hang for several seconds.<br/>
After removing this job, the cluster is running smoothly.