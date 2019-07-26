| Title                | First time seen remote named pipe                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | This detection excludes known namped pipes accessible remotely and notify on newly observed ones, may help to detect lateral movement and remote exec using named pipes                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0008: Lateral Movement](https://attack.mitre.org/tactics/TA0008)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1077: Windows Admin Shares](https://attack.mitre.org/techniques/T1077)</li></ul>  |
| Data Needed          | <ul><li>[DN_0032_5145_network_share_object_was_accessed_detailed](../Data_Needed/DN_0032_5145_network_share_object_was_accessed_detailed.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1077: Windows Admin Shares](../Triggers/T1077.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>update the excluded named pipe to filter out any newly observed legit named pipe</li></ul>  |
| Development Status   |  Development Status wasn't defined for this Detection Rule yet  |
| References           | <ul><li>[https://twitter.com/menasec1/status/1104489274387451904](https://twitter.com/menasec1/status/1104489274387451904)</li></ul>  |
| Author               | Samir Bousseaden |


## Detection Rules

### Sigma rule

```
title: First time seen remote named pipe
description: This detection excludes known namped pipes accessible remotely and notify on newly observed ones, may help to detect lateral movement and remote exec using named pipes
author: Samir Bousseaden
references:
    - https://twitter.com/menasec1/status/1104489274387451904
tags:
    - attack.lateral_movement
    - attack.t1077
logsource:
    product: windows
    service: security
    description: 'The advanced audit policy setting "Object Access > Audit Detailed File Share" must be configured for Success/Failure'
detection:
    selection1:
        EventID: 5145
        ShareName: \\*\IPC$
    selection2:
        EventID: 5145
        ShareName: \\*\IPC$
        RelativeTargetName:
         - 'atsvc'
         - 'samr'
         - 'lsarpc'
         - 'winreg'
         - 'netlogon'
         - 'srvsvc'
         - 'protected_storage'
         - 'wkssvc'
         - 'browser'
         - 'netdfs'
    condition: selection1 and not selection2
falsepositives: 
    - update the excluded named pipe to filter out any newly observed legit named pipe
level: high

```





### es-qs
    
```
((EventID:"5145" AND ShareName:"\\\\*\\\\IPC$") AND (NOT (EventID:"5145" AND ShareName:"\\\\*\\\\IPC$" AND RelativeTargetName:("atsvc" "samr" "lsarpc" "winreg" "netlogon" "srvsvc" "protected_storage" "wkssvc" "browser" "netdfs"))))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/First-time-seen-remote-named-pipe <<EOF\n{\n  "metadata": {\n    "title": "First time seen remote named pipe",\n    "description": "This detection excludes known namped pipes accessible remotely and notify on newly observed ones, may help to detect lateral movement and remote exec using named pipes",\n    "tags": [\n      "attack.lateral_movement",\n      "attack.t1077"\n    ],\n    "query": "((EventID:\\"5145\\" AND ShareName:\\"\\\\\\\\*\\\\\\\\IPC$\\") AND (NOT (EventID:\\"5145\\" AND ShareName:\\"\\\\\\\\*\\\\\\\\IPC$\\" AND RelativeTargetName:(\\"atsvc\\" \\"samr\\" \\"lsarpc\\" \\"winreg\\" \\"netlogon\\" \\"srvsvc\\" \\"protected_storage\\" \\"wkssvc\\" \\"browser\\" \\"netdfs\\"))))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "((EventID:\\"5145\\" AND ShareName:\\"\\\\\\\\*\\\\\\\\IPC$\\") AND (NOT (EventID:\\"5145\\" AND ShareName:\\"\\\\\\\\*\\\\\\\\IPC$\\" AND RelativeTargetName:(\\"atsvc\\" \\"samr\\" \\"lsarpc\\" \\"winreg\\" \\"netlogon\\" \\"srvsvc\\" \\"protected_storage\\" \\"wkssvc\\" \\"browser\\" \\"netdfs\\"))))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'First time seen remote named pipe\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
((EventID:"5145" AND ShareName:"\\\\*\\\\IPC$") AND NOT (EventID:"5145" AND ShareName:"\\\\*\\\\IPC$" AND RelativeTargetName:("atsvc" "samr" "lsarpc" "winreg" "netlogon" "srvsvc" "protected_storage" "wkssvc" "browser" "netdfs")))
```


### splunk
    
```
((EventID="5145" ShareName="\\\\*\\\\IPC$") NOT (EventID="5145" ShareName="\\\\*\\\\IPC$" (RelativeTargetName="atsvc" OR RelativeTargetName="samr" OR RelativeTargetName="lsarpc" OR RelativeTargetName="winreg" OR RelativeTargetName="netlogon" OR RelativeTargetName="srvsvc" OR RelativeTargetName="protected_storage" OR RelativeTargetName="wkssvc" OR RelativeTargetName="browser" OR RelativeTargetName="netdfs")))
```


### logpoint
    
```
((EventID="5145" ShareName="\\\\*\\\\IPC$")  -(EventID="5145" ShareName="\\\\*\\\\IPC$" RelativeTargetName IN ["atsvc", "samr", "lsarpc", "winreg", "netlogon", "srvsvc", "protected_storage", "wkssvc", "browser", "netdfs"]))
```


### grep
    
```
grep -P '^(?:.*(?=.*(?:.*(?=.*5145)(?=.*\\\\.*\\IPC\\$)))(?=.*(?!.*(?:.*(?=.*5145)(?=.*\\\\.*\\IPC\\$)(?=.*(?:.*atsvc|.*samr|.*lsarpc|.*winreg|.*netlogon|.*srvsvc|.*protected_storage|.*wkssvc|.*browser|.*netdfs))))))'
```



