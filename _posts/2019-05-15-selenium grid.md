---
layout: post
title: Selenium Grid
modified: 2019-05-16
categories: [Testing]
tags: 
  - Selenium
comments: true
---
Recently, I am working on to use SELENIUM for automating testing. <br/>
A part of my goal is to use SELENIUM for stress test<br/>
We want to simulate the registration process to make sure the course system could be working as expected at the next semester when students register their courses.

I use ansible playbook to simplify the build of a selenium grid environment.

The following packages are mandatory.
<pre class="prettyprint lang-sh linenums=1 ">
sudo apt install python-docker
pip install docker-py
pip uninstall requests 
pip uninstall urllib3 
pip install requests
</pre>

The script creating a selenium grid: <br/> 
selenium_grid.yml
<pre class="prettyprint linenums=1 ">
---
- hosts: localhost
  connection: local
  gather_facts: False
  vars:
    nodes: 10
    state: started
    dns: 192.168.100.153
  tasks:
    - name: "&#123;&#123; '%s hub' | format(state) &#125;&#125;"
      docker_container:
        name: hub
        image: selenium/hub
        state: "&#123;&#123; state &#125;&#125;"
        published_ports: 5555:4444
        recreate: yes
        cleanup: yes
        dns_servers: "&#123;&#123; dns &#125;&#125;"

    - name: "&#123;&#123; '%s nodes' | format(state) &#125;&#125;"
      docker_container:
        name: "&#123;&#123; 'chrome%02d' | format(item) &#125;&#125;"
        image: selenium/node-chrome-debug
        state: "&#123;&#123; state &#125;&#125;"
        published_ports: "&#123;&#123; '59%02d:5900' | format(item) &#125;&#125;"
        recreate: yes
        cleanup: yes
        links: hub:hub
        dns_servers: "&#123;&#123; dns &#125;&#125;"
      loop: "&#123;&#123; range(1, nodes|int + 1, 1)|list &#125;&#125;"
</pre>
 
<pre class="prettyprint lang-sh linenums=1 ">
$ ansible-playbook selenium_grid.yml 
 [WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] *******************************************************************************************************************

TASK [started hub] *****************************************************************************************************************
changed: [localhost]

TASK [started nodes] ***************************************************************************************************************
changed: [localhost] => (item=1)
changed: [localhost] => (item=2)
changed: [localhost] => (item=3)
changed: [localhost] => (item=4)
changed: [localhost] => (item=5)
changed: [localhost] => (item=6)
changed: [localhost] => (item=7)
changed: [localhost] => (item=8)
changed: [localhost] => (item=9)
changed: [localhost] => (item=10)

PLAY RECAP *************************************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0   

zhangqiaoc@ubuntu01:~/ansible$ sudo docker ps -a
CONTAINER ID        IMAGE                        COMMAND                  CREATED              STATUS                     PORTS                    NAMES
d4dd9291caf0        selenium/node-chrome-debug   "/opt/bin/entry_poin"   About a minute ago   Up About a minute          0.0.0.0:5910->5900/tcp   chrome10
00849df50f3e        selenium/node-chrome-debug   "/opt/bin/entry_poin"   About a minute ago   Up About a minute          0.0.0.0:5909->5900/tcp   chrome09
76a362efadcc        selenium/node-chrome-debug   "/opt/bin/entry_poin"   About a minute ago   Up About a minute          0.0.0.0:5908->5900/tcp   chrome08
170eb6cc8193        selenium/node-chrome-debug   "/opt/bin/entry_poin"   About a minute ago   Up About a minute          0.0.0.0:5907->5900/tcp   chrome07
9bcf4bd1fb5e        selenium/node-chrome-debug   "/opt/bin/entry_poin"   About a minute ago   Up About a minute          0.0.0.0:5906->5900/tcp   chrome06
8e3dcb28ac73        selenium/node-chrome-debug   "/opt/bin/entry_poin"   2 minutes ago        Up About a minute          0.0.0.0:5905->5900/tcp   chrome05
8b82403b0e6c        selenium/node-chrome-debug   "/opt/bin/entry_poin"   2 minutes ago        Up 2 minutes               0.0.0.0:5904->5900/tcp   chrome04
dd358d28ae29        selenium/node-chrome-debug   "/opt/bin/entry_poin"   2 minutes ago        Up 2 minutes               0.0.0.0:5903->5900/tcp   chrome03
0843057f3900        selenium/node-chrome-debug   "/opt/bin/entry_poin"   2 minutes ago        Up 2 minutes               0.0.0.0:5902->5900/tcp   chrome02
434bf300c0b5        selenium/node-chrome-debug   "/opt/bin/entry_poin"   2 minutes ago        Up 2 minutes               0.0.0.0:5901->5900/tcp   chrome01
4c962835b46c        selenium/hub                 "/opt/bin/entry_poin"   2 minutes ago        Up 2 minutes               0.0.0.0:5555->4444/tcp   hub
99cd793962e2        oracle/database:18.3.0-ee    "/bin/sh -c 'exec $O"   3 weeks ago          Exited (137) 2 weeks ago                            oracle18
</pre>

You can check the status of the grid by the web interface.
<img src="images/20190515.1.png" width="600"/>

Then, write a python script to call the selenium testing python scripts.
<pre class="prettyprint lang-python linenums=1 ">
from subprocess import Popen

processes = []

cmd_pprd1 = "python main_pprd.py"
cmd_pprd2 = "python main_pprd2.py"
cmd_pprd3 = "python main_pprd3.py"

# processes.append(Popen(cmd_pprd1, shell=True))
# processes.append(Popen(cmd_pprd2, shell=True))
# processes.append(Popen(cmd_pprd3, shell=True))

for n in range(10):
    processes.append(Popen(cmd_pprd1, shell=True))

print len(processes)
for n in range(len(processes)):
    processes[n].wait()
</pre>




