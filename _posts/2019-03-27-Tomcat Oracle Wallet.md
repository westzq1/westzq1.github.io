---
layout: post
title: Oracle - Tomcat with Oracle Wallet
modified: 2019-03-27
categories: [Tomcat]
tags: 
  - Tomcat.Oracle
comments: true
---

We modify the password of applications regularly, it is such a painful work every time. So I want to use Oracle Wallet instead of password in the configuration files.

At first, I test with Tomcat:

1.Copy 4 jar files from $ORACLE_HOME to $CATALINA_HOME/lib
<pre class="prettyprint lang-sql linenums=1 ">
ojdbc7.jar
oraclepki.jar
osdt_cert.jar
osdt_core.jar
</pre>

2.Create folders for tnsnames.ora and wallet.
<pre class="prettyprint lang-sql linenums=1 ">
mkdir $CATALINA_HOME/network/admin
mkdir $CATALINA_HOME/network/wallet
</pre>

3.Create tnsnames.ora in $CATALINA_HOME/network/admin
<pre class="prettyprint lang-sh linenums=1 ">
$ cat $CATALINA_HOME/network/admin/tnsnames.ora
PPRD3_INTEGMGR =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(Host = xxx.pprd.xxx.edu)(Port = 2336))
    )
    (CONNECT_DATA =
      (SID = PPRD)
    )
  )  

PPRD3_SSOMGR =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(Host = xxx.pprd.xxx.edu)(Port = 2336))
    )
    (CONNECT_DATA =
      (SID = PPRD)
    )
  )  
</pre>

4.Set the path of tnsnames.ora and wallet in bin/setenv.sh
<pre class="prettyprint lang-sh linenums=1 ">
export CATALINA_HOME=/l01/app/tomcat_8446
export CATALINA_PORT=8446
export JAVA_OPTS="-server -Xms2g -Xmx4g -Doracle.jdbc.autoCommitSpecCompliant=false -Dlog4j.configuration=config.properties -Doracle.net.tns_admin=${CATALINA_HOME}/network/admin -Doracle.net.wallet_location=${CATALINA_HOME}/network/wallet"
CATALINA_PID="${CATALINA_HOME}/catalina${CATALINA_PORT}.pid"
</pre>

5.Modify connect strings in conf/setenv.sh
<pre class="prettyprint lang-xml linenums=1 ">
&lt;pre class="prettyprint lang-sh linenums=1 "&gt;
     &lt;Resource name="jdbc/ssomgr_integmgr_banner" auth="Container" type="javax.sql.DataSource"
       driverClassName="oracle.jdbc.OracleDriver"
       url="jdbc:oracle:thin:@PPRD3_INTEGMGR"
        initialSize="5" maxTotal="600" maxIdle="-1" maxWaitMillis="30000"
        validationQuery="select 1 from dual"
        accessToUnderlyingConnectionAllowed = "true"
        removeAbandonedOnBorrow = "true"
        testOnBorrow="true"/&gt;

    &lt;Resource name="jdbc/ssomgr" auth="Container"
        type="javax.sql.DataSource"
        driverClassName="oracle.jdbc.OracleDriver"
        url="jdbc:oracle:thin:@PPRD3_SSOMGR"
        initialSize="5" maxTotal="600" maxIdle="-1" maxWaitMillis="30000"
        validationQuery="select 1 from dual" 
        accessToUnderlyingConnectionAllowed = "true"
        removeAbandonedOnBorrow = "true"
        testOnBorrow="true"/&gt;
&lt;/pre&gt;
</pre>

6.Create wallet on the other machine which has Oracle Client / Database installed.
<pre class="prettyprint lang-sh linenums=1 ">
echo &lt;WALLET PWD&gt; | mkstore -wrl /tmp/wallet -create
echo &lt;WALLET PWD&gt; | mkstore -wrl /tmp/wallet -createCredential PPRD3_INTEGMGR INTEGMGR &lt;DB USER PWD&gt;
echo &lt;WALLET PWD&gt; | mkstore -wrl /tmp/wallet -createCredential PPRD3_SSOMGR SSOMGR &lt;DB USER PWD&gt;
</pre>

7.Copy the wallet to the Tomcat

8.Exclude jar in the Tomcat Jar Filter, in conf/catalina.properties
<pre class="prettyprint lang-sql linenums=1 ">
tomcat.util.scan.StandardJarScanFilter.jarsToSkip=
Add oraclepki.jar,osdt_cert.jar,osdt_core.jar at the end.
</pre>


