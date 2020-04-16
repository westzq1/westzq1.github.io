---
layout: post
title: Ethos Installation(1)-Install API
modified: 2020-04-09
categories: [Banner]  
tags: 
  - Banner.Ethos
comments: true
---
<span style="color:#ff0000;"><strong>1. Structure</strong></span><br>
<img src="/images/20200409-1.png"/> 

<br>Ethos has two data flows:
<br>&nbsp;1. red line, the third party requests data through Ethos from an institution
<br>&nbsp;2. orange line, the Banner push the modifications to the subscribers.
<br>
<br>The following components need to be installed if you only use the first one
<br>&nbsp;1. Ethos API DB Upgrade
<br>&nbsp;2. Ethos API Management Center - emb1	
<br>&nbsp;3. Banner Student API - emb1	
<br>&nbsp;4. Banner Integration API - emb1	

<br>If you plan to use subscription, you need to install
<br>&nbsp;1. BEP - bep
<br>&nbsp;2. Ellucian Messaging Service - emb1	
<br>&nbsp;3. Ellucian Messaging Adapter - emb1	

<span style="color:#ff0000;"><strong>2. compatibility</strong></span>
<br>We should go to 
<br>https://ecommunities.ellucian.com/thread/60814 
<br>to check the compatibility matrix at first to plan our installation.


<span style="color:#ff0000;"><strong>3. Prepare</strong></span>
<br> The password of the following users
<br>&nbsp; 1. apiuser  -- Used by Ethos cloud to access the local banner database.
<br>&nbsp; 2. odsstg
<br>&nbsp; 3. banproxy
<br>
<br> The following user will be created for Ethos
<br>&nbsp; 1. cdcadmin
<br>&nbsp; 2. events
<br>&nbsp; 3. banguidgen
<br>
<br> The rabbitmq is required when you install BEP and Ellucian Messaging Service.
<br> I will give you a guide about the install and configure BEP in another article.


<span style="color:#ff0000;"><strong>4. Ethos API DB Upgrade</strong></span>
<br> Using ESM to install. No application componment
<br> From Ethos 9.9, the GUID generation which is very time consuming was splited from the DB install.

<span style="color:#ff0000;"><strong>5. Ethos API Management Center</strong></span>
<br> Using ESM to install.
<br> Configuration:
<br>
* encipher banproxy passowrd
<br> &nbsp; https://ellucian.force.com/clients/s/article/Ethos-API-Management-Center-login-denied
<pre class="prettyprint lang-sql linenums=1 ">
bash-4.1$ bash ./encrypt.sh input=&lt;banproxy password&gt; password=&lt;encipher password&gt; algorithm=PBEWithMD5AndDES


----ENVIRONMENT-----------------
Runtime: Sun Microsystems Inc. OpenJDK 64-Bit Server VM 23.41-b41 

----ARGUMENTS-------------------
algorithm: PBEWithMD5AndDES
input: &lt;banproxy password&gt;
password: &lt;encipher key&gt;

----OUTPUT----------------------
A93QdvdVv5pufChfukAEoor66iZ4FBJJQmJ/7KF+oKtHdQxNUHilZA==
</pre>
The output here is encrypted password strings.

* application.properties
<pre class="prettyprint lang-sh linenums=1 ">
# *******************************************************************************
#   Copyright 2018 Ellucian Company L.P. and its affiliates.
# *******************************************************************************

spring.datasource.url=jdbc:oracle:thin:@bannerdb.pprd2.odu.edu:2336:PPRD2
spring.datasource.password=ENC(A93QdvdVv5pufChfukAEoor66iZ4FBJJQmJ/7KF+oKtHdQxNUHilZA==)     <- banproxy password
app.oracle.useRadiusAuthentication=false
</pre>

* Tomcat - setenv.sh
<pre class="prettyprint lang-sh linenums=1 ">
export JAVA_OPTS="-server -Xms3g -Xmx5g -XX:MaxPermSize=1024m 
                  -Doracle.jdbc.autoCommitSpecCompliant=false 
                  -Dlog4j.configuration=config.properties 
                  -Djasypt.encryptor.password=&lt;encipher key&gt;"  <- The key which used to encipher
</pre>

* The users you want to use to log into Ethos Management Center, you have to grant BAN_EEAMC_C / BAN_GENERAL_C to them.

<span style="color:#ff0000;"><strong>6. Banner Student / Integration API </strong></span>
<br>Just use ESM to install
<br>
<br>Until now, we have done the installation of Ethos, only for data requesst.
<br>You can go to Ethos Cloud to configuration applications and use postman for testing.
<br>There is a example: https://ecommunities.ellucian.com/docs/DOC-11172