```bash
docker pull postgres:14

docker run -itd \
--name pgsql \
-p 5432:5432 \
-e POSTGRES_PASSWORD=123456 \
-v /usr/server/postgresql/data:/var/lib/postgresql/data \
postgres:14
```

