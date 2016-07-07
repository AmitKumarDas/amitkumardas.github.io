---
layout: post
title: Using cURL & json to create a CloudByte volume
---

# Using cURL & json to create a CloudByte volume

> This is the second part in the series of articles starting with
> [post 1](https://amitkumardas.github.io/2016/07/01/using-curl-as-rest-client-to-cloudbyte-elasticenter.html)

<br/>

## Using ```json```

- [json](http://trentm.com/json/)

<br />

## Installing json

```bash

# Need to have NodeJS runtime.
$ node -v
v0.12.2

$ npm -v
2.7.4

$ npm install -g json
C:\Users\amit\AppData\Roaming\npm\json
  -> C:\Users\amit\AppData\Roaming\npm\node_modules\json\lib\json.js
json@9.0.4 C:\Users\amit\AppData\Roaming\npm\node_modules\json

$ json --version
json 9.0.4
written by Trent Mick
https://github.com/trentm/json

```

<br />

## First steps in using json: ```Pass In - Pass Out -- Do Nothing```

- It validates & pretty prints. Amazing stuff even with nothing !!!

```bash

$ curl -K ec112.70 -d command=listUsers -d @ecapi -G -k -s -S | json
{
  "listusersresponse": {
    "count": 1,
    "user": [
      {
        "id": "46a34524-f253-4132-b894-afd49ca90af4",
        "username": "admin",
        "firstname": "Abhishek",
        "lastname": "Shrivastava",
        "email": "abhishek@cloudbyte.com",
        "created": "2016-04-27 14:50:12",
        "state": "enabled",
        "account": "admin",
        "accounttype": 1,
        "domainid": "58ea5ec4-ccd9-44c2-a361-074841dce9eb",
        "domain": "ROOT",
        "apikey": "jLQfSMx4QXwQedR5Xkrm_yphF1Hk_63kpOfwjvAeX7YQHoajevA",
        "secretkey": "1gXpKaz-HHAH_6dCsHAhO4i4HaBLC0zYP22Z8lVBz-bkGAMfA",
        "accountid": "3395d7ba-7e32-4ed5-ad39-a17ee7d0c6c4",
        "flag": "false"
      }
    ]
  }
}
```

<br />

## Using json: ```looking up fields```

```bash

$ curl -K ec112.70 -d command=listUsers -d @ecapi -G -k -s -S \
  | json listusersresponse.user[0].id

46a34524-f253-4132-b894-afd49ca90af4

$ curl -K ec112.70 -d command=listUsers -d @ecapi -G -k -s -S \
  | json listusersresponse.user[0].id listusersresponse.user[0].email

46a34524-f253-4132-b894-afd49ca90af4 abhishek@cloudbyte.com
```

<br />

## Using json: An advanced usage involving Unix pipes.

- NOTE - -a is used for operations on a json array.
  - also used to display the stream of texts -into- a single line separated by space.
- NOTE - The commands can be copied & pasted directly on the terminal.
- Keep an eye on below **FACTS** seen in the snippet:
  - ```Error handling```.
  - ```No need to use of grep & sed```.
  - Running the operations in sequence.
    - i.e. ```chaining operations``` via Unix pipe.
  - Each operation can be ```tested in isolation``` if one wants to do so.
  - ```Output of a command``` can be ```fed as input``` to next command.

```bash

$ curl -K ec112.70 -d command=listUsers -d @ecapi -G -k -s -S  | json
{
  "listusersresponse": {
    "count": 3,
    "user": [
      {
        "id": "46a34524-f253-4132-b894-afd49ca90af4",
        "username": "admin",
        "firstname": "Abhishek",
        "lastname": "Shrivastava",
        "email": "abhishek@cloudbyte.com",
        "created": "2016-04-27 14:50:12",
        ...        
        "accountid": "3395d7ba-7e32-4ed5-ad39-a17ee7d0c6c4",
        "flag": "false"
      },
      {
        "id": "0ea51c0b-ae0f-4d10-85d4-737f1e7299af",
        "username": "amitdas@cloudbyte.com",
        "created": "2016-06-16 10:36:17",
        "state": "enabled",
        "account": "amitdas@cloudbyte.com",
        ...
        "sites": [
          {
            "siteId": "1",
            "siteName": "SITE1"
          }
        ]
      },
      {
        "id": "14e0c793-c718-4c67-b24f-25840c12d2fd",
        "username": "das@cb.com",
        "created": "2016-06-16 10:37:33",
        "state": "enabled",
        "account": "das@cb.com",
        "accounttype": 32,
        ...
        "flag": "true",
        "accounts": [
          {
            "accountName": "NewAc1",
            "accountId": "3"
          }
        ]
      }
    ]
  }
}

$ curl -K ec112.70 -d command=listUsers -d @ecapi -G -k -s -S  \
  | json listusersresponse.user \
  | json -a id username email state

46a34524-f253-4132-b894-afd49ca90af4 admin abhishek@cloudbyte.com enabled
0ea51c0b-ae0f-4d10-85d4-737f1e7299af amitdas@cloudbyte.com  enabled
14e0c793-c718-4c67-b24f-25840c12d2fd das@cb.com  enabled

$ curl -K ec112.70 -d command=listUsers -d @ecapi -G -k -s -S  \
  | json listusersresponse.user \
  | json -a id username email state \
  | awk 'NF == 4 {print "OK " $1} NF < 4 {print "ERROR Number_of_fields_is_not_4"}'

OK 46a34524-f253-4132-b894-afd49ca90af4
ERROR Number_of_fields_is_not_4
ERROR Number_of_fields_is_not_4

$ curl -K ec112.70 -d command=listUsers -d @ecapi -G -k -s -S  \
  | json listusersresponse.user \
  | json -a id username email state \
  | awk 'NF == 4 {print "OK " $1} NF < 4 {print "ERROR Number_of_fields_is_not_4"}' \
  | awk '$1 == "OK" {print $2}'

46a34524-f253-4132-b894-afd49ca90af4

$ curl -K ec112.70 -d command=listUsers -d @ecapi -G -k -s -S  \
  | json listusersresponse.user \
  | json -a id username email state \
  | awk 'NF == 4 {print "OK " $1} NF < 4 {print "ERROR Number_of_fields_is_not_4"}' \
  | awk '$1 == "OK" {print "id="$2}' \
  | curl -K ec112.70 -d command=listUsers -d @- -d @ecapi -G -k -s -S \
  | json listusersresponse.user[0].email

abhishek@cloudbyte.com
```

<br />

## Billion dollar task: ```Create an ElastiStor ISCSI volume using json & cURL```

- ASSUMPTIONS:
  - The VSM Name & Account Name are provided.
    - VSM Name is ```VSMTEST2``` & Account Name is ```ACC1```
  - The new volume's named properties are as below:
    - name = ```coolvol```
    - size = ```10GB```
    - iops = ```10```
  - A file named addqos contains the common query parameters & values.
  - A file named addvol contains the common query parameters & values.
  - A file named ec112.70 contains the ElastiCenter REST path.
  - A file named ecapi contains the apiKey.
  - Environment:
    - NodeJS is installed.
    - NPM is installed.
    - A npm library called json is installed.
    - awk & curl are installed.  
- Assumptions are displayed in below snippets:

```bash

$ cat addqos
latency=15&networkspeed=0&memlimit=0&tpcontrol=false&throughput=0&iopscontrol=true

$ cat addvol
blocklength=512B&compression=off&deduplication=off&sync=always&recordsize=16k&protocoltype=ISCSI

$ cat ec112.70
url=https://20.10.112.70/client/api

$ cat ecapi
apiKey=ePg1fSMx4QXwQedR5Xkrm_yphF1Hk_63kpOfwjvAeX7YQHoajevA&response=json
```

<br />

- Requirements to create a volume via ElastiCenter.
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

- (m) refers to match, provided by user
- .. indicates presence of more query parameters
- __ indicates none
- repeat if indicates the same task may be retried again & again

<br />

### Step 1 i.e. ```listAccount```

```bash

$ curl -K ec112.70 -d command=listAccount -d @ecapi -G -k -s -S \
   | json listAccountResponse.account \
   | json -a id name

38e05c02-b3e0-44b2-b0dd-99ed7e5c551d ACC1
c7195113-83c7-4054-b659-b2dd151bcc12 Account
480cedec-534b-4099-bec4-d05b65d912e0 NewAc1
```

<br />

### Step 1 till 2 i.e. ```listAccount``` & fetching ```accountid```

```bash

$ curl -K ec112.70 -d command=listAccount -d @ecapi -G -k -s -S \
   | json listAccountResponse.account \
   | json -a id name \
   | awk '$2 == "ACC1" {print "accountid="$1}'

accountid=38e05c02-b3e0-44b2-b0dd-99ed7e5c551d
```

<br />

### Step 1 till 3 i.e. ```listAccount``` till ```listTsm```

```bash

$ curl -K ec112.70 -d command=listAccount -d @ecapi -G -k -s -S \
   | json listAccountResponse.account \
   | json -a id name \
   | awk '$2 == "ACC1" {print "accountid="$1}' \
   | curl -K ec112.70 -d command=listTsm -d @- -d @ecapi -G -k -s -S \
   | json listTsmResponse.listTsm \
   | json -a id name

d9a86229-9698-382a-a240-ff26b489a1f0 VSMTEST2
```

<br />

### Step 1 till 4 i.e. listAccount till ```addQosGroup```

- NOTE - We have **set the IOPS to 0** here as IOPS were not available in my EC.

```bash

$ curl -K ec112.70 -d command=listAccount -d @ecapi -G -k -s -S \
   | json listAccountResponse.account \
   | json -a id name \
   | awk '$2 == "ACC1" {print "accountid="$1}' \
   | curl -K ec112.70 -d command=listTsm -d @- -d @ecapi -G -k -s -S \
   | json listTsmResponse.listTsm \
   | json -a id name \
   | awk '$2 == "VSMTEST2" {print "name=QoS_coolvol&iops=0&tsmid="$1}' \
   | curl -K ec112.70 -d command=addQosGroup -d @- -d @addqos -d @ecapi -G -k -s -S \
   | json addqosgroupresponse.qosgroup \
   | json -a id tsmid parentid

3012580f-9117-3252-8b5a-6004c158a1a2 d9a86229-9698-382a-a240-ff26b489a1f0 30b63b2a-e38b-3bff-81bb-d8ddba75f68b

# NOTE - The Qos Group created here.
# This needs to be deleted before proceeding with step 1 till next.

$ curl -K ec112.70 -d command=listQosGroup -d @ecapi -G -k -s -S \
  | json listQosgroupResponse.qosgroup \
  | json -a id name

23afe0f5-622c-3cda-bf38-bb62252d5704 QoS_SampleNFSVolACC1VSMTEST2
bd0fa1c3-1867-3bec-9935-14a17630c5d7 QoS_Vol1ACC1VSM23
d1740e18-1843-3c89-8d3d-4769b9526a8d QoS_coolvolACC1VSMTEST2

$ curl -K ec112.70 -d command=deleteQosGroup -d id=d1740e18-1843-3c89-8d3d-4769b9526a8d -d @ecapi -G -k -s -S

{ "deleteQosgroupResponse" : { "success" : "true"}  }
```

<br />

### Step 1 till 5 i.e. ```listAccount``` till ```createVolume```

```bash

$ curl -K ec112.70 -d command=listAccount -d @ecapi -G -k -s -S \
   | json listAccountResponse.account \
   | json -a id name \
   | awk '$2 == "ACC1" {print "accountid="$1}' \
   | curl -K ec112.70 -d command=listTsm -d @- -d @ecapi -G -k -s -S \
   | json listTsmResponse.listTsm \
   | json -a id name \
   | awk '$2 == "VSMTEST2" {print "name=QoS_coolvol&iops=0&tsmid="$1}' \
   | curl -K ec112.70 -d command=addQosGroup -d @- -d @addqos -d @ecapi -G -k -s -S \
   | json addqosgroupresponse.qosgroup \
   | json -a id tsmid parentid \
   | awk '{print "name=coolvol&datasetid="$3"&quotasize=10G&tsmid="$2"&qosgroupid="$1}' \
   | curl -K ec112.70 -d command=createVolume -d @- -d @addvol -d @ecapi -G -k -s -S \
   | json createvolumeresponse.jobid \


# NOTE - The volume would have got created.
# This needs to be deleted before proceeding with step 1 till next.

$ curl -K ec112.70 -d command=listFileSystem -d @ecapi -G -k -s -S \
  | json listFilesystemResponse.filesystem \
  | json -a id name

444fc933-e907-3817-8316-f2ffe5f0bb3c SampleNFSVol
1689ce42-26b3-32d5-bef9-50de5461f4ae Vol1
8bee4c6f-8c5d-3a06-8eee-db8d361c6de6 coolvol

$ curl -K ec112.70 -d command=deleteFileSystem \
  -d id=8bee4c6f-8c5d-3a06-8eee-db8d361c6de6 -d @ecapi -G -k -s -S

{ "deleteFileSystemResponse" : {
  "jobid": "213156a2-4d65-4e9d-9a4e-2bd29c53bb68"
} }

$ curl -K ec112.70  -d command=queryAsyncJobResult \
  -d jobId=213156a2-4d65-4e9d-9a4e-2bd29c53bb68 -d @ecapi -G -k -s

{ "queryasyncjobresultresponse" : {
  "accountid": "3395d7ba-7e32-4ed5-ad39-a17ee7d0c6c4",
  "userid": "46a34524-f253-4132-b894-afd49ca90af4",
  "cmd": "com.cloudbyte.api.commands.DeleteFileSystemCmd",
  "msg": "Deleted Successfully",
  "jobstatus": 1,
  "jobprocstatus": 0,
  "jobresultcode": 0,
  "jobresulttype": "object",
  "jobresult": {
    "success": true
  },
  "created": "2016-06-17 15:30:47",
  "jobid": "213156a2-4d65-4e9d-9a4e-2bd29c53bb68"
} }
```

<br />

### Step 1 till 6 i.e. ```listAccount``` till ```queryAsyncJobResult```

- NOTE - We have used ```sleep``` to handle this async call.

```bash

$ curl -K ec112.70 -d command=listAccount -d @ecapi -G -k -s -S \
  | json listAccountResponse.account \
  | json -a id name \
  | awk '$2 == "ACC1" {print "accountid="$1}' \
  | curl -K ec112.70 -d command=listTsm -d @- -d @ecapi -G -k -s -S \
  | json listTsmResponse.listTsm \
  | json -a id name \
  | awk '$2 == "VSMTEST2" {print "name=QoS_coolvol&iops=0&tsmid="$1}' \
  | curl -K ec112.70 -d command=addQosGroup -d @- -d @addqos -d @ecapi -G -k -s -S \
  | json addqosgroupresponse.qosgroup \
  | json -a id tsmid parentid \
  | awk '{print "name=coolvol&datasetid="$3"&quotasize=10G&tsmid="$2"&qosgroupid="$1}' \
  | curl -K ec112.70 -d command=createVolume -d @- -d @addvol -d @ecapi -G -k -s -S \
  | json createvolumeresponse.jobid \
  | awk '{system("sleep 10"); print "jobId="$1}' \
  | curl -K ec112.70 -d command=queryAsyncJobResult -d @- -d @ecapi -G -k -s -S \
  | json -a queryasyncjobresultresponse.jobid queryasyncjobresultresponse.jobstatus \
  | awk '$2 == 1 {print "SUCCESS "$1} $2 == 2 {print "ERROR "$1} $2 == 0 {print "IN-PROGRESS "$1}'
```

### Step 1 till 7 i.e. ```listAccount``` till ```new volume confirmation```

```bash

$ curl -K ec112.70 -d command=listAccount -d @ecapi -G -k -s -S \
  | json listAccountResponse.account \
  | json -a id name \
  | awk '$2 == "ACC1" {print "accountid="$1}' \
  | curl -K ec112.70 -d command=listTsm -d @- -d @ecapi -G -k -s -S \
  | json listTsmResponse.listTsm \
  | json -a id name \
  | awk '$2 == "VSMTEST2" {print "name=QoS_coolvol&iops=0&tsmid="$1}' \
  | curl -K ec112.70 -d command=addQosGroup -d @- -d @addqos -d @ecapi -G -k -s -S \
  | json addqosgroupresponse.qosgroup \
  | json -a id tsmid parentid \
  | awk '{print "name=coolvol&datasetid="$3"&quotasize=10G&tsmid="$2"&qosgroupid="$1}' \
  | curl -K ec112.70 -d command=createVolume -d @- -d @addvol -d @ecapi -G -k -s -S \
  | json createvolumeresponse.jobid \
  | awk '{system("sleep 10"); print "jobId="$1}' \
  | curl -K ec112.70 -d command=queryAsyncJobResult -d @- -d @ecapi -G -k -s -S \
  | json -a queryasyncjobresultresponse.jobid queryasyncjobresultresponse.jobstatus \
  | awk '$2 == 1 {print "SUCCESS "$1} $2 == 2 {print "ERROR "$1} $2 == 0 {print "IN-PROGRESS "$1}' \
  | awk '$1 == "SUCCESS" {print "jobId="$2} $1 != "SUCCESS" {print $0; exit 1}' \
  | curl -K ec112.70 -d command=queryAsyncJobResult -d @- -d @ecapi -G -k -s -S \
  | json -a queryasyncjobresultresponse.jobresult.storage.id
```
