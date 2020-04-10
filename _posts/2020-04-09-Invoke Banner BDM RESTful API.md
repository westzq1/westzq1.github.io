---
layout: post
title: Invoke Banner BDM RESTful API
modified: 2020-04-08
categories: [Banner]  
tags: 
  - Banner.Code
comments: true
---
We are using Banner BDM RESTful API to upload file from the local desktop to the BDM as batch. <br>
The key point here is that we should use form-data with multipe parts as body to post data.<br>
In python, I found requests_toolbelt.MultipartEncoder can achieve this goal. <br>

And there are two API:
1. axbatches is used to create a batch, and return the id of the batch.
2. axbatchpages, with batch id to add pages to it

<pre class="prettyprint lang-python linenums=1 ">
import json
import os
import datetime
import requests_toolbelt
import requests

class AppXtenderReST:
    site = "https://Server/AppXtenderReST/api/axdatasources/PPRD2"

    def __init__(self, auth, appid):
        self.auth = auth
        self.appid = appid

    def createHeader(self, contentType):
        headers = {
            'Accept': 'application/vnd.emc.ax+json',
            'Authorization': 'Basic ' + self.auth,
            'Host': 'webxt.pprd.odu.edu',
            'Content-Type': contentType
        }
        return headers

    def getFName(self, filepath):
        head, tail = os.path.split(filepath)
        return tail;

    def axbatches(self, name,  filepath):
        url = self.site + "/" + "axbatches" + "/" + str(self.appid)
        payload = {'Name': name ,
                   'Description': 'created by Python Automation Script for App ' + str(self.appid)
                                  + ' on '+ datetime.datetime.now().strftime('%Y-%m-%d'),
                   'Private': False
                   }
        m = requests_toolbelt.MultipartEncoder(
            fields={'data': (None, json.dumps(payload), 'application/vnd.emc.ax+json; charset=utf-8'),
                    'bin': (self.getFName(filepath), open(filepath, 'rb'), 'application/bin')}
                    # 'annotation' : ()
                    # 'text' : ()
            )
        response = requests.post(url, headers=self.createHeader(m.content_type), data=m)
        info = response.json()
        if response.status_code == 200:
            return info['ID'], info['PageCount']
        else:
            raise Exception(response.status_code, info)

    def axbatchpages(self, batchid, filepath):
        url = self.site + "/axapps/" + str(self.appid) + '/' + "axbatchpages" + "/" + str(batchid)
        m = requests_toolbelt.MultipartEncoder(
            fields={'bin': (self.getFName(filepath), open(filepath, 'rb'), 'application/bin')}
            )

        response = requests.post(url, headers=self.createHeader(m.content_type), data=m)
        info = response.json()
        if response.status_code == 200:
            return info['Page']
        else:
            raise Exception(response.status_code, info)



if __name__ == "__main__":
    # generate key by using b64encode 
    # from base64 import b64encode
    # auth = b64encode(b"user:password").decode("ascii")  
    rest = AppXtenderReST("key", 509)  # replace appid here  
    rest.upload('batch_test_5', 'C:/Users/q1zhang/Downloads/test')

</pre>   



