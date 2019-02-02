---
layout: post
title: Oracle - OMS responded illegally [ERROR- Failed to Update Target Type Metadata]
modified: 2018-12-17
categories: [Oracle]
tags: 
  - Oracle.OEM
comments: true
---
We want to upgrade the JDK for EM agent.
Following the DOC:<br/>
<strong>How to Use JDK 7 Update 191 with EM 13.2 / 13.3 OMS (Doc ID 2241358.1)</strong>

The list of JDK:<br/>
<strong>All Java SE Downloads on MOS (Doc ID 1439822.1)</strong>

In order to upgrade JDK, one-off patch 28373706,28033677 should be applied on AGENT home in advance.
1. 28373706 EM-AGENT Bundle Patch 13.2.0.0.180831
2. 28033677 RTI:21262694 - jdk , Existing timezone are not considered as valid time zone

After applying 28373706 EM-AGENT Bundle Patch 13.2.0.0.180831, the agent cannot connect to OMS
<pre class="prettyprint lang-sh linenums=1 ">
$ /l01/app/oracle/product/oem/agent12c/agent_13.2.0.0.0/bin/emctl status agent
Oracle Enterprise Manager Cloud Control 13c Release 2
Copyright (c) 1996, 2016 Oracle Corporation. All rights reserved.
---------------------------------------------------------------
Agent Version : 13.2.0.0.0
==> OMS Version : (unknown)                                                                    
Protocol Version : 12.1.0.1.0
Agent Home : /l01/app/oracle/product/oem/agent12c/agent_inst
Agent Log Directory : /l01/app/oracle/product/oem/agent12c/agent_inst/sysman/log
Agent Binaries : /l01/app/oracle/product/oem/agent12c/agent_13.2.0.0.0
Core JAR Location : /l01/app/oracle/product/oem/agent12c/agent_13.2.0.0.0/jlib
Agent Process ID : 6356
Parent Process ID : 6319
Agent URL : https://tara.pprd.odu.edu:3872/emd/main/
Local Agent URL in NAT : https://tara.pprd.odu.edu:3872/emd/main/
Repository URL : https://ariel.prod.odu.edu:1159/empbs/upload
Started at : 2018-12-12 13:22:11
Started by user : oracle
Operating System : Linux version 2.6.32-754.3.5.el6.x86_64 (amd64)
Number of Targets : 18
Last Reload : (none)
Last successful upload : (none)
Last attempted upload : (none)
Total Megabytes of XML files uploaded so far : 0
Number of XML files pending upload : 38
Size of XML files pending upload(MB) : 0.04
Available disk space on upload filesystem : 51.99%
Collection Status : Collections enabled
==> Heartbeat Status : OMS responded illegally [ERROR- Failed to Update Target Type Metadata]  
Last attempted heartbeat to OMS : 2018-12-12 13:22:19
Last successful heartbeat to OMS : (none)
Next scheduled heartbeat to OMS : 2018-12-12 13:22:49

---------------------------------------------------------------

$ /l01/app/oracle/product/oem/agent12c/agent_13.2.0.0.0/bin/emctl upload agent
Oracle Enterprise Manager Cloud Control 13c Release 2
Copyright (c) 1996, 2016 Oracle Corporation. All rights reserved.
---------------------------------------------------------------
==> EMD upload error:full upload has failed: uploadXMLFiles skipped :: OMS version not checked yet. If this issue persists check trace files for ping to OMS related errors. (OMS_DOWN) 

$ tail -n 10 /l01/app/oracle/product/oem/agent12c/agent_inst/sysman/log/gcagent.log|grep WARN
2018-12-12 13:26:51,303 [176:9FC136A7] WARN - Ping protocol error
2018-12-12 13:27:19,514 [169:E866DA04:GC.SysExecutor.2 (AgentSystemMonitorTask)] WARN - Subsystem (Upload Manager) returned bad status of {+ Upload Manager: *Warning* +}
2018-12-12 13:27:19,514 [169:E866DA04] WARN - Subsystem (Ping Manager) returned bad status of {+ Ping Manager: *Warning* +}
2018-12-12 13:27:21,371 [182:BE87CC23] WARN - improper ping interval (EM_PING_NOTIF_RESPONSE: ERROR- Failed to Update Target Type Metadata)
2018-12-12 13:27:21,371 [182:BE87CC23] WARN - Ping protocol error
==> 2018-12-12 13:27:25,953 [43:BF500E2C] WARN - improper ping interval (EM_PING_NOTIF_RESPONSE: ERROR- Failed to Update Target Type Metadata)      
</pre>

This is because the mandatory patch 25163555 is not applied on OMS home in advance.<br/>
<strong>Agent Upload Error: OMS responded illegally [ERROR- Failed to Update Target Type Metadata] (Doc ID 2243040.1)</strong>
<pre class="prettyprint lang-sql linenums=1 ">
SQL> select type_meta_ver from sysman.mgmt_target_type_versions where target_type = 'oracle_emd';

TYPE_META_VER
--------------------------------------------------------------------------------
12.0
12.2
12.3
12.4
13.1
<span style="color:#ff0000;">-- 13.2 should be here</span>

$ export ORACLE_HOME=/l01/app/oracle/product/oem/em13c
$ $ORACLE_HOME/OPatch/opatch lsinventory|grep 25163555
<span style="color:#ff0000;">-- 25163555 should be shown here</span>
</pre>

If 25163555 is not listed, we should rollback 28373706 on the agent and apply 25163555 on OMS

<strong>Patch 25163555: Tracking bug for Back-porting 24588124 oms side fix</strong>
<strong>Release: Enterprise Manager Base Platform - OMS 13.2.0.0.0</strong>
<strong>Platform: Generic Platform</strong>
<strong>Language: American English</strong>

After 25163555 is applied on OMS home, run the some query above to verify that 13.2 listed
Then, patch agent again.

So, In summay, the following patch should be applied on OMSs or Agents in advance before upgrade JDK which Agents is using.

<span style="color:#ff0000;"><strong>OMS</strong></span> <br/>
Apply 25163555 for OMS, then do testing. <br/>
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Patch 25163555: Tracking bug for Back-porting 24588124 oms side fix</p>
27612395 and 27994582 are prerequisites <br/>
<p dir="ltr" style="margin-left: 20px; margin-right: 0px"><s>Patch 27612395: EMBP Patch Set Update 13.2.0.0.180417 <br/>
Patch 27994582: [EM 13.2]: April Agent BP deploy from Console fails with 13.20811 metadata error </s></p>

Using 28970534 to instead.
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">
# EM Cloud Control PSU : List of Available PSUs (Patch Set Updates) (Doc ID 1605609.1)	 <br/>
Patch 28970534: EMBP Patch Set Update 13.2.0.0.190115 <br/></p>


<span style="color:#ff0000;"><strong>AGENT</strong></span> 
<S>Patch 28373706: EM-AGENT Bundle Patch 13.2.0.0.180831 </S>
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Patch 28966303: EM-AGENT Bundle Patch 13.2.0.0.181231 <br/>
Patch 28033677: RTI:21262694 - jdk , Existing timezone are not considered as valid time zone </p>

 
<span style="color:#ff0000;"><strong>Patch 27895402: Oracle JDK 7 Update 191 </strong></span>
<pre class="prettyprint lang-sql linenums=1 ">
$AGENT_INST_HOME/bin>./emctl stop agent 
Go to AGENT_HOME/oracle_common/ 
$mv jdk jdk_bak 
# unzip new jdk here
$AGENT_INST_HOME/bin>./emctl start agent 
</pre>
 