---
layout: post
title: Oracle - How to strengthen Oracle Cloud Control Security?
modified: 2018-12-8
categories: [Oracle]
tags: 
  - Oracle.OEM
comments: true
---

The following Document you may need to read to do this:

<span style="color:#ff0000;"><strong>Self-signed certificate & Untrusted CA
</strong></span><br/>
EM 13c, 12c: How to Configure the Enterprise Manager Cloud Control Management Agent for Secure Socket Layer (SSL) Certificates (Doc ID 2213661.1)

<span style="color:#ff0000;"><strong>Disable TLSv1.0 / TLSv1.1 / SSLv3
</strong></span><br/>
EM 12c can just support TLS v1.0<br/>
EM 13c: Enterprise Manager 13c Cloud Control Configuration with Specific Transport Layer Security Protocol:TLSv1.0,TLSv1.1,TLSv1.2 (Doc ID 2212006.1)<br/>
EM 12c: Configure Enterprise Manager 12c Cloud Control to Accept Connections with TLSv1.0 Protocol (Doc ID 1602983.1)

<span style="color:#ff0000;"><strong>Disable Weak Cipher Suites</strong></span><br/>

13c: How to Disable Weak SSLCipherSuites in Enterprise Manager 13c Cloud Control (Doc ID 2138391.1)<br/>
EM 12c: How to Disable Weak SSLCipherSuites Used by Enterprise Manager 12c Cloud Control (Doc ID 1477287.1)<br/>
How To Disable Anonymous and Weak Cipher Suites in Oracle WebLogic Server (Doc ID 1067411.1)

<span style="color:#ff0000;"><strong>If you are using EM13.2, JDK should be upgraded in advance to at least 1.7_131</strong></span><br/>

How to Use JDK 7 Update 191 with EM 13.2 / 13.3 OMS (Doc ID 2241358.1)

<span style="color:#ff0000;"><strong>Somethings you may need:</strong></span><br/>

1-Determine the port of OMS / agent
<pre class="prettyprint lang-sh linenums=1 ">
$ /u01/app/oracle/agent/agent_13.3.0.0.0/bin/emctl status agent|grep URL
Agent URL : https://oem13:3872/emd/main/
Local Agent URL in NAT : https://oem13:3872/emd/main/
Repository URL : https://oem13:4903/empbs/upload

$ emctl status oms -details
Oracle Enterprise Manager Cloud Control 13c Release 3
Copyright (c) 1996, 2018 Oracle Corporation. All rights reserved.
Enter Enterprise Manager Root (SYSMAN) Password :
Console Server Host : oem13
HTTP Console Port : 7788
HTTPS Console Port : 7803
HTTP Upload Port : 4889
HTTPS Upload Port : 4903
EM Instance Home : /u01/app/oracle/gc_inst/em/EMGC_OMS1
OMS Log Directory Location : /u01/app/oracle/gc_inst/em/EMGC_OMS1/sysman/log
OMS is not configured with SLB or virtual hostname
Agent Upload is locked.
OMS Console is locked.
Active CA ID: 1
Console URL: https://oem13:7803/em
Upload URL: https://oem13:4903/empbs/upload

WLS Domain Information
Domain Name : GCDomain
Admin Server Host : oem13
Admin Server HTTPS Port: 7102
Admin Server is RUNNING

Oracle Management Server Information
Managed Server Instance Name: EMGC_OMS1
Oracle Management Server Instance Host: oem13
WebTier is Up
Oracle Management Server is Up
JVMD Engine is Up

BI Publisher Server Information
BI Publisher Managed Server Name: BIP
BI Publisher Server is Up

BI Publisher HTTP Managed Server Port : 9701
BI Publisher HTTPS Managed Server Port : 9803
BI Publisher HTTP OHS Port : 9788
BI Publisher HTTPS OHS Port : 9851
BI Publisher is locked.
BI Publisher Server named ‘BIP’ running at URL: https://oem13:9851/xmlpserver/servlet/home
BI Publisher Server Logs: /u01/app/oracle/gc_inst/user_projects/domains/GCDomain/servers/BIP/logs/
BI Publisher Log : /u01/app/oracle/gc_inst/user_projects/domains/GCDomain/servers/BIP/logs/bipublisher/bipublisher.log

$ cd /u01/app/oracle/
$ find . -name nodemanager.properties
./gc_inst/user_projects/domains/GCDomain/nodemanager/nodemanager.properties
$ cat ./gc_inst/user_projects/domains/GCDomain/nodemanager/nodemanager.properties|grep ListenPort
ListenPort=7403
</pre>

2-Check TLSv1.0 / TLSv1.1 / SSLv3 / Weak Cipher
<pre class="prettyprint lang-sh linenums=1 ">
$ openssl s_client -connect localhost:3872 -ssl3
$ openssl s_client -connect localhost:3872 -tls1
$ openssl s_client -connect localhost:3872 -tls1_1
$ openssl s_client -connect localhost:3872 -cipher MEDIUM
</pre>

3-Determine WLS Version
<pre class="prettyprint lang-sh linenums=1 ">
$ source ./setWLSEnv.sh
$ java weblogic.version
</pre>

4-Change the password of SYSMAN in database<br/>
https://docs.oracle.com/cd/E73210_01/EMSEC/GUID-5DD3B11A-1159-40BD-8AEB-41EDE664AB12.htm#EMSEC12918

During password change, if see error
<pre class="prettyprint lang-sh linenums=1 ">
$ /u01/app/oracle/middleware/bin/emctl config oms -change_repos_pwd -use_sys_pwd -sys_pwd oracle -new_pwd Oracle$123
Oracle Enterprise Manager Cloud Control 13c Release 3
Copyright (c) 1996, 2018 Oracle Corporation. All rights reserved.

Changing passwords in backend …
Passwords changed in backend successfully.
Updating repository password in Credential Store…
javax.management.RuntimeMBeanException: javax.management.RuntimeMBeanException
Error occurred. Check the log /u01/app/oracle/gc_inst/em/EMGC_OMS1/sysman/log/secure.log
</pre>

And also find exception in the log file<br/>
./gc_inst/em/EMGC_OMS1/sysman/log/secure.log

Refer the following document to deal with.<br/>
OPSS: Applying EM/OMS 12.1.0.3.3 PSU Patch 18604893 Fails. emctl.log Shows: Failed to find OMS details: JPS-01030: Cannot get credential. | JPS-10000: There was an internal error in the policy store.. (Doc ID 2015186.1)

5-Reset agent registration password<br/>
Enterprise Manager Cloud Control Security: How to Create or Edit the Agent Registration Password for Agent to OMS Secure Communication (Doc ID 1367946.1)