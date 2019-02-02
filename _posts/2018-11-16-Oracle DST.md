---
layout: post
title: Oracle - DST
modified: 2018-11-16
categories: [Oracle]
tags: 
  - Oracle.Configure
comments: true
---

<strong>What is DST?</strong><br/>
DST is the abbreviation of Daylight Saving Time.

<strong>DST and Timezone in Oracle</strong><br/>
Nothing special for the DST/Time Zone, if we are using the number format of Timezone, for example, -5:00 for New York, and +8:00 for Beijing. The application decides which time zone for a particular city/location.<br/>
But we can also use a legible area/city name to use timezone, for example, we are using Europe/Moscow, which is the same as +4:00. This way is called NAMED Timezone.

<pre class="prettyprint lang-sql linenums=1 ">
SQL> with t1(tm) as(
  2    select '2013-11-08T10:11:31+02:00' from dual
  3  )
  4  select cast(to_timestamp_tz(tm, 'yyyy-mm-dd"T"hh24:mi:ss TZH:TZM')
  5           at time zone '+4:00' as date)   as this_way
  6       , cast(to_timestamp_tz(tm, 'yyyy-mm-dd"T"hh24:mi:ss TZH:TZM')
  7           at time zone 'Europe/Moscow' as date) as or_this_way
  8    from t1
  9  ;

THIS_WAY            OR_THIS_WAY
------------------- -------------------
2013-11-08 12:11:31 2013-11-08 12:11:31
</pre>

If we are using NAMED Timzone, we will face a problem that, the time zone of a city or the start/end date of DST of cities/areas may be changed.

For example, we can see this from the update of DSTv31
<ul>
	<li>Northern Cyprus has decided to resume EU rules starting 2017-10-29.</li>
	<li>Fiji ends DST 2018-01-14 instead of the 2018-01-21 previously predicted.</li>
	<li>Namibia will switch from +01 with DST to +02 all year on 2017-09-03 at 02:00.</li>
	<li>Sudan will switch from +03 to +02 on 2017-11-01. South Sudan is not switching.</li>
	<li>Tonga cancels DST and will not adjust its clocks on 2017-11-05.</li>
	<li>Turks & Caicos will switch from -04 all year to -05 with US DST on 2018-03-11 at 03:00.</li>
</ul>
So, we need to apply DST patch to coordinate these unpredictable changes.

<strong>DST Version</strong><br/>
Due to named timezone may be changed, to coordinate this change, Oracle provides DST patches for this situation.

To check the current version for RDBMS:
<pre class="prettyprint lang-sql linenums=1 ">
SQL> SELECT version FROM v$timezone_file;

   VERSION
----------
        31

SQL> SELECT PROPERTY_NAME, SUBSTR(property_value, 1, 30) value
  2  FROM DATABASE_PROPERTIES
  3  WHERE PROPERTY_NAME LIKE 'DST_%'
  4  ORDER BY PROPERTY_NAME;   

PROPERTY_NAME                  VALUE
------------------------------ ------------------------------
DST_PRIMARY_TT_VERSION         31
DST_SECONDARY_TT_VERSION       0
DST_UPGRADE_STATE              NONE
</pre>

To check the current DST version of JVM:
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Testing your Oracle JVM Time Zone Setup (Doc ID 416860.1)</p>

The more information about Patches list can be found:
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Updated DST Transitions and New Time Zones in Oracle RDBMS and OJVM Time Zone File Patches (Doc ID 412160.1)</p>

<strong>Do I need to apply DST patch?</strong><br/>
That depends on whether you are using NAMED Timezone.<br/>
In Oracle database, there are two places which are related with NAMED Timezone.<br/>
One is that tables or views with "* WITH TIME ZONE" data type.

For example:
<pre class="prettyprint lang-sql linenums=1 ">
SQL> create table ctais2.test1(id number, tdata TIMESTAMP WITH TIME ZONE);
SQL> insert into ctais2.test1 values(1,to_timestamp_tz('2013-11-08T10:11:31 Europe/Moscow', 'yyyy-mm-dd"T"hh24:mi:ss TZR TZD'));

