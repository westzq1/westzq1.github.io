---
layout: post
title: Oracle - How to check whether RMAN can restore the database to a specific time?
modified: 2019-01-25
categories: [Oracle]
tags: 
  - Oracle.RECOVERY
comments: true
---
Restore database preview can be used to achieve this goal.
It can be done on the local database, but it is also can be done with catalog on the another box.

<b>On the source database</b>
1. Get the dbid from v$database

<b>On a different box</b>  
1. Create pwd file, password doesn't need to be the same with the source.
2. Create pfile , only one parameter is needed, *.db_name=xxxx
3. Set ORACLE_SID, start instance to nomount.
4. Run script below, using “set dbid” to set database id and “set until time” to set your target.

<pre class="prettyprint lang-sql linenums=1 ">
rman target /
connect catalog rman/xxxxx@CATALOG_DB

run {
set DBID 451988051;
set until time "to_date('Jan 7 2019 17:30:00','Mon DD YYYY HH24:MI:SS')";
restore database preview;
}
</pre>
<br />
But there is no guarantee that all backup pieces are there, it would be better to check it with <span style="color:#ff0000;"><strong>CROSSCHECK</strong></span>

<pre class="prettyprint lang-sql linenums=1 ">
CROSSCHECK BACKUP DEVICE TYPE DISK COMPLETED BETWEEN '07-JAN-19' AND '08-JAN-19';
CROSSCHECK BACKUPSET 1338, 1339, 1340;
</pre>
<br />
Before Restore, we can use <span style="color:#ff0000;"><strong>RESTORE DATABASE VALIDATE</strong></span> to make sure the recoverable of backup pieces. We can also use <strong><span style="color:#ff0000;">VALIDATE BACKUPSET</span> </strong>to check a particular backup piece.
<br />
The output of RESTORE DATABASE PREVIEW below.
The all backup pieces this restore needs are list, including LEVEL0, LEVEL2, and Archive log.
<pre class="prettyprint lang-sql linenums=1 ">
RMAN> run {
set dbid 451988051;
set until time "to_date('Jan 7 2019 17:30:00','Mon DD YYYY HH24:MI:SS')";
restore database preview;
}
2> 3> 4> 5>
executing command: SET DBID
database name is "XXX" and DBID is 451988051

executing command: SET until clause

Starting restore at 29-JAN-19
allocated channel: ORA_DISK_1
channel ORA_DISK_1: SID=1923 device type=DISK
allocated channel: ORA_DISK_2
channel ORA_DISK_2: SID=1985 device type=DISK

List of Backup Sets
===================


BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723040 Incr 0  25.79G     DISK        00:09:50     07-JAN-19      
        BP Key: 425723070   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139758_piece1_20190107_fetmmi2j_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723040
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  1    0  Incr 143493006556 07-JAN-19 /l06/app/oracle/oradata/ORCL/system01.dbf
  39   0  Incr 143493006556 07-JAN-19 /l06/app/oracle/oradata/ORCL/faisndx_ORCL_01.dbf
  99   0  Incr 143493006556 07-JAN-19 /l07/app/oracle/oradata/ORCL/satundx_ORCL_05.dbf
  147  0  Incr 143493006556 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_07.dbf
  167  0  Incr 143493006556 07-JAN-19 /l07/app/oracle/oradata/ORCL/workflow_ORCL_06.dbf
  198  0  Incr 143493006556 07-JAN-19 /l06/app/oracle/oradata/ORCL/sysaux07.dbf
  223  0  Incr 143493006556 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_18.dbf
  230  0  Incr 143493006556 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfndx_ORCL_04.dbf
  236  0  Incr 143493006556 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_08.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723044 Incr 0  27.52G     DISK        00:10:37     07-JAN-19      
        BP Key: 425723074   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139762_piece1_20190107_fitmmjdi_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723044
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  2    0  Incr 143493256991 07-JAN-19 /l06/app/oracle/oradata/ORCL/sysaux01.dbf
  38   0  Incr 143493256991 07-JAN-19 /l07/app/oracle/oradata/ORCL/faisdata_ORCL_01.dbf
  51   0  Incr 143493256991 07-JAN-19 /l07/app/oracle/oradata/ORCL/fimsdata_ORCL_01.dbf
  95   0  Incr 143493256991 07-JAN-19 /l07/app/oracle/oradata/ORCL/satundx_ORCL_01.dbf
  124  0  Incr 143493256991 07-JAN-19 /l06/app/oracle/oradata/ORCL/payrdata_ORCL_05.dbf
  140  0  Incr 143493256991 07-JAN-19 /l06/app/oracle/oradata/ORCL/fimsdata_ORCL_06.dbf
  166  0  Incr 143493256991 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_21.dbf
  218  0  Incr 143493256991 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_04.dbf
  229  0  Incr 143493256991 07-JAN-19 /l07/app/oracle/oradata/ORCL/fimsdata_ORCL_07.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723039 Incr 0  14.85G     DISK        00:06:17     07-JAN-19      
        BP Key: 425723069   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139757_piece1_20190107_fdtmmhvj_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723039
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  3    0  Incr 143492996067 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_01.dbf
  92   0  Incr 143492996067 07-JAN-19 /l07/app/oracle/oradata/ORCL/satudata_ORCL_04.dbf
  109  0  Incr 143492996067 07-JAN-19 /l06/app/oracle/oradata/ORCL/shrtdata_ORCL_05.dbf
  141  0  Incr 143492996067 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_11.dbf
  161  0  Incr 143492996067 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_18.dbf
  194  0  Incr 143492996067 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_06.dbf
  220  0  Incr 143492996067 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_16.dbf
  225  0  Incr 143492996067 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_33.dbf
  256  0  Incr 143492996067 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_36.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723046 Incr 0  20.13G     DISK        00:09:56     07-JAN-19      
        BP Key: 425723076   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139765_piece1_20190107_fltmmk1q_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723046
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  4    0  Incr 143493345254 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_02.dbf
  22   0  Incr 143493345254 07-JAN-19 /l06/app/oracle/oradata/ORCL/banproxy_ORCL_01.dbf
  46   0  Incr 143493345254 07-JAN-19 /l06/app/oracle/oradata/ORCL/fgbtrnd_acctndx_ORCL_01.dbf
  54   0  Incr 143493345254 07-JAN-19 /l07/app/oracle/oradata/ORCL/fimsdata_ORCL_04.dbf
  87   0  Incr 143493345254 07-JAN-19 /l06/app/oracle/oradata/ORCL/RMS_DATA_03.dbf
  114  0  Incr 143493345254 07-JAN-19 /l06/app/oracle/oradata/ORCL/taisdata_ORCL_03.dbf
  155  0  Incr 143493345254 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_15.dbf
  173  0  Incr 143493345254 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_08.dbf
  244  0  Incr 143493345254 07-JAN-19 /l06/app/oracle/oradata/ORCL/devl_ORCL_23.dbf
  249  0  Incr 143493345254 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_34.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723048 Incr 0  16.87G     DISK        00:09:52     07-JAN-19      
        BP Key: 425723078   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139767_piece1_20190107_fntmmkks_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723048
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  5    0  Incr 143493408784 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_03.dbf
  48   0  Incr 143493408784 07-JAN-19 /l06/app/oracle/oradata/ORCL/fgbtrnd_glacndx_ORCL_01.dbf
  57   0  Incr 143493408784 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_01.dbf
  88   0  Incr 143493408784 07-JAN-19 /l06/app/oracle/oradata/ORCL/RMS_DATA_04.dbf
  115  0  Incr 143493408784 07-JAN-19 /l06/app/oracle/oradata/ORCL/taisndx_ORCL_01.dbf
  120  0  Incr 143493408784 07-JAN-19 /l06/app/oracle/oradata/ORCL/users_ORCL_01.dbf
  160  0  Incr 143493408784 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_17.dbf
  176  0  Incr 143493408784 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_25.dbf
  245  0  Incr 143493408784 07-JAN-19 /l06/app/oracle/oradata/ORCL/devl_ORCL_24.dbf
  251  0  Incr 143493408784 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_35.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723037 Incr 0  26.24G     DISK        00:09:53     07-JAN-19      
        BP Key: 425723067   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139755_piece1_20190107_fbtmmhcm_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723037
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  6    0  Incr 143492923974 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_04.dbf
  66   0  Incr 143492923974 07-JAN-19 /l06/app/oracle/oradata/ORCL/legandx_ORCL_02.dbf
  81   0  Incr 143492923974 07-JAN-19 /l06/app/oracle/oradata/ORCL/posnndx_ORCL_01.dbf
  91   0  Incr 143492923974 07-JAN-19 /l07/app/oracle/oradata/ORCL/satudata_ORCL_03.dbf
  108  0  Incr 143492923974 07-JAN-19 /l06/app/oracle/oradata/ORCL/shrtdata_ORCL_07.dbf
  144  0  Incr 143492923974 07-JAN-19 /l07/app/oracle/oradata/ORCL/axiom_ORCL_04.dbf
  159  0  Incr 143492923974 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_04.dbf
  213  0  Incr 143492923974 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_32.dbf
  222  0  Incr 143492923974 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_17.dbf
  224  0  Incr 143492923974 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_07.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723049 Incr 0  17.22G     DISK        00:10:54     07-JAN-19      
        BP Key: 425723079   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139766_piece1_20190107_fmtmmkkq_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723049
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  7    0  Incr 143493408505 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_05.dbf
  9    0  Incr 143493408505 07-JAN-19 /l06/app/oracle/oradata/ORCL/atsdata_ORCL_01.dbf
  32   0  Incr 143493408505 07-JAN-19 /l06/app/oracle/oradata/ORCL/emdata_ORCL_03.dbf
  37   0  Incr 143493408505 07-JAN-19 /l07/app/oracle/oradata/ORCL/faisdata_ORCL_02.dbf
  93   0  Incr 143493408505 07-JAN-19 /l07/app/oracle/oradata/ORCL/satudata_ORCL_05.dbf
  153  0  Incr 143493408505 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_08.dbf
  164  0  Incr 143493408505 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_19.dbf
  228  0  Incr 143493408505 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_08.dbf
  241  0  Incr 143493408505 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_13.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723050 Incr 0  22.91G     DISK        00:09:23     07-JAN-19      
        BP Key: 425723080   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139768_piece1_20190107_fotmml7i_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723050
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  8    0  Incr 143493466122 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_06.dbf
  49   0  Incr 143493466122 07-JAN-19 /l06/app/oracle/oradata/ORCL/fgbtrnd_keyndx_ORCL_01.dbf
  58   0  Incr 143493466122 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_02.dbf
  69   0  Incr 143493466122 07-JAN-19 /l06/app/oracle/oradata/ORCL/odsdata_ORCL_01.dbf
  121  0  Incr 143493466122 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_01.dbf
  163  0  Incr 143493466122 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_09.dbf
  177  0  Incr 143493466122 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_26.dbf
  246  0  Incr 143493466122 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_25.dbf
  255  0  Incr 143493466122 07-JAN-19 /l06/app/oracle/oradata/ORCL/posnndx_ORCL_03.dbf
  258  0  Incr 143493466122 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_38.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723051 Incr 0  27.54G     DISK        00:10:45     07-JAN-19      
        BP Key: 425723081   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139769_piece1_20190107_fptmml9k_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723051
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  10   0  Incr 143493471961 07-JAN-19 /l06/app/oracle/oradata/ORCL/atsndx_ORCL_01.dbf
  17   0  Incr 143493471961 07-JAN-19 /l06/app/oracle/oradata/ORCL/axiom_ORCL_01.dbf
  68   0  Incr 143493471961 07-JAN-19 /l06/app/oracle/oradata/ORCL/mlogdata_ORCL_02.dbf
  71   0  Incr 143493471961 07-JAN-19 /l07/app/oracle/oradata/ORCL/payrdata_ORCL_02.dbf
  101  0  Incr 143493471961 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_02.dbf
  119  0  Incr 143493471961 07-JAN-19 /l06/app/oracle/oradata/ORCL/traveldata_ORCL_01.dbf
  137  0  Incr 143493471961 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_10.dbf
  191  0  Incr 143493471961 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfndx_ORCL_03.dbf
  192  0  Incr 143493471961 07-JAN-19 /l06/app/oracle/oradata/ORCL/devl_ORCL_13.dbf
  260  0  Incr 143493471961 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_30.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723053 Incr 0  24.96G     DISK        00:12:12     07-JAN-19      
        BP Key: 425723083   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139771_piece1_20190107_frtmmlu2_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723053
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  11   0  Incr 143493547427 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_01.dbf
  50   0  Incr 143493547427 07-JAN-19 /l06/app/oracle/oradata/ORCL/fimscol_ORCL_01.dbf
  52   0  Incr 143493547427 07-JAN-19 /l07/app/oracle/oradata/ORCL/fimsdata_ORCL_02.dbf
  59   0  Incr 143493547427 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_03.dbf
  118  0  Incr 143493547427 07-JAN-19 /l06/app/oracle/oradata/ORCL/tools_ORCL_01.dbf
  123  0  Incr 143493547427 07-JAN-19 /l06/app/oracle/oradata/ORCL/payrdata_ORCL_04.dbf
  170  0  Incr 143493547427 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_10.dbf
  181  0  Incr 143493547427 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_09.dbf
  247  0  Incr 143493547427 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_26.dbf
  259  0  Incr 143493547427 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_39.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723052 Incr 0  20.64G     DISK        00:07:53     07-JAN-19      
        BP Key: 425723082   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139770_piece1_20190107_fqtmmlpf_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723052
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  12   0  Incr 143493526642 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_02.dbf
  21   0  Incr 143493526642 07-JAN-19 /l06/app/oracle/oradata/ORCL/baniam_ORCL_01.dbf
  33   0  Incr 143493526642 07-JAN-19 /l06/app/oracle/oradata/ORCL/emdata_ORCL_04.dbf
  53   0  Incr 143493526642 07-JAN-19 /l07/app/oracle/oradata/ORCL/fimsdata_ORCL_03.dbf
  60   0  Incr 143493526642 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_04.dbf
  126  0  Incr 143493526642 07-JAN-19 /l06/app/oracle/oradata/ORCL/system02.dbf
  174  0  Incr 143493526642 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_23.dbf
  186  0  Incr 143493526642 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_10.dbf
  248  0  Incr 143493526642 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_27.dbf
  262  0  Incr 143493526642 07-JAN-19 /l07/app/oracle/oradata/ORCL/faisdata_ORCL_09.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723054 Incr 0  17.96G     DISK        00:10:39     07-JAN-19      
        BP Key: 425723084   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139772_piece1_20190107_fstmmm8j_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723054
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  13   0  Incr 143493590003 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_03.dbf
  56   0  Incr 143493590003 07-JAN-19 /l06/app/oracle/oradata/ORCL/flexdata_ORCL_01.dbf
  61   0  Incr 143493590003 07-JAN-19 /l06/app/oracle/oradata/ORCL/genndx_ORCL_01.dbf
  83   0  Incr 143493590003 07-JAN-19 /l06/app/oracle/oradata/ORCL/rbs_ORCL_01.dbf
  96   0  Incr 143493590003 07-JAN-19 /l07/app/oracle/oradata/ORCL/satundx_ORCL_02.dbf
  127  0  Incr 143493590003 07-JAN-19 /l06/app/oracle/oradata/ORCL/system03.dbf
  175  0  Incr 143493590003 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_24.dbf
  187  0  Incr 143493590003 07-JAN-19 /l07/app/oracle/oradata/ORCL/workflow_ORCL_11.dbf
  250  0  Incr 143493590003 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_28.dbf
  266  0  Incr 143493590003 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfndx_ORCL_05.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723055 Incr 0  18.25G     DISK        00:06:25     07-JAN-19      
        BP Key: 425723085   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139773_piece1_20190107_fttmmmla_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723055
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  14   0  Incr 143493636887 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_04.dbf
  30   0  Incr 143493636887 07-JAN-19 /l06/app/oracle/oradata/ORCL/emdata_ORCL_01.dbf
  62   0  Incr 143493636887 07-JAN-19 /l06/app/oracle/oradata/ORCL/genndx_ORCL_02.dbf
  84   0  Incr 143493636887 07-JAN-19 /l06/app/oracle/oradata/ORCL/rbsbat_ORCL_01.dbf
  97   0  Incr 143493636887 07-JAN-19 /l07/app/oracle/oradata/ORCL/satundx_ORCL_03.dbf
  128  0  Incr 143493636887 07-JAN-19 /l06/app/oracle/oradata/ORCL/system04.dbf
  182  0  Incr 143493636887 07-JAN-19 /l06/app/oracle/oradata/ORCL/axiom_ORCL_05.dbf
  188  0  Incr 143493636887 07-JAN-19 /l07/app/oracle/oradata/ORCL/workflow_ORCL_12.dbf
  253  0  Incr 143493636887 07-JAN-19 /l06/app/oracle/oradata/ORCL/fimsdata_ORCL_08.dbf
  267  0  Incr 143493636887 07-JAN-19 /l07/app/oracle/oradata/ORCL/genndx_ORCL_08.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723038 Incr 0  21.26G     DISK        00:07:13     07-JAN-19      
        BP Key: 425723068   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139756_piece1_20190107_fctmmhko_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723038
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  15   0  Incr 143492955664 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_05.dbf
  47   0  Incr 143492955664 07-JAN-19 /l06/app/oracle/oradata/ORCL/fgbtrnd_encdndx_ORCL_01.dbf
  63   0  Incr 143492955664 07-JAN-19 /l06/app/oracle/oradata/ORCL/legadata_ORCL_01.dbf
  98   0  Incr 143492955664 07-JAN-19 /l07/app/oracle/oradata/ORCL/satundx_ORCL_04.dbf
  131  0  Incr 143492955664 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_07.dbf
  162  0  Incr 143492955664 07-JAN-19 /l07/app/oracle/oradata/ORCL/workflow_ORCL_05.dbf
  184  0  Incr 143492955664 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_11.dbf
  189  0  Incr 143492955664 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_12.dbf
  254  0  Incr 143492955664 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_29.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723043 Incr 0  18.61G     DISK        00:07:07     07-JAN-19      
        BP Key: 425723073   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139761_piece1_20190107_fhtmmj66_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723043
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  16   0  Incr 143493219128 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_06.dbf
  67   0  Incr 143493219128 07-JAN-19 /l06/app/oracle/oradata/ORCL/mlogdata_ORCL_01.dbf
  100  0  Incr 143493219128 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_01.dbf
  111  0  Incr 143493219128 07-JAN-19 /l06/app/oracle/oradata/ORCL/streamsdata_ORCL_01.dbf
  135  0  Incr 143493219128 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_09.dbf
  185  0  Incr 143493219128 07-JAN-19 /l06/app/oracle/oradata/ORCL/devl_ORCL_12.dbf
  190  0  Incr 143493219128 07-JAN-19 /l06/app/oracle/oradata/ORCL/genndx_ORCL_05.dbf
  200  0  Incr 143493219128 07-JAN-19 /l07/app/oracle/oradata/ORCL/sysaux08.dbf
  257  0  Incr 143493219128 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_37.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723028 Incr 0  23.95G     DISK        00:11:33     07-JAN-19      
        BP Key: 425723058   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139746_piece1_20190107_f2tmmerr_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723028
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  18   0  Incr 143491310070 07-JAN-19 /l06/app/oracle/oradata/ORCL/axiom_ORCL_02.dbf
  45   0  Incr 143491310070 07-JAN-19 /l06/app/oracle/oradata/ORCL/fgbtndx_ORCL_01.dbf
  70   0  Incr 143491310070 07-JAN-19 /l07/app/oracle/oradata/ORCL/payrdata_ORCL_01.dbf
  72   0  Incr 143491310070 07-JAN-19 /l07/app/oracle/oradata/ORCL/payrdata_ORCL_03.dbf
  122  0  Incr 143491310070 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_03.dbf
  129  0  Incr 143491310070 07-JAN-19 /l06/app/oracle/oradata/ORCL/system05.dbf
  142  0  Incr 143491310070 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_12.dbf
  193  0  Incr 143491310070 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_12.dbf
  196  0  Incr 143491310070 07-JAN-19 /l06/app/oracle/oradata/ORCL/sysaux05.dbf
  261  0  Incr 143491310070 07-JAN-19 /l07/app/oracle/oradata/ORCL/axiom_ORCL_06.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723029 Incr 0  24.87G     DISK        00:11:54     07-JAN-19      
        BP Key: 425723059   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139747_piece1_20190107_f3tmmers_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723029
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  19   0  Incr 143491310807 07-JAN-19 /l06/app/oracle/oradata/ORCL/axiom_ORCL_03.dbf
  55   0  Incr 143491310807 07-JAN-19 /l06/app/oracle/oradata/ORCL/fimsndx_ORCL_01.dbf
  73   0  Incr 143491310807 07-JAN-19 /l07/app/oracle/oradata/ORCL/payrdata_ORCL_04.dbf
  74   0  Incr 143491310807 07-JAN-19 /l06/app/oracle/oradata/ORCL/payrndx_ORCL_01.dbf
  125  0  Incr 143491310807 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_05.dbf
  143  0  Incr 143491310807 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfndx_ORCL_02.dbf
  165  0  Incr 143491310807 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_20.dbf
  202  0  Incr 143491310807 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_13.dbf
  203  0  Incr 143491310807 07-JAN-19 /l07/app/oracle/oradata/ORCL/workflow_ORCL_13.dbf
  263  0  Incr 143491310807 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_31.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723047 Incr 0  19.79G     DISK        00:10:00     07-JAN-19      
        BP Key: 425723077   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139764_piece1_20190107_fktmmk1m_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723047
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  20   0  Incr 143493344742 07-JAN-19 /l06/app/oracle/oradata/ORCL/banaq_ORCL_01.dbf
  36   0  Incr 143493344742 07-JAN-19 /l07/app/oracle/oradata/ORCL/faisdata_ORCL_05.dbf
  44   0  Incr 143493344742 07-JAN-19 /l06/app/oracle/oradata/ORCL/fgbtdata_ORCL_03.dbf
  86   0  Incr 143493344742 07-JAN-19 /l06/app/oracle/oradata/ORCL/RMS_DATA_02.dbf
  113  0  Incr 143493344742 07-JAN-19 /l06/app/oracle/oradata/ORCL/taisdata_ORCL_02.dbf
  152  0  Incr 143493344742 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_14.dbf
  172  0  Incr 143493344742 07-JAN-19 /l06/app/oracle/oradata/ORCL/bansdata_ORCL_02.dbf
  232  0  Incr 143493344742 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_22.dbf
  240  0  Incr 143493344742 07-JAN-19 /l06/app/oracle/oradata/ORCL/undo_ORCL_11.dbf
  243  0  Incr 143493344742 07-JAN-19 /l06/app/oracle/oradata/ORCL/genndx_ORCL_07.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723041 Incr 0  25.82G     DISK        00:13:58     07-JAN-19      
        BP Key: 425723071   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139759_piece1_20190107_fftmmibh_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723041
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  23   0  Incr 143493043450 07-JAN-19 /l06/app/oracle/oradata/ORCL/bansdata_ORCL_01.dbf
  41   0  Incr 143493043450 07-JAN-19 /l06/app/oracle/oradata/ORCL/faisndx_ORCL_03.dbf
  102  0  Incr 143493043450 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfndx_ORCL_01.dbf
  148  0  Incr 143493043450 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_10.dbf
  168  0  Incr 143493043450 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_07.dbf
  199  0  Incr 143493043450 07-JAN-19 /l07/app/oracle/oradata/ORCL/sysaux07.dbf
  226  0  Incr 143493043450 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_19.dbf
  233  0  Incr 143493043450 07-JAN-19 /l06/app/oracle/oradata/ORCL/mlogdata_ORCL_03.dbf
  237  0  Incr 143493043450 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_09.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723031 Incr 0  29.48G     DISK        00:12:22     07-JAN-19      
        BP Key: 425723061   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139748_piece1_20190107_f4tmmfhl_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723031
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  24   0  Incr 143491842792 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_01.dbf
  75   0  Incr 143491842792 07-JAN-19 /l06/app/oracle/oradata/ORCL/payrndx_ORCL_02.dbf
  79   0  Incr 143491842792 07-JAN-19 /l07/app/oracle/oradata/ORCL/posndata_ORCL_02.dbf
  80   0  Incr 143491842792 07-JAN-19 /l07/app/oracle/oradata/ORCL/posndata_ORCL_03.dbf
  130  0  Incr 143491842792 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_07.dbf
  145  0  Incr 143491842792 07-JAN-19 /l06/app/oracle/oradata/ORCL/genndx_ORCL_03.dbf
  178  0  Incr 143491842792 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_27.dbf
  204  0  Incr 143491842792 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_13.dbf
  205  0  Incr 143491842792 07-JAN-19 /l06/app/oracle/oradata/ORCL/genndx_ORCL_06.dbf
  264  0  Incr 143491842792 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_32.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723030 Incr 0  19.21G     DISK        00:08:58     07-JAN-19      
        BP Key: 425723060   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139749_piece1_20190107_f5tmmfih_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723030
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  25   0  Incr 143491850503 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_02.dbf
  76   0  Incr 143491850503 07-JAN-19 /l06/app/oracle/oradata/ORCL/payrndx_ORCL_03.dbf
  94   0  Incr 143491850503 07-JAN-19 /l07/app/oracle/oradata/ORCL/satudata_ORCL_06.dbf
  116  0  Incr 143491850503 07-JAN-19 /l06/app/oracle/oradata/ORCL/taisndx_ORCL_02.dbf
  132  0  Incr 143491850503 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_08.dbf
  146  0  Incr 143491850503 07-JAN-19 /l06/app/oracle/oradata/ORCL/sysaux02.dbf
  197  0  Incr 143491850503 07-JAN-19 /l06/app/oracle/oradata/ORCL/sysaux06.dbf
  206  0  Incr 143491850503 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_14.dbf
  207  0  Incr 143491850503 07-JAN-19 /l06/app/oracle/oradata/ORCL/satundx_ORCL_05.dbf
  265  0  Incr 143491850503 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_34.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723032 Incr 0  27.00G     DISK        00:10:31     07-JAN-19      
        BP Key: 425723062   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139750_piece1_20190107_f6tmmg3k_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723032
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  26   0  Incr 143491921115 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_03.dbf
  77   0  Incr 143491921115 07-JAN-19 /l06/app/oracle/oradata/ORCL/payrndx_ORCL_04.dbf
  103  0  Incr 143491921115 07-JAN-19 /l06/app/oracle/oradata/ORCL/shrtdata_ORCL_01.dbf
  117  0  Incr 143491921115 07-JAN-19 /l06/app/oracle/oradata/ORCL/taisndx_ORCL_03.dbf
  133  0  Incr 143491921115 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_05.dbf
  150  0  Incr 143491921115 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_02.dbf
  201  0  Incr 143491921115 07-JAN-19 /l07/app/oracle/oradata/ORCL/sysaux09.dbf
  208  0  Incr 143491921115 07-JAN-19 /l06/app/oracle/oradata/ORCL/payrndx_ORCL_05.dbf
  209  0  Incr 143491921115 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_14.dbf
  268  0  Incr 143491921115 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_33.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723033 Incr 0  23.51G     DISK        00:09:06     07-JAN-19      
        BP Key: 425723063   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139751_piece1_20190107_f7tmmg95_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723033
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  27   0  Incr 143491951966 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_04.dbf
  78   0  Incr 143491951966 07-JAN-19 /l07/app/oracle/oradata/ORCL/posndata_ORCL_01.dbf
  104  0  Incr 143491951966 07-JAN-19 /l06/app/oracle/oradata/ORCL/shrtdata_ORCL_02.dbf
  134  0  Incr 143491951966 07-JAN-19 /l06/app/oracle/oradata/ORCL/devl_ORCL_08.dbf
  154  0  Incr 143491951966 07-JAN-19 /l06/app/oracle/oradata/ORCL/workflow_ORCL_03.dbf
  179  0  Incr 143491951966 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_28.dbf
  210  0  Incr 143491951966 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_30.dbf
  211  0  Incr 143491951966 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_15.dbf
  212  0  Incr 143491951966 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_31.dbf
  269  0  Incr 143491951966 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_35.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723034 Incr 0  20.32G     DISK        00:07:57     07-JAN-19      
        BP Key: 425723064   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139752_piece1_20190107_f8tmmgnf_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723034
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  28   0  Incr 143492027925 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_05.dbf
  82   0  Incr 143492027925 07-JAN-19 /l06/app/oracle/oradata/ORCL/posnndx_ORCL_02.dbf
  105  0  Incr 143492027925 07-JAN-19 /l06/app/oracle/oradata/ORCL/shrtdata_ORCL_03.dbf
  136  0  Incr 143492027925 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_09.dbf
  156  0  Incr 143492027925 07-JAN-19 /l06/app/oracle/oradata/ORCL/faisdata_ORCL_06.dbf
  180  0  Incr 143492027925 07-JAN-19 /l07/app/oracle/oradata/ORCL/auditdata_ORCL_29.dbf
  214  0  Incr 143492027925 07-JAN-19 /l07/app/oracle/oradata/ORCL/undo_ORCL_07.dbf
  216  0  Incr 143492027925 07-JAN-19 /l06/app/oracle/oradata/ORCL/bansdata_ORCL_03.dbf
  235  0  Incr 143492027925 07-JAN-19 /l06/app/oracle/oradata/ORCL/mlogdata_ORCL_05.dbf
  270  0  Incr 143492027925 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_36.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723035 Incr 0  24.67G     DISK        00:09:30     07-JAN-19      
        BP Key: 425723065   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139753_piece1_20190107_f9tmmgqg_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723035
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  29   0  Incr 143492321405 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_06.dbf
  64   0  Incr 143492321405 07-JAN-19 /l06/app/oracle/oradata/ORCL/legadata_ORCL_02.dbf
  89   0  Incr 143492321405 07-JAN-19 /l07/app/oracle/oradata/ORCL/satudata_ORCL_01.dbf
  106  0  Incr 143492321405 07-JAN-19 /l06/app/oracle/oradata/ORCL/shrtdata_ORCL_04.dbf
  138  0  Incr 143492321405 07-JAN-19 /l07/app/oracle/oradata/ORCL/gendata_ORCL_06.dbf
  157  0  Incr 143492321405 07-JAN-19 /l07/app/oracle/oradata/ORCL/faisdata_ORCL_07.dbf
  183  0  Incr 143492321405 07-JAN-19 /l06/app/oracle/oradata/ORCL/sysaux03.dbf
  215  0  Incr 143492321405 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_15.dbf
  219  0  Incr 143492321405 07-JAN-19 /l07/app/oracle/oradata/ORCL/faisdata_ORCL_08.dbf
  271  0  Incr 143492321405 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_37.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723045 Incr 0  16.79G     DISK        00:07:28     07-JAN-19      
        BP Key: 425723075   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139763_piece1_20190107_fjtmmjjn_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723045
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  31   0  Incr 143493299019 07-JAN-19 /l06/app/oracle/oradata/ORCL/emdata_ORCL_02.dbf
  35   0  Incr 143493299019 07-JAN-19 /l07/app/oracle/oradata/ORCL/faisdata_ORCL_04.dbf
  43   0  Incr 143493299019 07-JAN-19 /l06/app/oracle/oradata/ORCL/fgbtdata_ORCL_02.dbf
  85   0  Incr 143493299019 07-JAN-19 /l06/app/oracle/oradata/ORCL/RMS_DATA_01.dbf
  112  0  Incr 143493299019 07-JAN-19 /l06/app/oracle/oradata/ORCL/taisdata_ORCL_01.dbf
  151  0  Incr 143493299019 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_13.dbf
  171  0  Incr 143493299019 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_22.dbf
  231  0  Incr 143493299019 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_21.dbf
  239  0  Incr 143493299019 07-JAN-19 /l06/app/oracle/oradata/ORCL/undo_ORCL_12.dbf
  242  0  Incr 143493299019 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_09.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723042 Incr 0  21.87G     DISK        00:12:49     07-JAN-19      
        BP Key: 425723072   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139760_piece1_20190107_fgtmmild_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723042
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  34   0  Incr 143493151807 07-JAN-19 /l07/app/oracle/oradata/ORCL/faisdata_ORCL_03.dbf
  42   0  Incr 143493151807 07-JAN-19 /l06/app/oracle/oradata/ORCL/fgbtdata_ORCL_01.dbf
  110  0  Incr 143493151807 07-JAN-19 /l06/app/oracle/oradata/ORCL/shrtndx_ORCL_01.dbf
  149  0  Incr 143493151807 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_11.dbf
  169  0  Incr 143493151807 07-JAN-19 /l06/app/oracle/oradata/ORCL/genndx_ORCL_04.dbf
  227  0  Incr 143493151807 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_20.dbf
  234  0  Incr 143493151807 07-JAN-19 /l06/app/oracle/oradata/ORCL/mlogdata_ORCL_04.dbf
  238  0  Incr 143493151807 07-JAN-19 /l06/app/oracle/oradata/ORCL/undo_ORCL_10.dbf
  252  0  Incr 143493151807 07-JAN-19 /l06/app/oracle/oradata/ORCL/sfrfdata_ORCL_10.dbf

