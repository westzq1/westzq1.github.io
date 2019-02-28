---
layout: post
title: Oracle - Database Workload Replay
modified: 2019-02-22
categories: [Oracle]
tags: 
  - Oracle.Test
comments: true
---

Recently, I am working on the loading test of our new environment.<br/> 
Due to we dont have the team for the loading test specifically, I decided to use Database Replay for testing.<br/> 
Be aware that the Database Replay needs the additional license.

First, we need to capture the activities on the production database. This is very simple, make sure we have enough disk space to keep the trace file. <br/> 
The space needed to store them are vary, for our application, from 9am to 5pm, the system has 35003359 user calls and generates 11GB archived logs and 8.42 GB replay files.<br/> 
And also, turn capture on will increase around 5% CPU usage.

It is recommended to restart the database, and put the database into restrict mode before start capture to make sure all activities can be captured. <br/> 
But it is not mandatory, and it is really hard to achive. So, I ignored this suggestion.

I started capture at 8:58 am, and run it for the next 8 hours(28800 seconds)<br/> 
We can do filter on the capture side, but it is hard to decide which user/module you want to filter at the begin, it would be better to do it on the replay side.
<pre class="prettyprint lang-sql linenums=1 ">
create directory db_replay_capture_dir as '/mnt/old_air04/app/oracle/admin/AIR/db_replay_capture';
exec dbms_workload_capture.start_capture(name=>'pprd_capture_1', dir=>'DB_REPLAY_CAPTURE_DIR', duration=> 28800);
</pre>

After capture finished, generate replay report
<pre class="prettyprint lang-sql linenums=1 ">
set linesize 170 pagesize 999
set long 30000000 longchunksize 1000 serverout on
variable cap_id number;
exec :cap_id:= dbms_workload_capture.get_capture_info(dir=>'DB_REPLAY_CAPTURE_DIR');
variable cap_rpt clob;
exec :cap_rpt := dbms_workload_capture.report(capture_id=>:cap_id, format=> dbms_workload_capture.type_html);
select :cap_rpt from dual;
</pre>
This report is very similar with an ASH report plus some metadata of capture:<br/>
<img src="images/20190222.1.png" width="600"/>

Export AWR, it is needed when we run workload analyzer on the replay side. The dmp file is on the capture directory.
<pre class="prettyprint lang-sql linenums=1 ">
exec dbms_workload_capture.export_awr(capture_id=>1)
</pre>
Copy the folder to the replay side.

Next, we need to prepare our target database. Using rman to duplicate one on the another machine is very simple.<br/>
Making sure the FORCE_LOGGING is on 

1.Get the restore target SCN
<pre class="prettyprint lang-sql linenums=1 ">
SQL> select START_SCN from dba_workload_captures;

	   START_SCN
--------------------
	145166523720
</pre>
2.Restore(with catalog, without connection to source)
<pre class="prettyprint lang-sql linenums=1 ">
run {
allocate auxiliary channel dupl1  device type DISK;
allocate auxiliary channel dupl2  device type DISK;
set dbid 451988051;
set until scn 145166523720;
duplicate database to PPRD3 pfile =?/dbs/initPPRD3.ora noresume;
}
</pre>
3.Setting FRA
<pre class="prettyprint lang-sql linenums=1 ">
*.DB_FLASHBACK_RETENTION_TARGET=4320  # not necessary to turn flashback on
*.db_recovery_file_dest=/t03/app/oracle/PPRD3_FRA
*.db_recovery_file_dest_size=100G
*.log_archive_dest_1='LOCATION=USE_DB_RECOVERY_FILE_DEST mandatory'
</pre>
4.Restore target database into mount mode, create a guarantee restore point
<pre class="prettyprint lang-sql linenums=1 ">
RMAN> create restore point before_db_replay guarantee flashback database;
</pre>

If you are doing upgrade testing, apply patchset before creating a restore point.

