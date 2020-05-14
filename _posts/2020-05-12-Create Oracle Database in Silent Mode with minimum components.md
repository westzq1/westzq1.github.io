---
layout: post
title: Oracle - Create a Database in Silent Mode with minimum components
modified: 2020-04-28
categories: [Oracle]  
tags: 
  - Oracle.Installation
comments: true
---
<br>The template New_Database.dbt is used to run the script to create a database.
<br>Using -dbOptions to disable all unecessary componments.

<pre class="prettyprint lang-sh linenums=1 ">
dbca -silent -createDatabase \
  -sid PHUB \
  -gdbName PHUB.WORLD \
  -templateName New_Database.dbt \
  -datafileDestination /data01/app/oracle/oradata \
  -systemPassword oracle \
  -sysPassword oracle \
  -characterSet US7ASCII \
  -storageType FS \
  -databaseConfigType SINGLE \
  -emConfiguration NONE \
  -dvConfiguration false \
  -enableArchive false \
  -memoryMgmtType AUTO_SGA \
  -useOMF true\
  -sampleSchema false \
  -dbOptions APEX:false,JSERVER:false,DV:false,SPATIAL:false,CWMLITE:false,ORACLE_TEXT:false,IMEDIA:false,XDB_PROTOCOLS:false
</pre>  


<span style="color:#ff0000;"><strong>OWM will be installed. I don't find the option name of it. We can deinstall OWM after creation.</strong></span>
<pre class="prettyprint lang-sql linenums=1 ">
select * from all_wm_versioned_tables;  -- return 0
$ORACLE_HOME/rdbms/admin/owmuinst.plb
</pre>

<span style="color:#ff0000;"><strong>XDB is mandatory after 12.1</strong></span>
