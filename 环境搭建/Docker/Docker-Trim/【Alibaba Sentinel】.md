## Docker安装Sentinel

```shell
docker pull bladex/sentinel-dashboard:1.8.0

docker run -d \
--name sentinel \
--restart=always \
-p 8858:8858 \
bladex/sentinel-dashboard:1.8.0
```