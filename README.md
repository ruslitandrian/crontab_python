# crontab_python

## 排程時間設定

sudo vi /etc/crontab
```sh
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
#MAILTO=root
MAILTO=""
 
# For details see man 4 crontabs
 
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
00 23 *  *  *   root    /usr/sbin/ntpdate 192.168.111.3 > /dev/null 2>&1
# 每天的　19:24 19:34 20:24 20:34 21:24 21:34
24,34 19,20,21 * * * root /usr/bin/python /data/jobs/dbaMonitor.py > /dev/null 2>&1
# 每天中午 12 點
0 12 * * * root /usr/bin/python /data/jobs/cpuUsage.py > /dev/null 2>&1
# 每小時的 15分＆30分
15,30 * * * * root /usr/bin/python /data/jobs/dataExport.py > /dev/null 2>&1

```

## 排程邏輯撰寫範本
```sh
#!/usr/bin/python
 
import requests
import json
 
# 參數
data = {"Data": { "Token": "你的 Token "}}
# 被呼叫時打到哪一台API
url = "http://YYYYY.com:42111/DxYYYYYRequest?format=json"
headers = {'Content-type': 'application/json', 'Accept': 'text/plain'}
response = requests.post(url, data=json.dumps(data), headers=headers)
result = json.loads(response.content)#印出回傳結果#print(result)
 
 ```
 
## 如何新增新的排程
1. sudo cp /data/jobs/dbaMonitor.py /data/jobs/xxx.py  << 複製一份
2. sudo vi xxx.py << 編輯且換掉參數及POST 目標 URL
3. sudo vi /etc/crontab   << 設定排程時間
4. 驗證排程時間是否如預期　http://cron.schlitt.info/index.php
5. /usr/bin/python /data/jobs/xxx.py << 測試 python 是否有寫錯　

## 平日監看排程是否有在跑
```sh
tail -f  /var/log/cron
```
