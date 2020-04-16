---
layout: post
title: Ethos Installation(3)-Install BEP and Messaging Service/Adapter
modified: 2020-04-11
categories: [Banner]  
tags: 
  - Banner.Ethos
comments: true
---
<span style="color:#ff0000;"><strong>1. BEP</strong></span>
<pre class="prettyprint linenums=1 ">
1. Set up Server TrustStore, copy from RabbitMQ we set up in the previous article.
# mkdir /l01/app/oracle/rabbitmqcertificates
# cp "emb1.pprd.odu.edu folder"/server/cert.pem -> /l01/app/oracle/rabbitmqcertificates
# cp "emb1.pprd.odu.edu folder"/client/keycert.p12 -> /l01/app/oracle/rabbitmqcertificates

Create a new truststore file rabbitstore
# keytool -import -alias rabbitmqserver -file /l01/app/oracle/rabbitmqcertificates/cert.pem -keystore rabbitstore

2. Install through ESM, and then, configure the application
# BannerEventPublisher_configuration.groovy
/*******************************************************************************
 *                                                                              *
 *              Banner Event Publisher DataSource Configuration                  *
 *                                                                              *
 *******************************************************************************/

dataSource_cdcadmin {
    //JNDI configuration for use in 'production' environment
	jndiName = "java:comp/env/jdbc/cdcadmin"
    transactional = false
}

dataSource_events {
    //JNDI configuration for use in 'production' environment
	jndiName = "java:comp/env/jdbc/events"
	transactional = false
}

dataSource_bannerSsbDataSource {
    //JNDI configuration for use in 'production' environment
	jndiName = "java:comp/env/jdbc/bannerSsbDataSource"
	transactional = false
}

dataSource_bannerDataSource {
    //JNDI configuration for use in 'production' environment
        jndiName = "java:comp/env/jdbc/bannerDataSource"
        transactional = false
}


bep {
	//App Server
	//Possible values are either "TOMCAT" or "WEBLOGIC"
    app.server = "TOMCAT"
	
	//Message Broker
	//Possible values are "RABBITMQ" or "WEBLOGIC" or "RABBITMQ/WEBLOGIC"
	message.broker = "RABBITMQ"

	//Retry interval to publish to broker in SECONDS
    publish.retry.interval = 45
}

//RabbitMQ configuration
rabbitmq {
	host = "emb1.pprd.odu.edu"
	port = "5671"
	userName = "ellucian"
	password = "password"
        virtualHostName = "bep_events_host"
	exchangeName = "bep_events_topic"    
	enableSSL = "true"

	//Validate rabbit connections
        validate = true

	//Put an actual path to a file starting with "file:" otherwise leave the value as NO_FILE
	keyStoreFileName = "file:/l01/app/oracle/rabbitmqcertificates/keycert.p12" 		
	keyStorePassPhrase = "pass"
    
	//Put an actual path to a file starting with "file:" otherwise leave the value as NO_FILE
	trustStoreFileName = "file:/l01/app/oracle/rabbitmqcertificates/rabbitstore" 	    
	trustStorePassPhrase = "pass"
}

jms {
	validate = true
}

// This configuration needs to be done in milliseconds for the footer to appear in the screen
footerFadeAwayTime=2000

// Application Navigator opens embedded applications within an iframe. To protect against the clickjacking vulnerability,
// integrating applications will have to set the X-Frame options to protect the "login/auth" URI from loading in the iframe and
// set it to denied mode. This setting is needed if the application needs to work inside Application Navigator and
// the secured application pages will be accessible as part of the single-sign on solution.
grails.plugin.xframeoptions.urlPattern = '/login/auth'
grails.plugin.xframeoptions.deny = true


// End of configuration

