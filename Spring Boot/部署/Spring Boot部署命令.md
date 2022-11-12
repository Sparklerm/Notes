## Linux

启动项目(start_boot.sh)

```sh
#!/bin/bash
if [ ! -n "$1" ]; then
    echo Please Enter jar file name
    exit
fi

NAME=$1

nohup java -Duser.timezone=Asia/Shanghai -Xms512m -Xmx1024m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m -XX:+HeapDumpOnOutOfMemoryError -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:NewRatio=1 -XX:SurvivorRatio=30 -XX:+UseParallelGC -XX:+UseParallelOldGC -jar -Dspring.profiles.active=prod $NAME.jar --server.port=8080  > ./logs/$NAME.log 2>&1 &

echo $NAME START SUCCESS.
data-generator
```

```bash
nohup java -Duser.timezone=Asia/Shanghai -Xms512m -Xmx1024m -Xmn256m -XX:+HeapDumpOnOutOfMemoryError -jar --server.port=8080 $NAME.jar  > ./logs/$NAME.log 2>&1 &
```



停止项目(shutdown_boot.sh)

```bash
#!/bin/bash
if [ ! -n "$1" ]; then
    echo Please Enter jar file name
    exit
fi
NAME=$1
PID=$(ps -ef | grep $NAME.jar | grep -v grep | awk '{print $2}')
if [ -z "$PID"]
then
echo Application is already stopped.
else
echo kill -15 $PID
kill -15 $PID
fi
echo $NAME.jar Shutdown Success.
```

项目重启(restart_boot.sh)

```bash
#!/bin/bash
. ./shutdown_boot.sh && . ./start_boot.sh
ECHO $NAME.jar Restart Success.
```

```
[-Dname=target/ruoyi-vue.jar, -Duser.timezone=Asia/Shanghai, -Xms512m, -Xmx1024m, -XX:MetaspaceSize=128m, -XX:MaxMetaspaceSize=512m, -XX:+HeapDumpOnOutOfMemoryError, -XX:+PrintGCDateStamps, -XX:+PrintGCDetails, -XX:NewRatio=1, -XX:SurvivorRatio=30, -XX:+UseParallelGC, -XX:+UseParallelOldGC]
```





## windows

```

```

