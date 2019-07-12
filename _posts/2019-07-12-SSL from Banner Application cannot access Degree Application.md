---
layout: post
title: SSL from Banner Application cannot access Degree Application
modified: 2019-07-12
categories: [Banner]
tags: 
  - Banner.Troubleshooting
comments: true
---

Banner application has a module, get the data from DegreeWork application with SSL request.

In the past, It was not working because the domain name degree.xyz.edu cannot accept TLSv1.0 request, TLSv1.2 only, and Banner is only using v1.0 to request data. We set the firewall to allow TLSv1.0 request, the problem solved.

Last week, the problem happened again. <br/>
I used curl to test, we can get the page without any error.<br/>

Then, use openssl to test connection, TLSv1.0 is allowed without any restriction<br/>
<pre class="prettyprint lang-sql linenums=1 ">
# echo QUIT | openssl s_client -connect degree.odu.edu:8442 -tls1
CONNECTED(00000003)
depth=0 C = US, postalCode = 23529, ST = VA, L = NNN, street = xxx, O = xxx University, OU = ITS, CN = degree.xyz.edu
verify error:num=20:unable to get local issuer certificate
verify return:1
depth=0 C = US, postalCode = 23529, ST = VA, L = NNN, street = xxx, O = xxx University, OU = ITS, CN = degree.xyz.edu
verify error:num=21:unable to verify the first certificate
verify return:1
---
Certificate chain
 0 s:/C=US/postalCode=23529/ST=VA/L=NNN/street=xxx/O=xxx University/OU=ITS/CN=degree.xyz.edu
   i:/C=US/ST=MI/L=Ann Arbor/O=Internet2/OU=InCommon/CN=InCommon RSA Server CA
---
……
……
</pre>

But, there is an error <br/>
<span style="color:#ff0000;"><strong>   verify error:num=20:unable to get local issuer certificate</strong></span><br/>
<span style="color:#ff0000;"><strong>   verify error:num=21:unable to verify the first certificate</strong></span><br/>

Then, I am using tcpdump to capture the communication 
<pre class="prettyprint lang-sql linenums=1 ">
# nohup tcpdump -i eth0 -w /tmp/`hostname`.pcap -vnn '(src host 128.82.96.201 and dst host degree.xyz.edu)' or '(src host 128.82.96.201 and dst host 128.82.96.18)'  or '(src host 128.82.96.201 and dst host 128.82.96.19)' &
</pre>

<img src="{{site.baseurl}}/images/20190712-1.png" width="800"/>
<img src="{{site.baseurl}}/images/20190712-2.png" width="800"/>

We can see that there is an error in the handshake phase. It also shows that something wrong about the certificate.

I submitted a ticket to the network team, they found that this is the issue on the Firewall, during the SSL decryption process the firewall was not presenting the full chain. They uploaded the certificate as seplitted files for root / intermediate. It is not working. But if combine all certificates together in one file, the problem solved.





