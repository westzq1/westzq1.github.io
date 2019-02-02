---
layout: post
title: Tomcat - Disable HTTP method
modified: 2018-12-12
categories: [Tomcat]
tags: 
  - Tomcat.Security
comments: true
---
The way is very simple, if you want to disable HTTP methods for all applications on this Tomcat
Add the following at conf/web.xml

Our requirement is to disable DELETE and OPTIONS methods
<pre class="prettyprint lang-xml linenums=1 ">&lt;security-constraint&gt;
  &lt;web-resource-collection&gt;
    &lt;web-resource-name>restricted methods&lt;/web-resource-name&gt;
    &lt;url-pattern&gt;/*&lt;/url-pattern&gt;
    &lt;http-method&gt;DELETE&lt;/http-method&gt;
    &lt;http-method&gt;OPTIONS&lt;/http-method&gt;
  &lt;/web-resource-collection&gt;
  &lt;auth-constraint /&gt;
&lt;/security-constraint&gt;
</pre>

<span style="color:#ff0000;"><strong>How to verify it is working?</strong></span>
<pre class="prettyprint lang-sh linenums=1 ">curl -v -X OPTIONS https://xxxx.pprd.xxxx.edu:5678/homepage.htm</pre>

For OPTIONS:
<img class="alignnone size-full wp-image-188" src="/images/20181212.1.png" alt="Capture1" width="1186" height="568" />
The left side, port 5678, DELETE is disabled on this Tomcat, we received a message that:<br/>
<strong>          HTTP Status 403 – Forbidden</strong><br/>
The right side, port 8441, DELETE is not disabled, all allowed method will be listed there:<br/>
<strong>          Allow: GET, HEAD, POST, PUT, DELETE, OPTIONS</strong>

For DELETE:
<img class="alignnone size-full wp-image-187" src="/images/20181212.2.png" alt="Capture" width="1182" height="549" />
The left side, port 5678, DELETE is disabled on this Tomcat, we received a message that:<br/>
<strong>          Access to the requested resource has been denied</strong><br/>
The right side, port 8441, DELETE is not disabled<br/>
<strong>          The server understood the request but refuses to authorize it.</strong><br/>