---
layout: post
title: Oracle - Procedure Recursively Calls Itself too Deep Leads hanging on Parsing
modified: 2019-01-17
categories: [Oracle]
tags: 
  - Oracle.Performance
comments: true
---
This week, our banner system was suffering high loading around 9pm.
Too many sessions were waiting on <span style="color:#ff0000;"><strong>cursor: pin S wait on X</strong></span>, both blocker session and blocked sessions are running the same SQL. After killing blocker, the database came back to normal.

In this situation, normally, the blocker is doing hard parsing, and hard parsing needs to hold the mutex exclusively on the related objects. For the blocked session, there are doing soft parsing, but still needs to get the related objects with shared locking.

I got the short stack of the blocker session 3 times, this session hung on the following call stack:
<pre class="prettyprint lang-sql linenums=1 ">
ksedsts()+244<-ksdxfstk()+58<-ksdxcb()+918<-sspuser()+224<-__sighandler()<-pfrgnc()+74<-qksceUnderSysPLSql()+95
  <-qkscePreFillCurEnv()+1325<-kksLoadChild()+4554<-kxsGetRuntimeLock()+2155<-kksfbc()+14431<-opiexe()+2536<-opipls()+2154
  <-opiodr()+1165<-rpidrus()+206<-skgmstack()+144<-rpiswu2()+739<-rpidrv()+1511<-psddr0()+478<-psdnal()+636<-pevm_EXECC()+304
  <-pfrinstr_EXECC()+79<-pfrrun_no_tool()+60<-pfrrun()+1155<-plsql_run()+708<-peicnt()+285<-kkxexe()+726<-opiexe()+19651
  <-kpoal8()+2848<-opiodr()+1165<-ttcpip()+2699<-opitsk()+1740<-opiino()+945<-opiodr()+1165<-opidrv()+587<-sou2o()+145
  <-opimai_real()+154<-ssthrdmain()+412<-main()+236<-__libc_start_main()+253
</pre>

And in the v$session(here is dba_hist_active_sess_history), the SQL_EXEC_ID and SEQ# of blocker session were unchanged, this means the session was hanging there. The event is null means the session is on CPU.
<pre class="prettyprint lang-sql linenums=1 "> SAMPLE_ID SAMPLE_TIME			SEQ#	EXEC_ID PROGRAM    EVENT	   SQL_ID	  TOP_LEVEL_SQL PLSQL_ID FLAG
---------- ------------------------- ------- ---------- ---------- --------------- -------------- ------------- -------- -----------------
 164832437 16-JAN-19 07.51.29.653 PM   16524   27509653 APEX Liste		   06th3aykfyckb  b7c37mhzaknv5    87175 NNNNYNNNNNNNNNNNN
 164832447 16-JAN-19 07.51.39.673 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832457 16-JAN-19 07.51.49.713 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832467 16-JAN-19 07.51.59.753 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832477 16-JAN-19 07.52.09.773 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832487 16-JAN-19 07.52.19.883 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832497 16-JAN-19 07.52.29.913 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832507 16-JAN-19 07.52.39.943 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832517 16-JAN-19 07.52.49.983 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832527 16-JAN-19 07.53.00.023 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832537 16-JAN-19 07.53.10.043 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832547 16-JAN-19 07.53.20.133 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832557 16-JAN-19 07.53.30.173 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832567 16-JAN-19 07.53.40.193 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832577 16-JAN-19 07.53.50.233 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832587 16-JAN-19 07.54.00.263 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832597 16-JAN-19 07.54.10.293 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832607 16-JAN-19 07.54.20.394 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832617 16-JAN-19 07.54.30.434 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832627 16-JAN-19 07.54.40.454 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832637 16-JAN-19 07.54.50.484 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832647 16-JAN-19 07.55.00.514 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832657 16-JAN-19 07.55.10.544 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
 164832667 16-JAN-19 07.55.20.634 PM   16524   16777235 APEX Liste		   b7c37mhzaknv5  b7c37mhzaknv5    87175 NNNNYYNNNNNNNNNNN
</pre>

