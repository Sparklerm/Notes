```yaml
version: '3'
services:
  rabbitmq:
    image: rabbitmq:3.9.0-management
    container_name: rabbitmq
    hostname: rabbit
    restart: always
    ports:
      - 5672:5672
      - 15672:15672
    environment:
      TZ: Asia/Shanghai
      LANG: en_US.UTF-8
    volumes:
      # - ./data:/var/lib/rabbitmq
      - ./conf:/etc/rabbitmq
```

