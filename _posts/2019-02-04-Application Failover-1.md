---
layout: post
title: Oracle - Application Failover
modified: 2019-02-04
categories: [Oracle]
tags: 
  - Oracle.Configure
comments: true
---

Many DBAs may never pay attentions on this topic. I talked with some of my friends about how to make the application failover smoothly, the answer is TAF.

Actually, TAF is a failover machnism for type 2 driver, like OCI. It is not working on JDBC Thin which is a type 4 driver.

Now, I want to talk a little bit about this TOPIC on RAC

There are four goals of failover:
1. a connect strings, can connect to alive node without any change.
2. a way to reconnect automatically.
3. a method to detect the node failure ASAP.
4. a mean to handle uncommit transaction after reconnect to alive nodes. 

For the first goal, we have at least the following three ways:
1. use DNS, but how DNS know the node/database failure is a little hard to archive.
2. put the IP addresses of all instances into connection strings.
3. use scan-ip after 11.2

I don't want to talk method 1, it is uncommon with Oracle RAC. 
The following is the configuration of method 2 and 3.
<pre class="prettyprint lang-sql linenums=1 ">
orcl-vip = 
(DESCRIPTION= 
  (FAILOVER=on) 
  (ADDRESS_LIST= 
    (LOAD_BALANCE=off)(CONNECT_TIMEOUT=3)(RETRY_COUNT=3) 
    (ADDRESS=(PROTOCOL=TCP)(HOST=database01-vip)(PORT=1521))
    (ADDRESS=(PROTOCOL=TCP)(HOST=database02-vip)(PORT=1521))
  )
  (CONNECT_DATA=(SERVICE_NAME=orcl))
)
-- or
orcl-scan = 
(DESCRIPTION= 
  (FAILOVER=on) 
  (ADDRESS_LIST= 
    (LOAD_BALANCE=on)(CONNECT_TIMEOUT=3)(RETRY_COUNT=3) 
    (ADDRESS=(PROTOCOL=TCP)(HOST=rac-scan.rac.database.zhangqiaoc.com)(PORT=1521))
  )
  (CONNECT_DATA=(SERVICE_NAME=orcl))
)
</pre>

Be aware, FAILOVER here is not TAF, it means if the first IP is not available, try next. It is ON by default. If set FAILOVER and LOAD_BALANCE to OFF, if the first IP is not available, a error will be reported:
<pre class="prettyprint lang-sql linenums=1 ">
$ sqlplus system/oracle@orcl-vip

SQL*Plus: Release 18.0.0.0.0 - Production on Mon Feb 4 15:15:51 2019
Version 18.3.0.0.0

Copyright (c) 1982, 2018, Oracle.  All rights reserved.

ERROR:
ORA-12514: TNS:listener does not currently know of service requested in connect
descriptor
</pre>

Now, our fisrt goal is accomplished. 
Next, how to reconnect to survive nodes without intervention.

There are two ways:
1. TAF (transparent Application Failover), is for type 2 drivers, such of OCI, JDBC OCI, and .NET except ODP.
2. FCF (Fast Connection Failover), is for type 4 drivers, such of JDBC Thin, ODP.net, but it is also working for OCI.

For TAF, both client side and server side are available.<br/>
The connection strings I give in part 1 is not client TAF on, so, after the fail of the instance 1
<pre class="prettyprint lang-sql linenums=1 ">
SQL> select instance_number from v$instance;

INSTANCE_NUMBER
---------------
	      1

-- after shutdown node 2
SQL> /
select instance_number from v$instance
*
ERROR at line 1:
ORA-03135: connection lost contact
Process ID: 1099
Session ID: 25 Serial number: 48031


SQL> /
ERROR:
ORA-03114: not connected to ORACLE
</pre>

We turn client TAF on by using FAILOVER_MODE:
<pre class="prettyprint lang-sql linenums=1 ">
orcl-vip = 
(DESCRIPTION=
  (FAILOVER=on)
  (ADDRESS_LIST= 
    (LOAD_BALANCE=on)(CONNECT_TIMEOUT=3)(RETRY_COUNT=3) 
    (ADDRESS=(PROTOCOL=TCP)(HOST=database01-vip.database.zhangqiaoc.com)(PORT=1521))
    (ADDRESS=(PROTOCOL=TCP)(HOST=database02-vip.database.zhangqiaoc.com)(PORT=1521))
  )
  (CONNECT_DATA=(SERVICE_NAME=orcl)
                (FAILOVER_MODE = (TYPE=SELECT)(METHOD=BASIC))
  )
)
</pre>

