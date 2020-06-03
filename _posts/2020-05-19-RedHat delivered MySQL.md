---
layout: post
title: MySQL - RedHat Delivered MySQL Deployment
modified: 2020-05-19
categories: [MySQL]  
tags: 
  - MySQL.Installation
comments: true
---
<br> For the RedHat delivered MySQL, the install is very simple, just use yum to install.
<br> But the position of configuration files are different.

<pre class="prettyprint lang-sh linenums=1 ">
my.cnf: /etc/opt/rh/rh-mysql80/my.cnf -> /etc/opt/rh/rh-mysql80/my.cnf.d/mysql-server.cnf
binary: /opt/rh/rh-mysql80/root/usr/bin
mysqld: mysql_secure_installation
Systemctl Script: /usr/lib/systemd/system/rh-mysql80-mysqld.service
</pre>

<br> We will use this database for wordpress. nearly 10 tables will be created for a website. So we have 30w tables.
<br> The MySQL parameter open_files_limit we set up to 12000. So we need to set "LimitNOFILE = 12000" in the rh-mysql80-mysqld.service

When I started service "rh-mysql80-mysqld.service", I encountered the error "'./binlog.index' not found (OS errno 13 - Permission denied)"
<pre class="prettyprint lang-sh linenums=1 ">
mysqld: File './binlog.index' not found (OS errno 13 - Permission denied)
2020-05-18T15:32:17.909138Z 0 [Warning] [MY-010091] [Server] Can't create test file /l01/data/mysqld_tmp_file_case_insensitive_test.lower-test
2020-05-18T15:32:17.909214Z 0 [System] [MY-010116] [Server] /opt/rh/rh-mysql80/root/usr/libexec/mysqld (mysqld 8.0.17) starting as process 3419
2020-05-18T15:32:17.912847Z 0 [Warning] [MY-010091] [Server] Can't create test file /l01/data/mysqld_tmp_file_case_insensitive_test.lower-test
2020-05-18T15:32:17.912864Z 0 [Warning] [MY-010159] [Server] Setting lower_case_table_names=2 because file system for /l01/data/ is case insensitive
2020-05-18T15:32:17.914169Z 0 [ERROR] [MY-010119] [Server] Aborting
</pre>

In the service log, we can see it is because of the SELinux, the service cannot access /l01/data which I set up for "datadir"
<pre class="prettyprint lang-sh linenums=1 ">
May 18 11:32:20 wordpresssq01p.pprd.odu.edu goferd[1425]: [INFO][worker-0] gofer.messaging.adapter.connect:30 - connected: proto
May 18 11:32:20 wordpresssq01p.pprd.odu.edu goferd[1425]: [WARNING][worker-0] gofer.messaging.adapter.proton.reliability:48 - re
May 18 11:32:21 wordpresssq01p.pprd.odu.edu setroubleshoot[3425]: failed to retrieve rpm info for /l01/data
May 18 11:32:21 wordpresssq01p.pprd.odu.edu setroubleshoot[3425]: SELinux is preventing /opt/rh/rh-mysql80/root/usr/libexec/mysq
May 18 11:32:21 wordpresssq01p.pprd.odu.edu python[3425]: SELinux is preventing /opt/rh/rh-mysql80/root/usr/libexec/mysqld from 
</pre>

So, we have to set up ACLs for the related directories and port
<pre class="prettyprint lang-sh linenums=1 ">
# chcon -R -t mysqld_db_t /l01/data
# chcon -t mysqld_db_t /run/rh-mysql80-mysqld/mysqld.pid
# chcon -t mysqld_db_t '/l01/.my.cnf'
# semanage port -a -t mysqld_port_t -p tcp 2321
</pre>