Now, we have the replay target database, and activity trace file on the target box, before replay, we need to preprocess trace file
<pre class="prettyprint lang-sql linenums=1 ">
exec dbms_workload_replay.process_capture('DB_REPLAY_CAPTURE_DIR');
</pre>
It takes 2 hours for 8GB trace files.

And run the workload analyzer.
<pre class="prettyprint lang-sh linenums=1 ">
java -classpath $ORACLE_HOME/jdbc/lib/ojdbc6.jar:$ORACLE_HOME/rdbms/jlib/dbrparser.jar:$ORACLE_HOME/rdbms/jlib/dbranalyzer.jar \
                oracle.dbreplay.workload.checker.CaptureChecker \
                /t02/app/oracle/oradata/PPRD3/db_replay_capture \
                jdbc:oracle:thin:@zoro.xxx.xxx.edu:1521:pprd3
</pre>

The output is on the CAPTURE folder, it shows the potential impact of increasing the replay time and divergence. <br/>
In my case:
1. A significant part of our workload comes from PL/SQL, it is very hard to synchronize the commit inside if we are using synchronized mode to replay. We need to consider to turn off synchronization mode.
2. Many captured sessions had been connected when I turn the capture on, if the connection is not stateless, we may encounter many divergences
<img src="images/20190222.2.png" width="600" />

After preprocessing, we can start to run replay. It is recommended to do SPA before Database Replay, but this time, we are doing physical migration without database upgrade, so I skipped this step. 
<pre class="prettyprint lang-sql linenums=1 ">
drop directory db_replay_capture_dir;

create directory db_replay_capture_dir as '/t02/app/oracle/oradata/PPRD3/db_replay_capture';
exec dbms_workload_replay.initialize_replay(replay_name => 'air_capture_1', replay_dir=>'DB_REPLAY_CAPTURE_DIR')

-- Set timeout of execution. 
-- If the execution time is longer than 1 minute(min_delay), Oracle will check the execution time on the source side.
-- If it has been 5 times(delay_factor) longer, the query will be aborted. 
-- And if the query is longer than 60 minutes(max_delay), it will also be terminated.
exec dbms_workload_replay.set_replay_timeout(enabled=>true, min_delay=>1, max_delay=>60, delay_factor=>5);

-- create some filters to exclude unnecessary sessions.
exec dbms_workload_replay.add_filter(fname=>'NO_ONBRDMGR', fattribute => 'USER', fvalue=>'ONBRDMGR')
exec dbms_workload_replay.add_filter(fname=>'NO_DBSNMP', fattribute => 'USER', fvalue=>'DBSNMP')
exec dbms_workload_replay.add_filter(fname=>'NO_SYSTEM', fattribute => 'USER', fvalue=>'SYSTEM')
exec dbms_workload_replay.add_filter(fname=>'NO_SYS', fattribute => 'USER', fvalue=>'SYS')

-- in order to use these filters, we need to create a filter set. 
-- All filters created after the last filter set will be included in this new filter set automatically.
-- the filter/filter set will be created in the replay folder, not in database.
-- slow, 5 minutes roughly.
exec dbms_workload_replay.create_filter_set(replay_dir=>'DB_REPLAY_CAPTURE_DIR', filter_set=> 'FILTER', default_action=>'INCLUDE')

-- use the filter we created.
exec dbms_workload_replay.use_filter_set(filter_set=>'FILTER');

-- slow, 3 minutes
exec dbms_workload_replay.prepare_replay(synchronization=>FALSE ,CONNECT_TIME_SCALE=>100, THINK_TIME_SCALE=>100, scale_up_multiplier=>1, think_time_auto_correct=>true );
</pre>
Then, go to the OS, do a calibrate to know how many clients we need.
<pre class="prettyprint lang-sh linenums=1 ">
$ wrc mode=calibrate replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture

Workload Replay Client: Release 12.1.0.2.0 - Production on Fri Feb 22 11:41:48 2019

Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.