<pre class="prettyprint lang-sql linenums=1 ">
SQL> select instance_number from v$instance;

INSTANCE_NUMBER
---------------
	      1

-- after shutdown node 2
SQL> /

INSTANCE_NUMBER
---------------
	      2
</pre>

TAF server side:<br/>
<pre class="prettyprint lang-sql linenums=1 ">
$ srvctl add service -d orcl -s taf -r orcl1,orcl2 -e select -m basic -w 10 -z 150
$ srvctl start service -d orcl -s taf
</pre>
For JDBC Thin mode, we need to use FCF. FCF doesn't work with a service which TAF is on, so we need to create a sperated service for FCF which TAF is off.<br/>
<pre class="prettyprint lang-sql linenums=1 ">
$ srvctl add service -d orcl -s fcf -r orcl1,orcl2 -e none -m none -w 0 -z 0
$ srvctl start service -d orcl -s fcf
</pre>
When we enable server side TAF / FCF, the connection string should be the same as STEP 1, which is client TAF disabled.

Next, we needs a way to notify the application when the node is out of work ASAP.

Oracle affords FAN(Fast Application Notificaton) for this, and a lot of clients has integrated FAN with:
1. OCI Session Pools
2. Universal Connection Pool for JAVA
3. Thin JDBC Driver (12.2 and later)
4. ODP.NET managed and unmanaged providers.
5. All Weblogic Server data sources, and Oracle Tuxedo.
6. PHP
7. Global Data Services
8. Data Guard Broker
9. OEM
10. Tomcat / Websphere with UCP

Let us see, at first, how the FAN works when the nodes down.

When the FAN is not properly configured, the session cannot break this "select" due to there is no further data communication. 
After I shut down the host 2 directly, the sqlplus session is still there until I kill it directly after two hours.
<pre class="prettyprint lang-sql linenums=1 ">
[2/5/2019 11:58:02 AM] INSTANCE_NUMBER
[2/5/2019 11:58:02 AM] ---------------
[2/5/2019 11:58:02 AM] 	      2
[2/5/2019 11:58:02 AM] 
[2/5/2019 11:58:04 AM] SQL> 
[2/5/2019 11:58:04 AM] SQL> 
[2/5/2019 11:58:25 AM] SQL> select count(*) from dba_objects, dba_objects;
                       -- shutdown node 2 
[2/5/2019 1:50:00 PM] 
[2/5/2019 1:50:00 PM] 
[2/5/2019 1:59:59 PM] 
[2/5/2019 1:59:59 PM] 
[2/5/2019 1:59:59 PM] 
                      -- after 2.5 hours
[2/5/2019 2:33:33 PM] Killed
</pre>

After I set up FAN for SQLPLUS:
<pre class="prettyprint lang-sql linenums=1 ">
$ srvctl modify service -d orcl -s  taf -notification true
</pre>

SQLPLUS can break the connection and reconnect to alive node almost 30 seconds.
<pre class="prettyprint lang-sql linenums=1 ">
[2/5/2019 2:49:46 PM] INSTANCE_NUMBER
[2/5/2019 2:49:46 PM] ---------------
[2/5/2019 2:49:46 PM] 	      2
[2/5/2019 2:49:46 PM] 
[2/5/2019 2:50:12 PM] SQL> select count(*) from dba_objects, dba_objects;
                      -- shutdown node 2
[2/5/2019 2:50:49 PM] 
[2/5/2019 2:50:49 PM] 
                      -- only after 30 seconds
[2/5/2019 2:51:18 PM] select count(*) from dba_objects, dba_objects
[2/5/2019 2:51:18 PM] *
[2/5/2019 2:51:18 PM] ERROR at line 1:
[2/5/2019 2:51:18 PM] ORA-25408: can not safely replay call
</pre>

On this example, ORA-25408 is reported, it is an expected error.
If we want to SELECT failover, we should set the failover_mode to SELECT
<pre class="prettyprint lang-sql linenums=1 ">
$ srvctl modify service -d orcl -s taf -e select
</pre>