BS Key  Type LV Size       Device Type Elapsed Time Completion Time
------- ---- -- ---------- ----------- ------------ ---------------
425723036 Incr 0  21.19G     DISK        00:07:30     07-JAN-19      
        BP Key: 425723066   Status: AVAILABLE  Compressed: NO  Tag: BACKUP_LEVEL0_WEEKLY
        Piece Name: /oraclebackup/rman/ddw/ORCL/backup_ORCL_set139754_piece1_20190107_fatmmh6j_1_1.bus
        Keep: LOGS               Until: 11-FEB-19      
  List of Datafiles in backup set 425723036
  File LV Type Ckp SCN    Ckp Time  Name
  ---- -- ---- ---------- --------- ----
  40   0  Incr 143492896006 07-JAN-19 /l06/app/oracle/oradata/ORCL/faisndx_ORCL_02.dbf
  65   0  Incr 143492896006 07-JAN-19 /l06/app/oracle/oradata/ORCL/legandx_ORCL_01.dbf
  90   0  Incr 143492896006 07-JAN-19 /l07/app/oracle/oradata/ORCL/satudata_ORCL_02.dbf
  107  0  Incr 143492896006 07-JAN-19 /l06/app/oracle/oradata/ORCL/shrtdata_ORCL_06.dbf
  139  0  Incr 143492896006 07-JAN-19 /l07/app/oracle/oradata/ORCL/fimsdata_ORCL_05.dbf
  158  0  Incr 143492896006 07-JAN-19 /l06/app/oracle/oradata/ORCL/auditdata_ORCL_16.dbf
  195  0  Incr 143492896006 07-JAN-19 /l06/app/oracle/oradata/ORCL/sysaux04.dbf
  217  0  Incr 143492896006 07-JAN-19 /l07/app/oracle/oradata/ORCL/devl_ORCL_16.dbf
  221  0  Incr 143492896006 07-JAN-19 /l06/app/oracle/oradata/ORCL/faisndx_ORCL_04.dbf
  272  0  Incr 143492896006 07-JAN-19 /l07/app/oracle/oradata/ORCL/axiom_ORCL_07.dbf
