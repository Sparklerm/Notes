在**http**块内或者在单个**server**块里添加后**重启nginx ./nginx -s reload**

```
# 开启gzip
gzip on;

gzip_disable "MSIE [1-6]\.";

gzip_proxied any;

gzip_min_length 1k;

gzip_comp_level 5;

gzip_types text/plain application/x-javascript application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
```