FAN is only defined event, so, there are some mechism to send out the event of node down, database down, and etc.

Two method for this:
1. AQ HA notfication, for OCI and ODP.NET unmanaged client before 12.1
2. ONS(Oracle Notification Service), for JDBC, and OCI and ODP.NET after 12.1

The followed graphic shows that the mechisms the different drivers use in 10,11,and 12.
<img class="alignnone size-full wp-image-187" src="/images/20190204.1.png" alt="Capture" width="591" height="280" /><br/>
Fast Application Notification (FAN) Includes fanWatcher: A utility to subscribe to ONS and view FAN events<br/>
https://www.oracle.com/technetwork/database/options/clustering/applicationcontinuity/learnmore/fastapplicationnotification12c-2538999.pdf

With the installation of GRID, we only need to create a dynamic service for clients. <br/>
Nothing needed to be set on the server side, except for OCI and ODP.NET<br/>
The notification attribute of the service has to be set to TRUE for them.
<pre class="prettyprint lang-sql linenums=1 ">
$ srvctl modify service -d orcl -s  taf -notification true
</pre>

But for the client side, it is extremely complicated.
You can read the following document to figure out how to set up:<br/>
1. Fast Application Notification (FAN) Includes fanWatcher: A utility to subscribe to ONS and view FAN events<br/>
2. Client Failover Best Practices for Highly Available Oracle Databases Oracle Database 12c <br/>
3. Client Failover Best Practices for Highly Available Oracle Databases: Oracle Database 11g Release 2

For example:<br/>
JDBC with UCP
1. code
    <pre class="prettyprint lang-java linenums=1 ">
    pds = PoolDataSourceFactory.getPoolDataSource();
    pds.setConnectionPoolName("FCFSamplePool");
    pds.setFastConnectionFailoverEnabled(true);
    // pds.setONSConfiguration("propertiesfile=/usr/ons/ons.properties");
    Properties prop = new Properties();
    prop.put(oracle.net.ns.SQLnetDef.TCP_CONNTIMEOUT_STR, ""+3000); // 3000ms
    prop.put("oracle.jdbc.thinForceDNSLoadBalancing","true");
    pds.setConnectionProperties(prop);
    pds.setConnectionFactoryClassName("oracle.jdbc.pool.OracleDataSource");
    pds.setUser("system");
    pds.setPassword("oracle");
    pds.setURL("jdbc:oracle:thin@((CONNECT_TIMEOUT=4)(RETRY_COUNT=30)(RETRY_DELAY=3) "+
    " (ADDRESS_LIST = "+
    " (LOAD_BALANCE=on) "+
    " ( ADDRESS = (PROTOCOL = TCP)(HOST=RAC-SCAN)(PORT=1521))) "+
    " (ADDRESS_LIST = "+
    " (LOAD_BALANCE=on) "+
    "( ADDRESS = (PROTOCOL = TCP)(HOST=DG-SCAN)(PORT=1521)))"+
    "(CONNECT_DATA=(SERVICE_NAME=service_name)))");
    </pre>

2. jar
ons.jar, ucp.jar ,and the jdbc driver jar file should in the CLASSPATH


Now, I know what is TAF, FCF, FAN, and ONS, and our application can survive from the node / instance failure. 
But for TAF / FCF, it only affored the failover of SELECT statement, the transaction have to be rollback after failover.
<pre class="prettyprint lang-sql linenums=1 ">

INSTANCE_NUMBER
---------------
	      1

SQL> truncate table test1;        

Table truncated.

SQL> select * from test1;

no rows selected

SQL> insert into test1 values(1);

1 row created.

-- shutdown abort instance 1;

SQL> @i.sql
select instance_number from v$instance
            *
ERROR at line 1:
ORA-25402: transaction must roll back


SQL> rollback;

Rollback complete.

SQL> @i.sql

INSTANCE_NUMBER
---------------
	      2
</pre> 

From 12.1, Oracle affords Application Continuity with Transaction Guard to handle this. <br/>
For 12.1, AC is only supported on Java, from 12.2, AC can be used on OCI, ODP.net

How to enable Application Continuity on Service?<br/>
-failovertype transaction -- AC <br/>
-commit_outcome TRUE      -- TG <br/>
<pre class="prettyprint lang-sql linenums=1 ">
srvctl add service -d orcl -pdb hr -s ac -failovertype transaction -failovermethod basic \
                                         -commit_outcome TRUE -preferred orcl1,orcl2