3. Set up Tomcat
# server.xml, configure database connection

  &lt;GlobalNamingResources&gt;
    &lt;Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" /&gt;
     &lt;Resource name="jdbc/bannerDataSource" auth="Container" type="javax.sql.DataSource"
       driverClassName="oracle.jdbc.OracleDriver"
       url="jdbc:oracle:thin:@//bannerdb.pprd2.odu.edu:2336/PPRD2"
       username="banproxy" password="password"
        initialSize="5" maxTotal="600" maxIdle="-1" maxWaitMillis="30000"
        validationQuery="select 1 from dual"
        accessToUnderlyingConnectionAllowed = "true"
        removeAbandonedOnBorrow = "true"
        testOnBorrow="true"/&gt;
    &lt;Resource name="jdbc/bannerSsbDataSource" auth="Container"
        type="javax.sql.DataSource"
        driverClassName="oracle.jdbc.OracleDriver"
        url="jdbc:oracle:thin:@//bannerdb.pprd2.odu.edu:2336/PPRD2"
        username="ban_ss_user" password="password"
        initialSize="5" maxTotal="600" maxIdle="-1" maxWaitMillis="30000"
        validationQuery="select 1 from dual" 
        accessToUnderlyingConnectionAllowed = "true"
        removeAbandonedOnBorrow = "true"
        testOnBorrow="true"/&gt;

    &lt;Resource name="jdbc/events" auth="Container"
        type="javax.sql.DataSource"
        driverClassName="oracle.jdbc.OracleDriver"
        url="jdbc:oracle:thin:@//database.domain.edu:2336/PPRD2"
        username="events" password="password"
        initialSize="5" maxTotal="600" maxIdle="-1" maxWaitMillis="30000"
        validationQuery="select 1 from dual" 
        accessToUnderlyingConnectionAllowed = "true"
        removeAbandonedOnBorrow = "true"
        testOnBorrow="true"/&gt;

    &lt;Resource name="jdbc/cdcadmin" auth="Container"
        type="javax.sql.DataSource"
        driverClassName="oracle.jdbc.OracleDriver"
        url="jdbc:oracle:thin:@//database.domain.edu:2336/PPRD2"
        username="cdcadmin" password="password"
        initialSize="5" maxTotal="600" maxIdle="-1" maxWaitMillis="30000"
        validationQuery="select 1 from dual" 
        accessToUnderlyingConnectionAllowed = "true"
        removeAbandonedOnBorrow = "true"
        testOnBorrow="true"/&gt;
  &lt;/GlobalNamingResources&gt;

# context.xml, configure datasource
     &lt;ResourceLink global="jdbc/bannerDataSource" name="jdbc/bannerDataSource" type="javax.sql.DataSource"/&gt;
     &lt;ResourceLink global="jdbc/bannerSsbDataSource" name="jdbc/bannerSsbDataSource" type="javax.sql.DataSource"/&gt;
     &lt;ResourceLink global="jdbc/events" name="jdbc/events" type="javax.sql.DataSource"/&gt;
     &lt;ResourceLink global="jdbc/cdcadmin" name="jdbc/cdcadmin" type="javax.sql.DataSource"/&gt; 

# The war file which deployed in Tomcat should be named as BannerEventPublisher.war

4. The user who needs to log into BEP should be granted:
Grant object BEP_ADMIN_OBJECT / BAN_DEFAULT_M to the user who needs access through INB/GSASECR, then 
https://ellucian.force.com/clients/s/article/Logging-into-BEP-gives-you-You-are-not-Authorized-to-view-this-page-error

SQL&gt; GRANT EXECUTE ON GOKFGAC TO &lt;BEP_USER&gt;;
SQL&gt; GRANT EXECUTE ON GB_COMMON TO &lt;BEP_USER&gt;;
SQL&gt; GRANT SELECT ON TWGRMENU TO &lt;BEP_USER&gt;;
SQL&gt; GRANT SELECT ON TWGRWMRL TO &lt;BEP_USER&gt;;
SQL&gt; GRANT SELECT ON TWGRROLE TO &lt;BEP_USER&gt;;
</pre>

<span style="color:#ff0000;"><strong>2. Messaging Service</strong></span>
<pre class="prettyprint linenums=1 ">
Configure ESM with the configuration of rabbitmq I created before.
./ConfigureEMS ellucian oracle123 bep_events_host 
</pre>


