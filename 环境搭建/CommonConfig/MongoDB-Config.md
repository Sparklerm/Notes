## 4.4.6

```shell
## content
systemLog:
  destination: file
  logAppend: true
  path: /www/server/mongodb/log/config.log
 
# Where and how to store data.
storage:
  dbPath: /www/server/mongodb/data
  directoryPerDB: true

  journal:
    enabled: true
# how the process runs
processManagement:
  fork: true
  pidFilePath: /www/server/mongodb/log/configsvr.pid
 
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0
 
#operationProfiling:
#replication:
#    replSetName: bt_main   
security:
  authorization: disabled
  javascriptEnabled: false

#sharding:
#    clusterRole: shardsvr
```