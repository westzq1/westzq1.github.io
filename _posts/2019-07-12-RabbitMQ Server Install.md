---
layout: post
title: RabbitMQ Server Install in RHEL7
modified: 2019-07-12
categories: [Banner]
tags: 
  - Banner.install
comments: true
---

Before install RabbitMQ, we should install ERLANG which is the language used to code RabbitMQ. <br/>

We should be careful the compatibility between ERLANG and RabbitMQ. <br/>
https://www.rabbitmq.com/which-erlang.html#compatibility-matrix

If you encounter this kind of error
<pre class="prettyprint lang-sql linenums=1 ">
{"init terminating in do_boot",{undef,[{rabbit_prelaunch,start,[],[]},{init,start_em,1,[]},{init,do_boot,3,[]}]}}

Crash dump is being written to: /var/log/rabbitmq/erl_crash.dump...done
init terminating in do_boot ()
</pre>

You should check whether your erlang and rabbitmq are fit.<br/>

The rpm of ERLANG can be downloaded here, find the version you need. <br/>
https://dl.bintray.com/rabbitmq-erlang/rpm/erlang/

The error information in “journalctl” is a misunderstanding, we can use “/usr/sbin/rabbitmq-server” to start RabbitMQ to see the internal errors.