SQL> select tdata, to_char(tdata,'TZR') from ctais2.test1
  2  ;

TDATA                                                        TO_CHAR(TDATA,'TZR')
------------------------------------------------------------ --------------------------------
08-NOV-13 10.11.31.000000 AM EUROPE/MOSCOW                   EUROPE/MOSCOW
</pre>

Another is just as the SQL I used in the "DST and Timezone" part, do some simple conversion by SQL without table access.

For the first situation, we can use script cre_tz_views.sql to figure out whether there is a table or view using NAMED Timezone.
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">SCRIPT: cre_tz_views.sql - Assess Time Zone usage in a Database (Doc ID 412971.1)</p>

For the second situation, we may find some clue from shared pool, but it is not 100% to determine whether NAMED Timezone is used in the application. The best way is to ask vendor.<br/>

If you are using NAMED Timezone, you should consider to upgrade if you want to get the correct time for these related timezone.<br/>
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Updated DST Transitions and New Time Zones in Oracle RDBMS and OJVM Time Zone File Patches (Doc ID 412160.1)</p>
<ul>
	<li>If you use only US named Timezones and no dates beyond 2020 than DST version 4 to 8 is enough.</li>
	<li>If you are on DSTv9 or higher and US named Timezones you will have correct results up to the year 2040</li>
	<li>Otherwise, after upgrading the database, from older edition to 11.2.0.x or 12.1.0.x, we need to upgrade RDBMS DST to the latest. The simplest way is to run script in 1585343.1 to archive it automatically.</li>
	<li>If the older Oracle RDBMS version(11.2.0.x ,11.1.0.x, 10.2.0.x. etc) is using a RDBMS DST version higher than DSTv18 you need to install the SAME RDBMS DST patch as the old (11.2.0.x ,11.1.0.x, 10.2.0.x. etc) DST version on the 12.1.0.1 side before the Oracle RDBMS version upgrade. Note the usage of " the same RDBMS DST version" not a higher/newer DST version.</li>
</ul>
<strong>Default DST version for each Database Edition</strong>
<ul>
	<li>8.1.7.4 and lower: has no timezone information</li>
	<li>9.0.1 up to 9.2.0.8: includes and uses RDBMS DSTv1, includes and uses OJVM DSTv1</li>
	<li>10.1 up to 10.2.0.2: includes and uses RDBMS DSTv2, includes and uses OJVM DSTv2</li>
	<li>10.2.0.3: includes and uses RDBMS DSTv3, includes and uses OJVM DSTv3</li>
	<li>10.2.0.4, 10.2.0.5 , 11.1.0.6, 11.1.0.7: includes and uses RDBMS DSTv4, includes and uses OJVM DSTv4</li>
	<li>11.2.0.1:
includes all RDBMS DST updates from DSTv1 up to DSTv11, the default RDBMS DST version used is DSTv11.
includes and uses OJVM DSTv4</li>
	<li>11.2.0.2:
includes all RDBMS DST updates from DSTv1 up to DSTv14, the default RDBMS DST version used is DSTv14.
includes and uses OJVM DSTv14</li>
	<li>11.2.0.3:
includes all RDBMS DST updates from DSTv1 up to DSTv14, the default RDBMS DST version used is DSTv14.
includes and uses OJVM DSTv16</li>
	<li>11.2.0.4:
includes all RDBMS DST updates from DSTv1 up to DSTv14, the default RDBMS DST version used is DSTv14.
includes and uses OJVM DSTv20</li>
	<li>12.1.0.1:
includes all RDBMS DST updates from DSTv1 up to DSTv18, the default RDBMS DST version used is DSTv18.
includes and uses OJVM DSTv19</li>
	<li>12.1.0.2:
includes all RDBMS DST updates from DSTv1 up to DSTv18, the default RDBMS DST version used is DSTv18.
includes and uses OJVM DSTv22</li>
	<li>12.2.0.1:
includes all RDBMS DST updates from DSTv1 up to DSTv26, the default RDBMS DST version used is DSTv26.
includes and uses OJVM DSTv26</li>
</ul>
<strong>How to upgrade DST?</strong><br/>
Two DST patches, one for RDBMS, one for OJVM.<br/>
If you are using DST in JAVA procedure, you need OJVM DST patch, otherwise, you can ignore it and only apply RDBMS DST patch.
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Updated DST Transitions and New Time Zones in Oracle RDBMS and OJVM Time Zone File Patches (Doc ID 412160.1)</p>
<ul>
	<li>Pre-requirist checklist</li>
	<li>DST patches list</li>
	<li>Related Document for OEM, ERP</li>
	<li>Updated information</li>
	<li>DST patches are cumulative.</li>
	<li>DST patches cannot be downgraded.</li>
</ul>
Before upgrade, we should to check the current DST version, <span style="color:#ff0000;"><strong>if it is higher than the default DST version of new Oracle Edition, the new Oracle Edition has been patched to the same. If lower, the DST version will not change after upgrade, we should do it manually.</strong></span>

After database upgrade, DST cannot be updated automatically in Database Dictionary. So:
If you are using DBUA to upgrade Data Dictionary, you can choose "Upgrades Timezone Data"
<ul>
	<li>Updating the RDBMS DST version in 11g Release 2 (11.2.0.1 and up) using DBMS_DST (Doc ID 977512.1)</li>
	<li>Updating the RDBMS DST version in 12c Release 1 (12.1.0.1 and up) using DBMS_DST (Doc ID 1509653.1)</li>
	<li>Scripts to automatically update the RDBMS DST (timezone) version in an 11gR2 or 12c database . (Doc ID 1585343.1)</li>
	<li>For the releases from 12.2.0.x, the timezone upgrade scripts are included in the target ORACLE_HOME under rdbms/admin directory. Refer to Database Globalization Support Guide in "Upgrading the Time Zone File and Timestamp with Time Zone Data" Section.</li>
</ul>
For example, After upgrade database O11204 from 11.2.0.4 to 12.1.0.2, We can see, timezone version is not updated to v18, it is still v14 now.

<pre class="prettyprint lang-sql linenums=1 ">SQL> SELECT version FROM v$timezone_file;

   VERSION
----------
        14

SQL> SELECT PROPERTY_NAME, SUBSTR(property_value, 1, 30) value
  2  FROM DATABASE_PROPERTIES
  3  WHERE PROPERTY_NAME LIKE 'DST_%'
  4  ORDER BY PROPERTY_NAME;

PROPERTY_NAME
--------------------------------------------------------------------------------
VALUE
--------------------------------------------------------------------------------
DST_PRIMARY_TT_VERSION
14

DST_SECONDARY_TT_VERSION
0

DST_UPGRADE_STATE
NONE
</pre>
So, if we try to get a time on "Pacific/Chuuk" which is introduced in v15, an error will be report:

<pre class="prettyprint lang-sql linenums=1 ">
SQL> select cast(to_timestamp_tz('2013-11-08T10:11:31+02:00', 'yyyy-mm-dd"T"hh24:mi:ss TZH:TZM')
  2           at time zone 'Pacific/Chuuk' as date) as t
  3    from dual;