using channel ORA_DISK_1
using channel ORA_DISK_2


List of Backup Sets
===================


BS Key  Size
------- ----------
425723456 882.47M

  List of Archived Logs in backup set 425723456
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1117959 143491200056 06-JAN-19 143491907280 07-JAN-19

  Backup Set Copy #1 of backup set 425723456
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:29     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723456 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723523 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139787_p1_c1_20190107.log

  Backup Set Copy #2 of backup set 425723456
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:29     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723456 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723524 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139787_p1_c2_20190107.log

BS Key  Size
------- ----------
425723478 489.49M

  List of Archived Logs in backup set 425723478
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1117960 143491907280 07-JAN-19 143493013913 07-JAN-19
  1    1117961 143493013913 07-JAN-19 143493023128 07-JAN-19
  1    1117962 143493023128 07-JAN-19 143493024719 07-JAN-19
  1    1117963 143493024719 07-JAN-19 143493032688 07-JAN-19
  1    1117964 143493032688 07-JAN-19 143493038754 07-JAN-19
  1    1117965 143493038754 07-JAN-19 143493048089 07-JAN-19
  1    1117966 143493048089 07-JAN-19 143493058445 07-JAN-19
  1    1117967 143493058445 07-JAN-19 143493068958 07-JAN-19
  1    1117968 143493068958 07-JAN-19 143493080706 07-JAN-19
  1    1117969 143493080706 07-JAN-19 143493086406 07-JAN-19

  Backup Set Copy #1 of backup set 425723478
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:16     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723478 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723567 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139810_p1_c1_20190107.log

  Backup Set Copy #2 of backup set 425723478
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:16     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723478 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723568 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139810_p1_c2_20190107.log

