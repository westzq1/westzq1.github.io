---
layout: post
title: Oracle - PDB$SEED
modified: 2019-02-14
categories: [Oracle]
tags: 
  - Oracle.Multitenent
comments: true
---

<span style="color:#ff0000;"><strong>Can I have more than one PDB$SEED?</strong></span>

No, one CDB can have and only have one PDB$SEED.
But, we can put a database to read only as SEED database, and clone from it.

<span style="color:#ff0000;"><strong>Can I close PDB$SEED?</strong></span>

By default, PDB$SEED cannot be modified and close
<pre class="prettyprint lang-sql linenums=1 ">
SQL> alter pluggable database PDB$SEED close;
alter pluggable database PDB$SEED close
                         *
ERROR at line 1:
ORA-65017: seed pluggable database may not be dropped or altered


SQL> alter pluggable database all close;

Pluggable database altered.

SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 HR                             MOUNTED
</pre>         
But, with dbms_pdb.exec_as_oracle_script, we can do this:
<pre class="prettyprint lang-sql linenums=1 ">
SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 HR                             MOUNTED
SQL>  exec dbms_pdb.exec_as_oracle_script('alter pluggable database PDB$SEED close');

PL/SQL procedure successfully completed.

SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       MOUNTED
         3 HR                             MOUNTED
SQL> exec dbms_pdb.exec_as_oracle_script('alter pluggable database PDB$SEED open');

PL/SQL procedure successfully completed.

SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ WRITE NO
         3 HR                             MOUNTED
SQL> exec dbms_pdb.exec_as_oracle_script('alter pluggable database PDB$SEED close');

PL/SQL procedure successfully completed.

SQL> exec dbms_pdb.exec_as_oracle_script('alter pluggable database PDB$SEED open read only');

PL/SQL procedure successfully completed.

SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 HR                             MOUNTED
</pre>         

<span style="color:#ff0000;"><strong>How to modify PDB$SEED?</strong></span>

<p dir="ltr" style="margin-left: 20px; margin-right: 0px">How To Modify the Common Profile Limit in Multitenant Database? (Doc ID 2328994.1)	
</p>

<pre class="prettyprint lang-sql linenums=1 ">
alter session set "_oracle_script"=true;

SQL> alter session set container=pdb$seed;

Session altered.

SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
SQL> alter session set "_oracle_script"=true;

Session altered.

SQL> shutdown immediate;
Pluggable Database closed.
SQL> startup
Pluggable Database opened.
SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ WRITE NO
SQL> create tablespace test datafile size 10M;

Tablespace created.

SQL> shutdown immediate;
Pluggable Database closed.

SQL> startup open read only;
Pluggable Database opened.
SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO

-- after reconnect to cdb
SQL> startup force;
ORACLE instance started.

Total System Global Area  713031680 bytes
Fixed Size                  2928488 bytes
Variable Size             520093848 bytes
Database Buffers          184549376 bytes
Redo Buffers                5459968 bytes
Database mounted.
Database opened.
SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 HR                             MOUNTED
</pre>

<span style="color:#ff0000;"><strong>What will happen if the datafile of PDB$SEED missing and how to recreate without backup?
</strong></span>

CDB can open, PDB$SEED can open read only if the missing datafile is not SYSTEM/UNDO.
<pre class="prettyprint lang-sql linenums=1 ">
SQL> select file_name from dba_data_files;         
select file_name from dba_data_files
                      *
ERROR at line 1:
ORA-01157: cannot identify/lock data file 13 - see DBWR trace file
ORA-01110: data file 13: '/opt/oracle/oradata/O12102/7DE2B53C634D1689E053291010532345/datafile/o1_mf_test_g6pwnokd_.dbf'
</pre>
If you don't clone an new PDB, there is no impact.

There is no simple way to create PDB$SEED with some scripts. <br/>
The standard way is recreate a new CDB and unplug from the old cdb and plug into the new one.
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Missing PDB$SEED's Datafiles with no backup (Doc ID 2104370.1)	
</p>

<span style="color:#ff0000;"><strong>How to create a new PDB$SEED from an existing PDB?
</strong></span>

We can use dbms_pdb.exec_as_oracle_script to drop PDB$SEED and clone a new one from an existing PDB<br/>
But it is not an documented way.

<pre class="prettyprint lang-sql linenums=1 ">
SQL> exec dbms_pdb.exec_as_oracle_script('alter pluggable database PDB$SEED close');

PL/SQL procedure successfully completed.

SQL> exec dbms_pdb.exec_as_oracle_script('drop pluggable database PDB$SEED including datafiles');

PL/SQL procedure successfully completed.

SQL> show pdbs   

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         3 HR                             MOUNTED
         
SQL> alter pluggable database hr open;

Pluggable database altered.

SQL> create pluggable database PDB$SEED FROM HR;

Pluggable database created.

SQL> show pdbs              

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       MOUNTED
         3 HR                             READ WRITE NO
         
SQL> alter pluggable database PDB$SEED open;
alter pluggable database PDB$SEED open
                         *
ERROR at line 1:
ORA-65017: seed pluggable database may not be dropped or altered         

SQL>  exec dbms_pdb.exec_as_oracle_script('alter pluggable database PDB$SEED open');

PL/SQL procedure successfully completed.

SQL> exec dbms_pdb.exec_as_oracle_script('alter pluggable database PDB$SEED close');   

PL/SQL procedure successfully completed.

SQL> exec dbms_pdb.exec_as_oracle_script('alter pluggable database PDB$SEED open read only');

PL/SQL procedure successfully completed.
</pre>         


<span style="color:#ff0000;"><strong>Do I need to patch PDB$SEED manually?</strong></span><br/>
Patching the PDB$SEED (Doc ID 2038564.1)	<br/>
PDB$SEED patching is automatically handled through datapatch so we shouldn't have to manually patch the PDB$SEED.

<span style="color:#ff0000;"><strong>What can I redefine when create a new PDB from PDB$SEED?
</strong></span><br/>
<pre class="prettyprint lang-sql linenums=1 ">
create pluggable database pdb1 
admin user ctais2 identified by oracle roles=(dba)
parallel 2
default tablespace test datafile size 10M
storage unlimited
tempfile reuse
logging
-- file_name_convert    # useful is we don't use OMF
-- create_file_dest     # useful is we don't use OMF
-- service_name_convert # there is no other service in the PDB$SEED
-- path_prefix          # used to restrict the path of directory objects
-- standbys             # used to exclude this PDB in the standby side
-- user_tablespaces     # not useful, PDB$SEED doesn't have other tablespaces
-- host= port=          # not useful, we create from PDB locally.
/
</pre>