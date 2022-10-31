```
docker run -d \
--name zk \
-p 2181:2181 \
-v /root/zookeeper/3.6.3/data:/data \
-v /root/zookeeper/3.6.3/datalog:/datalog \
-e "TZ=Asia/Shanghai" \
zookeeper:3.6.3
```