I made processstat for the blocker session, and in the trace file, it shows that a lot of cursor invalidated because the rolling invalidate window exceeded. 
<pre class="prettyprint lang-sql linenums=1 ">         NamespaceDump:
          Parent Cursor:  sql_id=06th3aykfyckb parent=0x203fbf2268 maxchild=22 plk=y ppn=n
            CursorDiagnosticsNodes:
              ChildNode:  ChildNumber=11 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=11 ID=34 reason=Rolling Invalidate Window Exceeded(3) size=2x4 invalidation_window=1547689423 ksugctm=1547689428
              ChildNode:  ChildNumber=10 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=10 ID=34 reason=Rolling Invalidate Window Exceeded(3) size=2x4 invalidation_window=1547688568 ksugctm=1547688569
              ChildNode:  ChildNumber=0 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=2 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=2 ID=34 reason=Rolling Invalidate Window Exceeded(3) size=2x4 invalidation_window=1547686558 ksugctm=1547686560
              ChildNode:  ChildNumber=8 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=8 ID=34 reason=Rolling Invalidate Window Exceeded(3) size=2x4 invalidation_window=1547686414 ksugctm=1547686439
              ChildNode:  ChildNumber=9 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=9 ID=34 reason=Rolling Invalidate Window Exceeded(3) size=2x4 invalidation_window=1547684845 ksugctm=1547684850
              ChildNode:  ChildNumber=4 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=4 ID=34 reason=Rolling Invalidate Window Exceeded(3) size=2x4 invalidation_window=1547684253 ksugctm=1547684279
              ChildNode:  ChildNumber=14 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=14 ID=34 reason=Rolling Invalidate Window Exceeded(3) size=2x4 invalidation_window=1547682551 ksugctm=1547682568
              ChildNode:  ChildNumber=6 ID=34 reason=Rolling Invalidate Window Exceeded(2) size=0x0 details=already_processed
              ChildNode:  ChildNumber=6 ID=34 reason=Rolling Invalidate Window Exceeded(3) size=2x4 invalidation_window=1547681275 ksugctm=1547681278
</pre>

And I checked the last_analyzed of related table, they were analyzed just a few hours before. So, I locked to statistics on them. 
Unfortunately, after the peace of three days, the problem occured again.

I opened a SR on metalink, the Oracle gave me some suggestions that the call stack is related with bugs:
1. Bug 20264808 SPINNING ON KKSLOADCHILD / KXSGETRUNTIMELOCK CAUSING MUTEX AND TX WAITS
2. Bug 24291191 SPINNING ON PFRGNC 

Both are not public, we cannot see the details. The Oracle engineer copied some explain to me:

These two bugs have been closed that those bugs as not a bug with comments below 
"I think that the application has entered into such a recursion, so deep, that the loop in qksceUnderSysPLSql which calls pfrgnc to walk through the frames, is simply wasting time traversing through the deep level of pl/sql frames. 
kindly request the customer to fix the application recursion, in the first place, and then see if the issue still reproduces or not." 

I read the processstat again and found that there is a pl/sql stack section. In my case, the PEKFASG is calling itself on the same place recusively.
<pre class="prettyprint lang-sql linenums=1 "> ----- PL/SQL Stack -----
----- PL/SQL Call Stack -----
  object      line  object
  handle    number  name
0x204f940eb8       116  package body NLSUSER.G$_NLS
0x204f940eb8       127  package body NLSUSER.G$_NLS
0x204f940eb8       174  package body NLSUSER.G$_NLS
0x1f6fa7fec8      5836  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  package body BANINST1.PEKFASG
0x1f6fa7fec8      5839  packag
</pre>

After checking the source code, I found there is a procedure in the package calling itself if it goes to exception section, like following:
<pre class="prettyprint lang-sql linenums=1 "> 5894 PROCEDURE p_write_error(
…...
5926 EXCEPTION
5927 WHEN api_error THEN
…...
5934 WHEN OTHERS THEN
…...
5938 p_write_error(
5939 p_pidm    => p_pidm,
5940 p_term    => p_term,
5941 p_message => api_error_msg,
5942 p_user_id => p_user_id);
5943 END p_write_error;
</pre>

I could not find any logs on Tomcat about this, I submitted this problem to the vendor and waiting for response.