select cast(to_timestamp_tz('2013-11-08T10:11:31+02:00', 'yyyy-mm-dd"T"hh24:mi:ss TZH:TZM')
*
ERROR at line 1:
ORA-01882: timezone region not found

</pre>
if we try to get a time after 2011 on Egypt, whose DST policy changed in v16, we can get the different time with in 18c, whose DST version is v31
<pre class="prettyprint lang-sql linenums=1 ">
-- after update to 12.1.0.2
SQL> select cast(to_timestamp_tz('2013-05-08T10:11:31+02:00', 'yyyy-mm-dd"T"hh24:mi:ss TZH:TZM')
  2           at time zone 'Egypt' as date) as t
  3    from dual;

T
-------------------
2013-05-08 11:11:31

-- in 18c
SQL> select cast(to_timestamp_tz('2013-05-08T10:11:31+02:00', 'yyyy-mm-dd"T"hh24:mi:ss TZH:TZM')
  2           at time zone 'Egypt' as date) as t
  3    from dual;

T
-------------------
2013-05-08 10:11:31
</pre>
In this situation, after upgrade to a 12.1.0.2 and we also expect to get right time, we should update RDBMS / OJVM DST version to the default version of 12.1.0.2 (v18). This process<span style="color:#ff0000;"><strong> cannot be rolling</strong></span>, downtime is needed.

I am using the script
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Scripts to automatically update the RDBMS DST (timezone) version in an 11gR2 or 12c database . (Doc ID 1585343.1)</p>
If you cannot upgrade database to latest edition, but we want to update DST information, we can apply DST patches.There is no downtime for just patching ORACLE_HOME, but for upgrading DST version, it cannot be rolling update, downtime is needed.

The version and patch ID you can find here
<p dir="ltr" style="margin-left: 20px; margin-right: 0px">Updated DST Transitions and New Time Zones in Oracle RDBMS and OJVM Time Zone File Patches (Doc ID 412160.1)</p>
<ul>
	<li>Version 1 N/A N/A</li>
	<li>Version 2 patch 3171059 N/A</li>
	<li>Version 3 patch 4689959 N/A</li>
	<li>Version 4 - tzdata2006g update - patch 5632264 patch 5865568</li>
	<li>Version 5 patch 5880630 N/A</li>
	<li>Version 6 - tzdata2007f update - patch 6113507 patch 6113568</li>
	<li>Version 7 - tzdata2007k update - patch 6672979 patch 6708377</li>
	<li>Version 8 N/A N/A</li>
	<li>Version 9 - tzdata2008f update - patch 7181943 patch 7449057</li>
	<li>Version 10 - tzdata2008i update - patch 7580744 patch 7708501</li>
	<li>Version 11 - tzdata2009g update - patch 8524113 * patch 8603182</li>
	<li>Version 12 N/A N/A</li>
	<li>Version 13 - tzdata2009s update - patch 9146268 * patch 9197935</li>
	<li>Version 14 - tzdata2010j update - patch 9742718 * patch 9751299</li>
	<li>Version 15 - tzdata2010o update - patch 10272702 * patch 10406470</li>
	<li>Version 16 - tzdata2011g update - patch 12320006 * patch 12565410</li>
	<li>Version 17 - tzdata2011j update - patch 12949905 * patch 12949919 ( tzdata2011i for OJVM )</li>
	<li>Version 18 - tzdata2012c update - patch 13417321 * patch 14112098</li>
	<li>Version 19 - tzdata2012j update - patch 15897859 * patch 15897884</li>
	<li>Version 20 - tzdata2013c update - patch 16799735 * patch 16799766</li>
	<li>Version 21 - tzdata2013e update - patch 17432124 * patch 17443054</li>
	<li>Version 22 - tzdata2014c update - patch 18759211 * patch 18759260</li>
	<li>Version 23 - tzdata2014f update - patch 19396455 * patch 19397646</li>
	<li>Version 24 - tzdata2015d update - patch 20875898 * patch 20875943</li>
	<li>Version 25 - tzdata2015g update - patch 22037014 * patch 22037067</li>
	<li>Version 26 - tzdata2016d update - patch 22873635 * patch 22873666</li>
	<li>Version 27 - tzdata2016f update - patch 23614158 * patch 23614160</li>
	<li>Version 28 - tzdata2016g update - patch 24701840 * patch 24701882</li>
	<li>Version 29 - tzdata2016j update - patch 25173124 * patch 25173172</li>
	<li>Version 30 - tzdata2017b update - patch 25881255 * patch 25881271</li>
	<li>Version 31 - tzdata2017c update - patch 27015449 * patch 27015468</li>
</ul>