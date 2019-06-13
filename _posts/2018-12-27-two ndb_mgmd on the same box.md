---
layout: post
title: NDBCluster - How to run two ndb_mgmd on the same box?
modified: 2018-12-27
categories: [MySQL, NDBCluster]
tags: 
  - MySQL.NDBCluster
comments: true
---
For the test propose, I want to save some resource, so I decided to start two management processes for PRIMARY and STANDBY clusters on the same machine.

Unfortunately, we cannot just invoke ndb_mgmd with different configuration files twice, because some files on disk will be overwritten. 

We have to do the following things:

<b>Two folders for the cache of configuration files</b>
1. /usr/local/mysql/mysql-cluster
2. /usr/local/mysql/mysql-standby

<b>Two DataDir folders which used for log files and lock files, if using the same NodeId number for both clusters.</b>
1. /var/lib/mysql-cluster
2. /var/lib/mysql-standby

<b>Two configuration files</b>
1. /var/lib/mysql-cluster/config.ini
2. /var/lib/mysql-cluster/standby.ini

<b>Change the following parameters for standby cluster.</b>
1. DataDir, if using the same NodeId for the management node</li>
2. NodeId, if using the same DataDir for the management node</li>
3. PortNumber in [ndb_mgmd]</li>
4. ServerPort in [ndbd default] if the standby cluster on the same machines with primary cluster.
5. The data node / sql node information in [ndbd] and [mysqld]

For my configuration
<img src="{{site.baseurl}}/images/20181227.1.png" />

Then, start the Primary Cluster
<pre class="prettyprint lang-sh linenums=1 ">/usr/local/mysql/bin/ndb_mgmd -f /var/lib/mysql-cluster/config.ini</pre>

Next, the Standby Cluster
<pre class="prettyprint lang-sh linenums=1 ">/usr/local/mysql/bin/ndb_mgmd -f /var/lib/mysql-cluster/standby.ini --configdir /usr/local/mysql/mysql-standby</pre>