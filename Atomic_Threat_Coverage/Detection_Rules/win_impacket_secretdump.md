| Title                | Possible Impacket SecretDump remote activity                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detect AD credential dumping using impacket secretdump HKTL                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0006: Credential Access](https://attack.mitre.org/tactics/TA0006)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1003: Credential Dumping](https://attack.mitre.org/techniques/T1003)</li></ul>  |
| Data Needed          | <ul><li>[DN_0032_5145_network_share_object_was_accessed_detailed](../Data_Needed/DN_0032_5145_network_share_object_was_accessed_detailed.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1003: Credential Dumping](../Triggers/T1003.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>pentesting</li></ul>  |
| Development Status   |  Development Status wasn't defined for this Detection Rule yet  |
| References           | <ul><li>[https://blog.menasec.net/2019/02/threat-huting-10-impacketsecretdump.html](https://blog.menasec.net/2019/02/threat-huting-10-impacketsecretdump.html)</li></ul>  |
| Author               | Samir Bousseaden |


## Detection Rules

### Sigma rule

```
title: Possible Impacket SecretDump remote activity
description: Detect AD credential dumping using impacket secretdump HKTL
author: Samir Bousseaden
references:
    - https://blog.menasec.net/2019/02/threat-huting-10-impacketsecretdump.html
tags:
    - attack.credential_access
    - attack.t1003
logsource:
    product: windows
    service: security
    description: 'The advanced audit policy setting "Object Access > Audit Detailed File Share" must be configured for Success/Failure'
detection:
    selection:
        EventID: 5145
        ShareName: \\*\ADMIN$
        RelativeTargetName: 'SYSTEM32\*.tmp'
    condition: selection
falsepositives: 
    - pentesting
level: high

```





### es-qs
    
```
(EventID:"5145" AND ShareName:"\\\\*\\\\ADMIN$" AND RelativeTargetName:"SYSTEM32\\*.tmp")
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/Possible-Impacket-SecretDump-remote-activity <<EOF\n{\n  "metadata": {\n    "title": "Possible Impacket SecretDump remote activity",\n    "description": "Detect AD credential dumping using impacket secretdump HKTL",\n    "tags": [\n      "attack.credential_access",\n      "attack.t1003"\n    ],\n    "query": "(EventID:\\"5145\\" AND ShareName:\\"\\\\\\\\*\\\\\\\\ADMIN$\\" AND RelativeTargetName:\\"SYSTEM32\\\\*.tmp\\")"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(EventID:\\"5145\\" AND ShareName:\\"\\\\\\\\*\\\\\\\\ADMIN$\\" AND RelativeTargetName:\\"SYSTEM32\\\\*.tmp\\")",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Possible Impacket SecretDump remote activity\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:"5145" AND ShareName:"\\\\*\\\\ADMIN$" AND RelativeTargetName:"SYSTEM32\\*.tmp")
```


### splunk
    
```
(EventID="5145" ShareName="\\\\*\\\\ADMIN$" RelativeTargetName="SYSTEM32\\*.tmp")
```


### logpoint
    
```
(EventID="5145" ShareName="\\\\*\\\\ADMIN$" RelativeTargetName="SYSTEM32\\*.tmp")
```


### grep
    
```
grep -P '^(?:.*(?=.*5145)(?=.*\\\\.*\\ADMIN\\$)(?=.*SYSTEM32\\.*\\.tmp))'
```



