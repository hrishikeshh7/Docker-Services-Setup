---------------------------------------------------------------------------------------------------------------------------
# Docker Compose Setup Guide
This guide will help you set up multiple services using Docker Compose. Follow these steps to host the services 

#### Step 1: Create docker-compose.yml File
#### Step 2: Paste the following code into docker-compose.yml:


```
version: "3.8"

services:

  #------------------------------------------------------------------------------>
  # POSTGRES DATABASE
  #------------------------------------------------------------------------------>

  postgres_db:
    container_name: postgres_db
    image: postgres:14.1
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - TZ=Asia/Kolkata
    ports:
      - '5440:5432'
    volumes:
      - ./postgres/data:/var/lib/postgresql/data

  #------------------------------------------------------------------------------>
  # PORTAINER
  #------------------------------------------------------------------------------>

  portainer:
    container_name: portainer
    restart: unless-stopped
    image: portainer/portainer
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./portainer_data:/data
    environment:
      - TZ=Asia/Kolkata
    logging:
      driver: "json-file"
      options:
        max-size: "10m"

  #------------------------------------------------------------------------------>
  # GRAFANA
  #------------------------------------------------------------------------------>

  grafana:
    container_name: grafana
    restart: unless-stopped
    image: grafana/grafana:8.5.22
    ports:
      - "3000:3000"
    volumes:
      - ./grafana_data:/var/lib/grafana
      - ./grafana_log:/var/log/grafana
      - ./grafana_etc:/etc/grafana
    logging:
      driver: "json-file"
      options:
        max-size: "10m"

  #------------------------------------------------------------------------------>
  # INFLUXDB DATABASE
  #------------------------------------------------------------------------------>
        
  influxdb:
    container_name: influxdb2
    restart: unless-stopped
    image: influxdb:latest
    ports:
      - '8086:8086'
    environment:
      - DOCKER_INFLUXDB_INIT_MODE=setup
      - DOCKER_INFLUXDB_INIT_USERNAME=root
      - DOCKER_INFLUXDB_INIT_PASSWORD=rootrootroot
      - DOCKER_INFLUXDB_INIT_ORG=iam
      - DOCKER_INFLUXDB_INIT_BUCKET=data
    volumes:
      - ./config:/etc/influxdb2
      - ./data:/var/lib/influxdb2
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        
  #------------------------------------------------------------------------------>
  # REDIS DATABASE
  #------------------------------------------------------------------------------>

  redis_db:
    container_name: redis_db
    image: redis:latest
    restart: always
    ports:
      - '6379:6379'
    command: redis-server --save 60 1 --loglevel warning
    volumes:
      - ./redis_stack:/data
    logging:
      driver: "json-file"
      options:
        max-size: "10m"

  #------------------------------------------------------------------------------>
  # REDIS INSIGHT
  #------------------------------------------------------------------------------>
  redis-insight:
    container_name: redis-insight
    image: redislabs/redisinsight:latest
    ports:
      - '8001:8001'
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
    depends_on:
      - redis_db
```
#### Step 3: Deploy Services
```
sudo docker-compose up -d
```

#### Step 4: Verify Containers
```
sudo docker ps
```

