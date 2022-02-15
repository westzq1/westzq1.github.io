---
layout: post
title: Oracle - Oracle Database on Docker
modified: 2019-03-26
categories: [Oracle]
tags: 
  - Oracle.Installation
comments: true
---

Oracle Database on Docker
https://github.com/oracle/docker-images/tree/master/OracleDatabase

Oracle Support for Database Running on Docker (Doc ID 2216342.1)	

Support for Docker Running on Oracle Linux (Doc ID 1921163.1)	

Oracle Database Enterprise Edition - Docker Hub - docker.com‎
https://hub.docker.com/_/oracle-database-enterprise-edition?gclid=CjwKCAjwm-fkBRBBEiwA966fZPDZpr05ox_CQQGBNItmJyoMY5bkk5__0n6b1oiRHtByqGNhr90OuRoCBowQAvD_BwE

Deploy Oracle Database as a Docker Container in Oracle Container Cloud Service
https://www.oracle.com/technetwork/articles/cloudcomp/deploy-database-in-container-cloud-3876722.html

Docker : An Oracle DBAs Guide to Docker
https://oracle-base.com/articles/linux/docker-oracle-dba-guide-to-docker



https://blogs.oracle.com/virtualization/install-docker-on-oracle-linux-7-v2

https://github.com/oracle/docker-images/tree/master/OracleDatabase/SingleInstance


---------------------------

