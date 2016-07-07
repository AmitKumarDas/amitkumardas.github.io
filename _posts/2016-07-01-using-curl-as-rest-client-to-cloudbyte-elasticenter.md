---
layout: post
title: Using cURL as REST client to CloudByte ElastiCenter
---

# Using cURL as REST client to CloudByte ElastiCenter

<br/>

## Mission Impossible - I ```Mastering cURL```

<br />

## How to learn cURL ?

- ```curl --help```
- [cURL manual](https://curl.haxx.se/docs/manual.html)
- [cURL & stderr redirections](http://unix.stackexchange.com/questions/166359/how-to-grep-the-output-of-curl)
- [cURL & Unix pipes](http://serverfault.com/questions/313599/how-do-i-pipe-the-output-of-uptime-df-to-curl)

```bash

# -d option in curl with a **@-** argument to accept input from a pipe
# backticks imply that the enclosed command will be executed

$ echo "time=`uptime`" | curl -d @- http://URL  
```

<br />

## My Environment

- ElastiCenter - 20.10.112.70  
- Client Environment
  - Git Bash on Windows 7

```bash

$ curl --version
curl 7.30.0 (i386-pc-win32) libcurl/7.30.0 OpenSSL/0.9.8x zlib/1.2.7
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtsp smtp smtps telnet tftp
Features: AsynchDNS GSS-Negotiate IPv6 Largefile NTLM SPNEGO SSL SSPI libz
```

<br />

### cURL - Getting Started - Use -k to ignore security

```bash

$ curl https://20.10.112.70/client/api?command=listTsm -k

<?xml version="1.0" encoding="ISO-8859-1"?>
  <listTsmResponse cloud-stack-version="1.4.0.897">
  <errorcode>401</errorcode>
  <errortext>unable to verify user credentials and/or request signature</errortext>
  <licensetype>trial</licensetype>
</listTsmResponse>

```

<br />

### cURL - Pass query parameters via -d

- Use of -G is a must with -d as ElastiCenter is a pure GET API !!!

```bash

$ curl https://20.10.112.70/client/api -d 'command=listTsm' -G -k

<?xml version="1.0" encoding="ISO-8859-1"?>
  <listTsmResponse cloud-stack-version="1.4.0.897">
  <errorcode>401</errorcode>
  <errortext>unable to verify user credentials and/or request signature</errortext>
  <licensetype>trial</licensetype>
</listTsmResponse>

```

<br />

### Pass multiple query parameters

```bash

$ curl https://20.10.112.70/client/api -d 'command=listTsm' -d 'response=json' -G -k

{ "listTsmResponse" : {
  "uuidList": [],
  "errorcode": 401,
  "errortext": "unable to verify user credentials and/or request signature",
  "licensetype": "trial"
} }

```

<br />

### What to do with password ?

- NOTE - Store api key in a file to avoid repetition.

```bash

$ echo apiKey=2jLQePg1fSMx4QXwQedR5Xkrm_yphF1Hk_63kpOfwjvAeX7YQHoajevA > ecapi

$ cat ecapi
apiKey=2jLQePg1fSMx4QXwQedR5Xkrm_yphF1Hk_63kpOfwjvAeX7YQHoajevA
```

<br />

### I want data from ElastiCenter REST API!!

- Example of using listTsm command

```bash

$ curl https://20.10.112.70/client/api -d 'command=listTsm' -d 'response=json' -d @ecapi -G -k

{ "listTsmResponse" : { "count":2 ,"listTsm" : [  {
  "id": "d9a86229-9698-382a-a240-ff26b489a1f0",
  "simpleid": 146,
  "name": "VSMTEST2",
  "ipaddress": "20.10.39.239",
  "accountname": "ACC1",
  "sitename": "SITE1",
  "clustername": "HAG1",
  ....
  ....
  "totalprovisionquota": "24117248",
  "haNodeStatus": "Available",
  "ispooltakeoveronpartialfailure": true,
  "cifsauthentication": "user",
  "minrecommendedvolblocksize": 0
} ] } }
```

<br />

### Can we reduce verbosity ?

- Yes.
- NOTE - Store the URL in a file as shown below & understood by cURL.
- NOTE - Use cURL with -K option.

```bash

$ echo "url=https://20.10.112.70/client/api" > ec112.70

$ cat ec112.70
url=https://20.10.112.70/client/api
```

<br />

### Show me the terse syntax !!!

```bash

$ curl -K ec112.70 -d 'command=listTsm' -d 'response=json' -d @ecapi -G -k

{ "listTsmResponse" : { "count":1 ,"listTsm" : [  {
  "id": "d9a86229-9698-382a-a240-ff26b489a1f0",
  "simpleid": 146,
  "name": "VSMTEST2",
  "ipaddress": "20.10.39.239",
  "accountname": "ACC1",
  "sitename": "SITE1",
  "clustername": "HAG1",
  "controllerName": "NODE1",
  ...
  ...
  "qosgrouplist": [
    {
      "id": "23afe0f5-622c-3cda-bf38-bb62252d5704",
      "networkspeed": "0",      
      "name": "QoS_SampleNFSVolACC1VSMTEST2",
      "memlimit": "0",
      "throughput": "0"
    }
  ]
} ] } }  
```

<br />

### Can we limit the verboseness further ?

- Yes.
- NOTE - Put the common stuff in ecapi file itself.
- NOTE - Let the URL stuff be present in ec112.70 file.

```bash
$ cat ecapi
apiKey=2jLQePg1fSMx4QXwQedR5Xkrm_yphF1Hk_63kpOfwjvAeX7YQHoajevA

$ cat ec112.70
url=https://20.10.112.70/client/api
```

<br />

### Limit the verboseness further. Is it possible ?

- Yes.
- NOTE - The quotes '' for providing value to -d seems optional.
- Here we go. **The terse ever syntax !!!**

```bash

$ curl -K ec112.70 -d command=listTsm -d @ecapi -G -k

{ "listTsmResponse" : { "count":1 ,"listTsm" : [  {
  "id": "d9a86229-9698-382a-a240-ff26b489a1f0",
  "simpleid": 146,  
  ...
  ...
  "qosgrouplist": [
    {
      "id": "23afe0f5-622c-3cda-bf38-bb62252d5704",
      "networkspeed": "0",
      ...  
      "throughput": "0"
    }
  ]
} ] } }  
```

<br />

### Million dollar question: **Can I use it in Unix pipes** ?

- Oh Yes !!!
- NOTE - Redirect std err to std output & then use it in Unix pipes.

```bash
$ curl -K ec112.70 -d 'command=listTsm' -d 'response=json' -G -k --stderr - \
  | grep uuidList

"uuidList": [],
```

<br />

### Is there a better way to use cURL in Unix pipes ?

- Yes.
- Alternative & compact way to use the output from curl in Unix pipes
  - -s -> silent, i.e. do not show progress
  - -S -> show errors

```bash

$ curl -K ec112.70 -d command=listUserss -d @ecapi -G -k -s -S \
  | awk '{print NR ": " $0}'

1: { "errorresponse" : {
2:   "uuidList": [],
3:   "errorcode": 432,
4:   "errortext": "The given command does not exist",
5:   "licensetype": "trial"
6: } }
```

<br />

### Do you have any billion dollar question ?

- ```Can I create a ElastiStor volume with such ease ?```
- In other words, can we avoid resorting to programming for such complex tasks ?
- Remember, this is the best of a complex REST client use-case w.r.t ElastiCenter.
- Refer - create_volume in python (OpenStack cinder plugin for CloudByte)

<br />

| Task                           | REST Command           | Query Parameters       | Filter            |
| :-------------                 | :-------------         | :-------------         | :-------------    |
| Account ID from Account Name   | listAccount            | __                     | account name (m)  |
| List VSM details               | listTsm                | accountid              | VSM name (m)      |
| Add QOS group                  | addQosGroup            | name, tsmid, ..        | id                |
| Create Volume                  | createVolume           | datasetid, name, ..    | jobid             |
| Verify Job Result (repeat if)  | queryAsyncJobResult    | jobid                  | jobstatus         |
| Fetch volume id                | listFileSystem         | __                     | volume name (m)   |
| Fetch iscsi id                 | listVolumeiSCSIService | storageid              | volume id (m)     |
| Fetch initiator group id       | listiSCSIInitiator     | accountid              | name (m)          |
| Update iscsi service           | updateVolumeiSCSIService | id, igid, ..         | __                |

<br />

- (m) refers to match; is provided by user
- .. indicates presence of more query parameters
- __ indicates none
- repeat if indicates the same task may be retried again & again

<br />

### Seems like a ```Mission Impossible``` part - II sequence

- Separate article(s) will show how above use-case is achievable.
- Trailer: 
  - Need to take help from ```json parser cli``` libraries.
- List of json cli helps:
  - [underscore-cli](https://github.com/ddopson/underscore-cli)
  - [json](http://trentm.com/json/)
  - [Lenses, Folds, and Traversals ](https://github.com/ekmett/lens)
  - [Lens Tutorial from School of Haskell](https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/a-little-lens-starter-tutorial)
  - [jq](https://stedolan.github.io/jq/)
- Full movie:
  - [Misson Impossible - II](https://amitkumardas.github.io/2016/07/02/using-curl-and-json-to-create-a-cloudbyte-volume.html)

<br />

## Miscellaneous

### Network & Speed Calculations

- How cURL does it ?

```bash

$ curl -L https://raw.githubusercontent.com/micha/resty/master/resty > resty
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  5651  100  5651    0     0   2683      0  0:00:02  0:00:02 --:--:--  2703
```
