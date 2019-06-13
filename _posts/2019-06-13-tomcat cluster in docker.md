---
layout: post
title: Tomcat Cluster Session Replication in Docker
modified: 2019-06-13
categories: [docker]
tags: 
  - testing
comments: true
---
I am testing how to move our applications to docker. 
In order to take advantage of k8s, we want to have session replication.

First, I am testing with Tomcat Cluster:

<span style="color:#ff0000;"><strong>Dockerfile</strong></span>
<pre class="prettyprint lang-sql linenums=1 ">
# cat Dockerfile 
FROM tomcat:7.0.94-jre7-alpine
MAINTAINER q1zhang@odu.edu

ADD index.jsp /usr/local/tomcat/webapps/ROOT
ADD web.xml /usr/local/tomcat/conf
ADD server.xml /usr/local/tomcat/conf
ADD content.xml /usr/local/tomcat/conf
ADD rootweb.xml /usr/local/tomcat/webapps/ROOT/WEB-INF/web.xml
ADD pre.sh /usr/local/tomcat

expose 8080
WORKDIR /usr/local/tomcat

ENTRYPOINT bash ./pre.sh && catalina.sh run
</pre>

<span style="color:#ff0000;"><strong>index.jsp</strong></span>, used for testing, to display current hostname and session id.
<pre class="prettyprint lang-sql linenums=1 ">
# cat index.jsp 
&lt;%@ page language="java" %&gt;
&lt;html&gt;
  &lt;head&gt;&lt;title&gt;Test&lt;/title&gt;&lt;/head&gt;
  &lt;body&gt;
    &lt;h1&gt;&lt;font color="red"&gt;#HOSTNAME#&lt;/font&gt;&lt;/h1&gt;
      &lt;table align="centre" border="1"&gt;
      &lt;tr&gt;
        &lt;td&gt;Session ID&lt;/td&gt;
        &lt;% session.setAttribute("magedu.com","magedu.com"); %&gt;
        &lt;td&gt;&lt;%= session.getId() %&gt;&lt;/td&gt;
      &lt;/tr&gt;
      &lt;tr&gt;
        &lt;td&gt;Created on&lt;/td&gt;
        &lt;td&gt;&lt;%= session.getCreationTime() %&gt;&lt;/td&gt;
      &lt;/tr&gt;
    &lt;/table&gt;
  &lt;/body&gt;
&lt;/html&gt;
</pre>

<span style="color:#ff0000;"><strong>pre.sh</strong></span>, used to change some configuration files before start tomcat.
<pre class="prettyprint lang-sql linenums=1 ">
# cat pre.sh 
sed -i "s/#HOSTNAME#/`hostname`/g" /usr/local/tomcat/webapps/ROOT/index.jsp
sed -i "s/#HOSTNAME#/localhost/g" /usr/local/tomcat/conf/server.xml
sed -i "s/#IPADDRESS#/`ifconfig eth0|grep "inet addr"|awk '{print $2}'|sed 's/addr://g'`/g" /usr/local/tomcat/conf/server.xml
</pre>

<span style="color:#ff0000;"><strong>server.xml</strong></span>, copy from the image, /usr/local/tomcat/conf, and add the configuration for the cluster.
<pre class="prettyprint lang-sql linenums=1 ">
      &lt;Host name="#HOSTNAME#"  appBase="webapps"
            unpackWARs="true" autoDeploy="true"&gt;

        &lt;!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html --&gt;
        &lt;!--
        &lt;Valve className="org.apache.catalina.authenticator.SingleSignOn" /&gt;
        --&gt;

        &lt;!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" --&gt;
        &lt;Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="#HOSTNAME#_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" /&gt;
        &lt;Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
                channelSendOptions="8"&gt;

        &lt;Manager className="org.apache.catalina.ha.session.DeltaManager"
                expireSessionsOnShutdown="false"
                notifyListenersOnReplication="true"/&gt;

        &lt;Channel className="org.apache.catalina.tribes.group.GroupChannel"&gt;
        &lt;Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/&gt;
        &lt;Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                        address="#IPADDRESS#" 
                        port="4000"
                        autoBind="100"
                        selectorTimeout="5000"
                        maxThreads="6"/&gt;

        &lt;Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter"&gt;
        &lt;Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/&gt;
        &lt;/Sender&gt;
        &lt;Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/&gt;
        &lt;Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/&gt;
        &lt;/Channel&gt;
        &lt;Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
                filter=""/&gt;
        &lt;Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/&gt;

        &lt;Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                tempDir="/tmp/war-temp/"
                deployDir="/tmp/war-deploy/"
                watchDir="/tmp/war-listen/"
                watchEnabled="false"/&gt;

        &lt;ClusterListener className="org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener"/&gt;
        &lt;ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/&gt;
        &lt;/Cluster&gt;
      &lt;/Host&gt;
</pre>

<span style="color:#ff0000;"><strong>web.xml</strong></span>, copy from the image, /usr/local/tomcat/conf, and add &lt;distributable/&gt;
<pre class="prettyprint lang-sql linenums=1 ">
web.xml
    &lt;welcome-file-list&gt;
        &lt;welcome-file&gt;index.html&lt;/welcome-file&gt;
        &lt;welcome-file&gt;index.htm&lt;/welcome-file&gt;
        &lt;welcome-file&gt;index.jsp&lt;/welcome-file&gt;
    &lt;/welcome-file-list&gt;
    &lt;distributable/&gt;
&lt;/web-app&gt;

<span style="color:#ff0000;"><strong>rootweb.xml</strong></span>, copy from the image, /usr/local/tomcat/webapps/ROOT/WEB-INF/web.xml, and add &lt;distributable/&gt;
rootweb.xml
  &lt;display-name&gt;Welcome to Tomcat&lt;/display-name&gt;
  &lt;description&gt;
     Welcome to Tomcat
  &lt;/description&gt;
  &lt;distributable/&gt; 
&lt;/web-app&gt;
</pre>

<span style="color:#ff0000;"><strong>content.xml</strong></span>, copy from the image, /usr/local/tomcat/conf, and set distributable to true.
<pre class="prettyprint lang-sql linenums=1 ">
&lt;Context distributable="true"&gt;
</pre>

Create the image and run 2 instances.
<pre class="prettyprint lang-sql linenums=1 ">
# docker build -t myapp:v2 .
# docker run --name myapp1 -it --rm -p 8888:8080 myapp:v2 &
# docker run --name myapp2 -it --rm -p 8889:8080 myapp:v2 &
</pre>

In the nginx:
<pre class="prettyprint lang-sql linenums=1 ">
# cat /etc/nginx/conf.d/proxy.conf
upstream web{
  server 83.16.16.73:8888 weight=1;
  server 83.16.16.73:8889 weight=1;
}

server {
  listen 80 default_server;
  index index.jsp index.html;
  location / {
    proxy_pass http://web;
  }
}
</pre>

First access:<br/>
<img src="{{site.baseurl}}/images/20190613.1.png" width="400"/>

Refresh, then, you can see that session is on the another node, with the same session id<br/>
<img src="{{site.baseurl}}/images/20190613.2.png" width="400"/>