Report for Workload in: /t02/app/oracle/oradata/PPRD3/db_replay_capture
-----------------------

Recommendation:
Consider using at least 22 clients divided among 6 CPU(s)
You will need at least 127 MB of memory per client process.
If your machine(s) cannot match that number, consider using more clients.

Workload Characteristics:
- max concurrency: 748 sessions
- total number of sessions: 134805

Assumptions:
- 1 client process per 50 concurrent sessions
- 4 client processes per CPU
- 256 KB of memory cache per concurrent session
- think time scale = 100
- connect time scale = 100
- synchronization = TRUE
</pre>
In my case, we need 22 clients. It is recommended that have clients on the separate machine.
<pre class="prettyprint lang-sh linenums=1 ">
export ORACLE_SID=PPRD3
export ADR_BASE=/l01/app/oracle/replay_client
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc1.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc2.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc3.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc4.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc5.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc6.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc7.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc8.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc9.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc10.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc11.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc12.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc13.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc14.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc15.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc16.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc17.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc18.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc19.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc20.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc21.log &
nohup wrc system/xxxxxxx mode=replay dscn_off=true replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture > wrc22.log &
</pre>
Then, go back to database and we can start replay:
<pre class="prettyprint lang-sql linenums=1 ">
exec dbms_workload_replay.start_replay;
</pre>
The script which can be used to monitor the approximate process of the replay. <br/>
But it is not working with synchronization=of
<pre class="prettyprint lang-sql linenums=1 ">
set serveroutput on 

DECLARE 
my_next_ticker NUMBER; 
clock NUMBER; 
wait_for_scn NUMBER; 
counts NUMBER; 
replay_id NUMBER; 
thr_failure NUMBER; 
start_time DATE; 
num_tickers NUMBER; 
min_scn NUMBER; 
max_scn NUMBER; 
done NUMBER; 
total_time INTERVAL DAY TO SECOND; 

CURSOR get_next_ticker(my_next_ticker NUMBER) IS 
SELECT spid, event, inst_id, wrc_id, client_pid 
FROM gv$workload_replay_thread 
WHERE file_id = my_next_ticker; 

BEGIN 
dbms_output.put_line('********************************'); 
dbms_output.put_line('* Replay Status Report *'); 
dbms_output.put_line('********************************'); 

----------------------------------------- 
-- Make sure that a replay is in progress 
----------------------------------------- 
SELECT count(*) INTO counts 
FROM dba_workload_replays 
WHERE status='IN PROGRESS'; 

if (counts = 0) then 
dbms_output.put_line('No replay in progress!'); 
return; 
end if; 

------------------- 
-- Get replay state 
------------------- 
SELECT id,start_time INTO replay_id, start_time 
FROM dba_workload_replays 
WHERE status='IN PROGRESS'; 

SELECT count(*) INTO counts 
FROM gv$workload_replay_thread 
WHERE session_type = 'REPLAY'; 

SELECT min(wait_for_scn), max(next_ticker), max(clock) 
INTO wait_for_scn, my_next_ticker, clock 
FROM v$workload_replay_thread 
WHERE wait_for_scn <> 0 
AND session_type = 'REPLAY'; 

