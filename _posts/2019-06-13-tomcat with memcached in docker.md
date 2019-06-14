---
layout: post
title: Tomcat with Memcached for Session Replication in Docker
modified: 2019-06-13
categories: [docker]
tags: 
  - testing
comments: true
---

I tested using Tomcat Cluster for the session replication. <br/>
<a href="{{site.baseurl}}/docker/2019/06/13/tomcat-cluster-in-docker.html">Tomcat Cluster Session Replication in Docker</a>

Next, I want to use memcached to instead of Tomcat Cluter for the session replication.

First, create two memcached containers: my-memcache1 and my-memcache2
<pre class="prettyprint lang-sql linenums=1 ">
# docker run --name my-memcache1 --rm -d memcached memcached -m 64
# docker run --name my-memcache2 --rm -d memcached memcached -m 64
</pre>

Next, we should prepare the jar files which Tomcat needs to connect to Memcached.
<pre class="prettyprint lang-sql linenums=1 ">
http://repo1.maven.org/maven2/de/javakaffee/msm/
-- download the latest one
   memcached-session-manager-tc7-2.3.2.jar
   memcached-session-manager-tc8-2.3.2.jar
   spymemcached-2.11.1.jar
   memcached-session-manager-2.3.2.jar
   msm-flexjson-serializer-2.3.2.jar
   msm-kryo-serializer-2.3.2.jar
   msm-javolution-serializer-2.1.1.jar
   msm-serializer-benchmark-2.1.1.jar
   msm-xstream-serializer-2.3.2.jar

https://github.com/EsotericSoftware/kryo/releases        
-- Don't download RC edition.
   minlog-1.3.0.jar
   kryo-4.0.2.jar
   objenesis-2.6.jar
   reflectasm-1.11.6.jar

https://github.com/magro/kryo-serializers
-- download source code and compare to jar
   git clone https://github.com/magro/kryo-serializers
   mvn package
   cd target 
   mv kryo-serializers-0.46-SNAPSHOT.jar kryo-serializers-0.46.jar 
</pre>

<span style="color:#ff0000;"><strong>Dockerfile</strong></span>
<pre class="prettyprint lang-sql linenums=1 ">
FROM tomcat:7.0.94-jre7-alpine
MAINTAINER q1zhang@odu.edu

ADD index.jsp /usr/local/tomcat/webapps/ROOT
ADD web.xml /usr/local/tomcat/conf
ADD server.xml /usr/local/tomcat/conf
ADD content.xml /usr/local/tomcat/conf
ADD rootweb.xml /usr/local/tomcat/webapps/ROOT/WEB-INF/web.xml
ADD pre.sh /usr/local/tomcat
ADD *.jar /usr/local/tomcat/lib/
expose 8080
WORKDIR /usr/local/tomcat

ENTRYPOINT bash ./run.sh && catalina.sh run
</pre>

<span style="color:#ff0000;"><strong>server.xml</strong></span>, add the following in the Host
<pre class="prettyprint lang-sql linenums=1 ">
        &lt;Context path="/" docBase="ROOT" reloadalbe=""&gt;
                &lt;Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
                memcachedNodes="n1:my-memcache1:11211,n2:my-memcache2:11211"
                failoverNodes="n1"
                requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"
                transcoderFactoryClass="de.javakaffee.web.msm.serializer.kryo.KryoTranscoderFactory"
                /&gt;
        &lt;/Context&gt;
      &lt;/Host&gt;
</pre>

The other files are the same as the previous example.

Then, generate the image and start two instance.
<pre class="prettyprint lang-sql linenums=1 ">
# docker build -t myapp:v3 .
# docker run --name myapp1 --link=my-memcache1 --link=my-memcache2 -it --rm -p 8888:8080 myapp:v3
# docker run --name myapp2 --link=my-memcache1 --link=my-memcache2 -it --rm -p 8889:8080 myapp:v3
</pre>

Now, we can do test to verify.