BS Key  Size
------- ----------
425723492 26.05M

  List of Archived Logs in backup set 425723492
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1117970 143493086406 07-JAN-19 143493095621 07-JAN-19
  1    1117971 143493095621 07-JAN-19 143493105774 07-JAN-19
  1    1117972 143493105774 07-JAN-19 143493115684 07-JAN-19
  1    1117973 143493115684 07-JAN-19 143493126042 07-JAN-19
  1    1117974 143493126042 07-JAN-19 143493136713 07-JAN-19
  1    1117975 143493136713 07-JAN-19 143493145982 07-JAN-19
  1    1117976 143493145982 07-JAN-19 143493148167 07-JAN-19
  1    1117977 143493148167 07-JAN-19 143493150240 07-JAN-19
  1    1117978 143493150240 07-JAN-19 143493151991 07-JAN-19
  1    1117979 143493151991 07-JAN-19 143493159029 07-JAN-19

  Backup Set Copy #1 of backup set 425723492
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:01     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723492 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723595 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139824_p1_c1_20190107.log

  Backup Set Copy #2 of backup set 425723492
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:01     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723492 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723596 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139824_p1_c2_20190107.log

BS Key  Size
------- ----------
425723461 874.93M

  List of Archived Logs in backup set 425723461
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1117980 143493159029 07-JAN-19 143493165844 07-JAN-19
  1    1117981 143493165844 07-JAN-19 143493178649 07-JAN-19
  1    1117982 143493178649 07-JAN-19 143493301959 07-JAN-19
  1    1117983 143493301959 07-JAN-19 143493489821 07-JAN-19

  Backup Set Copy #1 of backup set 425723461
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:29     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723461 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723533 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139793_p1_c1_20190107.log

  Backup Set Copy #2 of backup set 425723461
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:29     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723461 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723534 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139793_p1_c2_20190107.log

BS Key  Size
------- ----------
425723480 487.30M

  List of Archived Logs in backup set 425723480
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1117984 143493489821 07-JAN-19 143493501566 07-JAN-19
  1    1117985 143493501566 07-JAN-19 143493511880 07-JAN-19
  1    1117986 143493511880 07-JAN-19 143493692073 07-JAN-19
  1    1117987 143493692073 07-JAN-19 143493699894 07-JAN-19
  1    1117988 143493699894 07-JAN-19 143493702060 07-JAN-19

  Backup Set Copy #1 of backup set 425723480
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:16     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723480 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723571 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139812_p1_c1_20190107.log

  Backup Set Copy #2 of backup set 425723480
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:16     07-JAN-19       NO         TAG20190107T022215

    List of Backup Pieces for backup set 425723480 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425723572 1   AVAILABLE   /oraclebackup/rman/ddw/ORCL/archive_07_s139812_p1_c2_20190107.log

BS Key  Size
------- ----------
425779419 92.24M

  List of Archived Logs in backup set 425779419
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1117989 143493702060 07-JAN-19 143493715096 07-JAN-19
  1    1117990 143493715096 07-JAN-19 143493717773 07-JAN-19
  1    1117991 143493717773 07-JAN-19 143493724098 07-JAN-19
  1    1117992 143493724098 07-JAN-19 143493727561 07-JAN-19
  1    1117993 143493727561 07-JAN-19 143493732970 07-JAN-19
  1    1117994 143493732970 07-JAN-19 143493737867 07-JAN-19
  1    1117995 143493737867 07-JAN-19 143493741032 07-JAN-19
  1    1117996 143493741032 07-JAN-19 143493744120 07-JAN-19
  1    1117997 143493744120 07-JAN-19 143493745419 07-JAN-19
  1    1117998 143493745419 07-JAN-19 143493746530 07-JAN-19
  1    1117999 143493746530 07-JAN-19 143493747637 07-JAN-19
  1    1118000 143493747637 07-JAN-19 143493748638 07-JAN-19
  1    1118001 143493748638 07-JAN-19 143493749602 07-JAN-19
  1    1118002 143493749602 07-JAN-19 143493750622 07-JAN-19
  1    1118003 143493750622 07-JAN-19 143493751812 07-JAN-19
  1    1118004 143493751812 07-JAN-19 143493752946 07-JAN-19
  1    1118005 143493752946 07-JAN-19 143493754047 07-JAN-19
  1    1118006 143493754047 07-JAN-19 143493755226 07-JAN-19
  1    1118007 143493755226 07-JAN-19 143493756832 07-JAN-19
  1    1118008 143493756832 07-JAN-19 143493758144 07-JAN-19

  Backup Set Copy #1 of backup set 425779419
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:06     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779419 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779495 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139894_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779419
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:06     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779419 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779496 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139894_p1_c2_20190108.log

BS Key  Size
------- ----------
425779384 1.80G

  List of Archived Logs in backup set 425779384
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118009 143493758144 07-JAN-19 143493764740 07-JAN-19
  1    1118010 143493764740 07-JAN-19 143493911309 07-JAN-19
  1    1118011 143493911309 07-JAN-19 143494083643 07-JAN-19
  1    1118012 143494083643 07-JAN-19 143494094538 07-JAN-19
  1    1118013 143494094538 07-JAN-19 143494104083 07-JAN-19
  1    1118014 143494104083 07-JAN-19 143494238242 07-JAN-19
  1    1118015 143494238242 07-JAN-19 143494244337 07-JAN-19

  Backup Set Copy #1 of backup set 425779384
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:02:45     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779384 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779425 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139858_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779384
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:02:45     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779384 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779426 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139858_p1_c2_20190108.log

BS Key  Size
------- ----------
425779403 530.03M

  List of Archived Logs in backup set 425779403
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118016 143494244337 07-JAN-19 143494248929 07-JAN-19
  1    1118017 143494248929 07-JAN-19 143494249091 07-JAN-19
  1    1118018 143494249091 07-JAN-19 143494249126 07-JAN-19
  1    1118019 143494249126 07-JAN-19 143494249863 07-JAN-19
  1    1118020 143494249863 07-JAN-19 143494253876 07-JAN-19
  1    1118021 143494253876 07-JAN-19 143494256112 07-JAN-19
  1    1118022 143494256112 07-JAN-19 143494257889 07-JAN-19
  1    1118023 143494257889 07-JAN-19 143494260101 07-JAN-19
  1    1118024 143494260101 07-JAN-19 143494263428 07-JAN-19
  1    1118025 143494263428 07-JAN-19 143494264190 07-JAN-19
  1    1118026 143494264190 07-JAN-19 143494264922 07-JAN-19
  1    1118027 143494264922 07-JAN-19 143494265647 07-JAN-19
  1    1118028 143494265647 07-JAN-19 143494266456 07-JAN-19
  1    1118029 143494266456 07-JAN-19 143494267041 07-JAN-19
  1    1118030 143494267041 07-JAN-19 143494268226 07-JAN-19
  1    1118031 143494268226 07-JAN-19 143494268994 07-JAN-19
  1    1118032 143494268994 07-JAN-19 143494269638 07-JAN-19
  1    1118033 143494269638 07-JAN-19 143494270335 07-JAN-19
  1    1118034 143494270335 07-JAN-19 143494271116 07-JAN-19
  1    1118035 143494271116 07-JAN-19 143494272495 07-JAN-19

  Backup Set Copy #1 of backup set 425779403
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:20     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779403 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779463 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139877_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779403
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:20     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779403 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779464 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139877_p1_c2_20190108.log