dbms_output.put_line('Replay has been running for: ' || 
to_char(systimestamp - start_time)); 
dbms_output.put_line('Current clock is: ' || clock); 
dbms_output.put_line('Replay is waiting on clock: ' || 
wait_for_scn); 
dbms_output.put_line(counts || ' threads are currently being 
replayed.'); 

---------------------------------------- 
-- Find info about the next clock ticker 
---------------------------------------- 
num_tickers := 0; 
for rec in get_next_ticker(my_next_ticker) loop 
-- We only want the next clock ticker 
num_tickers := num_tickers + 1; 
exit when num_tickers > 1; 

dbms_output.put_line('Next ticker is process ' || rec.spid || 
' (' || rec.wrc_id || ',' || rec.client_pid || 
') in instance ' || rec.inst_id || 
' and is waiting on '); 
dbms_output.put_line(' ' || rec.event); 

end loop; 

--------------------------------------------------------------------------------------- 
-- Compute the replay progression and estimate the time left 
-- Note: This is an estimated time only, not an absolute value as it is based on SCN.
--------------------------------------------------------------------------------------- 
SELECT min(post_commit_scn), max(post_commit_scn) 
INTO min_scn,max_scn 
FROM wrr$_replay_scn_order; 

done := (clock - min_scn) / (max_scn - min_scn); 
total_time := (systimestamp - start_time) / done; 

dbms_output.put_line('Estimated progression in replay: ' || 
to_char(100*done, '00') || '% done.'); 
dbms_output.put_line('Estimated time before completion: ' || 
((1 - done) * total_time)); 
dbms_output.put_line('Estimated total time for replay: ' || 
total_time); 
dbms_output.put_line('Estimated final time for replay: ' || 
to_char(start_time + total_time, 
'DD-MON-YY HH24:MI:SS')); 

END; 
/
</pre>

After replay:
<pre class="prettyprint lang-sql linenums=1 ">
set linesize 170 pagesize 999
set long 30000000 longchunksize 1000 serverout on
select dbms_workload_replay.report(replay_id => 53,format => 'HTML') from dual;
</pre>
<img src="images/20190222.3.png" width="600" />

And also, we need to export the AWR of this replay, it is needed to do compare with the other replays.
<pre class="prettyprint lang-sql linenums=1 ">
exec DBMS_WORKLOAD_REPLAY.EXPORT_AWR (replay_id => 1);
-- Import:
CREATE USER capture_awr;
SELECT DBMS_WORKLOAD_REPLAY.IMPORT_AWR (replay_id => 1, staging_schema => 'capture_awr') FROM DUAL;
</pre>

Before start database replay, I suggest to read the following articles:<br/>
<span style="color:#ff0000;"><strong>Troubleshooting Slow Application Testing Replay (Doc ID 2043847.1)	</strong></span>
1. Enough clients, run calibrate before start replay
2. If too many session on WCR: replay lock order, add parameter dscn_off=true to the  WRC client to ignore SCN dependencies during replay. 
3. If too many session on WCR: replay clock, try setting sync=false to speed up the replay. The reason is that some user action, such as calls to dbms_pipe, dbms_lock.sleep cannot synchronized during replay, and A significant part of the workload coming from PL/SQL and the PL/SQL blocks or functions have 'complicated' logic or multiple commits in them, they are hard to synchronize.
4. Checking replay divergence
5. JAVA_XA are not supported, it will be captured as normal PL/SQL workload. To avoid problems during workload replay, consider dropping the JAVA_XA package on the replay system to enable the replay to complete successfully.
6. If application uses a large number of sequences, increase _wcr_seq_cache_size to 131070. It will increase the consuming of PGA

<span style="color:#ff0000;"><strong>Database Capture and Replay: Common Errors and Reasons (Doc ID 463263.1)</strong></span>
1. The performance of file system which is used to store capture/replay files
2. The folder which used for capture must be empty, even no any hidden file.
3. The Capture directory can not be on an ASM disk.
4. In order to replay on RAC, the capture/replay folder has to be on the shared file system.
5. When do capture, CAPTURE_STS=TRUE is not supported on RAC
6. STATISTICS_LEVEL cannot be BASIC, it has to be TYPICAL at least.
7. In sqlnet.ora, DIAG_ADR_ENABLED should be set to ON or set $ADR_BASE for client.
8. The version we use to preprocess trace files should be the same as the version of database we are planning to play on.
9. Increase the nproc for the user who is running clients.

<span style="color:#ff0000;"><strong>Database Real Application Testing Diagnostic Information (Doc ID 1287620.1)</strong></span><br/>
1.Server-side tracing
<pre class="prettyprint lang-sql linenums=1 ">
alter system set _wcr_control=8;
alter system set _wcr_control=0;
</pre>
2.Client-side tracing
<pre class="prettyprint lang-sh linenums=1 ">
wrc user/passwd@server replaydir=\<capture-dir\> workdir=\<client-traces-dir\> debug=ON
</pre>

<span style="color:#ff0000;"><strong>Scripts to Debug Slow Replay (Doc ID 760402.1)	</strong></span>

If we want to re-execute the replay, we need to flashback database, in order to release the space on FRA, we need to recreate the restore point and delete all archived logs.
<pre class="prettyprint lang-sql linenums=1 ">
RMAN> flashback database to restore point before_db_replay;
RMAN> alter database open resetlogs;
RMAN> drop restore point before_db_replay;
-- all flb files will be removed automatically
RMAN> shutdown immediate;
RMAN> startup mount;
RMAN> delete archivelog all;
RMAN> create restore point before_db_replay guarantee flashback database;
RMAN> alter database open; 
</pre>

Miscellaneous:
1. Login Strom Test<br/>
    Set CONNECT_TIME_SCALE to 1 and all threads will try to connect to database at the same time.
2. Pressure Test<br/>
    Set THINK_TIME_SCALE to 1 and all threads will try to issue the sql statements ASAP
    Set SCALE_UP_MULTIPLIER to larger than 1, and the QUERY statements will be run multiple times. 
3. Loading Test<br/>
    Keep the CONNECT_TIME_SCALE, THINK_TIME_SCALE, and SCALE_UP_MULTIPLIER  to be the default value, check the exact replay time, it should be close to the capture time. 
4. Query-only mode <br/>
    Query-Only Mode Database Replay (Doc ID 1919760.1)	<br/>
    https://docs.oracle.com/database/121/RATUG/GUID-756772FF-1F7E-4EB5-AFB5-152CB3DF6704.htm#RATUG4204<br/>
    Read-only replay is a good and easy way to find regressions of the select statements.  The advantage of read-only replay over database replay is that it will execute queries with all bind sets. The captured workload will be executed concurrently as it was captured.

<pre class="prettyprint lang-sql linenums=1 ">
-- create a subdirectory under the replay folder.
create directory DB_REPLAY_CAPTURE_DIR_RO as '/t02/app/oracle/oradata/PPRD3/db_replay_capture/readonly';
-- create a subset
EXEC DBMS_WORKLOAD_REPLAY.GENERATE_CAPTURE_SUBSET ('DB_REPLAY_CAPTURE_DIR','DB_REPLAY_CAPTURE_DIR_RO', 'READONLY', 0, TRUE, 36000, FALSE, 1);

EXEC DBMS_WORKLOAD_REPLAY.PROCESS_CAPTURE ('DB_REPLAY_CAPTURE_DIR_RO');

exec dbms_workload_replay.set_replay_directory('DB_REPLAY_CAPTURE_DIR') ;

exec dbms_workload_replay.begin_replay_schedule('readonly-query-1') ;
select dbms_workload_replay.add_capture(capture_dir_name => 'DB_REPLAY_CAPTURE_DIR_RO', start_delay_seconds => 0, stop_replay => 'N', query_only => 'Y') from dual ;     
exec dbms_workload_replay.end_replay_schedule ;

exec dbms_workload_replay.initialize_replay(replay_name => 'pprd_capture_4', replay_dir=>'DB_REPLAY_CAPTURE_DIR_RO')

exec dbms_workload_replay.prepare_replay(synchronization=>FALSE ,CONNECT_TIME_SCALE=>1, THINK_TIME_SCALE=>1 );

$ wrc mode=calibrate replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture/readonly
$ wrc system/xxxxxxx mode=replay replaydir=/t02/app/oracle/oradata/PPRD3/db_replay_capture/readonly

exec dbms_workload_replay.start_replay;
</pre>










































