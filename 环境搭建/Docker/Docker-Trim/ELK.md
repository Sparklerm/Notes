```
docker run -d \
--name es \
-p 9200:9200 \
-p 9300:9300 \
-v /root/elk7/es/config/:/usr/share/elasticsearch/config \
-v /root/elk7/es/data/:/usr/share/elasticsearch/data \
-v /root/elk7/es/plugins//:/usr/share/elasticsearch/plugins \
-v /root/elk7/es/logs/:/usr/share/elasticsearch/logs \
-e "discovery.type=single-node" \
--privileged=true \
elasticsearch:7.16.2
```

```
docker run -d \
--name kibana \
-p 5601:5601 \
--link es \
-e ELASTICSEARCH_URL=http://es:9200 \
-v /root/elk7/kibana/config/:/usr/share/kibana/config \
-v /root/elk7/kibana/data/:/usr/share/kibana/data \
-v /root/elk7/kibana/plugins/:/usr/share/kibana/plugins \
kibana:7.16.2
```

```
docker run -d \
--name logstash \
--link es \
-v /root/elk7/logstash/config/:/usr/share/logstash/config \
-v /root/elk7/logstash/data/:/usr/share/logstash/data \
-v /root/elk7/logstash/pipeline/:/usr/share/logstash/pipeline \
logstash:7.16.2
```

