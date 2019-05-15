---
layout: post
title: Oracle - TAF&FCF and DBLink
modified: 2019-03-26
categories: [Oracle]
tags: 
  - Oracle.Configure
comments: true
---

I have an artile about Application Failover<br/>
https://westzq1.github.io/oracle/2019/02/04/Application-Failover-1.html

But I didn't have any test about the how TAF&FCF work with DBLINK

Today, I find an doc about this:
Database Link: DBLink Creation using TAF or SCAN TNS Connect String (Doc ID 399453.1)	

The key points are:
1. Database links do not support Transparent Application Failover. 
2. Configuring a dblink using a SCAN listener address in the TNS connect descriptor is not recommended.
3. A DBLink connection is more reliable using a local listener(VIP)

I tested wit FCF also in 11.2.0.4 and 18.3.0.0, it is also not working with DBLINK, like TAF.

But with FAILOVER=ON attribute in TNS, at least the connection can reconnect to another node with retry.

<pre class="prettyprint lang-sql linenums=1 ">
SQL> select instance_number from v$instance@to_o11204_taf
  2  ;

INSTANCE_NUMBER
---------------
	      1

-- kill instance 1
SQL> /

INSTANCE_NUMBER
---------------
	      2
</pre>

AC also is tested and not working with DBLINK.

