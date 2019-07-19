---
layout: post
title: opatch: Permission denied during 2019 July PSU Patching
modified: 2019-07-19
categories: [Oracle]
tags: 
  - Oracle.Installation
comments: true
---

When I do Oracle database July 2019 PSU patching on the machines which /tmp folder is mounted with noexec option, error “opatch: Permission denied” reported.
<pre class="prettyprint lang-sql linenums=1 ">
$ORACLE_HOME/OPatch/opatch apply 29494060/
Oracle Interim Patch Installer version 12.2.0.1.17
Copyright (c) 2019, Oracle Corporation.  All rights reserved.


Oracle Home       : /l01/app/oracle/product/12.1.0.2
Central Inventory : /l01/app/oraInventory
   from           : /l01/app/oracle/product/12.1.0.2/oraInst.loc
OPatch version    : 12.2.0.1.17
OUI version       : 12.1.0.2.0
Log file location : /l01/app/oracle/product/12.1.0.2/cfgtoollogs/opatch/opatch2019-07-19_11-21-09AM_1.log

Verifying environment and performing prerequisite checks...

--------------------------------------------------------------------------------
Start OOP by Prereq process.
Launch OOP...

/l01/app/oracle/product/12.1.0.2/OPatch/opatch: line 1395: /tmp/oracle-home-1563549677485107/OPatch/opatch: Permission denied
/l01/app/oracle/product/12.1.0.2/OPatch/opatch: line 1395: exec: /tmp/oracle-home-1563549677485107/OPatch/opatch: cannot execute: Permission denied
</pre>

But we don’t have this problem when did Apr 2019 PSU Patching. <br/>
Setting variables TMP / TEMP / TMPDIR / TEMPDIR is not workong

The simplest way to solve the problem is to remount /tmp without “noexec” option.

Oracle mentioned in Oracle Database 19c Release Update & Release Update Revision July 2019 Known Issues (Doc ID 2534815.1) that:
<pre class="prettyprint lang-sql linenums=1 ">
1 Known Issues
1.1 OOP Ignorable messages when running Opatch

The message "Start OOP by Prereq process" and the message "Launch OOP..." prints on console during 19.4.0.0.190716 DB RU rollback using the specified Opatch 12.2.0.1.17 release.

Both of these informational messages have no functional impact to the rollback flow and are printed in error. Please ignore them. The issue will be corrected with the next version of Opatch 12.2.0.1.18 release.
</pre>

So, it is supposed to be a OPatch bug.<br/>

I checked the opatch script, and debug it in my test environment.<br/>
For Apr 2019 PSU patching, opatch script will invoke java program to patch directly. But for July 2019 PSU patching, it invokes the same java program to generate a shell file to do out-of-place opatch patching. It will copy itself to the /tmp/xxxxx/OPatch folder to do patching.

One way to prevent the OPatch goes to self patching OOP is to use “-restart” option.<br/>
	$ORACLE_HOME/OPatch/opatch apply 29774383 -silent -restart

This option is undocumented. I am not sure whether I can use it safely, waiting for Oracle to confirm.