BS Key  Size
------- ----------
425779398 1.72G

  List of Archived Logs in backup set 425779398
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118036 143494272495 07-JAN-19 143494274513 07-JAN-19
  1    1118037 143494274513 07-JAN-19 143494279119 07-JAN-19
  1    1118038 143494279119 07-JAN-19 143494391685 07-JAN-19
  1    1118039 143494391685 07-JAN-19 143494532511 07-JAN-19
  1    1118040 143494532511 07-JAN-19 143494537168 07-JAN-19
  1    1118041 143494537168 07-JAN-19 143494543197 07-JAN-19
  1    1118042 143494543197 07-JAN-19 143494638996 07-JAN-19
  1    1118043 143494638996 07-JAN-19 143494771244 07-JAN-19

  Backup Set Copy #1 of backup set 425779398
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:54     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779398 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779453 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139872_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779398
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:54     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779398 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779454 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139872_p1_c2_20190108.log

BS Key  Size
------- ----------
425779416 515.74M

  List of Archived Logs in backup set 425779416
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118044 143494771244 07-JAN-19 143494971300 07-JAN-19
  1    1118045 143494971300 07-JAN-19 143494978082 07-JAN-19
  1    1118046 143494978082 07-JAN-19 143494979149 07-JAN-19
  1    1118047 143494979149 07-JAN-19 143494983261 07-JAN-19
  1    1118048 143494983261 07-JAN-19 143494986103 07-JAN-19
  1    1118049 143494986103 07-JAN-19 143494989186 07-JAN-19
  1    1118050 143494989186 07-JAN-19 143494992155 07-JAN-19
  1    1118051 143494992155 07-JAN-19 143494995076 07-JAN-19
  1    1118052 143494995076 07-JAN-19 143494999342 07-JAN-19
  1    1118053 143494999342 07-JAN-19 143494999981 07-JAN-19
  1    1118054 143494999981 07-JAN-19 143495000657 07-JAN-19
  1    1118055 143495000657 07-JAN-19 143495001155 07-JAN-19
  1    1118056 143495001155 07-JAN-19 143495001571 07-JAN-19
  1    1118057 143495001571 07-JAN-19 143495002032 07-JAN-19
  1    1118058 143495002032 07-JAN-19 143495002483 07-JAN-19
  1    1118059 143495002483 07-JAN-19 143495002959 07-JAN-19
  1    1118060 143495002959 07-JAN-19 143495003472 07-JAN-19
  1    1118061 143495003472 07-JAN-19 143495003924 07-JAN-19
  1    1118062 143495003924 07-JAN-19 143495004349 07-JAN-19
  1    1118063 143495004349 07-JAN-19 143495004846 07-JAN-19

  Backup Set Copy #1 of backup set 425779416
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:18     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779416 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779489 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139890_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779416
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:18     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779416 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779490 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139890_p1_c2_20190108.log

BS Key  Size
------- ----------
425779396 1.72G

  List of Archived Logs in backup set 425779396
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118064 143495004846 07-JAN-19 143495005275 07-JAN-19
  1    1118065 143495005275 07-JAN-19 143495008082 07-JAN-19
  1    1118066 143495008082 07-JAN-19 143495188187 07-JAN-19
  1    1118067 143495188187 07-JAN-19 143495442878 07-JAN-19
  1    1118068 143495442878 07-JAN-19 143495458730 07-JAN-19
  1    1118069 143495458730 07-JAN-19 143495466159 07-JAN-19
  1    1118070 143495466159 07-JAN-19 143495668035 07-JAN-19
  1    1118071 143495668035 07-JAN-19 143495851386 07-JAN-19

  Backup Set Copy #1 of backup set 425779396
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:57     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779396 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779449 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139870_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779396
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:57     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779396 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779450 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139870_p1_c2_20190108.log

BS Key  Size
------- ----------
425779401 566.85M

  List of Archived Logs in backup set 425779401
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118072 143495851386 07-JAN-19 143496852187 07-JAN-19
  1    1118073 143496852187 07-JAN-19 143496852832 07-JAN-19
  1    1118074 143496852832 07-JAN-19 143496853108 07-JAN-19
  1    1118075 143496853108 07-JAN-19 143496853737 07-JAN-19
  1    1118076 143496853737 07-JAN-19 143496854667 07-JAN-19
  1    1118077 143496854667 07-JAN-19 143496855241 07-JAN-19
  1    1118078 143496855241 07-JAN-19 143496856278 07-JAN-19
  1    1118079 143496856278 07-JAN-19 143496857070 07-JAN-19
  1    1118080 143496857070 07-JAN-19 143496858396 07-JAN-19
  1    1118081 143496858396 07-JAN-19 143496858596 07-JAN-19
  1    1118082 143496858596 07-JAN-19 143496858698 07-JAN-19
  1    1118083 143496858698 07-JAN-19 143496858898 07-JAN-19
  1    1118084 143496858898 07-JAN-19 143496859148 07-JAN-19
  1    1118085 143496859148 07-JAN-19 143496859416 07-JAN-19
  1    1118086 143496859416 07-JAN-19 143496859485 07-JAN-19
  1    1118087 143496859485 07-JAN-19 143496859688 07-JAN-19
  1    1118088 143496859688 07-JAN-19 143496859955 07-JAN-19
  1    1118089 143496859955 07-JAN-19 143496860301 07-JAN-19
  1    1118090 143496860301 07-JAN-19 143496860616 07-JAN-19
  1    1118091 143496860616 07-JAN-19 143496860802 07-JAN-19

  Backup Set Copy #1 of backup set 425779401
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:20     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779401 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779459 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139876_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779401
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:20     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779401 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779460 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139876_p1_c2_20190108.log

BS Key  Size
------- ----------
425779383 1.81G

  List of Archived Logs in backup set 425779383
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118092 143496860802 07-JAN-19 143496861058 07-JAN-19
  1    1118093 143496861058 07-JAN-19 143496861784 07-JAN-19
  1    1118094 143496861784 07-JAN-19 143497104620 07-JAN-19
  1    1118095 143497104620 07-JAN-19 143497501693 07-JAN-19
  1    1118096 143497501693 07-JAN-19 143497545648 07-JAN-19
  1    1118097 143497545648 07-JAN-19 143497608327 07-JAN-19
  1    1118098 143497608327 07-JAN-19 143499899786 07-JAN-19
  1    1118099 143499899786 07-JAN-19 143500925443 07-JAN-19

  Backup Set Copy #1 of backup set 425779383
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:02:44     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779383 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779423 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139857_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779383
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:02:44     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779383 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779424 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139857_p1_c2_20190108.log

BS Key  Size
------- ----------
425779409 520.10M

  List of Archived Logs in backup set 425779409
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118100 143500925443 07-JAN-19 143501235762 07-JAN-19
  1    1118101 143501235762 07-JAN-19 143501257095 07-JAN-19
  1    1118102 143501257095 07-JAN-19 143501259076 07-JAN-19
  1    1118103 143501259076 07-JAN-19 143501267837 07-JAN-19
  1    1118104 143501267837 07-JAN-19 143501282516 07-JAN-19
  1    1118105 143501282516 07-JAN-19 143501312607 07-JAN-19
  1    1118106 143501312607 07-JAN-19 143501355908 07-JAN-19
  1    1118107 143501355908 07-JAN-19 143501402427 07-JAN-19
  1    1118108 143501402427 07-JAN-19 143501450162 07-JAN-19
  1    1118109 143501450162 07-JAN-19 143501469943 07-JAN-19
  1    1118110 143501469943 07-JAN-19 143501470717 07-JAN-19
  1    1118111 143501470717 07-JAN-19 143501471092 07-JAN-19
  1    1118112 143501471092 07-JAN-19 143501472722 07-JAN-19
  1    1118113 143501472722 07-JAN-19 143501484145 07-JAN-19
  1    1118114 143501484145 07-JAN-19 143501489502 07-JAN-19
  1    1118115 143501489502 07-JAN-19 143501490907 07-JAN-19
  1    1118116 143501490907 07-JAN-19 143501493579 07-JAN-19
  1    1118117 143501493579 07-JAN-19 143501495933 07-JAN-19
  1    1118118 143501495933 07-JAN-19 143501497657 07-JAN-19
  1    1118119 143501497657 07-JAN-19 143501498148 07-JAN-19

  Backup Set Copy #1 of backup set 425779409
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:15     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779409 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779475 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139883_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779409
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:15     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779409 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779476 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139883_p1_c2_20190108.log

