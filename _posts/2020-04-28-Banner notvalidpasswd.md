---
layout: post
title: Banner-@NOTVALIDPASSWD
modified: 2020-04-28
categories: [Banner]  
tags: 
  - Banner.Troubleshooting
comments: true
---
Yesterday, we encountered an error in Banner PPRD env.
Form REBCD21 failed due to the TNSNAMES used to connect to database was wrong.

<pre class="prettyprint lang-sql linenums=1 ">
:[jobpprd]:[PPRD]:/misc/jobpprd ... >>cat rebcd21_6188134.log
 
    Error Logging on to Oracle.. Aborting
 
    Error During GJBPRUN Delete.. Aborting
    SQLCODE=24324
    SQLERRMC=ORA-24324: service handle not initialized
                            
    Failure During Rollback.. FBA2
    SQLCODE=01012
    SQLERRMC=ORA-01012: not logged on                                              
 
Report Name..................REBCD21                       
Program Version..............8.37     
One Up Number ...............6188134                       
Aid Year ....................    
Resend Prev.Processed Recs... 
Resend for Prior Date .......           
Application ID ..............                              
Selection ID.................                              
Creator ID...................                              
User ID......................                              
Resend for Prior Date/Hour...  
Resend for Prior Date/Minute.  
Send with Discrepant Trans #.  
Processing Type..............     
Federal School Code..........      
 
Date and time stamp 
  for DATE SENT     
  DATE            =            
  Hour            =   
  Minute          =   
 
Inst.Change requests processed = 00000
Hous. Change requests processed = 00000
Corrections processed = 00000
Change/correction records written = 00000
 
ORA-12154: TNS:could not resolve the connect identifier specified
    
Username: ORA-12545: Connect failed because target host or object does not exist

Username: ORA-12545: Connect failed because target host or object does not exist
ERM-NOLOGIN: Unable to CONNECT to ORACLE after 3 attempts

:[jobpprd]:[PPRD]:/misc/jobpprd ... >>cat rebcd21_6188134.debug 
 START OF FORMFUSION 
 PRINTFF............. lp -c -dDATABASE
 END OF FORM FUSION DEBUG
Print option ..  enscript -B -r -fCourier8 -PDATABASE   
# ------- Current Jobsub Environment ---
 HOME............ /home/jobpprd
 USER............ jobpprd
 LANG............ en_US.UTF-8
 ORACLE_HOME..... /oracle_home
 ORACLE_SID...... PPRD
 ORAENV_ASK...... NO
 TWO_TASK........ 
 TNS_ADMIN....... /jobsubFolder/proxy_setup
 ORACLE_PATH..... .:/bannerFolder:/bannerFolder/pprd/odu/links:/home/jobpprd:/bannerFolder/pprd/charfmx:/bannerFolder/pprd/links:/bannerFolder/pprd/admin:/bannerFolder/pprd/admin/v6/oratools
 SQLPATH......... .:/bannerFolder:/bannerFolder/pprd/odu/links:/home/jobpprd:/bannerFolder/pprd/charfmx:/bannerFolder/pprd/links:/bannerFolder/pprd/admin:/bannerFolder/pprd/admin/v6/oratools
 BANNER_HOME..... /bannerFolder/pprd
 BANNER_LINKS.... /bannerFolder/pprd/links
 DATA_HOME....... /bannerFolder/pprd/dataload
 EXE_HOME........ /bannerFolder/pprd/general/exe
 LD_LIBRARY_PATH. /opt/FJSVcbl64/lib:/opt/FJSVXbsrt/lib:/opt/FJSVXmeft/lib:/oracle_home/lib32:/oracle_home/lib:/usr/local/microfocus/cobol51/lib:/bannerFolder/pprd/general/exe:/usr/local/lib:/usr/lib
 COBPREF......... perl /bannerFolder/pprd/links/banfjsv.pl
#------------- Current gjajobs.shl parameter values -------
 H............. /home/jobpprd
 JOB........... rebcd21.shl
 PROC.......... rebcd21
 PROG.......... REBCD21
 BANUID........ [Q1ZHANG]
 BUID........ Q1ZHANG
 PSWD.......... @NOTVALIDPASSWD
 UIPW.......... [Q1ZHANG]/NOTVALIDPASSWD
 TEMP.......... rebcd21_6188134
 LOG........... /home/jobpprd/rebcd21_6188134.log
 FORM..........  enscript -B -r -fCourier8 -PDATABASE 
 PRNT.......... DATABASE
 PRNTOPT.......  enscript -B -r -fCourier8 -PDATABASE   
 ONE_UP........ 6188134
 SUBTIME....... 
#----------------------------------------
NLS_LANG setting .. AMERICAN_AMERICA.AL32UTF8
LC_ALL setting .. 
#----------------------------------------
----- rebcd21_6188134.in -----
6188134
----- rebcd21_6188134.shl -----
$JOB $BANUID $PSWD $ONE_UP $PROG $PRNT $BUID
if [ -f $H/$TEMP.in ]
then
/bin/rm $H/$TEMP.in
fi
if [ -f $H/$TEMP.shl ]
then
/bin/rm $H/$TEMP.shl
fi
</pre>

<br>Normally, the password given here should be the TNSNAME - JOBSUB_PPRD, but here it is NOTVALIDPASSWD.
<br>This is the reason why the form cannot connect to the database and throw the error.

<br>
<br>I did research on Ellucian support, NOTVALIDPASSWD means the form will use the filesystem credential.
<br>When filesystem credential was on, a file with credential will be generated automatically with named one_up_num.creds 

<pre class="prettyprint lang-sql linenums=1 ">
:[jobpprd]:[PPRD]:/home/jobpprd ... >>cat 6188159.creds
[Q1ZHANG] JOBSUB_PPRD 6188159 REBCD21 DATABASE
</pre>

<br>In the rebcd21.shl, there is a "if" control to decide which method will be used.
<pre class="prettyprint lang-sql linenums=1 ">
#
if [ "$2" != "NOTVALIDPASSWD" ]
then
    CMDFILE=$HOME/$$.prm;export CMDFILE;
</pre>

<br>Unfortunately, The password given from the form is "@NOTVALIDPASSWD"
<br>There is an additional "@" at the head. So I added "@" in the script and the problem was solved.

<br>But I don't think it is the right way for this problem.
<br>Opened a ticket to Ellucian, and found the filesystem cred was set to on in the GJAJOBS.
<br>It can be disabled by using sqlplus 
<pre class="prettyprint lang-sql linenums=1 ">
update GJBJOBS set GJBJOBS_CREDS_IN_FILE=NULL where GJBJOBS_CREDS_IN_FILE='Y';
</pre>