[root@database02 /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
oracle/database     12.2.0.1-ee         d02828f1f8a0        36 seconds ago      5.98GB
oraclelinux         7-slim              93c6055c3338        13 days ago         118MB


[root@database02 containerd]# docker history  oracle/database:12.2.0.1-ee
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
d02828f1f8a0        About an hour ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "exec…   0B                  
ac1da44861cd        About an hour ago   /bin/sh -c #(nop)  HEALTHCHECK &{["CMD-SHELL…   0B                  
61fab7e94ac7        About an hour ago   /bin/sh -c #(nop)  EXPOSE 1521 5500             0B                  
f53d5afb2e3b        About an hour ago   /bin/sh -c #(nop)  VOLUME [/opt/oracle/orada…   0B                  
4f96d5ce22a2        About an hour ago   /bin/sh -c #(nop) WORKDIR /home/oracle          0B                  
7923897d31c5        About an hour ago   /bin/sh -c #(nop)  USER oracle                  0B                  
e0b528dfa71d        About an hour ago   /bin/sh -c $ORACLE_BASE/oraInventory/orainst…   11.2MB              
9a338aa6c713        About an hour ago   /bin/sh -c #(nop)  USER root                    0B                  
837aa0b30954        About an hour ago   /bin/sh -c #(nop) COPY --chown=oracle:dbadir…   5.67GB              
a4a2807470db        About an hour ago   /bin/sh -c #(nop)  USER oracle                  0B                  
d16135b0b9bc        About an hour ago   /bin/sh -c chmod ug+x $INSTALL_DIR/*.sh &&  …   173MB               
24bb3e5db9d2        About an hour ago   /bin/sh -c #(nop) COPY multi:21527a9ab2ee058…   22.2kB              
4c53690fed69        About an hour ago   /bin/sh -c #(nop) COPY multi:a6ae334abcdcf01…   1.85kB              
662b70d5864a        About an hour ago   /bin/sh -c #(nop)  ENV PATH=/opt/oracle/prod…   0B                  
1bba7f3dde85        About an hour ago   /bin/sh -c #(nop)  ENV ORACLE_BASE=/opt/orac…   0B                  
de356b02fd4b        About an hour ago   /bin/sh -c #(nop)  MAINTAINER Gerald Venzl <…   0B                  
93c6055c3338        13 days ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B                  
<missing>           13 days ago         /bin/sh -c #(nop) ADD file:4d11753c2bf556fa7…   118MB               
<missing>           6 months ago        /bin/sh -c #(nop)  MAINTAINER Oracle Linux P…   0B    


[root@database02 ~]# docker search tomcat
INDEX               NAME                                       DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
docker.io           tomcat                                     Apache Tomcat is an open source implementati…   2325                [OK]                
docker.io           tomee                                      Apache TomEE is an all-Apache Java EE certif…   64                  [OK]                
docker.io           dordoka/tomcat                             Ubuntu 14.04, Oracle JDK 8 and Tomcat 8 base…   53                                      [OK]
docker.io           davidcaste/alpine-tomcat                   Apache Tomcat 7/8 using Oracle Java 7/8 with…   34                                      [OK]
docker.io           bitnami/tomcat                             Bitnami Tomcat Docker Image                     28                                      [OK]
docker.io           cloudesire/tomcat                          Tomcat server, 6/7/8                            14                                      [OK]
docker.io           meirwa/spring-boot-tomcat-mysql-app        a sample spring-boot app using tomcat and My…   12                                      [OK]
docker.io           aallam/tomcat-mysql                        Debian, Oracle JDK, Tomcat & MySQL              11                                      [OK]
docker.io           tutum/tomcat                               Base docker image to run a Tomcat applicatio…   11                                      
docker.io           jeanblanchard/tomcat                       Minimal Docker image with Apache Tomcat         8                                       
docker.io           arm32v7/tomcat                             Apache Tomcat is an open source implementati…   6                                       
docker.io           maluuba/tomcat7-java8                      Tomcat7 with java8.                             3                                       
docker.io           rightctrl/tomcat                           CentOS , Oracle Java, tomcat application ssl…   3                                       [OK]
docker.io           amd64/tomcat                               Apache Tomcat is an open source implementati…   2                                       
docker.io           arm64v8/tomcat                             Apache Tomcat is an open source implementati…   2                                       
docker.io           fabric8/tomcat-8                           Fabric8 Tomcat 8 Image                          2                                       [OK]
docker.io           99taxis/tomcat7                            Tomcat7                                         1                                       [OK]
docker.io           camptocamp/tomcat-logback                  Docker image for tomcat with logback integra…   1                                       [OK]
docker.io           1and1internet/debian-9-java-8-tomcat-8.5   Our tomcat 8.5 image                            0                                       [OK]
docker.io           cfje/tomcat-resource                       Tomcat Concourse Resource                       0                                       
docker.io           jelastic/tomcat                            An image of the Tomcat Java application serv…   0                                       
docker.io           oobsri/tomcat8                             Testing CI Jobs with different names.           0                                       
docker.io           picoded/tomcat7                            tomcat7 with jre8 and MANAGER_USER / MANAGER…   0                                       [OK]
docker.io           s390x/tomcat                               Apache Tomcat is an open source implementati…   0                                       
docker.io           swisstopo/service-print-tomcat             backend tomcat for service-print "the true, …   0                                       
[root@database02 ~]# docker search oracle
INDEX               NAME                                  DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
docker.io           oraclelinux                           Official Docker builds of Oracle Linux.         568                 [OK]                
docker.io           jaspeen/oracle-11g                    Docker image for Oracle 11g database            97                                      [OK]
docker.io           oracle/openjdk                        Docker images containing OpenJDK Oracle Linux   54                                      [OK]
docker.io           oracle/nosql                          Oracle NoSQL on a Docker Image with Oracle L…   18                                      [OK]
docker.io           collinestes/docker-node-oracle        A container with Node.js/Oracle instant clie…   17                                      [OK]
docker.io           bofm/oracle12c                        Docker image for Oracle Database                16                                      [OK]
docker.io           frolvlad/alpine-oraclejre8            [DEPRECATED IN FAVOR OF frolvlad/alpine-java…   12                                      [OK]
docker.io           datagrip/oracle                       Oracle 11.2 & 12.1.0.2-se2 & 11.2.0.2-xe        8                                       [OK]
docker.io           openweb/oracle-tomcat                 A fork off of Official tomcat image with Ora…   8                                       [OK]
docker.io           oracle/weblogic-kubernetes-operator   Docker images containing the Oracle WebLogic…   7                                       
docker.io           driv/docker-maven-java-oracle         Maven image based on java oracle                3                                       [OK]
docker.io           yannig/oracledb_exporter              Oracle DB exporter for Prometheus               1                                       
docker.io           iamseth/oracledb_exporter             A Prometheus exporter for Oracle modeled aft…   1                                       
docker.io           publicisworldwide/oracle-core         This is the core image based on Oracle Linux…   1                                       [OK]
docker.io           mminks/docker-oracle-jdk-confluence   Dockerized Atlassian Confluence installation…   1                                       [OK]
docker.io           amd64/oraclelinux                     Official Docker builds of Oracle Linux.         0                                       
docker.io           bitnami/oraclelinux-extras            Oracle Linux base images                        0                                       [OK]
docker.io           bitnami/oraclelinux-runtimes          Oracle Linux runtime-optimized images           0                                       [OK]
docker.io           paulosalgado/oracle-java8-ubuntu-16   Oracle Java 8 on Ubuntu 16.04 LTS.              0                                       [OK]
docker.io           pivotaldata/oracle7-test              Oracle Enterprise Linux (OEL) image for GPDB…   0                                       
docker.io           sigma/nimbus-lock-oracle                                                              0                                       [OK]
docker.io           softwareplant/oracle                  oracle db                                       0                                       [OK]
docker.io           toolsmiths/oracle7-test                                                               0                                       
docker.io           toolsmiths/oraclelinux7.5-dev                                                         0                                       
docker.io           trollin/oraclelinux                                                                   0                                       




docker exec -ti o12201 bash

--

--rm delete container after exit.

docker run --name o12201 \
-p 1512:1521 -p 5512:5500 \
-e ORACLE_SID=o12201 \
-e ORACLE_PDB=hr \
-e ORACLE_PWD=oracle \
-e ORACLE_CHARACTERSET=AL32UTF8 \
-v /home/oracle/oradata \
oracle/database:12.2.0.1-ee

--
docker pull alexeiled/docker-oracle-xe-11g

docker pull tomcat:7

docker run -h "oracle" --name "oracle" -d -p 22:22 -p 1521:1521 -p 8080:8080 alexeiled/docker-oracle-xe-11g

docker run -d -p 8080:8080 --name tomcat7 -v ~/tomcat7/webapps:/usr/local/tomcat/webapps tomcat:7

docker run -d -p 8080:8080 --name tomcat7 -v ~/tomcat7/webapps:/usr/local/tomcat/webapps --link oracle:oracle tomcat:7


--------------------------------------------------------------------------
-- All stopped and running containers
docker ps -a 

-- Upload a public image
docker login
docker push

-- To set which registry an image will be uploaded to
docker tag

--------------------------------------------------------------------------
docker run hello-world
docker run -it ubuntu bash

docker ps --format $FORMAT

docker ps -a
docker info
 
-------------------------------------------------
docker ps -a
docker ps -l

docker commit <ID>
docker tag <IMAGE ID> <NAME>

docker commit <NAMES> <NAME>

docker run -d -ti ubuntu bash   (-d background running)
docker attach <names>
control-p / control-q

docker attach o12201


--------------
docker logs <name>

docker kill <name>
docker rm <name>


----------------------
resource:
docker run --memory maximum-allowed-memory image-name command
docker run --cpu-shares 
docker run --cpu-quota

Network
docker run --rm -ti -p 45678:45678 -p 45679:45679 --name echo-server ubuntu:14.04 bash

nc -lp 45678 | nc -lp 45679

nc localhost 45678
nc localhost 45679

---
docker run --rm -ti -p 45678 -p 45679 --name echo-server ubuntu:14.04 bash
docker port <names>

-- UDP
docker run --rm -ti -p 45678:45678/(tcp/udp)

----------------------------------------------------
docker run -ti --rm --name server ubuntu:14.04 bash
nc -lp 1234

docker run --rm --ti --link server --name client ubuntu:14.04 bash
nc server 1234

----------------------------------

docker network create example
docker run --rm -ti --net=example --name server ubuntu:14.04 bash
docker run --rm -ti --link server --net=example --name client ubuntu:14.04 bash

docker run -p 127.0.0.1:1234:1234/tcp


--------------
docker rmi image-name:tag
docker rmi image_id

----------------------
volume share

docker run -ti -v /Users/zhangqiaoc/example:/shared-folder ubuntu bash

docker run -ti -v /shared-folder ubuntu bash
docker run -ti --volumns-from <names> ubuntu bash

----
Registry
docker search ubuntu
docker login
docker push debian:sid
docker tag debian:sid q1zhang/test-image-42:v99.9
docker push q1zhang/test-image-42:v99.9

-----
dockerfile:

docker build -t name-of-result . 



/var/run/docker.sock 

brctl show


--------------------------------------------------------------------------

[root@database02 dockerfiles]# ./buildDockerImage.sh -v 12.2.0.1 -e
Checking if required packages are present and valid...
linuxx64_12201_database.zip: OK
==========================
DOCKER info:
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 16
Server Version: 18.09.3
Storage Driver: overlay2
 Backing Filesystem: xfs
 Supports d_type: true
 Native Overlay Diff: false
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: e6b3f5632f50dbc4e9cb6288d911bf4f5e95b18e
runc version: 6635b4f0c6af3810594d2770f662f34ddc15b40d
init version: fec3683
Security Options:
 seccomp
  Profile: default
Kernel Version: 4.1.12-112.16.4.el7uek.x86_64
Operating System: Oracle Linux Server 7.5
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 1.701GiB
Name: database02
ID: ZEDU:PVAL:Z2OX:MVJJ:YWW5:XRHM:Y3FB:CPB3:IOH7:YOVQ:22F3:5J3J
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
Product License: Community Engine

==========================
Building image 'oracle/database:12.2.0.1-ee' ...
Sending build context to Docker daemon  3.454GB
Step 1/24 : FROM oraclelinux:7-slim as base
 ---> 93c6055c3338
Step 2/24 : MAINTAINER Gerald Venzl <gerald.venzl@oracle.com>
 ---> Running in a4ff20e75368
Removing intermediate container a4ff20e75368
 ---> ac17c6cbc6f5
Step 3/24 : ENV ORACLE_BASE=/opt/oracle     ORACLE_HOME=/opt/oracle/product/12.2.0.1/dbhome_1     INSTALL_DIR=/opt/install     INSTALL_FILE_1="linuxx64_12201_database.zip"     INSTALL_RSP="db_inst.rsp"     CONFIG_RSP="dbca.rsp.tmpl"     PWD_FILE="setPassword.sh"     RUN_FILE="runOracle.sh"     START_FILE="startDB.sh"     CREATE_DB_FILE="createDB.sh"     SETUP_LINUX_FILE="setupLinuxEnv.sh"     CHECK_SPACE_FILE="checkSpace.sh"     CHECK_DB_FILE="checkDBStatus.sh"     USER_SCRIPTS_FILE="runUserScripts.sh"     INSTALL_DB_BINARIES_FILE="installDBBinaries.sh"
 ---> Running in e23265459516
Removing intermediate container e23265459516
 ---> c243c0269b63
Step 4/24 : ENV PATH=$ORACLE_HOME/bin:$ORACLE_HOME/OPatch/:/usr/sbin:$PATH     LD_LIBRARY_PATH=$ORACLE_HOME/lib:/usr/lib     CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
 ---> Running in 0b526931748d
Removing intermediate container 0b526931748d
 ---> cf2943f10ca6
Step 5/24 : COPY $SETUP_LINUX_FILE $CHECK_SPACE_FILE $INSTALL_DIR/
 ---> 95ca41f98467
Step 6/24 : COPY $RUN_FILE $START_FILE $CREATE_DB_FILE $CONFIG_RSP $PWD_FILE $CHECK_DB_FILE $USER_SCRIPTS_FILE $ORACLE_BASE/
 ---> cccd2f4f4329
Step 7/24 : RUN chmod ug+x $INSTALL_DIR/*.sh &&     sync &&     $INSTALL_DIR/$CHECK_SPACE_FILE &&     $INSTALL_DIR/$SETUP_LINUX_FILE &&     rm -rf $INSTALL_DIR
 ---> Running in 503e0d74a5ad
Loaded plugins: ovl
Resolving Dependencies
--> Running transaction check
---> Package openssl.x86_64 1:1.0.2k-16.0.1.el7_6.1 will be installed
--> Processing Dependency: make for package: 1:openssl-1.0.2k-16.0.1.el7_6.1.x86_64
---> Package oracle-database-server-12cR2-preinstall.x86_64 0:1.0-4.el7 will be installed
--> Processing Dependency: binutils for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: initscripts for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: sysstat for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: xorg-x11-utils for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: bind-utils for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: net-tools for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: pam for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: compat-libcap1 for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: ksh for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: libaio-devel for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: xorg-x11-xauth for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: util-linux-ng for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: libaio for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: psmisc for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: unzip for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: module-init-tools for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: glibc-devel for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: compat-libstdc++-33 for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: libstdc++-devel for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: ethtool for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: procps for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: openssh-clients for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: nfs-utils for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Processing Dependency: smartmontools for package: oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_64
--> Running transaction check
---> Package bind-utils.x86_64 32:9.9.4-73.el7_6 will be installed
--> Processing Dependency: bind-libs = 32:9.9.4-73.el7_6 for package: 32:bind-utils-9.9.4-73.el7_6.x86_64
--> Processing Dependency: libisccc.so.90()(64bit) for package: 32:bind-utils-9.9.4-73.el7_6.x86_64
--> Processing Dependency: liblwres.so.90()(64bit) for package: 32:bind-utils-9.9.4-73.el7_6.x86_64
--> Processing Dependency: libbind9.so.90()(64bit) for package: 32:bind-utils-9.9.4-73.el7_6.x86_64
--> Processing Dependency: libisc.so.95()(64bit) for package: 32:bind-utils-9.9.4-73.el7_6.x86_64
--> Processing Dependency: libdns.so.100()(64bit) for package: 32:bind-utils-9.9.4-73.el7_6.x86_64
--> Processing Dependency: libGeoIP.so.1()(64bit) for package: 32:bind-utils-9.9.4-73.el7_6.x86_64
--> Processing Dependency: libisccfg.so.90()(64bit) for package: 32:bind-utils-9.9.4-73.el7_6.x86_64
---> Package binutils.x86_64 0:2.27-34.base.0.1.el7 will be installed
---> Package compat-libcap1.x86_64 0:1.10-7.el7 will be installed
---> Package compat-libstdc++-33.x86_64 0:3.2.3-72.el7 will be installed
---> Package ethtool.x86_64 2:4.8-9.el7 will be installed
---> Package glibc-devel.x86_64 0:2.17-260.0.15.el7_6.3 will be installed
--> Processing Dependency: glibc-headers = 2.17-260.0.15.el7_6.3 for package: glibc-devel-2.17-260.0.15.el7_6.3.x86_64
--> Processing Dependency: glibc-headers for package: glibc-devel-2.17-260.0.15.el7_6.3.x86_64
---> Package initscripts.x86_64 0:9.49.46-1.0.1.el7 will be installed
--> Processing Dependency: udev >= 125-1 for package: initscripts-9.49.46-1.0.1.el7.x86_64
--> Processing Dependency: sysvinit-tools >= 2.87-5 for package: initscripts-9.49.46-1.0.1.el7.x86_64
--> Processing Dependency: /sbin/arping for package: initscripts-9.49.46-1.0.1.el7.x86_64
--> Processing Dependency: systemd for package: initscripts-9.49.46-1.0.1.el7.x86_64
--> Processing Dependency: iproute for package: initscripts-9.49.46-1.0.1.el7.x86_64
--> Processing Dependency: hostname for package: initscripts-9.49.46-1.0.1.el7.x86_64
---> Package kmod.x86_64 0:20-23.0.1.el7 will be installed
--> Processing Dependency: /usr/bin/gzip for package: kmod-20-23.0.1.el7.x86_64
--> Processing Dependency: /usr/bin/xz for package: kmod-20-23.0.1.el7.x86_64
--> Processing Dependency: dracut for package: kmod-20-23.0.1.el7.x86_64
---> Package ksh.x86_64 0:20120801-139.0.1.el7 will be installed
---> Package libaio.x86_64 0:0.3.109-13.el7 will be installed
---> Package libaio-devel.x86_64 0:0.3.109-13.el7 will be installed
---> Package libstdc++-devel.x86_64 0:4.8.5-36.0.1.el7_6.1 will be installed
--> Processing Dependency: libstdc++(x86-64) = 4.8.5-36.0.1.el7_6.1 for package: libstdc++-devel-4.8.5-36.0.1.el7_6.1.x86_64
---> Package make.x86_64 1:3.82-23.el7 will be installed
---> Package net-tools.x86_64 0:2.0-0.24.20131004git.el7 will be installed
---> Package nfs-utils.x86_64 1:1.3.0-0.61.0.1.el7 will be installed
--> Processing Dependency: gssproxy >= 0.7.0-3 for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libtirpc >= 0.2.4-0.7 for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: rpcbind for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libdevmapper.so.1.02(Base)(64bit) for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: keyutils for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: quota for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libevent for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libdevmapper.so.1.02(DM_1_02_97)(64bit) for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libnfsidmap for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libdevmapper.so.1.02()(64bit) for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libevent-2.0.so.5()(64bit) for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libwrap.so.0()(64bit) for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libtirpc.so.1()(64bit) for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
--> Processing Dependency: libnfsidmap.so.0()(64bit) for package: 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64
---> Package openssh-clients.x86_64 0:7.4p1-16.el7 will be installed
--> Processing Dependency: openssh = 7.4p1-16.el7 for package: openssh-clients-7.4p1-16.el7.x86_64
--> Processing Dependency: fipscheck-lib(x86-64) >= 1.3.0 for package: openssh-clients-7.4p1-16.el7.x86_64
--> Processing Dependency: libfipscheck.so.1()(64bit) for package: openssh-clients-7.4p1-16.el7.x86_64
--> Processing Dependency: libedit.so.0()(64bit) for package: openssh-clients-7.4p1-16.el7.x86_64
---> Package pam.x86_64 0:1.1.8-22.el7 will be installed
--> Processing Dependency: libpwquality >= 0.9.9 for package: pam-1.1.8-22.el7.x86_64
--> Processing Dependency: cracklib-dicts >= 2.8 for package: pam-1.1.8-22.el7.x86_64
--> Processing Dependency: libcrack.so.2()(64bit) for package: pam-1.1.8-22.el7.x86_64
---> Package procps-ng.x86_64 0:3.3.10-23.el7 will be installed
--> Processing Dependency: libsystemd.so.0(LIBSYSTEMD_209)(64bit) for package: procps-ng-3.3.10-23.el7.x86_64
--> Processing Dependency: libsystemd.so.0()(64bit) for package: procps-ng-3.3.10-23.el7.x86_64
---> Package psmisc.x86_64 0:22.20-15.el7 will be installed
---> Package smartmontools.x86_64 1:6.5-1.el7 will be installed
--> Processing Dependency: mailx for package: 1:smartmontools-6.5-1.el7.x86_64
---> Package sysstat.x86_64 0:10.1.5-17.el7 will be installed
--> Processing Dependency: systemd-sysv for package: sysstat-10.1.5-17.el7.x86_64
--> Processing Dependency: /etc/cron.d for package: sysstat-10.1.5-17.el7.x86_64
--> Processing Dependency: libsensors.so.4()(64bit) for package: sysstat-10.1.5-17.el7.x86_64
---> Package unzip.x86_64 0:6.0-19.el7 will be installed
---> Package util-linux.x86_64 0:2.23.2-59.el7_6.1 will be installed
--> Processing Dependency: libmount = 2.23.2-59.el7_6.1 for package: util-linux-2.23.2-59.el7_6.1.x86_64
--> Processing Dependency: libsmartcols = 2.23.2-59.el7_6.1 for package: util-linux-2.23.2-59.el7_6.1.x86_64
--> Processing Dependency: libuuid = 2.23.2-59.el7_6.1 for package: util-linux-2.23.2-59.el7_6.1.x86_64
--> Processing Dependency: libblkid = 2.23.2-59.el7_6.1 for package: util-linux-2.23.2-59.el7_6.1.x86_64
--> Processing Dependency: libsmartcols.so.1(SMARTCOLS_2.25)(64bit) for package: util-linux-2.23.2-59.el7_6.1.x86_64
--> Processing Dependency: libutempter.so.0(UTEMPTER_1.1)(64bit) for package: util-linux-2.23.2-59.el7_6.1.x86_64
--> Processing Dependency: libutempter.so.0()(64bit) for package: util-linux-2.23.2-59.el7_6.1.x86_64
--> Processing Dependency: libuser.so.1()(64bit) for package: util-linux-2.23.2-59.el7_6.1.x86_64
--> Processing Dependency: libsmartcols.so.1()(64bit) for package: util-linux-2.23.2-59.el7_6.1.x86_64
---> Package xorg-x11-utils.x86_64 0:7.5-23.el7 will be installed
--> Processing Dependency: libXext.so.6()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXv.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXtst.so.6()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXrender.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXxf86vm.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXxf86misc.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libX11-xcb.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXrandr.so.2()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libdmx.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXinerama.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libX11.so.6()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXxf86dga.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libxcb.so.1()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libxcb-shape.so.0()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
--> Processing Dependency: libXi.so.6()(64bit) for package: xorg-x11-utils-7.5-23.el7.x86_64
---> Package xorg-x11-xauth.x86_64 1:1.0.9-1.el7 will be installed
--> Processing Dependency: libXau.so.6()(64bit) for package: 1:xorg-x11-xauth-1.0.9-1.el7.x86_64
--> Processing Dependency: libXmuu.so.1()(64bit) for package: 1:xorg-x11-xauth-1.0.9-1.el7.x86_64
--> Running transaction check
---> Package GeoIP.x86_64 0:1.5.0-13.el7 will be installed
---> Package bind-libs.x86_64 32:9.9.4-73.el7_6 will be installed
--> Processing Dependency: bind-license = 32:9.9.4-73.el7_6 for package: 32:bind-libs-9.9.4-73.el7_6.x86_64
---> Package cracklib.x86_64 0:2.9.0-11.el7 will be installed
---> Package cracklib-dicts.x86_64 0:2.9.0-11.el7 will be installed
---> Package cronie.x86_64 0:1.4.11-20.el7_6 will be installed
--> Processing Dependency: dailyjobs for package: cronie-1.4.11-20.el7_6.x86_64
---> Package device-mapper-libs.x86_64 7:1.02.149-10.0.1.el7_6.3 will be installed
--> Processing Dependency: device-mapper = 7:1.02.149-10.0.1.el7_6.3 for package: 7:device-mapper-libs-1.02.149-10.0.1.el7_6.3.x86_64
---> Package dracut.x86_64 0:033-554.0.3.el7 will be installed
--> Processing Dependency: /usr/bin/pkg-config for package: dracut-033-554.0.3.el7.x86_64
--> Processing Dependency: kpartx for package: dracut-033-554.0.3.el7.x86_64
--> Processing Dependency: tar for package: dracut-033-554.0.3.el7.x86_64
--> Processing Dependency: hardlink for package: dracut-033-554.0.3.el7.x86_64
---> Package fipscheck-lib.x86_64 0:1.4.1-6.el7 will be installed
--> Processing Dependency: /usr/bin/fipscheck for package: fipscheck-lib-1.4.1-6.el7.x86_64
---> Package glibc-headers.x86_64 0:2.17-260.0.15.el7_6.3 will be installed
--> Processing Dependency: kernel-headers >= 2.2.1 for package: glibc-headers-2.17-260.0.15.el7_6.3.x86_64
--> Processing Dependency: kernel-headers for package: glibc-headers-2.17-260.0.15.el7_6.3.x86_64
---> Package gssproxy.x86_64 0:0.7.0-21.el7 will be installed
--> Processing Dependency: libini_config >= 1.3.1-31 for package: gssproxy-0.7.0-21.el7.x86_64
--> Processing Dependency: libref_array.so.1(REF_ARRAY_0.1.1)(64bit) for package: gssproxy-0.7.0-21.el7.x86_64
--> Processing Dependency: libini_config.so.3(INI_CONFIG_1.1.0)(64bit) for package: gssproxy-0.7.0-21.el7.x86_64
--> Processing Dependency: libini_config.so.3(INI_CONFIG_1.2.0)(64bit) for package: gssproxy-0.7.0-21.el7.x86_64
--> Processing Dependency: libverto-module-base for package: gssproxy-0.7.0-21.el7.x86_64
--> Processing Dependency: libini_config.so.3()(64bit) for package: gssproxy-0.7.0-21.el7.x86_64
--> Processing Dependency: libbasicobjects.so.0()(64bit) for package: gssproxy-0.7.0-21.el7.x86_64
--> Processing Dependency: libref_array.so.1()(64bit) for package: gssproxy-0.7.0-21.el7.x86_64
--> Processing Dependency: libcollection.so.2()(64bit) for package: gssproxy-0.7.0-21.el7.x86_64
---> Package gzip.x86_64 0:1.5-10.el7 will be installed
---> Package hostname.x86_64 0:3.13-3.el7 will be installed
---> Package iproute.x86_64 0:4.16.0-1.0.1.el7 will be installed
--> Processing Dependency: libmnl.so.0(LIBMNL_1.0)(64bit) for package: iproute-4.16.0-1.0.1.el7.x86_64
--> Processing Dependency: libmnl.so.0()(64bit) for package: iproute-4.16.0-1.0.1.el7.x86_64
---> Package iputils.x86_64 0:20160308-10.el7 will be installed
---> Package keyutils.x86_64 0:1.5.8-3.el7 will be installed
---> Package libX11.x86_64 0:1.6.5-2.el7 will be installed
--> Processing Dependency: libX11-common >= 1.6.5-2.el7 for package: libX11-1.6.5-2.el7.x86_64
---> Package libXau.x86_64 0:1.0.8-2.1.el7 will be installed
---> Package libXext.x86_64 0:1.3.3-3.el7 will be installed
---> Package libXi.x86_64 0:1.7.9-1.el7 will be installed
---> Package libXinerama.x86_64 0:1.1.3-2.1.el7 will be installed
---> Package libXmu.x86_64 0:1.1.2-2.el7 will be installed
--> Processing Dependency: libXt.so.6()(64bit) for package: libXmu-1.1.2-2.el7.x86_64
---> Package libXrandr.x86_64 0:1.5.1-2.el7 will be installed
---> Package libXrender.x86_64 0:0.9.10-1.el7 will be installed
---> Package libXtst.x86_64 0:1.2.3-1.el7 will be installed
---> Package libXv.x86_64 0:1.0.11-1.el7 will be installed
---> Package libXxf86dga.x86_64 0:1.1.4-2.1.el7 will be installed
---> Package libXxf86misc.x86_64 0:1.0.3-7.1.el7 will be installed
---> Package libXxf86vm.x86_64 0:1.1.4-1.el7 will be installed
---> Package libblkid.x86_64 0:2.23.2-59.el7 will be updated
---> Package libblkid.x86_64 0:2.23.2-59.el7_6.1 will be an update
---> Package libdmx.x86_64 0:1.1.3-3.el7 will be installed
---> Package libedit.x86_64 0:3.0-12.20121213cvs.el7 will be installed
---> Package libevent.x86_64 0:2.0.21-4.el7 will be installed
---> Package libmount.x86_64 0:2.23.2-59.el7 will be updated
---> Package libmount.x86_64 0:2.23.2-59.el7_6.1 will be an update
---> Package libnfsidmap.x86_64 0:0.25-19.el7 will be installed
---> Package libpwquality.x86_64 0:1.2.3-5.el7 will be installed
---> Package libsmartcols.x86_64 0:2.23.2-59.el7_6.1 will be installed
---> Package libstdc++.x86_64 0:4.8.5-36.0.1.el7 will be updated
---> Package libstdc++.x86_64 0:4.8.5-36.0.1.el7_6.1 will be an update
---> Package libtirpc.x86_64 0:0.2.4-0.15.el7 will be installed
---> Package libuser.x86_64 0:0.60-9.el7 will be installed
---> Package libutempter.x86_64 0:1.1.6-4.el7 will be installed
---> Package libuuid.x86_64 0:2.23.2-59.el7 will be updated
---> Package libuuid.x86_64 0:2.23.2-59.el7_6.1 will be an update
---> Package libxcb.x86_64 0:1.13-1.el7 will be installed
---> Package lm_sensors-libs.x86_64 0:3.4.0-6.20160601gitf9185e5.el7 will be installed
---> Package mailx.x86_64 0:12.5-19.el7 will be installed
---> Package openssh.x86_64 0:7.4p1-16.el7 will be installed
---> Package quota.x86_64 1:4.01-17.el7 will be installed
--> Processing Dependency: quota-nls = 1:4.01-17.el7 for package: 1:quota-4.01-17.el7.x86_64
--> Processing Dependency: tcp_wrappers for package: 1:quota-4.01-17.el7.x86_64
--> Processing Dependency: libext2fs.so.2()(64bit) for package: 1:quota-4.01-17.el7.x86_64
---> Package rpcbind.x86_64 0:0.2.0-47.el7 will be installed
---> Package systemd.x86_64 0:219-62.0.4.el7_6.5 will be installed
--> Processing Dependency: dbus for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: acl for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libcryptsetup.so.12(CRYPTSETUP_2.0)(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libkmod.so.2(LIBKMOD_5)(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libdw.so.1(ELFUTILS_0.122)(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libdw.so.1(ELFUTILS_0.158)(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libdw.so.1(ELFUTILS_0.130)(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: liblz4.so.1()(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libdw.so.1()(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libkmod.so.2()(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libcryptsetup.so.12()(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
--> Processing Dependency: libqrencode.so.3()(64bit) for package: systemd-219-62.0.4.el7_6.5.x86_64
---> Package systemd-libs.x86_64 0:219-62.0.4.el7_6.5 will be installed
---> Package systemd-sysv.x86_64 0:219-62.0.4.el7_6.5 will be installed
---> Package sysvinit-tools.x86_64 0:2.88-14.dsf.el7 will be installed
---> Package tcp_wrappers-libs.x86_64 0:7.6-77.el7 will be installed
---> Package xz.x86_64 0:5.2.2-1.el7 will be installed
--> Running transaction check
---> Package acl.x86_64 0:2.2.51-14.el7 will be installed
---> Package bind-license.noarch 32:9.9.4-73.el7_6 will be installed
---> Package cronie-anacron.x86_64 0:1.4.11-20.el7_6 will be installed
--> Processing Dependency: crontabs for package: cronie-anacron-1.4.11-20.el7_6.x86_64
---> Package cryptsetup-libs.x86_64 0:2.0.3-3.el7 will be installed
--> Processing Dependency: libjson-c.so.2()(64bit) for package: cryptsetup-libs-2.0.3-3.el7.x86_64
---> Package dbus.x86_64 1:1.10.24-13.0.1.el7_6 will be installed
--> Processing Dependency: dbus-libs(x86-64) = 1:1.10.24-13.0.1.el7_6 for package: 1:dbus-1.10.24-13.0.1.el7_6.x86_64
--> Processing Dependency: libdbus-1.so.3(LIBDBUS_PRIVATE_1.10.24)(64bit) for package: 1:dbus-1.10.24-13.0.1.el7_6.x86_64
--> Processing Dependency: libdbus-1.so.3(LIBDBUS_1_3)(64bit) for package: 1:dbus-1.10.24-13.0.1.el7_6.x86_64
--> Processing Dependency: libdbus-1.so.3()(64bit) for package: 1:dbus-1.10.24-13.0.1.el7_6.x86_64
---> Package device-mapper.x86_64 7:1.02.149-10.0.1.el7_6.3 will be installed
---> Package e2fsprogs-libs.x86_64 0:1.42.9-13.el7 will be installed
---> Package elfutils-libs.x86_64 0:0.172-2.el7 will be installed
--> Processing Dependency: default-yama-scope for package: elfutils-libs-0.172-2.el7.x86_64
---> Package fipscheck.x86_64 0:1.4.1-6.el7 will be installed
---> Package hardlink.x86_64 1:1.0-19.el7 will be installed
---> Package kernel-headers.x86_64 0:3.10.0-957.10.1.el7 will be installed
---> Package kmod-libs.x86_64 0:20-23.0.1.el7 will be installed
---> Package kpartx.x86_64 0:0.4.9-123.el7 will be installed
---> Package libX11-common.noarch 0:1.6.5-2.el7 will be installed
---> Package libXt.x86_64 0:1.1.5-3.el7 will be installed
--> Processing Dependency: libICE.so.6()(64bit) for package: libXt-1.1.5-3.el7.x86_64
--> Processing Dependency: libSM.so.6()(64bit) for package: libXt-1.1.5-3.el7.x86_64
---> Package libbasicobjects.x86_64 0:0.1.1-32.el7 will be installed
---> Package libcollection.x86_64 0:0.7.0-32.el7 will be installed
---> Package libini_config.x86_64 0:1.3.1-32.el7 will be installed
--> Processing Dependency: libpath_utils.so.1(PATH_UTILS_0.2.1)(64bit) for package: libini_config-1.3.1-32.el7.x86_64
--> Processing Dependency: libpath_utils.so.1()(64bit) for package: libini_config-1.3.1-32.el7.x86_64
---> Package libmnl.x86_64 0:1.0.3-7.el7 will be installed
---> Package libref_array.x86_64 0:0.1.5-32.el7 will be installed
---> Package libverto-libevent.x86_64 0:0.2.5-4.el7 will be installed
---> Package lz4.x86_64 0:1.7.5-2.0.1.el7 will be installed
---> Package pkgconfig.x86_64 1:0.27.1-4.el7 will be installed
---> Package qrencode-libs.x86_64 0:3.4.1-3.el7 will be installed
---> Package quota-nls.noarch 1:4.01-17.el7 will be installed
---> Package tar.x86_64 2:1.26-35.el7 will be installed
---> Package tcp_wrappers.x86_64 0:7.6-77.el7 will be installed
--> Running transaction check
---> Package crontabs.noarch 0:1.11-6.20121102git.el7 will be installed
---> Package dbus-libs.x86_64 1:1.10.24-13.0.1.el7_6 will be installed
---> Package elfutils-default-yama-scope.noarch 0:0.172-2.el7 will be installed
---> Package json-c.x86_64 0:0.11-4.el7_0 will be installed
---> Package libICE.x86_64 0:1.0.9-9.el7 will be installed
---> Package libSM.x86_64 0:1.2.2-2.el7 will be installed
---> Package libpath_utils.x86_64 0:0.2.1-32.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                 Arch   Version                        Repository  Size
================================================================================
Installing:
 openssl                 x86_64 1:1.0.2k-16.0.1.el7_6.1        ol7_latest 492 k
 oracle-database-server-12cR2-preinstall
                         x86_64 1.0-4.el7                      ol7_latest  19 k
Installing for dependencies:
 GeoIP                   x86_64 1.5.0-13.el7                   ol7_latest 1.5 M
 acl                     x86_64 2.2.51-14.el7                  ol7_latest  81 k
 bind-libs               x86_64 32:9.9.4-73.el7_6              ol7_latest 1.0 M
 bind-license            noarch 32:9.9.4-73.el7_6              ol7_latest  86 k
 bind-utils              x86_64 32:9.9.4-73.el7_6              ol7_latest 205 k
 binutils                x86_64 2.27-34.base.0.1.el7           ol7_latest 5.9 M
 compat-libcap1          x86_64 1.10-7.el7                     ol7_latest  17 k
 compat-libstdc++-33     x86_64 3.2.3-72.el7                   ol7_latest 190 k
 cracklib                x86_64 2.9.0-11.el7                   ol7_latest  79 k
 cracklib-dicts          x86_64 2.9.0-11.el7                   ol7_latest 3.6 M
 cronie                  x86_64 1.4.11-20.el7_6                ol7_latest  91 k
 cronie-anacron          x86_64 1.4.11-20.el7_6                ol7_latest  35 k
 crontabs                noarch 1.11-6.20121102git.el7         ol7_latest  12 k
 cryptsetup-libs         x86_64 2.0.3-3.el7                    ol7_latest 337 k
 dbus                    x86_64 1:1.10.24-13.0.1.el7_6         ol7_latest 245 k
 dbus-libs               x86_64 1:1.10.24-13.0.1.el7_6         ol7_latest 169 k
 device-mapper           x86_64 7:1.02.149-10.0.1.el7_6.3      ol7_latest 292 k
 device-mapper-libs      x86_64 7:1.02.149-10.0.1.el7_6.3      ol7_latest 319 k
 dracut                  x86_64 033-554.0.3.el7                ol7_latest 328 k
 e2fsprogs-libs          x86_64 1.42.9-13.el7                  ol7_latest 166 k
 elfutils-default-yama-scope
                         noarch 0.172-2.el7                    ol7_latest  32 k
 elfutils-libs           x86_64 0.172-2.el7                    ol7_latest 285 k
 ethtool                 x86_64 2:4.8-9.el7                    ol7_latest 126 k
 fipscheck               x86_64 1.4.1-6.el7                    ol7_latest  21 k
 fipscheck-lib           x86_64 1.4.1-6.el7                    ol7_latest  10 k
 glibc-devel             x86_64 2.17-260.0.15.el7_6.3          ol7_latest 1.1 M
 glibc-headers           x86_64 2.17-260.0.15.el7_6.3          ol7_latest 685 k
 gssproxy                x86_64 0.7.0-21.el7                   ol7_latest 108 k
 gzip                    x86_64 1.5-10.el7                     ol7_latest 129 k
 hardlink                x86_64 1:1.0-19.el7                   ol7_latest  14 k
 hostname                x86_64 3.13-3.el7                     ol7_latest  16 k
 initscripts             x86_64 9.49.46-1.0.1.el7              ol7_latest 439 k
 iproute                 x86_64 4.16.0-1.0.1.el7               ol7_UEKR5  528 k
 iputils                 x86_64 20160308-10.el7                ol7_latest 147 k
 json-c                  x86_64 0.11-4.el7_0                   ol7_latest  30 k
 kernel-headers          x86_64 3.10.0-957.10.1.el7            ol7_latest 8.0 M
 keyutils                x86_64 1.5.8-3.el7                    ol7_latest  53 k
 kmod                    x86_64 20-23.0.1.el7                  ol7_latest 121 k
 kmod-libs               x86_64 20-23.0.1.el7                  ol7_latest  50 k
 kpartx                  x86_64 0.4.9-123.el7                  ol7_latest  76 k
 ksh                     x86_64 20120801-139.0.1.el7           ol7_latest 883 k
 libICE                  x86_64 1.0.9-9.el7                    ol7_latest  66 k
 libSM                   x86_64 1.2.2-2.el7                    ol7_latest  39 k
 libX11                  x86_64 1.6.5-2.el7                    ol7_latest 606 k
 libX11-common           noarch 1.6.5-2.el7                    ol7_latest 163 k
 libXau                  x86_64 1.0.8-2.1.el7                  ol7_latest  28 k
 libXext                 x86_64 1.3.3-3.el7                    ol7_latest  38 k
 libXi                   x86_64 1.7.9-1.el7                    ol7_latest  40 k
 libXinerama             x86_64 1.1.3-2.1.el7                  ol7_latest  13 k
 libXmu                  x86_64 1.1.2-2.el7                    ol7_latest  70 k
 libXrandr               x86_64 1.5.1-2.el7                    ol7_latest  27 k
 libXrender              x86_64 0.9.10-1.el7                   ol7_latest  25 k
 libXt                   x86_64 1.1.5-3.el7                    ol7_latest 172 k
 libXtst                 x86_64 1.2.3-1.el7                    ol7_latest  20 k
 libXv                   x86_64 1.0.11-1.el7                   ol7_latest  18 k
 libXxf86dga             x86_64 1.1.4-2.1.el7                  ol7_latest  18 k
 libXxf86misc            x86_64 1.0.3-7.1.el7                  ol7_latest  19 k
 libXxf86vm              x86_64 1.1.4-1.el7                    ol7_latest  17 k
 libaio                  x86_64 0.3.109-13.el7                 ol7_latest  24 k
 libaio-devel            x86_64 0.3.109-13.el7                 ol7_latest  12 k
 libbasicobjects         x86_64 0.1.1-32.el7                   ol7_latest  25 k
 libcollection           x86_64 0.7.0-32.el7                   ol7_latest  41 k
 libdmx                  x86_64 1.1.3-3.el7                    ol7_latest  15 k
 libedit                 x86_64 3.0-12.20121213cvs.el7         ol7_latest  88 k
 libevent                x86_64 2.0.21-4.el7                   ol7_latest 208 k
 libini_config           x86_64 1.3.1-32.el7                   ol7_latest  63 k
 libmnl                  x86_64 1.0.3-7.el7                    ol7_latest  22 k
 libnfsidmap             x86_64 0.25-19.el7                    ol7_latest  49 k
 libpath_utils           x86_64 0.2.1-32.el7                   ol7_latest  28 k
 libpwquality            x86_64 1.2.3-5.el7                    ol7_latest  84 k
 libref_array            x86_64 0.1.5-32.el7                   ol7_latest  27 k
 libsmartcols            x86_64 2.23.2-59.el7_6.1              ol7_latest 140 k
 libstdc++-devel         x86_64 4.8.5-36.0.1.el7_6.1           ol7_latest 1.5 M
 libtirpc                x86_64 0.2.4-0.15.el7                 ol7_latest  88 k
 libuser                 x86_64 0.60-9.el7                     ol7_latest 400 k
 libutempter             x86_64 1.1.6-4.el7                    ol7_latest  24 k
 libverto-libevent       x86_64 0.2.5-4.el7                    ol7_latest 8.2 k
 libxcb                  x86_64 1.13-1.el7                     ol7_latest 213 k
 lm_sensors-libs         x86_64 3.4.0-6.20160601gitf9185e5.el7 ol7_latest  41 k
 lz4                     x86_64 1.7.5-2.0.1.el7                ol7_latest  98 k
 mailx                   x86_64 12.5-19.el7                    ol7_latest 244 k
 make                    x86_64 1:3.82-23.el7                  ol7_latest 420 k
 net-tools               x86_64 2.0-0.24.20131004git.el7       ol7_latest 305 k
 nfs-utils               x86_64 1:1.3.0-0.61.0.1.el7           ol7_latest 410 k
 openssh                 x86_64 7.4p1-16.el7                   ol7_latest 509 k
 openssh-clients         x86_64 7.4p1-16.el7                   ol7_latest 653 k
 pam                     x86_64 1.1.8-22.el7                   ol7_latest 719 k
 pkgconfig               x86_64 1:0.27.1-4.el7                 ol7_latest  53 k
 procps-ng               x86_64 3.3.10-23.el7                  ol7_latest 290 k
 psmisc                  x86_64 22.20-15.el7                   ol7_latest 140 k
 qrencode-libs           x86_64 3.4.1-3.el7                    ol7_latest  49 k
 quota                   x86_64 1:4.01-17.el7                  ol7_latest 178 k
 quota-nls               noarch 1:4.01-17.el7                  ol7_latest  90 k
 rpcbind                 x86_64 0.2.0-47.el7                   ol7_latest  59 k
 smartmontools           x86_64 1:6.5-1.el7                    ol7_latest 460 k
 sysstat                 x86_64 10.1.5-17.el7                  ol7_latest 314 k
 systemd                 x86_64 219-62.0.4.el7_6.5             ol7_latest 5.1 M
 systemd-libs            x86_64 219-62.0.4.el7_6.5             ol7_latest 407 k
 systemd-sysv            x86_64 219-62.0.4.el7_6.5             ol7_latest  84 k
 sysvinit-tools          x86_64 2.88-14.dsf.el7                ol7_latest  62 k
 tar                     x86_64 2:1.26-35.el7                  ol7_latest 845 k
 tcp_wrappers            x86_64 7.6-77.el7                     ol7_latest  78 k
 tcp_wrappers-libs       x86_64 7.6-77.el7                     ol7_latest  65 k
 unzip                   x86_64 6.0-19.el7                     ol7_latest 169 k
 util-linux              x86_64 2.23.2-59.el7_6.1              ol7_latest 2.0 M
 xorg-x11-utils          x86_64 7.5-23.el7                     ol7_latest 114 k
 xorg-x11-xauth          x86_64 1:1.0.9-1.el7                  ol7_latest  29 k
 xz                      x86_64 5.2.2-1.el7                    ol7_latest 228 k
Updating for dependencies:
 libblkid                x86_64 2.23.2-59.el7_6.1              ol7_latest 180 k
 libmount                x86_64 2.23.2-59.el7_6.1              ol7_latest 182 k
 libstdc++               x86_64 4.8.5-36.0.1.el7_6.1           ol7_latest 304 k
 libuuid                 x86_64 2.23.2-59.el7_6.1              ol7_latest  82 k

Transaction Summary
================================================================================
Install  2 Packages (+108 Dependent packages)
Upgrade             (   4 Dependent packages)

Total download size: 47 M
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
--------------------------------------------------------------------------------
Total                                              1.1 MB/s |  47 MB  00:41     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : libuuid-2.23.2-59.el7_6.1.x86_64                           1/118 
  Updating   : libblkid-2.23.2-59.el7_6.1.x86_64                          2/118 
  Installing : tcp_wrappers-libs-7.6-77.el7.x86_64                        3/118 
  Updating   : libmount-2.23.2-59.el7_6.1.x86_64                          4/118 
  Installing : fipscheck-1.4.1-6.el7.x86_64                               5/118 
  Installing : fipscheck-lib-1.4.1-6.el7.x86_64                           6/118 
  Updating   : libstdc++-4.8.5-36.0.1.el7_6.1.x86_64                      7/118 
  Installing : gzip-1.5-10.el7.x86_64                                     8/118 
  Installing : cracklib-2.9.0-11.el7.x86_64                               9/118 
  Installing : cracklib-dicts-2.9.0-11.el7.x86_64                        10/118 
  Installing : pam-1.1.8-22.el7.x86_64                                   11/118 
  Installing : libpwquality-1.2.3-5.el7.x86_64                           12/118 
  Installing : libcollection-0.7.0-32.el7.x86_64                         13/118 
  Installing : xz-5.2.2-1.el7.x86_64                                     14/118 
  Installing : libtirpc-0.2.4-0.15.el7.x86_64                            15/118 
  Installing : lz4-1.7.5-2.0.1.el7.x86_64                                16/118 
  Installing : libevent-2.0.21-4.el7.x86_64                              17/118 
  Installing : libaio-0.3.109-13.el7.x86_64                              18/118 
  Installing : libref_array-0.1.5-32.el7.x86_64                          19/118 
  Installing : libbasicobjects-0.1.1-32.el7.x86_64                       20/118 
  Installing : libICE-1.0.9-9.el7.x86_64                                 21/118 
  Installing : libXau-1.0.8-2.1.el7.x86_64                               22/118 
  Installing : libxcb-1.13-1.el7.x86_64                                  23/118 
  Installing : 1:make-3.82-23.el7.x86_64                                 24/118 
  Installing : GeoIP-1.5.0-13.el7.x86_64                                 25/118 
  Installing : binutils-2.27-34.base.0.1.el7.x86_64                      26/118 
install-info: /usr/share/info/dir: could not read (No such file or directory) and could not create (No such file or directory)
install-info: /usr/share/info/dir: could not read (No such file or directory) and could not create (No such file or directory)
install-info: /usr/share/info/dir: could not read (No such file or directory) and could not create (No such file or directory)
install-info: /usr/share/info/dir: could not read (No such file or directory) and could not create (No such file or directory)
install-info: /usr/share/info/dir: could not read (No such file or directory) and could not create (No such file or directory)
  Installing : libSM-1.2.2-2.el7.x86_64                                  27/118 
  Installing : libaio-devel-0.3.109-13.el7.x86_64                        28/118 
  Installing : libverto-libevent-0.2.5-4.el7.x86_64                      29/118 
  Installing : libuser-0.60-9.el7.x86_64                                 30/118 
  Installing : libstdc++-devel-4.8.5-36.0.1.el7_6.1.x86_64               31/118 
  Installing : tcp_wrappers-7.6-77.el7.x86_64                            32/118 
  Installing : compat-libstdc++-33-3.2.3-72.el7.x86_64                   33/118 
  Installing : 1:quota-nls-4.01-17.el7.noarch                            34/118 
  Installing : json-c-0.11-4.el7_0.x86_64                                35/118 
  Installing : libX11-common-1.6.5-2.el7.noarch                          36/118 
  Installing : libX11-1.6.5-2.el7.x86_64                                 37/118 
  Installing : libXext-1.3.3-3.el7.x86_64                                38/118 
  Installing : libXi-1.7.9-1.el7.x86_64                                  39/118 
  Installing : libXrender-0.9.10-1.el7.x86_64                            40/118 
  Installing : libXrandr-1.5.1-2.el7.x86_64                              41/118 
  Installing : libXtst-1.2.3-1.el7.x86_64                                42/118 
  Installing : libXxf86dga-1.1.4-2.1.el7.x86_64                          43/118 
  Installing : libdmx-1.1.3-3.el7.x86_64                                 44/118 
  Installing : libXv-1.0.11-1.el7.x86_64                                 45/118 
  Installing : libXxf86misc-1.0.3-7.1.el7.x86_64                         46/118 
  Installing : libXinerama-1.1.3-2.1.el7.x86_64                          47/118 
  Installing : libXxf86vm-1.1.4-1.el7.x86_64                             48/118 
  Installing : xorg-x11-utils-7.5-23.el7.x86_64                          49/118 
  Installing : libXt-1.1.5-3.el7.x86_64                                  50/118 
  Installing : libXmu-1.1.2-2.el7.x86_64                                 51/118 
  Installing : 1:xorg-x11-xauth-1.0.9-1.el7.x86_64                       52/118 
  Installing : 32:bind-license-9.9.4-73.el7_6.noarch                     53/118 
  Installing : 32:bind-libs-9.9.4-73.el7_6.x86_64                        54/118 
  Installing : 32:bind-utils-9.9.4-73.el7_6.x86_64                       55/118 
  Installing : 2:ethtool-4.8-9.el7.x86_64                                56/118 
  Installing : mailx-12.5-19.el7.x86_64                                  57/118 
  Installing : hostname-3.13-3.el7.x86_64                                58/118 
  Installing : libsmartcols-2.23.2-59.el7_6.1.x86_64                     59/118 
  Installing : 2:tar-1.26-35.el7.x86_64                                  60/118 
  Installing : kernel-headers-3.10.0-957.10.1.el7.x86_64                 61/118 
  Installing : glibc-headers-2.17-260.0.15.el7_6.3.x86_64                62/118 
  Installing : glibc-devel-2.17-260.0.15.el7_6.3.x86_64                  63/118 
  Installing : qrencode-libs-3.4.1-3.el7.x86_64                          64/118 
  Installing : libmnl-1.0.3-7.el7.x86_64                                 65/118 
  Installing : iproute-4.16.0-1.0.1.el7.x86_64                           66/118 
  Installing : 1:hardlink-1.0-19.el7.x86_64                              67/118 
  Installing : keyutils-1.5.8-3.el7.x86_64                               68/118 
  Installing : libnfsidmap-0.25-19.el7.x86_64                            69/118 
  Installing : lm_sensors-libs-3.4.0-6.20160601gitf9185e5.el7.x86_64     70/118 
  Installing : psmisc-22.20-15.el7.x86_64                                71/118 
  Installing : kmod-libs-20-23.0.1.el7.x86_64                            72/118 
  Installing : libedit-3.0-12.20121213cvs.el7.x86_64                     73/118 
  Installing : libutempter-1.1.6-4.el7.x86_64                            74/118 
  Installing : unzip-6.0-19.el7.x86_64                                   75/118 
  Installing : libpath_utils-0.2.1-32.el7.x86_64                         76/118 
  Installing : libini_config-1.3.1-32.el7.x86_64                         77/118 
  Installing : e2fsprogs-libs-1.42.9-13.el7.x86_64                       78/118 
  Installing : compat-libcap1-1.10-7.el7.x86_64                          79/118 
  Installing : 1:pkgconfig-0.27.1-4.el7.x86_64                           80/118 
  Installing : acl-2.2.51-14.el7.x86_64                                  81/118 
  Installing : 7:device-mapper-1.02.149-10.0.1.el7_6.3.x86_64            82/118 
  Installing : kpartx-0.4.9-123.el7.x86_64                               83/118 
  Installing : procps-ng-3.3.10-23.el7.x86_64                            84/118 
  Installing : util-linux-2.23.2-59.el7_6.1.x86_64                       85/118 
  Installing : 7:device-mapper-libs-1.02.149-10.0.1.el7_6.3.x86_64       86/118 
  Installing : cryptsetup-libs-2.0.3-3.el7.x86_64                        87/118 
  Installing : dracut-033-554.0.3.el7.x86_64                             88/118 
  Installing : kmod-20-23.0.1.el7.x86_64                                 89/118 
  Installing : elfutils-libs-0.172-2.el7.x86_64                          90/118 
  Installing : systemd-libs-219-62.0.4.el7_6.5.x86_64                    91/118 
  Installing : 1:dbus-libs-1.10.24-13.0.1.el7_6.x86_64                   92/118 
  Installing : systemd-219-62.0.4.el7_6.5.x86_64                         93/118 
Failed to get D-Bus connection: Operation not permitted
  Installing : elfutils-default-yama-scope-0.172-2.el7.noarch            94/118 
  Installing : 1:dbus-1.10.24-13.0.1.el7_6.x86_64                        95/118 
  Installing : crontabs-1.11-6.20121102git.el7.noarch                    96/118 
  Installing : cronie-anacron-1.4.11-20.el7_6.x86_64                     97/118 
  Installing : cronie-1.4.11-20.el7_6.x86_64                             98/118 
  Installing : systemd-sysv-219-62.0.4.el7_6.5.x86_64                    99/118 
  Installing : rpcbind-0.2.0-47.el7.x86_64                              100/118 
  Installing : 1:quota-4.01-17.el7.x86_64                               101/118 
  Installing : sysstat-10.1.5-17.el7.x86_64                             102/118 
  Installing : 1:smartmontools-6.5-1.el7.x86_64                         103/118 
  Installing : iputils-20160308-10.el7.x86_64                           104/118 
  Installing : gssproxy-0.7.0-21.el7.x86_64                             105/118 
  Installing : 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64                    106/118 
  Installing : ksh-20120801-139.0.1.el7.x86_64                          107/118 
failed to link /usr/share/man/man1/ksh.1.gz -> /etc/alternatives/ksh-man: No such file or directory
  Installing : net-tools-2.0-0.24.20131004git.el7.x86_64                108/118 
  Installing : openssh-7.4p1-16.el7.x86_64                              109/118 
  Installing : openssh-clients-7.4p1-16.el7.x86_64                      110/118 
  Installing : sysvinit-tools-2.88-14.dsf.el7.x86_64                    111/118 
  Installing : initscripts-9.49.46-1.0.1.el7.x86_64                     112/118 
  Installing : oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_   113/118 
  Installing : 1:openssl-1.0.2k-16.0.1.el7_6.1.x86_64                   114/118 
  Cleanup    : libmount-2.23.2-59.el7.x86_64                            115/118 
  Cleanup    : libblkid-2.23.2-59.el7.x86_64                            116/118 
  Cleanup    : libuuid-2.23.2-59.el7.x86_64                             117/118 
  Cleanup    : libstdc++-4.8.5-36.0.1.el7.x86_64                        118/118 
  Verifying  : fipscheck-lib-1.4.1-6.el7.x86_64                           1/118 
  Verifying  : libXext-1.3.3-3.el7.x86_64                                 2/118 
  Verifying  : libXi-1.7.9-1.el7.x86_64                                   3/118 
  Verifying  : systemd-sysv-219-62.0.4.el7_6.5.x86_64                     4/118 
  Verifying  : libXrender-0.9.10-1.el7.x86_64                             5/118 
  Verifying  : libblkid-2.23.2-59.el7_6.1.x86_64                          6/118 
  Verifying  : binutils-2.27-34.base.0.1.el7.x86_64                       7/118 
  Verifying  : sysvinit-tools-2.88-14.dsf.el7.x86_64                      8/118 
  Verifying  : acl-2.2.51-14.el7.x86_64                                   9/118 
  Verifying  : systemd-219-62.0.4.el7_6.5.x86_64                         10/118 
  Verifying  : cryptsetup-libs-2.0.3-3.el7.x86_64                        11/118 
  Verifying  : GeoIP-1.5.0-13.el7.x86_64                                 12/118 
  Verifying  : 1:pkgconfig-0.27.1-4.el7.x86_64                           13/118 
  Verifying  : 1:openssl-1.0.2k-16.0.1.el7_6.1.x86_64                    14/118 
  Verifying  : crontabs-1.11-6.20121102git.el7.noarch                    15/118 
  Verifying  : fipscheck-1.4.1-6.el7.x86_64                              16/118 
  Verifying  : compat-libcap1-1.10-7.el7.x86_64                          17/118 
  Verifying  : 1:make-3.82-23.el7.x86_64                                 18/118 
  Verifying  : cronie-anacron-1.4.11-20.el7_6.x86_64                     19/118 
  Verifying  : e2fsprogs-libs-1.42.9-13.el7.x86_64                       20/118 
  Verifying  : libpath_utils-0.2.1-32.el7.x86_64                         21/118 
  Verifying  : openssh-7.4p1-16.el7.x86_64                               22/118 
  Verifying  : cracklib-dicts-2.9.0-11.el7.x86_64                        23/118 
  Verifying  : tcp_wrappers-libs-7.6-77.el7.x86_64                       24/118 
  Verifying  : libXau-1.0.8-2.1.el7.x86_64                               25/118 
  Verifying  : 1:smartmontools-6.5-1.el7.x86_64                          26/118 
  Verifying  : libXxf86dga-1.1.4-2.1.el7.x86_64                          27/118 
  Verifying  : unzip-6.0-19.el7.x86_64                                   28/118 
  Verifying  : libutempter-1.1.6-4.el7.x86_64                            29/118 
  Verifying  : 1:dbus-libs-1.10.24-13.0.1.el7_6.x86_64                   30/118 
  Verifying  : iproute-4.16.0-1.0.1.el7.x86_64                           31/118 
  Verifying  : libdmx-1.1.3-3.el7.x86_64                                 32/118 
  Verifying  : libICE-1.0.9-9.el7.x86_64                                 33/118 
  Verifying  : libedit-3.0-12.20121213cvs.el7.x86_64                     34/118 
  Verifying  : kmod-libs-20-23.0.1.el7.x86_64                            35/118 
  Verifying  : libbasicobjects-0.1.1-32.el7.x86_64                       36/118 
  Verifying  : libmount-2.23.2-59.el7_6.1.x86_64                         37/118 
  Verifying  : tcp_wrappers-7.6-77.el7.x86_64                            38/118 
  Verifying  : xorg-x11-utils-7.5-23.el7.x86_64                          39/118 
  Verifying  : libXtst-1.2.3-1.el7.x86_64                                40/118 
  Verifying  : 1:nfs-utils-1.3.0-0.61.0.1.el7.x86_64                     41/118 
  Verifying  : iputils-20160308-10.el7.x86_64                            42/118 
  Verifying  : psmisc-22.20-15.el7.x86_64                                43/118 
  Verifying  : lm_sensors-libs-3.4.0-6.20160601gitf9185e5.el7.x86_64     44/118 
  Verifying  : rpcbind-0.2.0-47.el7.x86_64                               45/118 
  Verifying  : 32:bind-utils-9.9.4-73.el7_6.x86_64                       46/118 
  Verifying  : libref_array-0.1.5-32.el7.x86_64                          47/118 
  Verifying  : elfutils-libs-0.172-2.el7.x86_64                          48/118 
  Verifying  : 1:xorg-x11-xauth-1.0.9-1.el7.x86_64                       49/118 
  Verifying  : libnfsidmap-0.25-19.el7.x86_64                            50/118 
  Verifying  : libuser-0.60-9.el7.x86_64                                 51/118 
  Verifying  : libXmu-1.1.2-2.el7.x86_64                                 52/118 
  Verifying  : keyutils-1.5.8-3.el7.x86_64                               53/118 
  Verifying  : 1:hardlink-1.0-19.el7.x86_64                              54/118 
  Verifying  : cracklib-2.9.0-11.el7.x86_64                              55/118 
  Verifying  : initscripts-9.49.46-1.0.1.el7.x86_64                      56/118 
  Verifying  : cronie-1.4.11-20.el7_6.x86_64                             57/118 
  Verifying  : 7:device-mapper-1.02.149-10.0.1.el7_6.3.x86_64            58/118 
  Verifying  : libmnl-1.0.3-7.el7.x86_64                                 59/118 
  Verifying  : gzip-1.5-10.el7.x86_64                                    60/118 
  Verifying  : libXv-1.0.11-1.el7.x86_64                                 61/118 
  Verifying  : libXxf86misc-1.0.3-7.1.el7.x86_64                         62/118 
  Verifying  : libstdc++-4.8.5-36.0.1.el7_6.1.x86_64                     63/118 
  Verifying  : libstdc++-devel-4.8.5-36.0.1.el7_6.1.x86_64               64/118 
  Verifying  : sysstat-10.1.5-17.el7.x86_64                              65/118 
  Verifying  : glibc-devel-2.17-260.0.15.el7_6.3.x86_64                  66/118 
  Verifying  : qrencode-libs-3.4.1-3.el7.x86_64                          67/118 
  Verifying  : kernel-headers-3.10.0-957.10.1.el7.x86_64                 68/118 
  Verifying  : libaio-0.3.109-13.el7.x86_64                              69/118 
  Verifying  : elfutils-default-yama-scope-0.172-2.el7.noarch            70/118 
  Verifying  : libevent-2.0.21-4.el7.x86_64                              71/118 
  Verifying  : gssproxy-0.7.0-21.el7.x86_64                              72/118 
  Verifying  : libXrandr-1.5.1-2.el7.x86_64                              73/118 
  Verifying  : libaio-devel-0.3.109-13.el7.x86_64                        74/118 
  Verifying  : 2:tar-1.26-35.el7.x86_64                                  75/118 
  Verifying  : libsmartcols-2.23.2-59.el7_6.1.x86_64                     76/118 
  Verifying  : 1:quota-4.01-17.el7.x86_64                                77/118 
  Verifying  : hostname-3.13-3.el7.x86_64                                78/118 
  Verifying  : ksh-20120801-139.0.1.el7.x86_64                           79/118 
  Verifying  : libuuid-2.23.2-59.el7_6.1.x86_64                          80/118 
  Verifying  : pam-1.1.8-22.el7.x86_64                                   81/118 
  Verifying  : libSM-1.2.2-2.el7.x86_64                                  82/118 
  Verifying  : 32:bind-libs-9.9.4-73.el7_6.x86_64                        83/118 
  Verifying  : openssh-clients-7.4p1-16.el7.x86_64                       84/118 
  Verifying  : 7:device-mapper-libs-1.02.149-10.0.1.el7_6.3.x86_64       85/118 
  Verifying  : mailx-12.5-19.el7.x86_64                                  86/118 
  Verifying  : lz4-1.7.5-2.0.1.el7.x86_64                                87/118 
  Verifying  : 2:ethtool-4.8-9.el7.x86_64                                88/118 
  Verifying  : libtirpc-0.2.4-0.15.el7.x86_64                            89/118 
  Verifying  : kpartx-0.4.9-123.el7.x86_64                               90/118 
  Verifying  : 32:bind-license-9.9.4-73.el7_6.noarch                     91/118 
  Verifying  : oracle-database-server-12cR2-preinstall-1.0-4.el7.x86_    92/118 
  Verifying  : libXinerama-1.1.3-2.1.el7.x86_64                          93/118 
  Verifying  : libXxf86vm-1.1.4-1.el7.x86_64                             94/118 
  Verifying  : dracut-033-554.0.3.el7.x86_64                             95/118 
  Verifying  : util-linux-2.23.2-59.el7_6.1.x86_64                       96/118 
  Verifying  : kmod-20-23.0.1.el7.x86_64                                 97/118 
  Verifying  : libXt-1.1.5-3.el7.x86_64                                  98/118 
  Verifying  : libX11-common-1.6.5-2.el7.noarch                          99/118 
  Verifying  : procps-ng-3.3.10-23.el7.x86_64                           100/118 
  Verifying  : 1:dbus-1.10.24-13.0.1.el7_6.x86_64                       101/118 
  Verifying  : libxcb-1.13-1.el7.x86_64                                 102/118 
  Verifying  : libini_config-1.3.1-32.el7.x86_64                        103/118 
  Verifying  : json-c-0.11-4.el7_0.x86_64                               104/118 
  Verifying  : 1:quota-nls-4.01-17.el7.noarch                           105/118 
  Verifying  : net-tools-2.0-0.24.20131004git.el7.x86_64                106/118 
  Verifying  : xz-5.2.2-1.el7.x86_64                                    107/118 
  Verifying  : libX11-1.6.5-2.el7.x86_64                                108/118 
  Verifying  : libverto-libevent-0.2.5-4.el7.x86_64                     109/118 
  Verifying  : libcollection-0.7.0-32.el7.x86_64                        110/118 
  Verifying  : systemd-libs-219-62.0.4.el7_6.5.x86_64                   111/118 
  Verifying  : glibc-headers-2.17-260.0.15.el7_6.3.x86_64               112/118 
  Verifying  : compat-libstdc++-33-3.2.3-72.el7.x86_64                  113/118 
  Verifying  : libpwquality-1.2.3-5.el7.x86_64                          114/118 
  Verifying  : libstdc++-4.8.5-36.0.1.el7.x86_64                        115/118 
  Verifying  : libuuid-2.23.2-59.el7.x86_64                             116/118 
  Verifying  : libblkid-2.23.2-59.el7.x86_64                            117/118 
  Verifying  : libmount-2.23.2-59.el7.x86_64                            118/118 

Installed:
  openssl.x86_64 1:1.0.2k-16.0.1.el7_6.1                                        
  oracle-database-server-12cR2-preinstall.x86_64 0:1.0-4.el7                    

Dependency Installed:
  GeoIP.x86_64 0:1.5.0-13.el7                                                   
  acl.x86_64 0:2.2.51-14.el7                                                    
  bind-libs.x86_64 32:9.9.4-73.el7_6                                            
  bind-license.noarch 32:9.9.4-73.el7_6                                         
  bind-utils.x86_64 32:9.9.4-73.el7_6                                           
  binutils.x86_64 0:2.27-34.base.0.1.el7                                        
  compat-libcap1.x86_64 0:1.10-7.el7                                            
  compat-libstdc++-33.x86_64 0:3.2.3-72.el7                                     
  cracklib.x86_64 0:2.9.0-11.el7                                                
  cracklib-dicts.x86_64 0:2.9.0-11.el7                                          
  cronie.x86_64 0:1.4.11-20.el7_6                                               
  cronie-anacron.x86_64 0:1.4.11-20.el7_6                                       
  crontabs.noarch 0:1.11-6.20121102git.el7                                      
  cryptsetup-libs.x86_64 0:2.0.3-3.el7                                          
  dbus.x86_64 1:1.10.24-13.0.1.el7_6                                            
  dbus-libs.x86_64 1:1.10.24-13.0.1.el7_6                                       
  device-mapper.x86_64 7:1.02.149-10.0.1.el7_6.3                                
  device-mapper-libs.x86_64 7:1.02.149-10.0.1.el7_6.3                           
  dracut.x86_64 0:033-554.0.3.el7                                               
  e2fsprogs-libs.x86_64 0:1.42.9-13.el7                                         
  elfutils-default-yama-scope.noarch 0:0.172-2.el7                              
  elfutils-libs.x86_64 0:0.172-2.el7                                            
  ethtool.x86_64 2:4.8-9.el7                                                    
  fipscheck.x86_64 0:1.4.1-6.el7                                                
  fipscheck-lib.x86_64 0:1.4.1-6.el7                                            
  glibc-devel.x86_64 0:2.17-260.0.15.el7_6.3                                    
  glibc-headers.x86_64 0:2.17-260.0.15.el7_6.3                                  
  gssproxy.x86_64 0:0.7.0-21.el7                                                
  gzip.x86_64 0:1.5-10.el7                                                      
  hardlink.x86_64 1:1.0-19.el7                                                  
  hostname.x86_64 0:3.13-3.el7                                                  
  initscripts.x86_64 0:9.49.46-1.0.1.el7                                        
  iproute.x86_64 0:4.16.0-1.0.1.el7                                             
  iputils.x86_64 0:20160308-10.el7                                              
  json-c.x86_64 0:0.11-4.el7_0                                                  
  kernel-headers.x86_64 0:3.10.0-957.10.1.el7                                   
  keyutils.x86_64 0:1.5.8-3.el7                                                 
  kmod.x86_64 0:20-23.0.1.el7                                                   
  kmod-libs.x86_64 0:20-23.0.1.el7                                              
  kpartx.x86_64 0:0.4.9-123.el7                                                 
  ksh.x86_64 0:20120801-139.0.1.el7                                             
  libICE.x86_64 0:1.0.9-9.el7                                                   
  libSM.x86_64 0:1.2.2-2.el7                                                    
  libX11.x86_64 0:1.6.5-2.el7                                                   
  libX11-common.noarch 0:1.6.5-2.el7                                            
  libXau.x86_64 0:1.0.8-2.1.el7                                                 
  libXext.x86_64 0:1.3.3-3.el7                                                  
  libXi.x86_64 0:1.7.9-1.el7                                                    
  libXinerama.x86_64 0:1.1.3-2.1.el7                                            
  libXmu.x86_64 0:1.1.2-2.el7                                                   
  libXrandr.x86_64 0:1.5.1-2.el7                                                
  libXrender.x86_64 0:0.9.10-1.el7                                              
  libXt.x86_64 0:1.1.5-3.el7                                                    
  libXtst.x86_64 0:1.2.3-1.el7                                                  
  libXv.x86_64 0:1.0.11-1.el7                                                   
  libXxf86dga.x86_64 0:1.1.4-2.1.el7                                            
  libXxf86misc.x86_64 0:1.0.3-7.1.el7                                           
  libXxf86vm.x86_64 0:1.1.4-1.el7                                               
  libaio.x86_64 0:0.3.109-13.el7                                                
  libaio-devel.x86_64 0:0.3.109-13.el7                                          
  libbasicobjects.x86_64 0:0.1.1-32.el7                                         
  libcollection.x86_64 0:0.7.0-32.el7                                           
  libdmx.x86_64 0:1.1.3-3.el7                                                   
  libedit.x86_64 0:3.0-12.20121213cvs.el7                                       
  libevent.x86_64 0:2.0.21-4.el7                                                
  libini_config.x86_64 0:1.3.1-32.el7                                           
  libmnl.x86_64 0:1.0.3-7.el7                                                   
  libnfsidmap.x86_64 0:0.25-19.el7                                              
  libpath_utils.x86_64 0:0.2.1-32.el7                                           
  libpwquality.x86_64 0:1.2.3-5.el7                                             
  libref_array.x86_64 0:0.1.5-32.el7                                            
  libsmartcols.x86_64 0:2.23.2-59.el7_6.1                                       
  libstdc++-devel.x86_64 0:4.8.5-36.0.1.el7_6.1                                 
  libtirpc.x86_64 0:0.2.4-0.15.el7                                              
  libuser.x86_64 0:0.60-9.el7                                                   
  libutempter.x86_64 0:1.1.6-4.el7                                              
  libverto-libevent.x86_64 0:0.2.5-4.el7                                        
  libxcb.x86_64 0:1.13-1.el7                                                    
  lm_sensors-libs.x86_64 0:3.4.0-6.20160601gitf9185e5.el7                       
  lz4.x86_64 0:1.7.5-2.0.1.el7                                                  
  mailx.x86_64 0:12.5-19.el7                                                    
  make.x86_64 1:3.82-23.el7                                                     
  net-tools.x86_64 0:2.0-0.24.20131004git.el7                                   
  nfs-utils.x86_64 1:1.3.0-0.61.0.1.el7                                         
  openssh.x86_64 0:7.4p1-16.el7                                                 
  openssh-clients.x86_64 0:7.4p1-16.el7                                         
  pam.x86_64 0:1.1.8-22.el7                                                     
  pkgconfig.x86_64 1:0.27.1-4.el7                                               
  procps-ng.x86_64 0:3.3.10-23.el7                                              
  psmisc.x86_64 0:22.20-15.el7                                                  
  qrencode-libs.x86_64 0:3.4.1-3.el7                                            
  quota.x86_64 1:4.01-17.el7                                                    
  quota-nls.noarch 1:4.01-17.el7                                                
  rpcbind.x86_64 0:0.2.0-47.el7                                                 
  smartmontools.x86_64 1:6.5-1.el7                                              
  sysstat.x86_64 0:10.1.5-17.el7                                                
  systemd.x86_64 0:219-62.0.4.el7_6.5                                           
  systemd-libs.x86_64 0:219-62.0.4.el7_6.5                                      
  systemd-sysv.x86_64 0:219-62.0.4.el7_6.5                                      
  sysvinit-tools.x86_64 0:2.88-14.dsf.el7                                       
  tar.x86_64 2:1.26-35.el7                                                      
  tcp_wrappers.x86_64 0:7.6-77.el7                                              
  tcp_wrappers-libs.x86_64 0:7.6-77.el7                                         
  unzip.x86_64 0:6.0-19.el7                                                     
  util-linux.x86_64 0:2.23.2-59.el7_6.1                                         
  xorg-x11-utils.x86_64 0:7.5-23.el7                                            
  xorg-x11-xauth.x86_64 1:1.0.9-1.el7                                           
  xz.x86_64 0:5.2.2-1.el7                                                       

Dependency Updated:
  libblkid.x86_64 0:2.23.2-59.el7_6.1      libmount.x86_64 0:2.23.2-59.el7_6.1 
  libstdc++.x86_64 0:4.8.5-36.0.1.el7_6.1  libuuid.x86_64 0:2.23.2-59.el7_6.1  

Complete!
Removing intermediate container 503e0d74a5ad
 ---> 9faeea638413
Step 8/24 : FROM base AS builder
 ---> 9faeea638413
Step 9/24 : ARG DB_EDITION
 ---> Running in fe4c7f25bdbf
Removing intermediate container fe4c7f25bdbf
 ---> 3924ae9bd43a
Step 10/24 : RUN yum -y install unzip
 ---> Running in 5595c04f89fa
Loaded plugins: ovl
Package unzip-6.0-19.el7.x86_64 already installed and latest version
Nothing to do
Removing intermediate container 5595c04f89fa
 ---> ebd1e3f76779
Step 11/24 : COPY --chown=oracle:dba $INSTALL_FILE_1 $INSTALL_RSP $INSTALL_DB_BINARIES_FILE $INSTALL_DIR/
 ---> 70ced06d5696
Step 12/24 : USER oracle
 ---> Running in 84a262c64c49
Removing intermediate container 84a262c64c49
 ---> 840d7579f71a
Step 13/24 : RUN chmod ug+x $INSTALL_DIR/*.sh &&     sync &&     $INSTALL_DIR/$INSTALL_DB_BINARIES_FILE $DB_EDITION
 ---> Running in d2a09a9caaf5
Archive:  linuxx64_12201_database.zip
   creating: database/
   creating: database/sshsetup/
  inflating: database/sshsetup/sshUserSetup.sh  
   creating: database/rpm/
  inflating: database/rpm/cvuqdisk-1.0.10-1.rpm  
   creating: database/response/
  inflating: database/response/dbca.rsp  
  inflating: database/response/netca.rsp  
  inflating: database/response/db_install.rsp  
   creating: database/install/
  inflating: database/install/detachHome.sh  
 extracting: database/install/addLangs.sh  
   creating: database/install/images/
  inflating: database/install/images/billboards.gif  
  inflating: database/install/unzip  
  inflating: database/install/runInstaller.sh  
  inflating: database/install/clusterparam.ini  
  inflating: database/install/oraparam.ini.deinstall  
  inflating: database/install/.oui   
   creating: database/install/resource/
  inflating: database/install/resource/cons_ja.nls  
  inflating: database/install/resource/cons_zh_CN.nls  
  inflating: database/install/resource/cons_ko.nls  
  inflating: database/install/resource/cons_fr.nls  
  inflating: database/install/resource/cons_zh_TW.nls  
  inflating: database/install/resource/cons.nls  
  inflating: database/install/resource/cons_de.nls  
  inflating: database/install/resource/cons_it.nls  
  inflating: database/install/resource/cons_es.nls  
  inflating: database/install/resource/cons_pt_BR.nls  
  inflating: database/install/oraparam.ini  
  inflating: database/install/attachHome.sh  
  inflating: database/install/lsnodes  
  inflating: database/welcome.html   
   creating: database/stage/
  inflating: database/stage/invDetails.properties  
  inflating: database/stage/shiphomeproperties.xml  
   creating: database/stage/ComponentList/
  inflating: database/stage/ComponentList/oracle.server_Custom.xml  
  inflating: database/stage/ComponentList/oracle.server_SE.xml  
  inflating: database/stage/ComponentList/oracle.server_EE.xml  
  inflating: database/stage/config.xml  
  inflating: database/stage/OuiConfigVariables.xml  
   creating: database/stage/Actions/
   creating: database/stage/Actions/jarActions/
   creating: database/stage/Actions/jarActions/10.2.0.0.0/
   creating: database/stage/Actions/jarActions/10.2.0.0.0/1/
  inflating: database/stage/Actions/jarActions/10.2.0.0.0/1/jarActionLib.jar  
   creating: database/stage/Actions/customFileActions/
   creating: database/stage/Actions/customFileActions/1.2.1/
   creating: database/stage/Actions/customFileActions/1.2.1/1/
  inflating: database/stage/Actions/customFileActions/1.2.1/1/customFileActions.jar  
   creating: database/stage/Actions/ntGrpActionLib/
   creating: database/stage/Actions/ntGrpActionLib/10.2.0.1.0/
   creating: database/stage/Actions/ntGrpActionLib/10.2.0.1.0/1/
  inflating: database/stage/Actions/ntGrpActionLib/10.2.0.1.0/1/ntGrpActionLib.jar  
   creating: database/stage/Actions/ServiceProcessActions/
   creating: database/stage/Actions/ServiceProcessActions/1.0/
   creating: database/stage/Actions/ServiceProcessActions/1.0/1/
  inflating: database/stage/Actions/ServiceProcessActions/1.0/1/ServiceProcessActions.jar  
   creating: database/stage/Actions/fileActions/
   creating: database/stage/Actions/fileActions/12.2.0.1.1/
   creating: database/stage/Actions/fileActions/12.2.0.1.1/1/
  inflating: database/stage/Actions/fileActions/12.2.0.1.1/1/fileActionLib.jar  
   creating: database/stage/Actions/unixActions/
   creating: database/stage/Actions/unixActions/10.2.0.3.0/
   creating: database/stage/Actions/unixActions/10.2.0.3.0/1/
  inflating: database/stage/Actions/unixActions/10.2.0.3.0/1/unixActions.jar  
   creating: database/stage/Actions/textFileActions/
   creating: database/stage/Actions/textFileActions/2.1.0.3.1/
   creating: database/stage/Actions/textFileActions/2.1.0.3.1/1/
  inflating: database/stage/Actions/textFileActions/2.1.0.3.1/1/textFileActions.jar  
   creating: database/stage/Actions/launchPadActions/
   creating: database/stage/Actions/launchPadActions/10.1.0.2.0/
   creating: database/stage/Actions/launchPadActions/10.1.0.2.0/1/
  inflating: database/stage/Actions/launchPadActions/10.1.0.2.0/1/launchpadaction.jar  
   creating: database/stage/Actions/aclActions/
   creating: database/stage/Actions/aclActions/12.2.0.1.0/
   creating: database/stage/Actions/aclActions/12.2.0.1.0/1/
  inflating: database/stage/Actions/aclActions/12.2.0.1.0/1/aclActionsLib.jar  
   creating: database/stage/Actions/ntServicesActions/
   creating: database/stage/Actions/ntServicesActions/10.2.0.6.0/
   creating: database/stage/Actions/ntServicesActions/10.2.0.6.0/1/
  inflating: database/stage/Actions/ntServicesActions/10.2.0.6.0/1/ntServicesActions.jar  
   creating: database/stage/Actions/ntCrsActionLib/
   creating: database/stage/Actions/ntCrsActionLib/10.2.0.1.0/
   creating: database/stage/Actions/ntCrsActionLib/10.2.0.1.0/1/
  inflating: database/stage/Actions/ntCrsActionLib/10.2.0.1.0/1/ntCrsActionLib.jar  
   creating: database/stage/Actions/wingeneralActions/
   creating: database/stage/Actions/wingeneralActions/10.2.0.1.0/
   creating: database/stage/Actions/wingeneralActions/10.2.0.1.0/1/
  inflating: database/stage/Actions/wingeneralActions/10.2.0.1.0/1/wingeneralActionLib.jar  
   creating: database/stage/Actions/Regsvr32ActionsLib/
   creating: database/stage/Actions/Regsvr32ActionsLib/11.2.0.3.0/
   creating: database/stage/Actions/Regsvr32ActionsLib/11.2.0.3.0/1/
  inflating: database/stage/Actions/Regsvr32ActionsLib/11.2.0.3.0/1/Regsvr32ActionsLib.jar  
   creating: database/stage/Actions/oradim/
   creating: database/stage/Actions/oradim/10.1.0.3.0/
   creating: database/stage/Actions/oradim/10.1.0.3.0/1/
  inflating: database/stage/Actions/oradim/10.1.0.3.0/1/oradim.jar  
   creating: database/stage/Actions/ntActionLib/
   creating: database/stage/Actions/ntActionLib/11.1.0.0.0/
   creating: database/stage/Actions/ntActionLib/11.1.0.0.0/1/
  inflating: database/stage/Actions/ntActionLib/11.1.0.0.0/1/ntActionLib.jar  
   creating: database/stage/Actions/w32OcxRegActions/
   creating: database/stage/Actions/w32OcxRegActions/10.2.0.1.0/
   creating: database/stage/Actions/w32OcxRegActions/10.2.0.1.0/1/
  inflating: database/stage/Actions/w32OcxRegActions/10.2.0.1.0/1/w32OcxLib.jar  
   creating: database/stage/Actions/w32RegActions/
   creating: database/stage/Actions/w32RegActions/10.2.0.1.0/
   creating: database/stage/Actions/w32RegActions/10.2.0.1.0/1/
  inflating: database/stage/Actions/w32RegActions/10.2.0.1.0/1/w32RegActionLib.jar  
   creating: database/stage/Actions/dbActions/
   creating: database/stage/Actions/dbActions/10.1.0.2.0/
   creating: database/stage/Actions/dbActions/10.1.0.2.0/1/
  inflating: database/stage/Actions/dbActions/10.1.0.2.0/1/dbActions.jar  
   creating: database/stage/Actions/clusterActions/
   creating: database/stage/Actions/clusterActions/10.1.0.2.0/
   creating: database/stage/Actions/clusterActions/10.1.0.2.0/1/
  inflating: database/stage/Actions/clusterActions/10.1.0.2.0/1/clusterActionLib.jar  
   creating: database/stage/Actions/ntw32FoldersActions/
   creating: database/stage/Actions/ntw32FoldersActions/10.2.0.3.0/
   creating: database/stage/Actions/ntw32FoldersActions/10.2.0.3.0/1/
  inflating: database/stage/Actions/ntw32FoldersActions/10.2.0.3.0/1/WindowsFolders.jar  
   creating: database/stage/Actions/generalActions/
   creating: database/stage/Actions/generalActions/10.2.0.9.0/
   creating: database/stage/Actions/generalActions/10.2.0.9.0/1/
  inflating: database/stage/Actions/generalActions/10.2.0.9.0/1/generalActions.jar  
   creating: database/stage/Actions/WindowsActionLib/
   creating: database/stage/Actions/WindowsActionLib/12.0.0.0.0/
   creating: database/stage/Actions/WindowsActionLib/12.0.0.0.0/1/
  inflating: database/stage/Actions/WindowsActionLib/12.0.0.0.0/1/WindowsActionLib.jar  
   creating: database/stage/Actions/SpawnActions/
   creating: database/stage/Actions/SpawnActions/10.1.0.3.4/
   creating: database/stage/Actions/SpawnActions/10.1.0.3.4/1/
  inflating: database/stage/Actions/SpawnActions/10.1.0.3.4/1/spawnActions.jar  
   creating: database/stage/Actions/docActionLib/
   creating: database/stage/Actions/docActionLib/2.2/
   creating: database/stage/Actions/docActionLib/2.2/1/
  inflating: database/stage/Actions/docActionLib/2.2/1/docActionLib.jar  
   creating: database/stage/Actions/rgsActions/
   creating: database/stage/Actions/rgsActions/10.1.0.3.0/
   creating: database/stage/Actions/rgsActions/10.1.0.3.0/1/
  inflating: database/stage/Actions/rgsActions/10.1.0.3.0/1/rgsActions.jar  
  inflating: database/stage/labels.txt  
  inflating: database/stage/libs.xml  
   creating: database/stage/fastcopy/
  inflating: database/stage/fastcopy/oracle.server_Custom_1.xml  
  inflating: database/stage/fastcopy/oracle.server_SE_exp_1.xml  
  inflating: database/stage/fastcopy/oracle.server_EE_exp_1.xml  
  inflating: database/stage/fastcopy/oracle.server_EE_1.xml  
  inflating: database/stage/fastcopy/oracle.server_EE_dirs.lst  
  inflating: database/stage/fastcopy/oracle.server_SE_filemap.jar  
  inflating: database/stage/fastcopy/oracle.server_EE_filemap.jar  
  inflating: database/stage/fastcopy/oracle.server_Custom_filemap.jar  
  inflating: database/stage/fastcopy/setperms1.sh  
  inflating: database/stage/fastcopy/oracle.server_SE_dirs.lst  
  inflating: database/stage/fastcopy/oracle.server_SE_1.xml  
  inflating: database/stage/fastcopy/racfiles.jar  
  inflating: database/stage/fastcopy/oracle.server_Custom_dirs.lst  
  inflating: database/stage/fastcopy/oracle.server_Custom_exp_1.xml  
  inflating: database/stage/oracle.server.12_2_0_1_0_1485446012691.bak  
   creating: database/stage/Queries/
   creating: database/stage/Queries/RunningProcessQuery/
   creating: database/stage/Queries/RunningProcessQuery/12.2.0.1.0/
   creating: database/stage/Queries/RunningProcessQuery/12.2.0.1.0/1/
  inflating: database/stage/Queries/RunningProcessQuery/12.2.0.1.0/1/RunningProcessesQuery.jar  
   creating: database/stage/Queries/OCAQueries/
   creating: database/stage/Queries/OCAQueries/1.0.1/
   creating: database/stage/Queries/OCAQueries/1.0.1/1/
  inflating: database/stage/Queries/OCAQueries/1.0.1/1/OCAQueries.jar  
   creating: database/stage/Queries/textFileQueries/
   creating: database/stage/Queries/textFileQueries/2.1.0.4.0/
   creating: database/stage/Queries/textFileQueries/2.1.0.4.0/1/
  inflating: database/stage/Queries/textFileQueries/2.1.0.4.0/1/textFileQueries.jar  
   creating: database/stage/Queries/fileQueries/
   creating: database/stage/Queries/fileQueries/10.1.0.3.0/
   creating: database/stage/Queries/fileQueries/10.1.0.3.0/1/
  inflating: database/stage/Queries/fileQueries/10.1.0.3.0/1/fileQueries.jar  
   creating: database/stage/Queries/WindowsGeneralQueries/
   creating: database/stage/Queries/WindowsGeneralQueries/10.2.0.1.0/
   creating: database/stage/Queries/WindowsGeneralQueries/10.2.0.1.0/1/
  inflating: database/stage/Queries/WindowsGeneralQueries/10.2.0.1.0/1/WindowsGeneralQueries.jar  
   creating: database/stage/Queries/NLSQueries/
   creating: database/stage/Queries/NLSQueries/12.2.0.1.0/
   creating: database/stage/Queries/NLSQueries/12.2.0.1.0/1/
  inflating: database/stage/Queries/NLSQueries/12.2.0.1.0/1/NLSQueries.jar  
   creating: database/stage/Queries/SIDQueries/
   creating: database/stage/Queries/SIDQueries/1.2.7/
   creating: database/stage/Queries/SIDQueries/1.2.7/1/
  inflating: database/stage/Queries/SIDQueries/1.2.7/1/SidQueries.jar  
   creating: database/stage/Queries/RepositoryQueries/
   creating: database/stage/Queries/RepositoryQueries/3.0.0.2.17/
   creating: database/stage/Queries/RepositoryQueries/3.0.0.2.17/1/
  inflating: database/stage/Queries/RepositoryQueries/3.0.0.2.17/1/Queries.jar  
   creating: database/stage/Queries/HealthCheckQueries/
   creating: database/stage/Queries/HealthCheckQueries/12.2.0.1.0/
   creating: database/stage/Queries/HealthCheckQueries/12.2.0.1.0/1/
  inflating: database/stage/Queries/HealthCheckQueries/12.2.0.1.0/1/HealthCheckQueries.jar  
   creating: database/stage/Queries/DLLQueries/
   creating: database/stage/Queries/DLLQueries/1.1/
   creating: database/stage/Queries/DLLQueries/1.1/1/
  inflating: database/stage/Queries/DLLQueries/1.1/1/DllQueries.jar  
   creating: database/stage/Queries/clusterQueriesEx/
   creating: database/stage/Queries/clusterQueriesEx/10.2.0.1.0/
   creating: database/stage/Queries/clusterQueriesEx/10.2.0.1.0/1/
  inflating: database/stage/Queries/clusterQueriesEx/10.2.0.1.0/1/clusterQuery.jar  
   creating: database/stage/Queries/ccrQueries/
   creating: database/stage/Queries/ccrQueries/10.3.0.1.0/
   creating: database/stage/Queries/ccrQueries/10.3.0.1.0/1/
  inflating: database/stage/Queries/ccrQueries/10.3.0.1.0/1/ccrQueries.jar  
   creating: database/stage/Queries/ClusterPreinstQueries/
   creating: database/stage/Queries/ClusterPreinstQueries/1.2.1/
   creating: database/stage/Queries/ClusterPreinstQueries/1.2.1/1/
  inflating: database/stage/Queries/ClusterPreinstQueries/1.2.1/1/ClusterPreinstQueries.jar  
   creating: database/stage/Queries/NtServicesQueries/
   creating: database/stage/Queries/NtServicesQueries/10.2.0.3.0/
   creating: database/stage/Queries/NtServicesQueries/10.2.0.3.0/1/
  inflating: database/stage/Queries/NtServicesQueries/10.2.0.3.0/1/NtServiceQueries.jar  
   creating: database/stage/Queries/Protocol_Queries/
   creating: database/stage/Queries/Protocol_Queries/1.1.4/
   creating: database/stage/Queries/Protocol_Queries/1.1.4/1/
  inflating: database/stage/Queries/Protocol_Queries/1.1.4/1/ProtocolQueries.jar  
   creating: database/stage/Queries/unixQueries/
   creating: database/stage/Queries/unixQueries/10.1.0.2.0/
   creating: database/stage/Queries/unixQueries/10.1.0.2.0/1/
  inflating: database/stage/Queries/unixQueries/10.1.0.2.0/1/unixQueries.jar  
   creating: database/stage/Queries/cfsprereqQueries/
   creating: database/stage/Queries/cfsprereqQueries/10.2.0.2.0/
   creating: database/stage/Queries/cfsprereqQueries/10.2.0.2.0/1/
  inflating: database/stage/Queries/cfsprereqQueries/10.2.0.2.0/1/cfsprereqQueries.jar  
   creating: database/stage/Queries/SpawnQueries/
   creating: database/stage/Queries/SpawnQueries/1.1.4/
   creating: database/stage/Queries/SpawnQueries/1.1.4/1/
  inflating: database/stage/Queries/SpawnQueries/1.1.4/1/SpawnQueries.jar  
   creating: database/stage/Queries/rgsQueries/
   creating: database/stage/Queries/rgsQueries/10.1.0.3.0/
   creating: database/stage/Queries/rgsQueries/10.1.0.3.0/1/
  inflating: database/stage/Queries/rgsQueries/10.1.0.3.0/1/rgsQueries.jar  
   creating: database/stage/Queries/w32RegQueries/
   creating: database/stage/Queries/w32RegQueries/10.2.0.1.0/
   creating: database/stage/Queries/w32RegQueries/10.2.0.1.0/1/
  inflating: database/stage/Queries/w32RegQueries/10.2.0.1.0/1/w32RegQueryLib.jar  
   creating: database/stage/Queries/OraBase_Queries/
   creating: database/stage/Queries/OraBase_Queries/1.2.1/
   creating: database/stage/Queries/OraBase_Queries/1.2.1/1/
  inflating: database/stage/Queries/OraBase_Queries/1.2.1/1/OraBaseQueries.jar  
   creating: database/stage/Queries/XMLFileQueries/
   creating: database/stage/Queries/XMLFileQueries/2.1.0.4.2/
   creating: database/stage/Queries/XMLFileQueries/2.1.0.4.2/1/
  inflating: database/stage/Queries/XMLFileQueries/2.1.0.4.2/1/XMLFileQueries.jar  
   creating: database/stage/Queries/WinSetAclQuery/
   creating: database/stage/Queries/WinSetAclQuery/1.0.7/
   creating: database/stage/Queries/WinSetAclQuery/1.0.7/1/
  inflating: database/stage/Queries/WinSetAclQuery/1.0.7/1/WinSetAclQuery.jar  
   creating: database/stage/Queries/generalPortQueries/
   creating: database/stage/Queries/generalPortQueries/2.1.0.19.8/
   creating: database/stage/Queries/generalPortQueries/2.1.0.19.8/1/
  inflating: database/stage/Queries/generalPortQueries/2.1.0.19.8/1/generalPortQueries.jar  
   creating: database/stage/Queries/globalVarQueries/
   creating: database/stage/Queries/globalVarQueries/12.2.0.1.0/
   creating: database/stage/Queries/globalVarQueries/12.2.0.1.0/1/
  inflating: database/stage/Queries/globalVarQueries/12.2.0.1.0/1/globalVarQueries.jar  
   creating: database/stage/Queries/PrerequisiteQueries/
   creating: database/stage/Queries/PrerequisiteQueries/1.1.12/
   creating: database/stage/Queries/PrerequisiteQueries/1.1.12/1/
  inflating: database/stage/Queries/PrerequisiteQueries/1.1.12/1/PrerequisiteQueries.jar  
   creating: database/stage/Queries/MemorySizeQuery/
   creating: database/stage/Queries/MemorySizeQuery/1.2.8.0.6/
   creating: database/stage/Queries/MemorySizeQuery/1.2.8.0.6/1/
  inflating: database/stage/Queries/MemorySizeQuery/1.2.8.0.6/1/MemorySizeQuery.jar  
   creating: database/stage/Queries/UtilQueries/
   creating: database/stage/Queries/UtilQueries/12.2.0.1.0/
   creating: database/stage/Queries/UtilQueries/12.2.0.1.0/1/
  inflating: database/stage/Queries/UtilQueries/12.2.0.1.0/1/UtilQueries.jar  
   creating: database/stage/Queries/ClusterQueries/
   creating: database/stage/Queries/ClusterQueries/12.2.0.1.0/
   creating: database/stage/Queries/ClusterQueries/12.2.0.1.0/1/
  inflating: database/stage/Queries/ClusterQueries/12.2.0.1.0/1/ClusterQueries.jar  
   creating: database/stage/Queries/dbQueries/
   creating: database/stage/Queries/dbQueries/10.1.0.2.0/
   creating: database/stage/Queries/dbQueries/10.1.0.2.0/1/
  inflating: database/stage/Queries/dbQueries/10.1.0.2.0/1/dbQueries.jar  
   creating: database/stage/Queries/areasQueries/
   creating: database/stage/Queries/areasQueries/10.2.0.1.0/
   creating: database/stage/Queries/areasQueries/10.2.0.1.0/1/
  inflating: database/stage/Queries/areasQueries/10.2.0.1.0/1/areasQueries.jar  
   creating: database/stage/Queries/generalQueries/
   creating: database/stage/Queries/generalQueries/10.2.0.2.1/
   creating: database/stage/Queries/generalQueries/10.2.0.2.1/1/
  inflating: database/stage/Queries/generalQueries/10.2.0.2.1/1/generalQueries.jar  
   creating: database/stage/Queries/ASMQueries/
   creating: database/stage/Queries/ASMQueries/12.2.0.1.0/
   creating: database/stage/Queries/ASMQueries/12.2.0.1.0/1/
  inflating: database/stage/Queries/ASMQueries/12.2.0.1.0/1/ASMQueries.jar  
   creating: database/stage/Queries/netQueries/
   creating: database/stage/Queries/netQueries/10.2.0.2.0/
   creating: database/stage/Queries/netQueries/10.2.0.2.0/1/
  inflating: database/stage/Queries/netQueries/10.2.0.2.0/1/netQueries.jar  
  inflating: database/stage/fastcopy.xml  
  inflating: database/stage/products.xml  
  inflating: database/stage/productlanguages.properties  
   creating: database/stage/properties/
  inflating: database/stage/properties/Paths.properties  
  inflating: database/stage/properties/ssPaths_sol.properties  
  inflating: database/stage/properties/system.properties  
  inflating: database/stage/properties/ssh_system.properties  
  inflating: database/stage/properties/oracle.server_SE.properties  
  inflating: database/stage/properties/oracle.server_PE.properties.pp  
  inflating: database/stage/properties/ssPaths_aix.properties  
  inflating: database/stage/properties/ssPaths_msplats.properties  
  inflating: database/stage/properties/userPaths.properties  
  inflating: database/stage/properties/ssPaths_solx86.properties  
  inflating: database/stage/properties/ssPaths_decunix.properties  
  inflating: database/stage/properties/oracle.server_EE.properties  
  inflating: database/stage/properties/oracle.server_PE.properties  
  inflating: database/stage/properties/ssPaths_linux_zseries64.properties  
  inflating: database/stage/properties/ssPaths_hpi.properties  
  inflating: database/stage/properties/logging_conf.xml  
  inflating: database/stage/properties/sPaths.properties  
  inflating: database/stage/properties/platformInfo.properties  
  inflating: database/stage/properties/ssPaths_linuxx64.properties  
  inflating: database/stage/properties/ssPaths_linux_ppc64.properties  
  inflating: database/stage/properties/ignoreMessages.txt  
  inflating: database/stage/properties/oracle.server_Custom.properties  
  inflating: database/stage/properties/oracle.server_SE.properties.pp  
  inflating: database/stage/properties/sshConnectivity-usage.txt  
  inflating: database/stage/properties/oracle.server_Custom.properties.pp  
  inflating: database/stage/properties/oracle.server_EE.properties.pp  
  inflating: database/stage/properties/ssPaths_linux_ia64.properties  
  inflating: database/stage/properties/ssPaths_hpuix.properties  
   creating: database/stage/sizes/
  inflating: database/stage/sizes/oracle.server12.2.0.1.0Custom.sizes.properties  
  inflating: database/stage/sizes/oracle.server.12.2.0.1.0.sizes.properties  
  inflating: database/stage/sizes/oracle.server12.2.0.1.0SE.sizes.properties  
  inflating: database/stage/sizes/oracle.server12.2.0.1.0EE.sizes.properties  
 extracting: database/stage/sizes/oracle.server.Custom.sizes.properties  
 extracting: database/stage/sizes/oracle.server.EE.sizes.properties  
 extracting: database/stage/sizes/oracle.server.SE.sizes.properties  
  inflating: database/stage/oracle.server.12_2_0_1_0.xml  
  inflating: database/stage/oracle.server.12_2_0_1_0_dep_default.properties  
   creating: database/stage/Dialogs/
   creating: database/stage/Dialogs/customDialogs/
   creating: database/stage/Dialogs/customDialogs/10.2.0.1.0/
   creating: database/stage/Dialogs/customDialogs/10.2.0.1.0/1/
  inflating: database/stage/Dialogs/customDialogs/10.2.0.1.0/1/customDialogs.jar  
   creating: database/stage/Dialogs/OiDynamicXYSpreadTable/
   creating: database/stage/Dialogs/OiDynamicXYSpreadTable/2.5.0.2.5/
   creating: database/stage/Dialogs/OiDynamicXYSpreadTable/2.5.0.2.5/1/
  inflating: database/stage/Dialogs/OiDynamicXYSpreadTable/2.5.0.2.5/1/OiDynamicXYSpreadTable.jar  
   creating: database/stage/Dialogs/TwoRadioStaticDynamicDialogs/
   creating: database/stage/Dialogs/TwoRadioStaticDynamicDialogs/2.5.0.0.27/
   creating: database/stage/Dialogs/TwoRadioStaticDynamicDialogs/2.5.0.0.27/1/
  inflating: database/stage/Dialogs/TwoRadioStaticDynamicDialogs/2.5.0.0.27/1/TwoRadioStaticDynamic.jar  
   creating: database/stage/Dialogs/standardDialogs/
   creating: database/stage/Dialogs/standardDialogs/10.2.0.1.0/
   creating: database/stage/Dialogs/standardDialogs/10.2.0.1.0/1/
  inflating: database/stage/Dialogs/standardDialogs/10.2.0.1.0/1/standardDialogs.jar  
   creating: database/stage/UserActions/
   creating: database/stage/UserActions/oracle.server/
   creating: database/stage/UserActions/oracle.server/UnixActions/
  inflating: database/stage/UserActions/oracle.server/UnixActions/makedeps.xml  
  inflating: database/stage/UserActions/oracle.server/useractions.properties  
   creating: database/stage/ext/
   creating: database/stage/ext/jlib/
  inflating: database/stage/ext/jlib/opatchsdk.jar  
  inflating: database/stage/ext/jlib/OraPrereqChecks.jar  
  inflating: database/stage/ext/jlib/remoteinterfaces.jar  
  inflating: database/stage/ext/jlib/emca.jar  
  inflating: database/stage/ext/jlib/orai18n-translation.jar  
  inflating: database/stage/ext/jlib/javax.security.jacc_1.0.0.0_1-1.jar  
  inflating: database/stage/ext/jlib/instdb.jar  
  inflating: database/stage/ext/jlib/prov_fixup.jar  
  inflating: database/stage/ext/jlib/entityManager_proxy.jar  
  inflating: database/stage/ext/jlib/cvu.jar  
  inflating: database/stage/ext/jlib/opatch.jar  
  inflating: database/stage/ext/jlib/installcommons_1.0.0b.jar  
  inflating: database/stage/ext/jlib/instcommon.jar  
  inflating: database/stage/ext/jlib/emCoreConsole.jar  
  inflating: database/stage/ext/jlib/orai18n-utility.jar  
  inflating: database/stage/ext/jlib/ojdbc8.jar  
  inflating: database/stage/ext/jlib/ssh.jar  
  inflating: database/stage/ext/jlib/adf-share-ca.jar  
  inflating: database/stage/ext/jlib/wsclient_extended.jar  
  inflating: database/stage/ext/jlib/orai18n-mapping.jar  
  inflating: database/stage/ext/jlib/jsch.jar  
  inflating: database/stage/ext/jlib/OraPrereq.jar  
  inflating: database/stage/ext/jlib/jmxspi.jar  
   creating: database/stage/ext/bin/
  inflating: database/stage/ext/bin/srawutl  
  inflating: database/stage/ext/bin/get_instances_using_asm.sql  
   creating: database/stage/ext/lib/
  inflating: database/stage/ext/lib/libhasgen12.so  
  inflating: database/stage/ext/lib/libociei.so  
  inflating: database/stage/ext/lib/libocr12.so  
  inflating: database/stage/ext/lib/libclntshcore.so.12.1  
  inflating: database/stage/ext/lib/libocrutl12.so  
  inflating: database/stage/ext/lib/libnnz12.so  
  inflating: database/stage/ext/lib/libclntsh.so.12.1  
  inflating: database/stage/ext/lib/libmql1.so  
  inflating: database/stage/ext/lib/libocrb12.so  
  inflating: database/stage/ext/lib/libclsce12.so  
  inflating: database/stage/ext/lib/libasmclntsh12.so  
  inflating: database/stage/ext/lib/libipc1.so  
  inflating: database/stage/ext/lib/libskgxp12.so  
  inflating: database/stage/ext/lib/libcell12.so  
  inflating: database/stage/ext/lib/libskgxn2.so  
  inflating: database/stage/oracle.server.12_2_0_1_0_1485446013558.bak  
   creating: database/stage/cvu/
   creating: database/stage/cvu/jlib/
  inflating: database/stage/cvu/jlib/cvuhelper122.jar  
  inflating: database/stage/cvu/jlib/cvuhelper112.jar  
  inflating: database/stage/cvu/jlib/cvuhelper111.jar  
  inflating: database/stage/cvu/jlib/cvuhelper102.jar  
  inflating: database/stage/cvu/jlib/cvuhelper121.jar  
  inflating: database/stage/cvu/cvu_prereq.xml  
   creating: database/stage/cvu/cv/
   creating: database/stage/cvu/cv/admin/
  inflating: database/stage/cvu/cv/admin/odnsdlite  
  inflating: database/stage/cvu/cv/admin/cvunetquery  
  inflating: database/stage/cvu/cv/admin/cvures.pl  
  inflating: database/stage/cvu/cv/admin/cvunetquery.bin  
  inflating: database/stage/cvu/cv/admin/cvu_config  
  inflating: database/stage/cvu/cv/admin/cvusys.sql  
   creating: database/stage/cvu/cv/cvdata/
  inflating: database/stage/cvu/cv/cvdata/taskfactory.xsd  
  inflating: database/stage/cvu/cv/cvdata/taskplacement.xml  
   creating: database/stage/cvu/cv/cvdata/101/
  inflating: database/stage/cvu/cv/cvdata/101/crsinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/101/Makefile  
  inflating: database/stage/cvu/cv/cvdata/101/dbinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/101/dbcfg_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/101/sihainst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/101/ora_software_cfg.xml  
  inflating: database/stage/cvu/cv/cvdata/cusInfoFile.txt  
   creating: database/stage/cvu/cv/cvdata/121/
  inflating: database/stage/cvu/cv/cvdata/121/crsinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/121/Makefile  
  inflating: database/stage/cvu/cv/cvdata/121/dbinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/121/dbcfg_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/121/sihainst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/121/ora_software_cfg.xml  
  inflating: database/stage/cvu/cv/cvdata/cvuxmlreport.xsd  
  inflating: database/stage/cvu/cv/cvdata/responsevars.xsd  
  inflating: database/stage/cvu/cv/cvdata/cvdata.mk  
  inflating: database/stage/cvu/cv/cvdata/admin.xml  
  inflating: database/stage/cvu/cv/cvdata/Makefile  
  inflating: database/stage/cvu/cv/cvdata/cvu_prereq.xml  
   creating: database/stage/cvu/cv/cvdata/112/
  inflating: database/stage/cvu/cv/cvdata/112/crsinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/112/Makefile  
  inflating: database/stage/cvu/cv/cvdata/112/dbinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/112/dbcfg_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/112/sihainst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/112/ora_software_cfg.xml  
  inflating: database/stage/cvu/cv/cvdata/admin.dtd  
  inflating: database/stage/cvu/cv/cvdata/constraints.xml  
  inflating: database/stage/cvu/cv/cvdata/prereq.xsd  
  inflating: database/stage/cvu/cv/cvdata/taskdefinition.xml  
  inflating: database/stage/cvu/cv/cvdata/relinkedFiles.txt  
  inflating: database/stage/cvu/cv/cvdata/excludedFiles.txt  
  inflating: database/stage/cvu/cv/cvdata/variables.xml  
   creating: database/stage/cvu/cv/cvdata/122/
  inflating: database/stage/cvu/cv/cvdata/122/crsinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/122/Makefile  
  inflating: database/stage/cvu/cv/cvdata/122/dbinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/122/dbcfg_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/122/sihainst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/122/ora_software_cfg.xml  
  inflating: database/stage/cvu/cv/cvdata/constraints.dtd  
   creating: database/stage/cvu/cv/cvdata/111/
  inflating: database/stage/cvu/cv/cvdata/111/crsinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/111/Makefile  
  inflating: database/stage/cvu/cv/cvdata/111/dbinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/111/dbcfg_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/111/sihainst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/111/ora_software_cfg.xml  
  inflating: database/stage/cvu/cv/cvdata/variables.xsd  
  inflating: database/stage/cvu/cv/cvdata/ora_software_cfg.xml  
  inflating: database/stage/cvu/cv/cvdata/cvufixup.xsd  
   creating: database/stage/cvu/cv/cvdata/102/
  inflating: database/stage/cvu/cv/cvdata/102/crsinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/102/Makefile  
  inflating: database/stage/cvu/cv/cvdata/102/dbinst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/102/dbcfg_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/102/sihainst_prereq.xml  
  inflating: database/stage/cvu/cv/cvdata/102/ora_software_cfg.xml  
  inflating: database/stage/cvu/cv/cvdata/responsevars.xml  
   creating: database/stage/cvu/cv/remenv/
   creating: database/stage/cvu/cv/remenv/jlib/
  inflating: database/stage/cvu/cv/remenv/jlib/cvuhelper122.jar  
  inflating: database/stage/cvu/cv/remenv/jlib/cvuhelper112.jar  
  inflating: database/stage/cvu/cv/remenv/jlib/cvuhelper111.jar  
  inflating: database/stage/cvu/cv/remenv/jlib/cvuhelper102.jar  
  inflating: database/stage/cvu/cv/remenv/jlib/cvuhelper121.jar  
  inflating: database/stage/cvu/cv/remenv/orarun.sh  
  inflating: database/stage/cvu/cv/remenv/runfixup.sh  
  inflating: database/stage/cvu/cv/remenv/exectask  
  inflating: database/stage/cvu/cv/remenv/cvuqdisk-1.0.10-1.rpm  
  inflating: database/stage/cvu/cv/remenv/exectask.sh  
  inflating: database/stage/cvu/cv/remenv/cvuhelper  
   creating: database/stage/cvu/cv/remenv/pluggable/
  inflating: database/stage/cvu/cv/remenv/pluggable/check_e1000.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkFSMountOptions.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_network_packet_reassembly.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkGSDResourceStatus.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_symvers.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/mus_check.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_default_gateway.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkIPHostModel.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/shutdown_hwclock_sync.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_network_param.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_vmm.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_bpf_devices.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/hangchecktimer.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/hangcheck_margin.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_rp_filter.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checksyslog.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/ora_07445_errors_analyzer.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/bdump_dest_trace_analyzer.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checktmpfs.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkmemlock.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkIOCPDeviceStatus.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/css_disk_timeout.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/ora_07445_errors_analyzer2.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/bdump_dest_trace_analyzer2.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkhugepage.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkportavail.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/alert_log_file_size_analyzer.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkcorefile.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_tcp_packet_retransmit.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/css_misscount.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/listener_naming_convention.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checksshd.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_vip_restart_attempt.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/alert_log_file_size_analyzer2.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/remove_cvuresource_baseline.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/core_dump_dest_analyzer.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/css_diagwait.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/zeroconf.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/getNICSpeed.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_network_bonding.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_jumbo_frames.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/ora_00600_errors_analyzer2.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/hangcheck_tick.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_dax_access.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/css_reboot_time.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_non_routable_network_interconnect.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/core_dump_dest_analyzer2.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_ofsctl.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/checkLogindIPCSettings.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/localenam_check.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_afd_drivers.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/ora_00600_errors_analyzer.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/hangcheck_reboot.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/common_include.sh  
  inflating: database/stage/cvu/cv/remenv/pluggable/check_disk_asynch_io_linking.sh  
  inflating: database/stage/TopLevel_UserAction.properties  
   creating: database/stage/Components/
   creating: database/stage/Components/oracle.jdk/
   creating: database/stage/Components/oracle.jdk/1.8.0.91.0/
   creating: database/stage/Components/oracle.jdk/1.8.0.91.0/1/
   creating: database/stage/Components/oracle.jdk/1.8.0.91.0/1/DataFiles/
  inflating: database/stage/Components/oracle.jdk/1.8.0.91.0/1/DataFiles/filegroup6.jar  
  inflating: database/stage/Components/oracle.jdk/1.8.0.91.0/1/DataFiles/filegroup4.jar  
  inflating: database/stage/Components/oracle.jdk/1.8.0.91.0/1/DataFiles/filegroup5.jar  
  inflating: database/stage/Components/oracle.jdk/1.8.0.91.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.jdk/1.8.0.91.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.jdk/1.8.0.91.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.ctx/
   creating: database/stage/Components/oracle.ctx/12.2.0.1.0/
   creating: database/stage/Components/oracle.ctx/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ctx/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ctx/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ctx/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.ctx/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.install.deinstalltool/
   creating: database/stage/Components/oracle.install.deinstalltool/12.2.0.1.0/
   creating: database/stage/Components/oracle.install.deinstalltool/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.install.deinstalltool/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.install.deinstalltool/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.install.deinstalltool/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.install.deinstalltool/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.ldap.security.osdt/
   creating: database/stage/Components/oracle.ldap.security.osdt/12.2.0.1.0/
   creating: database/stage/Components/oracle.ldap.security.osdt/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ldap.security.osdt/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ldap.security.osdt/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ldap.security.osdt/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.ldap.security.osdt/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.dbjava.server/
   creating: database/stage/Components/oracle.dbjava.server/12.2.0.1.0/
   creating: database/stage/Components/oracle.dbjava.server/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.dbjava.server/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.dbjava.server/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ordim.rdbms/
   creating: database/stage/Components/oracle.ordim.rdbms/12.2.0.1.0/
   creating: database/stage/Components/oracle.ordim.rdbms/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ordim.rdbms/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ordim.rdbms/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.ldap.ssl/
   creating: database/stage/Components/oracle.ldap.ssl/12.2.0.1.0/
   creating: database/stage/Components/oracle.ldap.ssl/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ldap.ssl/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ldap.ssl/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.has.deconfig/
   creating: database/stage/Components/oracle.has.deconfig/12.2.0.1.0/
   creating: database/stage/Components/oracle.has.deconfig/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.has.deconfig/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.has.deconfig/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.has.deconfig/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms/
   creating: database/stage/Components/oracle.rdbms/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/DataFiles/filegroup6.jar  
  inflating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/DataFiles/filegroup4.jar  
  inflating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/DataFiles/filegroup5.jar  
  inflating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/DataFiles/filegroup7.jar  
  inflating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.rdbms/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.sqlplus.rsf/
   creating: database/stage/Components/oracle.sqlplus.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.sqlplus.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.sqlplus.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sqlplus.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.sqlj.sqljruntime/
   creating: database/stage/Components/oracle.sqlj.sqljruntime/12.2.0.1.0/
   creating: database/stage/Components/oracle.sqlj.sqljruntime/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.sqlj.sqljruntime/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sqlj.sqljruntime/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.precomp/
   creating: database/stage/Components/oracle.precomp/12.2.0.1.0/
   creating: database/stage/Components/oracle.precomp/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.precomp/12.2.0.1.0/1/DataFiles/
   creating: database/stage/Components/oracle.perlint.expat/
   creating: database/stage/Components/oracle.perlint.expat/2.0.1.0.3/
   creating: database/stage/Components/oracle.perlint.expat/2.0.1.0.3/1/
   creating: database/stage/Components/oracle.perlint.expat/2.0.1.0.3/1/DataFiles/
  inflating: database/stage/Components/oracle.perlint.expat/2.0.1.0.3/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.perlint.expat/2.0.1.0.3/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.has.common/
   creating: database/stage/Components/oracle.has.common/12.2.0.1.0/
   creating: database/stage/Components/oracle.has.common/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.has.common/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.has.common/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.has.common/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.dbtoolslistener/
   creating: database/stage/Components/oracle.dbtoolslistener/12.2.0.1.0/
   creating: database/stage/Components/oracle.dbtoolslistener/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.dbtoolslistener/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.dbtoolslistener/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.help.share/
   creating: database/stage/Components/oracle.help.share/11.1.1.7.0/
   creating: database/stage/Components/oracle.help.share/11.1.1.7.0/1/
   creating: database/stage/Components/oracle.help.share/11.1.1.7.0/1/DataFiles/
  inflating: database/stage/Components/oracle.help.share/11.1.1.7.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.odbc.ic/
   creating: database/stage/Components/oracle.odbc.ic/12.2.0.1.0/
   creating: database/stage/Components/oracle.odbc.ic/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.odbc.ic/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.odbc.ic/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.odbc.ic/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.odbc.ic/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.rdbms.oci/
   creating: database/stage/Components/oracle.rdbms.oci/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.oci/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.oci/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.oci/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.oci/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.rdbms.oci/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.marvel/
   creating: database/stage/Components/oracle.marvel/12.2.0.1.0/
   creating: database/stage/Components/oracle.marvel/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.marvel/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.marvel/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.bali.ewt/
   creating: database/stage/Components/oracle.bali.ewt/11.1.1.6.0/
   creating: database/stage/Components/oracle.bali.ewt/11.1.1.6.0/1/
   creating: database/stage/Components/oracle.bali.ewt/11.1.1.6.0/1/DataFiles/
  inflating: database/stage/Components/oracle.bali.ewt/11.1.1.6.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.server/
   creating: database/stage/Components/oracle.server/12.2.0.1.0/
   creating: database/stage/Components/oracle.server/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.server/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.server/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.server/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.dbdev/
   creating: database/stage/Components/oracle.dbdev/12.2.0.1.0/
   creating: database/stage/Components/oracle.dbdev/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.dbdev/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.dbdev/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.dbdev/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.sqlplus/
   creating: database/stage/Components/oracle.sqlplus/12.2.0.1.0/
   creating: database/stage/Components/oracle.sqlplus/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.sqlplus/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sqlplus/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.sqlplus/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.sqlplus/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.dbjava.ic/
   creating: database/stage/Components/oracle.dbjava.ic/12.2.0.1.0/
   creating: database/stage/Components/oracle.dbjava.ic/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.dbjava.ic/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.dbjava.ic/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.dbjava.ic/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.dbjava.ic/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.odbc/
   creating: database/stage/Components/oracle.odbc/12.2.0.1.0/
   creating: database/stage/Components/oracle.odbc/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.odbc/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.odbc/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.odbc/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.swd.opatchautodb/
   creating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/
   creating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/
   creating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup8.jar  
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup6.jar  
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup4.jar  
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup5.jar  
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup7.jar  
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup9.jar  
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.swd.opatchautodb/12.2.0.1.5/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.rdbms.plsql/
   creating: database/stage/Components/oracle.rdbms.plsql/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.plsql/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.plsql/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.plsql/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.plsql/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.olap/
   creating: database/stage/Components/oracle.rdbms.olap/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.olap/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.olap/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.olap/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.rdbms.rsf/
   creating: database/stage/Components/oracle.rdbms.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.rdbms.rsf/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.rdbms.partitioning/
   creating: database/stage/Components/oracle.rdbms.partitioning/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.partitioning/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.partitioning/12.2.0.1.0/1/DataFiles/
   creating: database/stage/Components/oracle.nlsrtl.rsf/
   creating: database/stage/Components/oracle.nlsrtl.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.nlsrtl.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.nlsrtl.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.nlsrtl.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.nlsrtl.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.oraolap.api/
   creating: database/stage/Components/oracle.oraolap.api/12.2.0.1.0/
   creating: database/stage/Components/oracle.oraolap.api/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.oraolap.api/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.oraolap.api/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.xdk/
   creating: database/stage/Components/oracle.xdk/12.2.0.1.0/
   creating: database/stage/Components/oracle.xdk/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.xdk/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.xdk/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.xdk/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.nlsrtl.rsf.ic/
   creating: database/stage/Components/oracle.nlsrtl.rsf.ic/12.2.0.1.0/
   creating: database/stage/Components/oracle.nlsrtl.rsf.ic/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.nlsrtl.rsf.ic/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.nlsrtl.rsf.ic/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ovm/
   creating: database/stage/Components/oracle.ovm/12.2.0.1.0/
   creating: database/stage/Components/oracle.ovm/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ovm/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ovm/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.util/
   creating: database/stage/Components/oracle.rdbms.util/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.util/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.util/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.util/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.util/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.rdbms.util/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.dbjava.ucp/
   creating: database/stage/Components/oracle.dbjava.ucp/12.2.0.1.0/
   creating: database/stage/Components/oracle.dbjava.ucp/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.dbjava.ucp/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.dbjava.ucp/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.oracler.server/
   creating: database/stage/Components/oracle.oracler.server/12.2.0.1.0/
   creating: database/stage/Components/oracle.oracler.server/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.oracler.server/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.oracler.server/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.oracler.server/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.oracler.server/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.ldap.admin/
   creating: database/stage/Components/oracle.ldap.admin/12.2.0.1.0/
   creating: database/stage/Components/oracle.ldap.admin/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ldap.admin/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ldap.admin/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ldap.admin/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ordim.client/
   creating: database/stage/Components/oracle.ordim.client/12.2.0.1.0/
   creating: database/stage/Components/oracle.ordim.client/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ordim.client/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ordim.client/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ordim.client/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.perlint/
   creating: database/stage/Components/oracle.perlint/5.22.0.0.0/
   creating: database/stage/Components/oracle.perlint/5.22.0.0.0/1/
   creating: database/stage/Components/oracle.perlint/5.22.0.0.0/1/DataFiles/
  inflating: database/stage/Components/oracle.perlint/5.22.0.0.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.perlint/5.22.0.0.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.perlint/5.22.0.0.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.rdbms.rman/
   creating: database/stage/Components/oracle.rdbms.rman/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.rman/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.rman/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.rman/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.rman/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.dm/
   creating: database/stage/Components/oracle.rdbms.dm/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.dm/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.dm/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.dm/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.network/
   creating: database/stage/Components/oracle.network/12.2.0.1.0/
   creating: database/stage/Components/oracle.network/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.network/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.network/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.sdo.locator.jrf/
   creating: database/stage/Components/oracle.sdo.locator.jrf/12.2.0.1.0/
   creating: database/stage/Components/oracle.sdo.locator.jrf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.sdo.locator.jrf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sdo.locator.jrf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.duma/
   creating: database/stage/Components/oracle.duma/12.2.0.1.0/
   creating: database/stage/Components/oracle.duma/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.duma/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.duma/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.sqlplus.ic/
   creating: database/stage/Components/oracle.sqlplus.ic/12.2.0.1.0/
   creating: database/stage/Components/oracle.sqlplus.ic/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.sqlplus.ic/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sqlplus.ic/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.rdbms.install.seeddb/
   creating: database/stage/Components/oracle.rdbms.install.seeddb/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.install.seeddb/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.install.seeddb/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.install.seeddb/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.install.seeddb/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.buildtools.rsf/
   creating: database/stage/Components/oracle.buildtools.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.buildtools.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.buildtools.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.buildtools.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
 extracting: database/stage/Components/oracle.buildtools.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.buildtools.rsf/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.sdo.locator/
   creating: database/stage/Components/oracle.sdo.locator/12.2.0.1.0/
   creating: database/stage/Components/oracle.sdo.locator/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.sdo.locator/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sdo.locator/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.sdo.locator/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.oracore.rsf.core/
   creating: database/stage/Components/oracle.oracore.rsf.core/12.2.0.1.0/
   creating: database/stage/Components/oracle.oracore.rsf.core/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.oracore.rsf.core/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.oracore.rsf.core/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.hsodbc/
   creating: database/stage/Components/oracle.rdbms.hsodbc/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.hsodbc/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.hsodbc/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.hsodbc/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.hsodbc/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.rdbms.hsodbc/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.javavm.client/
   creating: database/stage/Components/oracle.javavm.client/12.2.0.1.0/
   creating: database/stage/Components/oracle.javavm.client/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.javavm.client/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.javavm.client/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.javavm.client/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ctx.atg/
   creating: database/stage/Components/oracle.ctx.atg/12.2.0.1.0/
   creating: database/stage/Components/oracle.ctx.atg/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ctx.atg/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ctx.atg/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ctx.atg/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.ctx.atg/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.oracore.rsf/
   creating: database/stage/Components/oracle.oracore.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.oracore.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.oracore.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.oracore.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.oracore.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.oracore.rsf/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.precomp.common/
   creating: database/stage/Components/oracle.precomp.common/12.2.0.1.0/
   creating: database/stage/Components/oracle.precomp.common/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.precomp.common/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.precomp.common/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.precomp.common/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.precomp.common/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.assistants.server/
   creating: database/stage/Components/oracle.assistants.server/12.2.0.1.0/
   creating: database/stage/Components/oracle.assistants.server/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.assistants.server/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.assistants.server/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.assistants.server/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.install.common/
   creating: database/stage/Components/oracle.rdbms.install.common/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.install.common/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.install.common/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.install.common/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.install.common/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rsf/
   creating: database/stage/Components/oracle.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.rdbms.rat/
   creating: database/stage/Components/oracle.rdbms.rat/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.rat/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.rat/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.rat/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.oraolap.dbscripts/
   creating: database/stage/Components/oracle.oraolap.dbscripts/12.2.0.1.0/
   creating: database/stage/Components/oracle.oraolap.dbscripts/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.oraolap.dbscripts/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.oraolap.dbscripts/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.nlsrtl.rsf.lbuilder/
   creating: database/stage/Components/oracle.nlsrtl.rsf.lbuilder/12.2.0.1.0/
   creating: database/stage/Components/oracle.nlsrtl.rsf.lbuilder/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.nlsrtl.rsf.lbuilder/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.nlsrtl.rsf.lbuilder/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.nlsrtl.rsf.lbuilder/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.sysman.ccr.client/
   creating: database/stage/Components/oracle.sysman.ccr.client/10.3.2.1.0/
   creating: database/stage/Components/oracle.sysman.ccr.client/10.3.2.1.0/1/
   creating: database/stage/Components/oracle.sysman.ccr.client/10.3.2.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sysman.ccr.client/10.3.2.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.sysman.ccr.client/10.3.2.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.install.plugins/
   creating: database/stage/Components/oracle.rdbms.install.plugins/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.install.plugins/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.install.plugins/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.install.plugins/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.install.plugins/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.swd.oui/
   creating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/
   creating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/
   creating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/filegroup8.jar  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/filegroup6.jar  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/filegroup4.jar  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/filegroup5.jar  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/filegroup7.jar  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/Expanded/
   creating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/Expanded/oui/
   creating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/Expanded/oui/instImages/
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/Expanded/oui/instImages/images.properties  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/Expanded/oui/instImages/10gOneClickbusybar.gif  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/Expanded/oui/instImages/oneclickbg.gif  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/Expanded/oui/instImages/splashScr.gif  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/Expanded/oui/instImages/bg.jpg  
  inflating: database/stage/Components/oracle.swd.oui/12.2.0.1.4/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.swd.commonlogging/
   creating: database/stage/Components/oracle.swd.commonlogging/13.3.0.0.0/
   creating: database/stage/Components/oracle.swd.commonlogging/13.3.0.0.0/1/
   creating: database/stage/Components/oracle.swd.commonlogging/13.3.0.0.0/1/DataFiles/
  inflating: database/stage/Components/oracle.swd.commonlogging/13.3.0.0.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ons.ic/
   creating: database/stage/Components/oracle.ons.ic/12.2.0.1.0/
   creating: database/stage/Components/oracle.ons.ic/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ons.ic/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ons.ic/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.tfa/
   creating: database/stage/Components/oracle.tfa/12.2.0.1.0/
   creating: database/stage/Components/oracle.tfa/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.tfa/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.tfa/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.tfa/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.lbac/
   creating: database/stage/Components/oracle.rdbms.lbac/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.lbac/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.lbac/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.lbac/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.lbac/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ldap.rsf/
   creating: database/stage/Components/oracle.ldap.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.ldap.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ldap.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ldap.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ldap.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.ldap.rsf/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.bali.ice/
   creating: database/stage/Components/oracle.bali.ice/11.1.1.7.0/
   creating: database/stage/Components/oracle.bali.ice/11.1.1.7.0/1/
   creating: database/stage/Components/oracle.bali.ice/11.1.1.7.0/1/DataFiles/
  inflating: database/stage/Components/oracle.bali.ice/11.1.1.7.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.nlsrtl.rsf.core/
   creating: database/stage/Components/oracle.nlsrtl.rsf.core/12.2.0.1.0/
   creating: database/stage/Components/oracle.nlsrtl.rsf.core/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.nlsrtl.rsf.core/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.nlsrtl.rsf.core/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.nlsrtl.rsf.core/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.swd.oui.core.min/
   creating: database/stage/Components/oracle.swd.oui.core.min/12.2.0.1.4/
   creating: database/stage/Components/oracle.swd.oui.core.min/12.2.0.1.4/1/
   creating: database/stage/Components/oracle.swd.oui.core.min/12.2.0.1.4/1/DataFiles/
  inflating: database/stage/Components/oracle.swd.oui.core.min/12.2.0.1.4/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.swd.oui.core.min/12.2.0.1.4/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.scheduler/
   creating: database/stage/Components/oracle.rdbms.scheduler/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.scheduler/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.scheduler/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.scheduler/12.2.0.1.0/1/DataFiles/filegroup4.jar  
  inflating: database/stage/Components/oracle.rdbms.scheduler/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.scheduler/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.rdbms.scheduler/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.assistants.netca.client/
   creating: database/stage/Components/oracle.assistants.netca.client/12.2.0.1.0/
   creating: database/stage/Components/oracle.assistants.netca.client/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.assistants.netca.client/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.assistants.netca.client/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.assistants.netca.client/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.dbjava.jdbc/
   creating: database/stage/Components/oracle.dbjava.jdbc/12.2.0.1.0/
   creating: database/stage/Components/oracle.dbjava.jdbc/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.dbjava.jdbc/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.dbjava.jdbc/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.help.ohj/
   creating: database/stage/Components/oracle.help.ohj/11.1.1.7.0/
   creating: database/stage/Components/oracle.help.ohj/11.1.1.7.0/1/
   creating: database/stage/Components/oracle.help.ohj/11.1.1.7.0/1/DataFiles/
  inflating: database/stage/Components/oracle.help.ohj/11.1.1.7.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.precomp.rsf/
   creating: database/stage/Components/oracle.precomp.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.precomp.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.precomp.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.precomp.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.precomp.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.locator/
   creating: database/stage/Components/oracle.rdbms.locator/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.locator/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.locator/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.locator/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.network.rsf/
   creating: database/stage/Components/oracle.network.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.network.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.network.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.network.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.network.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.network.rsf/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.xdk.xquery/
   creating: database/stage/Components/oracle.xdk.xquery/12.2.0.1.0/
   creating: database/stage/Components/oracle.xdk.xquery/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.xdk.xquery/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.xdk.xquery/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.bali.share/
   creating: database/stage/Components/oracle.bali.share/11.1.1.6.0/
   creating: database/stage/Components/oracle.bali.share/11.1.1.6.0/1/
   creating: database/stage/Components/oracle.bali.share/11.1.1.6.0/1/DataFiles/
  inflating: database/stage/Components/oracle.bali.share/11.1.1.6.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.precomp.common.core/
   creating: database/stage/Components/oracle.precomp.common.core/12.2.0.1.0/
   creating: database/stage/Components/oracle.precomp.common.core/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.precomp.common.core/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.precomp.common.core/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.precomp.common.core/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.xdk.rsf/
   creating: database/stage/Components/oracle.xdk.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.xdk.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.xdk.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.xdk.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.xdk.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.sysman.ccr.deconfig/
   creating: database/stage/Components/oracle.sysman.ccr.deconfig/10.3.1.0.0/
   creating: database/stage/Components/oracle.sysman.ccr.deconfig/10.3.1.0.0/1/
   creating: database/stage/Components/oracle.sysman.ccr.deconfig/10.3.1.0.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sysman.ccr.deconfig/10.3.1.0.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.sysman.ccr.deconfig/10.3.1.0.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.options/
   creating: database/stage/Components/oracle.options/12.2.0.1.0/
   creating: database/stage/Components/oracle.options/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.options/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.options/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.rdbms.rsf.ic/
   creating: database/stage/Components/oracle.rdbms.rsf.ic/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.rsf.ic/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.rsf.ic/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.rsf.ic/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.rsf.ic/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.rdbms.rsf.ic/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.ldap.owm/
   creating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/
   creating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/filegroup8.jar  
  inflating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/filegroup6.jar  
 extracting: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/filegroup4.jar  
  inflating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/filegroup5.jar  
  inflating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/filegroup7.jar  
  inflating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.ldap.owm/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.wwg.plsql/
   creating: database/stage/Components/oracle.wwg.plsql/12.2.0.1.0/
   creating: database/stage/Components/oracle.wwg.plsql/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.wwg.plsql/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.wwg.plsql/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.wwg.plsql/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.hs_common/
   creating: database/stage/Components/oracle.rdbms.hs_common/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.hs_common/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.hs_common/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.hs_common/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.bali.jewt/
   creating: database/stage/Components/oracle.bali.jewt/11.1.1.6.0/
   creating: database/stage/Components/oracle.bali.jewt/11.1.1.6.0/1/
   creating: database/stage/Components/oracle.bali.jewt/11.1.1.6.0/1/DataFiles/
  inflating: database/stage/Components/oracle.bali.jewt/11.1.1.6.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ctx.companion/
   creating: database/stage/Components/oracle.ctx.companion/12.2.0.1.0/
   creating: database/stage/Components/oracle.ctx.companion/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ctx.companion/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ctx.companion/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.swd.opatch/
   creating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/
   creating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/
   creating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/
  inflating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/filegroup8.jar  
  inflating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/filegroup4.jar  
  inflating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/filegroup5.jar  
  inflating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/filegroup7.jar  
  inflating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/filegroup9.jar  
  inflating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.swd.opatch/12.2.0.1.6/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.javavm.companion/
   creating: database/stage/Components/oracle.javavm.companion/12.2.0.1.0/
   creating: database/stage/Components/oracle.javavm.companion/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.javavm.companion/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.javavm.companion/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.javavm.companion/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ordim.server/
   creating: database/stage/Components/oracle.ordim.server/12.2.0.1.0/
   creating: database/stage/Components/oracle.ordim.server/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ordim.server/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ordim.server/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ordim.server/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.ordim.server/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.has.db/
   creating: database/stage/Components/oracle.has.db/12.2.0.1.0/
   creating: database/stage/Components/oracle.has.db/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.has.db/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.has.db/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.has.db/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.javavm.server/
   creating: database/stage/Components/oracle.javavm.server/12.2.0.1.0/
   creating: database/stage/Components/oracle.javavm.server/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.javavm.server/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.javavm.server/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.javavm.server/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.perlint.modules/
   creating: database/stage/Components/oracle.perlint.modules/5.22.0.0.0/
   creating: database/stage/Components/oracle.perlint.modules/5.22.0.0.0/1/
   creating: database/stage/Components/oracle.perlint.modules/5.22.0.0.0/1/DataFiles/
  inflating: database/stage/Components/oracle.perlint.modules/5.22.0.0.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.xdk.server/
   creating: database/stage/Components/oracle.xdk.server/12.2.0.1.0/
   creating: database/stage/Components/oracle.xdk.server/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.xdk.server/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.xdk.server/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.network.client/
   creating: database/stage/Components/oracle.network.client/12.2.0.1.0/
   creating: database/stage/Components/oracle.network.client/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.network.client/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.network.client/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.network.client/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.network.aso/
   creating: database/stage/Components/oracle.network.aso/12.2.0.1.0/
   creating: database/stage/Components/oracle.network.aso/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.network.aso/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.network.aso/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.network.aso/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.sdo/
   creating: database/stage/Components/oracle.sdo/12.2.0.1.0/
   creating: database/stage/Components/oracle.sdo/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.sdo/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sdo/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.has.rsf/
   creating: database/stage/Components/oracle.has.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.has.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.has.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.has.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.has.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.buildtools.common/
   creating: database/stage/Components/oracle.buildtools.common/12.2.0.1.0/
   creating: database/stage/Components/oracle.buildtools.common/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.buildtools.common/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.buildtools.common/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.swd.oui.core/
   creating: database/stage/Components/oracle.swd.oui.core/12.2.0.1.4/
   creating: database/stage/Components/oracle.swd.oui.core/12.2.0.1.4/1/
   creating: database/stage/Components/oracle.swd.oui.core/12.2.0.1.4/1/DataFiles/
  inflating: database/stage/Components/oracle.swd.oui.core/12.2.0.1.4/1/DataFiles/filegroup4.jar  
  inflating: database/stage/Components/oracle.swd.oui.core/12.2.0.1.4/1/DataFiles/filegroup5.jar  
  inflating: database/stage/Components/oracle.swd.oui.core/12.2.0.1.4/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.swd.oui.core/12.2.0.1.4/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.swd.oui.core/12.2.0.1.4/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.ldap.rsf.ic/
   creating: database/stage/Components/oracle.ldap.rsf.ic/12.2.0.1.0/
   creating: database/stage/Components/oracle.ldap.rsf.ic/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ldap.rsf.ic/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ldap.rsf.ic/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ldap.rsf.ic/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.mgw.common/
   creating: database/stage/Components/oracle.mgw.common/12.2.0.1.0/
   creating: database/stage/Components/oracle.mgw.common/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.mgw.common/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.mgw.common/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.mgw.common/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.oraolap/
   creating: database/stage/Components/oracle.oraolap/12.2.0.1.0/
   creating: database/stage/Components/oracle.oraolap/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.oraolap/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.oraolap/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.oraolap/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.hadoopcore/
   creating: database/stage/Components/oracle.hadoopcore/12.2.0.1.0/
   creating: database/stage/Components/oracle.hadoopcore/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.hadoopcore/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.hadoopcore/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.slax.rsf/
   creating: database/stage/Components/oracle.slax.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.slax.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.slax.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.slax.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.slax.rsf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.sysman.ccr/
   creating: database/stage/Components/oracle.sysman.ccr/12.1.2.0.0/
   creating: database/stage/Components/oracle.sysman.ccr/12.1.2.0.0/1/
   creating: database/stage/Components/oracle.sysman.ccr/12.1.2.0.0/1/DataFiles/
  inflating: database/stage/Components/oracle.sysman.ccr/12.1.2.0.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.sysman.ccr/12.1.2.0.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.javavm.server.core/
   creating: database/stage/Components/oracle.javavm.server.core/12.2.0.1.0/
   creating: database/stage/Components/oracle.javavm.server.core/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.javavm.server.core/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.javavm.server.core/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.assistants.deconfig/
   creating: database/stage/Components/oracle.assistants.deconfig/12.2.0.1.0/
   creating: database/stage/Components/oracle.assistants.deconfig/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.assistants.deconfig/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.assistants.deconfig/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.assistants.deconfig/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.dbscripts/
   creating: database/stage/Components/oracle.rdbms.dbscripts/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.dbscripts/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.dbscripts/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.dbscripts/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.dbscripts/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.rdbms.crs/
   creating: database/stage/Components/oracle.rdbms.crs/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.crs/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.crs/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.crs/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.crs/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.assistants.acf/
   creating: database/stage/Components/oracle.assistants.acf/12.2.0.1.0/
   creating: database/stage/Components/oracle.assistants.acf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.assistants.acf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.assistants.acf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.assistants.acf/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ordim.jai/
   creating: database/stage/Components/oracle.ordim.jai/12.2.0.1.0/
   creating: database/stage/Components/oracle.ordim.jai/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ordim.jai/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ordim.jai/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ons/
   creating: database/stage/Components/oracle.ons/12.2.0.1.0/
   creating: database/stage/Components/oracle.ons/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ons/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ons/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ons/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.ons/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.xdk.parser.java/
   creating: database/stage/Components/oracle.xdk.parser.java/12.2.0.1.0/
   creating: database/stage/Components/oracle.xdk.parser.java/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.xdk.parser.java/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.xdk.parser.java/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.xdk.parser.java/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.ldap.client/
   creating: database/stage/Components/oracle.ldap.client/12.2.0.1.0/
   creating: database/stage/Components/oracle.ldap.client/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ldap.client/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ldap.client/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.ldap.client/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.usm.deconfig/
   creating: database/stage/Components/oracle.usm.deconfig/12.2.0.1.0/
   creating: database/stage/Components/oracle.usm.deconfig/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.usm.deconfig/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.usm.deconfig/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.usm.deconfig/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.usm.deconfig/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.ctx.rsf/
   creating: database/stage/Components/oracle.ctx.rsf/12.2.0.1.0/
   creating: database/stage/Components/oracle.ctx.rsf/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.ctx.rsf/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.ctx.rsf/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.has.common.cvu/
   creating: database/stage/Components/oracle.has.common.cvu/12.2.0.1.0/
   creating: database/stage/Components/oracle.has.common.cvu/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.has.common.cvu/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.has.common.cvu/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.has.common.cvu/12.2.0.1.0/1/DataFiles/filegroup1.jar  
  inflating: database/stage/Components/oracle.has.common.cvu/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.rdbms.deconfig/
   creating: database/stage/Components/oracle.rdbms.deconfig/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.deconfig/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.deconfig/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.deconfig/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.rdbms.deconfig/12.2.0.1.0/1/DataFiles/filegroup3.jar  
   creating: database/stage/Components/oracle.precomp.lang/
   creating: database/stage/Components/oracle.precomp.lang/12.2.0.1.0/
   creating: database/stage/Components/oracle.precomp.lang/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.precomp.lang/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.precomp.lang/12.2.0.1.0/1/DataFiles/filegroup2.jar  
  inflating: database/stage/Components/oracle.precomp.lang/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.network.listener/
   creating: database/stage/Components/oracle.network.listener/12.2.0.1.0/
   creating: database/stage/Components/oracle.network.listener/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.network.listener/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.network.listener/12.2.0.1.0/1/DataFiles/filegroup2.jar  
   creating: database/stage/Components/oracle.rdbms.dv/
   creating: database/stage/Components/oracle.rdbms.dv/12.2.0.1.0/
   creating: database/stage/Components/oracle.rdbms.dv/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.rdbms.dv/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.rdbms.dv/12.2.0.1.0/1/DataFiles/filegroup1.jar  
   creating: database/stage/Components/oracle.blaslapack/
   creating: database/stage/Components/oracle.blaslapack/12.2.0.1.0/
   creating: database/stage/Components/oracle.blaslapack/12.2.0.1.0/1/
   creating: database/stage/Components/oracle.blaslapack/12.2.0.1.0/1/DataFiles/
  inflating: database/stage/Components/oracle.blaslapack/12.2.0.1.0/1/DataFiles/filegroup3.jar  
  inflating: database/stage/shiphomeinfo.properties  
   creating: database/stage/globalvariables/
  inflating: database/stage/globalvariables/variable.properties  
  inflating: database/stage/globalvariables/globalvar.xml  
  inflating: database/stage/install1.jar  
  inflating: database/runInstaller   
Starting Oracle Universal Installer...

Checking Temp space: must be greater than 500 MB.   Actual 22731 MB    Passed
Checking swap space: must be greater than 150 MB.   Actual 1860 MB    Passed
Preparing to launch Oracle Universal Installer from /tmp/OraInstall2019-03-27_10-53-19PM. Please wait ...[WARNING] [INS-32055] The Central Inventory is located in the Oracle base.
   ACTION: Oracle recommends placing this Central Inventory in a location outside the Oracle base directory.
You can find the log of this install session at:
 /opt/oracle/oraInventory/logs/installActions2019-03-27_10-53-19PM.log
The installation of Oracle Database 12c was successful.
Please check '/opt/oracle/oraInventory/logs/silentInstall2019-03-27_10-53-19PM.log' for more details.

As a root user, execute the following script(s):
        1. /opt/oracle/oraInventory/orainstRoot.sh
        2. /opt/oracle/product/12.2.0.1/dbhome_1/root.sh



Successfully Setup Software.
Removing intermediate container d2a09a9caaf5
 ---> 84ffa03f51a2
Step 14/24 : FROM base
 ---> 9faeea638413
Step 15/24 : USER oracle
 ---> Running in 2add7730d3dc
Removing intermediate container 2add7730d3dc
 ---> 6ffd1a81a101
Step 16/24 : COPY --chown=oracle:dba --from=builder $ORACLE_BASE $ORACLE_BASE
 ---> eb8412e8271d
Step 17/24 : USER root
 ---> Running in 3192e3981eb5
Removing intermediate container 3192e3981eb5
 ---> b4eb56cde52e
Step 18/24 : RUN $ORACLE_BASE/oraInventory/orainstRoot.sh &&     $ORACLE_HOME/root.sh
 ---> Running in fe278393b31b
Changing permissions of /opt/oracle/oraInventory.
Adding read,write permissions for group.
Removing read,write,execute permissions for world.

Changing groupname of /opt/oracle/oraInventory to dba.
The execution of the script is complete.
Check /opt/oracle/product/12.2.0.1/dbhome_1/install/root_fe278393b31b_2019-03-27_23-01-52-306954265.log for the output of root script
Removing intermediate container fe278393b31b
 ---> 4907292b1465
Step 19/24 : USER oracle
 ---> Running in 160132fae277
Removing intermediate container 160132fae277
 ---> c8f9293c5c14
Step 20/24 : WORKDIR /home/oracle
 ---> Running in 9daf521020fc
Removing intermediate container 9daf521020fc
 ---> 0a3e54f27f47
Step 21/24 : VOLUME ["$ORACLE_BASE/oradata"]
 ---> Running in 3218a6fb91a7
Removing intermediate container 3218a6fb91a7
 ---> a0a5039e57b6
Step 22/24 : EXPOSE 1521 5500
 ---> Running in f5906c3a50cd
Removing intermediate container f5906c3a50cd
 ---> 6955d7b4d4e6
Step 23/24 : HEALTHCHECK --interval=1m --start-period=5m    CMD "$ORACLE_BASE/$CHECK_DB_FILE" >/dev/null || exit 1
 ---> Running in d96a8361a3a8
Removing intermediate container d96a8361a3a8
 ---> d4e7d3b47d49
Step 24/24 : CMD exec $ORACLE_BASE/$RUN_FILE
 ---> Running in 045540a9d62a
Removing intermediate container 045540a9d62a
 ---> 0f1484c89821
Successfully built 0f1484c89821
Successfully tagged oracle/database:12.2.0.1-ee


  Oracle Database Docker Image for 'ee' version 12.2.0.1 is ready to be extended: 
    
    --> oracle/database:12.2.0.1-ee

  Build completed in 1023 seconds.