BS Key  Size
------- ----------
425779392 1.74G

  List of Archived Logs in backup set 425779392
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118120 143501498148 07-JAN-19 143501498596 07-JAN-19
  1    1118121 143501498596 07-JAN-19 143501545656 07-JAN-19
  1    1118122 143501545656 07-JAN-19 143502109201 07-JAN-19
  1    1118123 143502109201 07-JAN-19 143502510986 07-JAN-19
  1    1118124 143502510986 07-JAN-19 143502517129 07-JAN-19
  1    1118125 143502517129 07-JAN-19 143502518750 07-JAN-19
  1    1118126 143502518750 07-JAN-19 143502649683 07-JAN-19
  1    1118127 143502649683 07-JAN-19 143502889697 07-JAN-19

  Backup Set Copy #1 of backup set 425779392
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:01     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779392 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779441 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139866_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779392
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:01     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779392 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779442 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139866_p1_c2_20190108.log

BS Key  Size
------- ----------
425779420 514.81M

  List of Archived Logs in backup set 425779420
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118128 143502889697 07-JAN-19 143503422756 07-JAN-19
  1    1118129 143503422756 07-JAN-19 143503437926 07-JAN-19
  1    1118130 143503437926 07-JAN-19 143503439556 07-JAN-19
  1    1118131 143503439556 07-JAN-19 143503446788 07-JAN-19
  1    1118132 143503446788 07-JAN-19 143503454067 07-JAN-19
  1    1118133 143503454067 07-JAN-19 143503460809 07-JAN-19
  1    1118134 143503460809 07-JAN-19 143503467913 07-JAN-19
  1    1118135 143503467913 07-JAN-19 143503478946 07-JAN-19
  1    1118136 143503478946 07-JAN-19 143503492455 07-JAN-19
  1    1118137 143503492455 07-JAN-19 143503495299 07-JAN-19
  1    1118138 143503495299 07-JAN-19 143503498017 07-JAN-19
  1    1118139 143503498017 07-JAN-19 143503501837 07-JAN-19
  1    1118140 143503501837 07-JAN-19 143503504504 07-JAN-19
  1    1118141 143503504504 07-JAN-19 143503506819 07-JAN-19
  1    1118142 143503506819 07-JAN-19 143503510272 07-JAN-19
  1    1118143 143503510272 07-JAN-19 143503513583 07-JAN-19
  1    1118144 143503513583 07-JAN-19 143503513846 07-JAN-19
  1    1118145 143503513846 07-JAN-19 143503513909 07-JAN-19
  1    1118146 143503513909 07-JAN-19 143503513957 07-JAN-19
  1    1118147 143503513957 07-JAN-19 143503513994 07-JAN-19

  Backup Set Copy #1 of backup set 425779420
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:14     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779420 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779497 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139893_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779420
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:14     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779420 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779498 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139893_p1_c2_20190108.log

BS Key  Size
------- ----------
425779388 1.75G

  List of Archived Logs in backup set 425779388
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118148 143503513994 07-JAN-19 143503514046 07-JAN-19
  1    1118149 143503514046 07-JAN-19 143503514906 07-JAN-19
  1    1118150 143503514906 07-JAN-19 143504050808 07-JAN-19
  1    1118151 143504050808 07-JAN-19 143504259687 07-JAN-19
  1    1118152 143504259687 07-JAN-19 143504270447 07-JAN-19
  1    1118153 143504270447 07-JAN-19 143504281910 07-JAN-19
  1    1118154 143504281910 07-JAN-19 143504551446 07-JAN-19
  1    1118155 143504551446 07-JAN-19 143504770049 07-JAN-19

  Backup Set Copy #1 of backup set 425779388
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:01     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779388 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779433 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139862_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779388
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:01     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779388 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779434 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139862_p1_c2_20190108.log

BS Key  Size
------- ----------
425779418 515.27M

  List of Archived Logs in backup set 425779418
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118156 143504770049 07-JAN-19 143505052563 07-JAN-19
  1    1118157 143505052563 07-JAN-19 143505076223 07-JAN-19
  1    1118158 143505076223 07-JAN-19 143505081904 07-JAN-19
  1    1118159 143505081904 07-JAN-19 143505083055 07-JAN-19
  1    1118160 143505083055 07-JAN-19 143505090280 07-JAN-19
  1    1118161 143505090280 07-JAN-19 143505098573 07-JAN-19
  1    1118162 143505098573 07-JAN-19 143505109587 07-JAN-19
  1    1118163 143505109587 07-JAN-19 143505112295 07-JAN-19
  1    1118164 143505112295 07-JAN-19 143505115391 07-JAN-19
  1    1118165 143505115391 07-JAN-19 143505117108 07-JAN-19
  1    1118166 143505117108 07-JAN-19 143505118355 07-JAN-19
  1    1118167 143505118355 07-JAN-19 143505119308 07-JAN-19
  1    1118168 143505119308 07-JAN-19 143505120062 07-JAN-19
  1    1118169 143505120062 07-JAN-19 143505120107 07-JAN-19
  1    1118170 143505120107 07-JAN-19 143505120145 07-JAN-19
  1    1118171 143505120145 07-JAN-19 143505120184 07-JAN-19
  1    1118172 143505120184 07-JAN-19 143505120209 07-JAN-19
  1    1118173 143505120209 07-JAN-19 143505120229 07-JAN-19
  1    1118174 143505120229 07-JAN-19 143505120246 07-JAN-19
  1    1118175 143505120246 07-JAN-19 143505121017 07-JAN-19

  Backup Set Copy #1 of backup set 425779418
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:16     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779418 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779493 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139892_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779418
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:16     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779418 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779494 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139892_p1_c2_20190108.log

BS Key  Size
------- ----------
425779387 1.76G

  List of Archived Logs in backup set 425779387
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118176 143505121017 07-JAN-19 143505126552 07-JAN-19
  1    1118177 143505126552 07-JAN-19 143505146006 07-JAN-19
  1    1118178 143505146006 07-JAN-19 143505527476 07-JAN-19
  1    1118179 143505527476 07-JAN-19 143505680032 07-JAN-19
  1    1118180 143505680032 07-JAN-19 143505687367 07-JAN-19
  1    1118181 143505687367 07-JAN-19 143505694874 07-JAN-19
  1    1118182 143505694874 07-JAN-19 143506019142 07-JAN-19
  1    1118183 143506019142 07-JAN-19 143506163891 07-JAN-19

  Backup Set Copy #1 of backup set 425779387
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:00     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779387 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779431 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139861_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779387
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:00     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779387 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779432 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139861_p1_c2_20190108.log

BS Key  Size
------- ----------
425779415 516.19M

  List of Archived Logs in backup set 425779415
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118184 143506163891 07-JAN-19 143506555652 07-JAN-19
  1    1118185 143506555652 07-JAN-19 143506566883 07-JAN-19
  1    1118186 143506566883 07-JAN-19 143506567608 07-JAN-19
  1    1118187 143506567608 07-JAN-19 143506569782 07-JAN-19
  1    1118188 143506569782 07-JAN-19 143506570448 07-JAN-19
  1    1118189 143506570448 07-JAN-19 143506570639 07-JAN-19
  1    1118190 143506570639 07-JAN-19 143506572951 07-JAN-19
  1    1118191 143506572951 07-JAN-19 143506586261 07-JAN-19
  1    1118192 143506586261 07-JAN-19 143506592982 07-JAN-19
  1    1118193 143506592982 07-JAN-19 143506596005 07-JAN-19
  1    1118194 143506596005 07-JAN-19 143506601426 07-JAN-19
  1    1118195 143506601426 07-JAN-19 143506605662 07-JAN-19
  1    1118196 143506605662 07-JAN-19 143506610008 07-JAN-19
  1    1118197 143506610008 07-JAN-19 143506614593 07-JAN-19
  1    1118198 143506614593 07-JAN-19 143506618955 07-JAN-19
  1    1118199 143506618955 07-JAN-19 143506624369 07-JAN-19
  1    1118200 143506624369 07-JAN-19 143506628432 07-JAN-19
  1    1118201 143506628432 07-JAN-19 143506632741 07-JAN-19
  1    1118202 143506632741 07-JAN-19 143506636781 07-JAN-19
  1    1118203 143506636781 07-JAN-19 143506640025 07-JAN-19

  Backup Set Copy #1 of backup set 425779415
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:16     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779415 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779487 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139889_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779415
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:16     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779415 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779488 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139889_p1_c2_20190108.log

BS Key  Size
------- ----------
425779390 1.75G

  List of Archived Logs in backup set 425779390
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118204 143506640025 07-JAN-19 143506644418 07-JAN-19
  1    1118205 143506644418 07-JAN-19 143506664698 07-JAN-19
  1    1118206 143506664698 07-JAN-19 143506979275 07-JAN-19
  1    1118207 143506979275 07-JAN-19 143507317293 07-JAN-19
  1    1118208 143507317293 07-JAN-19 143507337144 07-JAN-19
  1    1118209 143507337144 07-JAN-19 143507343780 07-JAN-19
  1    1118210 143507343780 07-JAN-19 143507542293 07-JAN-19
  1    1118211 143507542293 07-JAN-19 143507836530 07-JAN-19

  Backup Set Copy #1 of backup set 425779390
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:00     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779390 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779437 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139864_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779390
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:00     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779390 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779438 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139864_p1_c2_20190108.log