</pre>
And, the REMOTE_LISTENER setting for the database must include the addresses in the ADDRESS_LISTs for all URL used for client connection, SCAN or VIPs

But, transaction guard can be enabled separately, Application Continuity has to be enabled with Transaction Guard.
<pre class="prettyprint lang-sh linenums=1 ">
[oracle@database01 ~]$ srvctl modify service -d orcl -pdb hr -s ac -failovertype select -failovermethod basic -commit_outcome TRUE
[oracle@database01 ~]$ srvctl modify service -d orcl -pdb hr -s ac -failovertype transaction  -failovermethod basic -commit_outcome FALSE
PRCD-1083 : Failed to modify service ac
PRCD-1271 : Failed to unset commit outcome because the failover type is set to TRANSACTION
</pre>

I tested with sqlplus, but AC is not working, the ORA-41412 is always reported, and the failover failed.
But, it is working fine with JDBC
    How To Test Application Continuity Using A Standalone Java Program (Doc ID 1602233.1)	

However, the code has some problem, we modified a little bit.<br/>
The sql we want to pretect should be in between beginRequest() and endRequest() functions.<br/>
<pre class="prettyprint lang-java linenums=1 ">
import java.sql.*;
import oracle.jdbc.*;
public class AcTest
{
public static void main(String[] args) throws SQLException,java.lang.InterruptedException
{
oracle.jdbc.replay.OracleDataSource  AcDatasource = oracle.jdbc.replay.OracleDataSourceFactory.getOracleDataSource();
AcDatasource.setURL("jdbc:oracle:thin:@rac-scan.rac.database.zhangqiaoc.com:1521/acservice");
AcDatasource.setUser("CTAIS2");
AcDatasource.setPassword("oracle");

Connection conn = AcDatasource.getConnection();

conn.setAutoCommit(false);

PreparedStatement stmt = conn.prepareStatement("select instance_name from v$instance");
ResultSet rset = stmt.executeQuery();
while (rset.next())
{
System.out.println("You are Connected to RAC Instance - "+ rset.getString(1));
}

// the code you want to protected should in between begin and end
((oracle.jdbc.replay.ReplayableConnection)conn).beginRequest();

stmt = conn.prepareStatement("insert into test1 values(1)");
stmt.execute();

// shutdown the pdb here
Thread.currentThread().sleep(60000);

PreparedStatement stmt1 = conn.prepareStatement("select instance_name from v$instance");
ResultSet rset1 = stmt1.executeQuery();
while (rset1.next())
{
System.out.println("After Replay Connected to RAC Instance - "+rset1.getString(1));
} 

// finally, we can see the new record 
conn.commit(); 

rset.close();
stmt.close();
rset1.close();
stmt1.close();

conn.close();

((oracle.jdbc.replay.ReplayableConnection)conn).endRequest();
}
}
</pre>

Compile and Test
<pre class="prettyprint lang-sh linenums=1 ">
$ export PATH=/u01/app/18.3.0/grid/jdk/bin:$PATH
$ export CLASSPATH=.:$CLASSPATH
$ javac -Djava.ext.dirs=/u01/app/oracle/product/18.3/db_1/jdbc/lib AcTest.java
$ java -Djava.ext.dirs=/u01/app/oracle/product/18.3/db_1/jdbc/lib:/u01/app/oracle/product/12.1/db_1/oc4j/lib  AcTest
[oracle@database01 ~]$ java -Djava.ext.dirs=/u01/app/oracle/product/18.3/db_1/jdbc/lib:/u01/app/oracle/product/12.1/db_1/oc4j/lib  AcTest
You are Connected to RAC Instance - orcl1
# shutdown instance 1
After Replay Connected to RAC Instance - orcl2
</pre>



Database Connection and Failover Errors Recognized by DBService (Doc ID 2268932.1)	
Failover-related errors 
    ORA-25401	Can not continue fetches
    ORA-25402	Transaction must roll back
    ORA-25405	Transaction status unknown
    ORA-25408	Can not safely replay call
    ORA-25409	Failover happened during the network operation (lost on fetching a LOB column)
    ORA-25425	Connection lost during rollback