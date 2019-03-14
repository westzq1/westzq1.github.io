---
layout: post
title: Oracle - Function in With Clause in Oracle 12C
modified: 2018-11-01
categories: [Oracle]
tags: 
  - Oracle.SQL
comments: true
---
In Oracle database 12C, we can define a temporary function in the With Clause directly.

It is really a very convenient feature, especially for the situation I worked with my clients' database.

I need to check suspended processes by searching the process data two times with 5 seconds interval, like following:

<pre class="prettyprint lang-sql linenums=1 ">
DECLARE
    ln_flag   NUMBER;
BEGIN
    SELECT COUNT (*) INTO ln_flag FROM dba_objects WHERE object_name = 'F_ZHANGQIAOC_SLEEP';
    IF ln_flag = 0
    THEN
        EXECUTE IMMEDIATE 'CREATE FUNCTION F_ZHANGQIAOC_SLEEP(AN_SEC NUMBER) RETURN NUMBER AS BEGIN dbms_lock.sleep(AN_SEC); RETURN AN_SEC; END;';
    END IF;
END;
/

WITH
a AS (SELECT /*+MATERIALIZE*/inst_id,SID,serial#,event,seq# FROM gv$session WHERE username IS NOT NULL AND status='ACTIVE' and state='WAITING'),
b AS (SELECT /*+MATERIALIZE*/F_ZHANGQIAOC_SLEEP(5) s1 FROM dual),
c AS (SELECT /*+MATERIALIZE*/inst_id,SID,serial#,event,seq# FROM gv$session WHERE username IS NOT NULL AND status='ACTIVE' and state='WAITING')
SELECT a.inst_id, a.sid, a.serial#, a.event, a.seq#
  FROM A, B, C
 WHERE     a.inst_id = c.inst_id
       AND a.sid = c.sid
       AND a.serial# = c.serial#
       AND a.event = c.event
       AND a.seq# = c.seq#
/
</pre>


Everytime, I have to ask the client whether I can create F_ZHANGQIAOC_SLEEP

With this new feature, I can rewrite the code:

<pre class="prettyprint lang-sql linenums=1 ">
WITH
    Function sleep (n in number) return number
    is
    begin
        dbms_lock.sleep(n);
        return n;
    end sleep;
    a AS (SELECT /*+MATERIALIZE*/inst_id,SID,serial#,event,seq# FROM gv$session
          WHERE username IS NOT NULL AND status='ACTIVE' and state='WAITING'),
    b AS (SELECT /*+MATERIALIZE*/sleep(5) s1 FROM dual),
    c AS (SELECT /*+MATERIALIZE*/inst_id,SID,serial#,event,seq# FROM gv$session
          WHERE username IS NOT NULL AND status='ACTIVE' and state='WAITING')
SELECT a.inst_id,a.sid,a.serial#,a.event,a.seq#
  FROM A, B, C
 WHERE a.inst_id = c.inst_id
   AND a.sid = c.sid
   AND a.serial# = c.serial#
   AND a.event = c.event
   AND a.seq# = c.seq#
/
</pre>

However, I do not think this feature would be very useful for business code. Because the SQL need to be compiled at first when we run it.

It is an exciting for management, I think.