BS Key  Size
------- ----------
425779414 516.26M

  List of Archived Logs in backup set 425779414
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118212 143507836530 07-JAN-19 143508146939 07-JAN-19
  1    1118213 143508146939 07-JAN-19 143508155978 07-JAN-19
  1    1118214 143508155978 07-JAN-19 143508157168 07-JAN-19
  1    1118215 143508157168 07-JAN-19 143508160883 07-JAN-19
  1    1118216 143508160883 07-JAN-19 143508164856 07-JAN-19
  1    1118217 143508164856 07-JAN-19 143508168460 07-JAN-19
  1    1118218 143508168460 07-JAN-19 143508172620 07-JAN-19
  1    1118219 143508172620 07-JAN-19 143508176467 07-JAN-19
  1    1118220 143508176467 07-JAN-19 143508179696 07-JAN-19
  1    1118221 143508179696 07-JAN-19 143508180885 07-JAN-19
  1    1118222 143508180885 07-JAN-19 143508182004 07-JAN-19
  1    1118223 143508182004 07-JAN-19 143508183052 07-JAN-19
  1    1118224 143508183052 07-JAN-19 143508184186 07-JAN-19
  1    1118225 143508184186 07-JAN-19 143508185199 07-JAN-19
  1    1118226 143508185199 07-JAN-19 143508187056 07-JAN-19
  1    1118227 143508187056 07-JAN-19 143508188348 07-JAN-19
  1    1118228 143508188348 07-JAN-19 143508189562 07-JAN-19
  1    1118229 143508189562 07-JAN-19 143508190751 07-JAN-19
  1    1118230 143508190751 07-JAN-19 143508192112 07-JAN-19
  1    1118231 143508192112 07-JAN-19 143508193435 07-JAN-19

  Backup Set Copy #1 of backup set 425779414
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:19     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779414 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779485 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139888_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779414
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:19     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779414 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779486 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139888_p1_c2_20190108.log

BS Key  Size
------- ----------
425779389 1.75G

  List of Archived Logs in backup set 425779389
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118232 143508193435 07-JAN-19 143508194762 07-JAN-19
  1    1118233 143508194762 07-JAN-19 143508202627 07-JAN-19
  1    1118234 143508202627 07-JAN-19 143508431038 07-JAN-19
  1    1118235 143508431038 07-JAN-19 143508642533 07-JAN-19
  1    1118236 143508642533 07-JAN-19 143508644614 07-JAN-19
  1    1118237 143508644614 07-JAN-19 143508647357 07-JAN-19
  1    1118238 143508647357 07-JAN-19 143508710559 07-JAN-19
  1    1118239 143508710559 07-JAN-19 143508757694 07-JAN-19

  Backup Set Copy #1 of backup set 425779389
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:58     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779389 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779435 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139863_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779389
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:58     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779389 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779436 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139863_p1_c2_20190108.log

BS Key  Size
------- ----------
425779407 524.10M

  List of Archived Logs in backup set 425779407
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118240 143508757694 07-JAN-19 143508830299 07-JAN-19
  1    1118241 143508830299 07-JAN-19 143508833439 07-JAN-19
  1    1118242 143508833439 07-JAN-19 143508834048 07-JAN-19
  1    1118243 143508834048 07-JAN-19 143508835368 07-JAN-19
  1    1118244 143508835368 07-JAN-19 143508836422 07-JAN-19
  1    1118245 143508836422 07-JAN-19 143508837759 07-JAN-19
  1    1118246 143508837759 07-JAN-19 143508839026 07-JAN-19
  1    1118247 143508839026 07-JAN-19 143508840416 07-JAN-19
  1    1118248 143508840416 07-JAN-19 143508841600 07-JAN-19
  1    1118249 143508841600 07-JAN-19 143508841790 07-JAN-19
  1    1118250 143508841790 07-JAN-19 143508842086 07-JAN-19
  1    1118251 143508842086 07-JAN-19 143508842462 07-JAN-19
  1    1118252 143508842462 07-JAN-19 143508842719 07-JAN-19
  1    1118253 143508842719 07-JAN-19 143508843056 07-JAN-19
  1    1118254 143508843056 07-JAN-19 143508843358 07-JAN-19
  1    1118255 143508843358 07-JAN-19 143508843656 07-JAN-19
  1    1118256 143508843656 07-JAN-19 143508843959 07-JAN-19
  1    1118257 143508843959 07-JAN-19 143508844355 07-JAN-19
  1    1118258 143508844355 07-JAN-19 143508844660 07-JAN-19
  1    1118259 143508844660 07-JAN-19 143508845131 07-JAN-19

  Backup Set Copy #1 of backup set 425779407
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:19     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779407 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779471 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139881_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779407
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:19     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779407 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779472 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139881_p1_c2_20190108.log

BS Key  Size
------- ----------
425779394 1.72G

  List of Archived Logs in backup set 425779394
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118260 143508845131 07-JAN-19 143508845451 07-JAN-19
  1    1118261 143508845451 07-JAN-19 143508847993 07-JAN-19
  1    1118262 143508847993 07-JAN-19 143508911976 07-JAN-19
  1    1118263 143508911976 07-JAN-19 143508977218 07-JAN-19
  1    1118264 143508977218 07-JAN-19 143508980635 07-JAN-19
  1    1118265 143508980635 07-JAN-19 143508985204 07-JAN-19
  1    1118266 143508985204 07-JAN-19 143509062917 07-JAN-19
  1    1118267 143509062917 07-JAN-19 143509164621 07-JAN-19

  Backup Set Copy #1 of backup set 425779394
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:01     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779394 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779445 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139868_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779394
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:01:01     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779394 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779446 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139868_p1_c2_20190108.log

BS Key  Size
------- ----------
425779405 528.42M

  List of Archived Logs in backup set 425779405
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118268 143509164621 07-JAN-19 143509212805 07-JAN-19
  1    1118269 143509212805 07-JAN-19 143509217762 07-JAN-19
  1    1118270 143509217762 07-JAN-19 143509218438 07-JAN-19
  1    1118271 143509218438 07-JAN-19 143509219112 07-JAN-19
  1    1118272 143509219112 07-JAN-19 143509219234 07-JAN-19
  1    1118273 143509219234 07-JAN-19 143509219365 07-JAN-19
  1    1118274 143509219365 07-JAN-19 143509219456 07-JAN-19
  1    1118275 143509219456 07-JAN-19 143509219554 07-JAN-19
  1    1118276 143509219554 07-JAN-19 143509219685 07-JAN-19
  1    1118277 143509219685 07-JAN-19 143509219753 07-JAN-19
  1    1118278 143509219753 07-JAN-19 143509220026 07-JAN-19
  1    1118279 143509220026 07-JAN-19 143509220537 07-JAN-19
  1    1118280 143509220537 07-JAN-19 143509221014 07-JAN-19
  1    1118281 143509221014 07-JAN-19 143509221438 07-JAN-19
  1    1118282 143509221438 07-JAN-19 143509222025 07-JAN-19
  1    1118283 143509222025 07-JAN-19 143509222374 07-JAN-19
  1    1118284 143509222374 07-JAN-19 143509222918 07-JAN-19
  1    1118285 143509222918 07-JAN-19 143509223407 07-JAN-19
  1    1118286 143509223407 07-JAN-19 143509224041 07-JAN-19
  1    1118287 143509224041 07-JAN-19 143509224457 07-JAN-19

  Backup Set Copy #1 of backup set 425779405
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:21     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779405 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779467 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139879_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779405
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:21     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779405 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779468 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139879_p1_c2_20190108.log

BS Key  Size
------- ----------
425779399 1.71G

  List of Archived Logs in backup set 425779399
  Thrd Seq     Low SCN    Low Time  Next SCN   Next Time
  ---- ------- ---------- --------- ---------- ---------
  1    1118288 143509224457 07-JAN-19 143509224867 07-JAN-19
  1    1118289 143509224867 07-JAN-19 143509230326 07-JAN-19
  1    1118290 143509230326 07-JAN-19 143509324396 07-JAN-19

  Backup Set Copy #1 of backup set 425779399
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:49     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779399 Copy #1
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779455 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139873_p1_c1_20190108.log

  Backup Set Copy #2 of backup set 425779399
  Device Type Elapsed Time Completion Time Compressed Tag
  ----------- ------------ --------------- ---------- ---
  DISK        00:00:49     08-JAN-19       NO         BACKUP_LEVEL2_DAILY

    List of Backup Pieces for backup set 425779399 Copy #2
    BP Key  Pc# Status      Piece Name
    ------- --- ----------- ----------
    425779456 1   AVAILABLE   /oraclebackup/rman/ddd/ORCL/archive_08_s139873_p1_c2_20190108.log
recovery will be done up to SCN 143509230326
Media recovery start SCN is 143491310070
Recovery must be done beyond SCN 143493651494 to clear datafile fuzziness
Finished restore at 29-JAN-19


</pre>

 