<span style="color:#ff0000;"><strong>3. Messaging Adapter</strong></span>
<br>Before configure Messaging Adapter, we need:
<br>&nbsp;&nbsp;&nbsp;1. Create two applications in the Ethos cloud, one for student API and another for integration API. Add all resources into them. And get the API keys of them
<br>&nbsp;&nbsp;&nbsp;2. One user in the local banner database, Ethos Cloud will use it to access banner to fetch database. Make sure you grant necessary permission to it.
<pre class="prettyprint linenums=1 ">
$ ./ConfigureEMS.sh
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;emsConfig&gt;
    &lt;clientErpType&gt;Banner&lt;/clientErpType&gt; &lt;!-- Colleague or Banner --&gt;
    &lt;configId&gt;ETHOS-INTEGRATION&lt;/configId&gt; &lt;!-- Change this to match config name for Colleague (HUB is delivered default), set to ETHOS-INTEGRATION for Banner --&gt;
    &lt;amqpUsername&gt;ellucian&lt;/amqpUsername&gt; &lt;!-- EMS (RabbitMQ) username --&gt;
    &lt;amqpPassword&gt;oracle123&lt;/amqpPassword&gt; &lt;!-- EMS (RabbitMQ) password --&gt;

    &lt;colleagueApiConfig&gt;
        &lt;baseUrl&gt;https://server:port/ColleagueApi/&lt;/baseUrl&gt; &lt;!-- Colleague Web Api Url --&gt;
        &lt;username&gt;required&lt;/username&gt; &lt;!-- Colleague Web Api username --&gt;
        &lt;password&gt;required&lt;/password&gt; &lt;!-- Colleague Web Api password --&gt;
        &lt;hubApiKey&gt;required&lt;/hubApiKey&gt; &lt;!-- API key from  your Ethos Integration application --&gt;
    &lt;/colleagueApiConfig&gt;

    &lt;bannerStudentConfig&gt;
        &lt;baseUrl&gt;https://apiserver:7005/studentapi/api&lt;/baseUrl&gt; &lt;!-- Banner Student Api Url --&gt;
        &lt;username&gt;apiuser&lt;/username&gt; &lt;!-- Banner Student Api username --&gt;
        &lt;password&gt;password&lt;/password&gt; &lt;!-- Banner Student Api password --&gt;
        &lt;apiKeys&gt; &lt;!-- Banner MEP clients add new apiKey sections for each VPDI code --&gt;
            &lt;apiKey&gt;
                &lt;hubApiKey&gt;apikey&lt;/hubApiKey&gt; &lt;!-- API key from  your Ethos Integration application --&gt;
                &lt;vpdiCode&gt;&lt;/vpdiCode&gt; &lt;!-- Leave blank if MEP is not used --&gt;
            &lt;/apiKey&gt;
        &lt;/apiKeys&gt;
        &lt;mepSharedDataApiKey&gt;&lt;/mepSharedDataApiKey&gt; &lt;!-- API key to use when publishing shared data in a MEP environment --&gt;
    &lt;/bannerStudentConfig&gt;

    &lt;bannerIntegrationConfig&gt;
        &lt;baseUrl&gt;https://apiserver:7005/intgrationapi/api&lt;/baseUrl&gt; &lt;!-- Banner Integration Api Url --&gt;
        &lt;username&gt;apiuser&lt;/username&gt; &lt;!-- Banner Integration Api username --&gt;
        &lt;password&gt;passwd&lt;/password&gt; &lt;!-- Banner Integration Api password --&gt;
        &lt;apiKeys&gt; &lt;!-- Banner MEP clients add new apiKey sections for each VPDI code --&gt;
            &lt;apiKey&gt;
                &lt;hubApiKey&gt;apikey&lt;/hubApiKey&gt; &lt;!-- API key from  your Ethos Integration application --&gt;
                &lt;vpdiCode&gt;&lt;/vpdiCode&gt; &lt;!-- Leave blank if MEP is not used --&gt;
            &lt;/apiKey&gt;
        &lt;/apiKeys&gt;
        &lt;mepSharedDataApiKey&gt;&lt;/mepSharedDataApiKey&gt; &lt;!-- API key to use when publishing shared data in a MEP environment --&gt;
    &lt;/bannerIntegrationConfig&gt;

    &lt;!-- startup logging level...this will be overridden by the value returned from the api config endpoint --&gt;
    &lt;logLevel&gt;INFO&lt;/logLevel&gt;

    &lt;!-- Number of messages to pull from RabbitMQ for parallel processing api calls --&gt;
    &lt;amqpBatchSize&gt;80&lt;/amqpBatchSize&gt;

    &lt;!-- Parallel processing settings for making api calls --&gt;
    &lt;autoConfigurePool&gt;true&lt;/autoConfigurePool&gt; &lt;!-- Pool grows as messages come in (max # of threads is limited by batch size). 60 sec idle threads will be evicted --&gt;
    &lt;processingThreads&gt;4&lt;/processingThreads&gt; &lt;!-- Number of connection pool threads to use for parallel api calls.  This is not used if 'autoConfigurePool' it true --&gt;

    &lt;!-- Api timeout retry settings --&gt;
    &lt;apiTimeoutInterval&gt;300000&lt;/apiTimeoutInterval&gt; &lt;!-- value in milliseconds --&gt;
    &lt;maxRetryAttempts&gt;1&lt;/maxRetryAttempts&gt;

    &lt;!-- Number of messages to publish to ethos integration at once...max of 20 --&gt;
    &lt;ethosPublishBatchSize&gt;20&lt;/ethosPublishBatchSize&gt;

    &lt;!-- Banner database connection settings.  This is only necessary for a Banner MEP environment --&gt;
    &lt;bannerConnectString&gt;jdbc:oracle:thin:@database.domain.edu:2336:PPRD2&lt;/bannerConnectString&gt;
    &lt;jdbcDriver&gt;oracle.jdbc.pool.OracleDataSource&lt;/jdbcDriver&gt;

&lt;/emsConfig&gt;

# set up a key used to encipher communication in the Tomcat's setenv.sh
export EMA_CONFIG=oracle123

# The Adapter cannot be deployed to the same Tomcat with API
</pre>