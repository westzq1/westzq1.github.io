---
layout: post
title: Banner - Use BEP on the user-defined tables
modified: 2020-06-03
categories: [Banner]  
tags: 
  - Banner.BEP
comments: true
---
<br> BEP, Banner Events Publisher, can be used to capture the data change on the specific tables, like ODI
<br> There are a lot of pre-defined events for the Ellucian delivered tables
<br> 
<br> Today, I will show you how to use BEP for the user-defined tables.
<br> 
<br> First, lets create a test table, two columns {TABLE_NAME}_VPDI_CODE and {TABLE_NAME}_GUID are mandatory, because the trigger the BEP generated uses these two columns. We don't have to fill them.
<pre class="prettyprint lang-sql linenums=1 ">
create table baninst1.bep_test_1(
id number,
name varchar2(30),
gender char(1),
comments varchar2(3000),
bep_test_1_VPDI_CODE varchar2(6),
bep_test_1_guid varchar2(36),
constraint pk_bep_test_1 primary key (id)
)
</pre>
<br> Then, go to the BEP console, create a new event.
<br> I use USER. as the prefix of the event name. The prefix is used by RabbitMQ to route data to the different queues.
<br><img src="{{site.baseurl}}/images/2020060301.png" width="300"/>

<br>Go to Capture Data tab to add this table into the event.
<br>I am using ID as the reference column. 
<br>In BEP, the reference column can only be one column, we can use GUID if it is meaningful.
<br><img src="{{site.baseurl}}/images/2020060302.png" width="300"/>

<br>Go to Triggers table to make sure the trigger was created. If not, click Activate and then Refresh
<br><img src="{{site.baseurl}}/images/2020060303.png" width="450"/>

<br>To the RabbitMQ:
<br>http://emb1.pprd.odu.edu:15672/
<br>Create a queue named user_defined_queue, with a binding
<br><img src="{{site.baseurl}}/images/2020060304.png" width="450"/>

<br>Then, we can insert a data into baninst1.bep_test_1
<pre class="prettyprint lang-sql linenums=1 ">
insert into  baninst1.bep_test_1 values(8,'bbb','F','ccc','dasda','dasdasdsadasd');
commit;
</pre>

<br>Then, monitor table baninst1.bep_test_1 to wait until the message is consumed
<pre class="prettyprint lang-sql linenums=1 ">
select * from cdcadmin.BEP_MULTI_CONSUMER_TABLE
</pre>

<br>Go to the page of the queue user_defined_queue
<br>We can check the message in RabbitMQ here
<br><img src="{{site.baseurl}}/images/2020060305.png" width="300"/>

The format of the message in "DEFAULT" integration type:
<pre class="prettyprint lang-json linenums=1 ">
Message 1
The server reported 2 messages remaining.
Exchange
bep_events_topic
Routing Key
USER.BEP_TEST_1
Redelivered

Properties
app_id:
BEP
priority:
0
delivery_mode:
2
headers:
event:
USER.BEP_TEST_1
content_type:
application/json
Payload 
624 bytes
Encoding: string
{
  "businessEvent": "USER.BEP_TEST_1",
  "messageId": "161572425917",
  "timeStamp": "2020-06-03T15:53:22.897-04:00",
  "sourceOfChange": "Banner",
  "key": "8",
  "entity": "BEP_TEST_1",
  "changeType": "INSERT",
  "changeData": [
    {
      "name": "ID",
      "new": "8"
    },
    {
      "name": "NAME",
      "new": "bbb"
    },
    {
      "name": "GENDER",
      "new": "F"
    },
    {
      "name": "COMMENTS",
      "new": "ccc"
    },
    {
      "name": "BEP_TEST_1_VPDI_CODE",
      "new": "dasda"
    },
    {
      "name": "BEP_TEST_1_GUID",
      "new": "dasdasdsadasd"
    }
  ],
  "additionalData": []
}
Message 2
The server reported 1 messages remaining.
Exchange
bep_events_topic
Routing Key
USER.BEP_TEST_1
Redelivered

Properties
app_id:
BEP
priority:
0
delivery_mode:
2
headers:
event:
USER.BEP_TEST_1
content_type:
application/json
Payload 
624 bytes
Encoding: string
{
  "businessEvent": "USER.BEP_TEST_1",
  "messageId": "161572451091",
  "timeStamp": "2020-06-03T16:00:26.028-04:00",
  "sourceOfChange": "Banner",
  "key": "8",
  "entity": "BEP_TEST_1",
  "changeType": "DELETE",
  "changeData": [
    {
      "name": "ID",
      "old": "8"
    },
    {
      "name": "NAME",
      "old": "bbb"
    },
    {
      "name": "GENDER",
      "old": "F"
    },
    {
      "name": "COMMENTS",
      "old": "ccc"
    },
    {
      "name": "BEP_TEST_1_VPDI_CODE",
      "old": "dasda"
    },
    {
      "name": "BEP_TEST_1_GUID",
      "old": "dasdasdsadasd"
    }
  ],
  "additionalData": []
}
Message 3
The server reported 0 messages remaining.
Exchange
bep_events_topic
Routing Key
USER.BEP_TEST_1
Redelivered

Properties
app_id:
BEP
priority:
0
delivery_mode:
2
headers:
event:
USER.BEP_TEST_1
content_type:
application/json
Payload 
752 bytes
Encoding: string
{
  "businessEvent": "USER.BEP_TEST_1",
  "messageId": "161572557570",
  "timeStamp": "2020-06-03T16:30:32.796-04:00",
  "sourceOfChange": "Banner",
  "key": "7",
  "entity": "BEP_TEST_1",
  "changeType": "UPDATE",
  "changeData": [
    {
      "name": "ID",
      "old": "7",
      "new": "7"
    },
    {
      "name": "NAME",
      "old": "bbb",
      "new": "bbb"
    },
    {
      "name": "GENDER",
      "old": "M",
      "new": "F"
    },
    {
      "name": "COMMENTS",
      "old": "ccc",
      "new": "ccc"
    },
    {
      "name": "BEP_TEST_1_VPDI_CODE",
      "old": "dasda",
      "new": "dasda"
    },
    {
      "name": "BEP_TEST_1_GUID",
      "old": "dasdasdsadasd",
      "new": "dasdasdsadasd"
    }
  ],
  "additionalData": []
}
</pre>