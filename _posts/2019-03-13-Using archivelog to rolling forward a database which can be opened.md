---
layout: post
title: Oracle - Using archive log to roll forward a database in a snapshot
modified: 2019-03-13
categories: [Oracle]
tags: 
  - Oracle.Recovery
comments: true
---

We are using NUTANIX machine, and want to use Application Consistency / Crash Consistency snapshot for DR.<br/>
We tested CC snapshot, the database can start up without any block corruptions.<br/>
But compare with AC, which needs to put the database into backup mode, it looks like not trustworth.<br/>

So, in our consideration, we will take AC/CC alternately. AC for once a day, and CC for every minutes.<br/>
If CC cannot be up, we plan to bring the latest AC up, and then, copy archive logs from the latest CC to AC, and then using it to roll forward.<br/> 


The AC is equal to have a copy when the database is in a hot backup mode.

We use it as a base for testing. When try to open the database, an error will be reported, we use rman for recovery.
<pre class="prettyprint lang-sql linenums=1 ">
SQL> startup;
ORACLE instance started.

Total System Global Area 1073741824 bytes
Fixed Size                  8801008 bytes
Variable Size             373294352 bytes
Database Buffers          683671552 bytes
Redo Buffers                7974912 bytes
Database mounted.
ORA-10873: file 1 needs to be either taken out of backup mode or media
recovered
ORA-01110: data file 1:
'/home/oracle/oradata/O12201/datafile/o1_mf_system_g76xpjr1_.dbf'

RMAN> catalog recovery area;

RMAN> recover database;

Starting recover at 14-MAR-19
allocated channel: ORA_DISK_1
channel ORA_DISK_1: SID=258 device type=DISK

starting media recovery
media recovery complete, elapsed time: 00:00:01

Finished recover at 14-MAR-19
</pre>
We can see, rman doesn't apply any archive log.

When we try to use "recover database" in SQL
<pre class="prettyprint lang-sql linenums=1 ">
SQL> recover database using backup controlfile until cancel;
ORA-00279: change 1914225 generated at 03/14/2019 21:49:02 needed for thread 1
ORA-00289: suggestion :
/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_6_g8
p1gtxv_.arc
ORA-00280: change 1914225 for thread 1 is in sequence #6


Specify log: {&lt;RET&gt;=suggested | filename | AUTO | CANCEL}
auto
ORA-00328: archived log ends at change 1814349, need later change 1914225
ORA-00334: archived log:
'/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_6_g
8p1gtxv_.arc'
</pre>

An error is reported and no archive log is applied. 
The recovery process need the redo record on SCN 1914225

But Accurately, the latest redo is less than this SCN
<pre class="prettyprint lang-sql linenums=1 ">
 SEQUENCE# FIRST_CHANGE# NEXT_CHANGE#
---------- ------------- ------------
         3       1814192      1814196
         4       1814196      1814200
         5       1814200      1814204
         6       1814204      1814350
         7       1814350      1814354
         8       1814354      1814358
         9       1814358      1814362
        10       1814362      1814369
</pre>

The recopy the database backup, and using "recover database using backup controlfile until cancel" in sqlplus directly.<br>
This time, there is no error and database can be opened.
<pre class="prettyprint lang-sql linenums=1 ">
SQL> recover database using backup controlfile until cancel;
</pre>

Let us go back to the previous situation.<br/>
If recover take the online redo to roll forward the copy. After forwarding, we can see in the file header, the checkpoint cnt is changed from 299 to 300, and scn is updated from 0x1baec1 to 0x1d3571

<pre class="prettyprint lang-sql linenums=1 ">
DATA FILE #1:                                                   DATA FILE #1: 
  name #6: /home/oracle/oradata/O12201/datafile/o1_mf_system_     name #6: /home/oracle/oradata/O12201/datafile/o1_mf_system_
creation size=0 block size=8192 status=0xe flg=0x1 head=6 tai | creation size=0 block size=8192 status=0x2e flg=0x1 head=6 ta
 pdb_id 1, tablespace 0, index=2 krfil=1 prev_file_in_ts=0 pr    pdb_id 1, tablespace 0, index=2 krfil=1 prev_file_in_ts=0 pr
 unrecoverable scn: 0x0000000000000000 01/01/1988 00:00:00       unrecoverable scn: 0x0000000000000000 01/01/1988 00:00:00
 Checkpoint cnt:299 scn: 0x00000000001baec1 03/14/2019 21:49: |  Checkpoint cnt:300 scn: 0x00000000001d3571 03/14/2019 21:49:
 Stop scn: 0xffffffffffffffff 03/14/2019 17:19:19             |  Stop scn: 0x00000000001d3571 03/14/2019 21:49:43
 Creation Checkpointed at scn:  0x0000000000000007 01/26/2017    Creation Checkpointed at scn:  0x0000000000000007 01/26/2017
 thread:0 rba:(0x0.0.0)                                          thread:0 rba:(0x0.0.0)
</pre>

Checkpoint cnt:300 scn: 0x00000000001d3571 03/14/2019 21:49:

0x00000000001d3571 => 1914225, which is the error reported.

After open the database resetlogs, we can see the scn in archive log are increase a lot.
<pre class="prettyprint lang-sql linenums=1 ">
 SEQUENCE# FIRST_CHANGE# NEXT_CHANGE#
---------- ------------- ------------
         2       1814188      1814192
         3       1814192      1814196
         4       1814196      1814200
         5       1814200      1814204
         6       1814204      2014227
</pre>
        
For normally situation, if we use OS cp to backup database with "begin backup", we don't copy redo logs due to it is useless.<br/>
So, in this situation, because the restore the whole machine from a snapshot, so the redo log is there.
It would be better to remove them before recover.
<pre class="prettyprint lang-sql linenums=1 ">
[oracle@testenv oradata]$ cd O12201/onlinelog/
[oracle@testenv onlinelog]$ ls -ltr
total 614412
-rw-r-----. 1 oracle oinstall 209715712 Mar 15 00:06 o1_mf_1_g76xsrcz_.log
-rw-r-----. 1 oracle oinstall 209715712 Mar 15 00:06 o1_mf_2_g76xsrfx_.log
-rw-r-----. 1 oracle oinstall 209715712 Mar 15 00:06 o1_mf_3_g76xsrj8_.log
[oracle@testenv onlinelog]$ rm *

RMAN> catalog recovery area;

RMAN> recover database;

Starting recover at 15-MAR-19
allocated channel: ORA_DISK_1
channel ORA_DISK_1: SID=237 device type=DISK

starting media recovery
media recovery failed
RMAN-00571: ===========================================================
RMAN-00569: =============== ERROR MESSAGE STACK FOLLOWS ===============
RMAN-00571: ===========================================================
RMAN-03002: failure of recover command at 03/15/2019 00:08:49
ORA-00283: recovery session canceled due to errors
RMAN-11003: failure during parse/execution of SQL statement: alter database recover
 if needed start
ORA-00283: recovery session canceled due to errors
ORA-00313: open failed for members of log group 3 of thread 1
ORA-00312: online log 3 thread 1: '/home/oracle/oradata/O12201/onlinelog/o1_mf_3_g76xsrj8_.log'
ORA-27037: unable to obtain file status
Linux-x86_64 Error: 2: No such file or directory
Additional information: 7

SQL> recover database using backup controlfile until cancel;
ORA-00279: change 1814209 generated at 03/14/2019 21:49:02 needed for thread 1
ORA-00289: suggestion :
/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_6_g8
p1gtxv_.arc
ORA-00280: change 1814209 for thread 1 is in sequence #6


Specify log: {&lt;RET&gt;=suggested | filename | AUTO | CANCEL}
auto
ORA-00279: change 1814350 generated at 03/14/2019 21:54:34 needed for thread 1
ORA-00289: suggestion :
/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_7_g8
p1gv1j_.arc
ORA-00280: change 1814350 for thread 1 is in sequence #7
ORA-00278: log file
'/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_6_g
8p1gtxv_.arc' no longer needed for this recovery

......

ORA-00308: cannot open archived log
'/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_15/o1_mf_1_11_
%u_.arc'
ORA-27037: unable to obtain file status
Linux-x86_64 Error: 2: No such file or directory
Additional information: 7
</pre>

If the backup is a cold backup, there is no this problem. <br/>
But rman is still not working.
<pre class="prettyprint lang-sql linenums=1 ">
SQL> startup mount;
ORACLE instance started.

Total System Global Area 1073741824 bytes
Fixed Size                  8801008 bytes
Variable Size             373294352 bytes
Database Buffers          683671552 bytes
Redo Buffers                7974912 bytes
Database mounted.

RMAN> catalog recovery area;

RMAN> recover database;

Starting recover at 14-MAR-19
using channel ORA_DISK_1

starting media recovery
media recovery complete, elapsed time: 00:00:01

Finished recover at 14-MAR-19


SQL> recover database using backup controlfile until cancel;
ORA-00279: change 1813573 generated at 03/14/2019 17:18:06 needed for thread 1
ORA-00289: suggestion :
/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_1_g8
okfwoq_.arc
ORA-00280: change 1813573 for thread 1 is in sequence #1


Specify log: {&lt;RET&gt;=suggested | filename | AUTO | CANCEL}
auto
ORA-00279: change 1813666 generated at 03/14/2019 17:21:00 needed for thread 1
ORA-00289: suggestion :
/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_2_g8
okfwtc_.arc
ORA-00280: change 1813666 for thread 1 is in sequence #2
ORA-00278: log file
'/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_1_g
8okfwoq_.arc' no longer needed for this recovery


......

ORA-00308: cannot open archived log
'/home/oracle/fast_recovery_area/O12201/O12201/archivelog/2019_03_14/o1_mf_1_6_%
u_.arc'
ORA-27037: unable to obtain file status
Linux-x86_64 Error: 2: No such file or directory
Additional information: 7

SQL> alter database open resetlogs;

Database altered.
</pre>




