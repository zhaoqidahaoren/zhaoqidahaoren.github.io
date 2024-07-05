---
title: Deploy
category: Self Service
order: 2
permalink: /Deploy/
ulOrder: 2
clickable: true
---

# CocoCat Self-Service Deployment Guide

This document primarily provides a self-service deployment guide for CocoCat, aiming to ensure the successful deployment and operation of self-service, and to provide the development team with an efficient and stable service infrastructure. It includes detailed installation and configuration of the main service, MongoDB, and Redis, as well as guidance on developing the CocoApp self-service project.

## Table of Contents

1. Installation and Configuration
    - 1.1 Minimum Hardware Requirements
    - 1.2 Source Code Installation
        - 1.2.1 Ubuntu System
        - 1.2.2 Other Operating Systems
    - 1.3 Docker Installation
2. Configuration Parameters
3. CocoApp Self-Service Configuration
4. Troubleshooting

---


## Installation and Configuration

### Minimum Hardware Requirements

- CPU: 2 cores
- Memory: 4GB

### Source Code Installation

#### Ubuntu System

**Operating System Requirements**

- Ubuntu 22.04 or higher

**Software Tools Requirements**

- Node.js version 16 or higher

1. **Download and extract** installation files from the [project official website]:(https://example.com/cococatserver.zip)

   ```bash
   wget https://cococat.com/cococatserver.zip
   unzip cococatserver.zip
   cd cococatserver
   ```

2. **Install project dependencies**

   ```bash
   npm install
   ```

3. **Install and configure MongoDB** Refer to the [official MongoDB installation guide](https://www.mongodb.com/docs/manual/installation/) and follow the overview steps below:

    1. **Install MongoDB**

   ```bash
   wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
   echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
   sudo apt update
   sudo apt install -y mongodb-org
   ```

    2. **Start MongoDB service**

   ```bash
   sudo systemctl start mongod
   sudo systemctl enable mongod
   ```

    3. **Configure MongoDB user**

   ```bash
   mongo
   use admin
   db.createUser({
    user: "admin",
    pwd: "adminpassword",
    roles: [{ role: "userAdminAnyDatabase", db: "admin" }]
   })
   ```

4. **Install and configure Redis** Refer to [the official Redis installation guide](https://redis.io/docs/getting-started/installation/) and follow the overview steps below:

    1. **Install Redis**

   ```bash
   sudo apt update
   sudo apt install -y redis-server
   ```

    2. **Configure Redis password** Edit the `/etc/redis/redis.conf` file:

   ```conf
   requirepass yourredispassword
   ```

5. **Start Redis service**

   ```bash
   sudo systemctl restart redis.service
   sudo systemctl enable redis.service
   ```

6. **Start self-service**

   After configuring `config.conf`, start the self-service. For detailed configuration, see [Configuration Parameters].(#Configuration Parameters).

   ```bash
   node cococat.js # It is recommended to run in the background
   ```

   Enter the `cococat-route` directory, configure `pango.conf`, and then start the service. For detailed configuration, see [Configuration Parameters] (#Configuration Parameters).

   ```bash
   cd cococat-route
   chmod +x cococatroute
   ./cococatroute # It is recommended to run in the background
   ```

7. **Confirm Successful Startup**
   Check the startup logs for the message `Service connection completed!`. `Logs` can be viewed in the logs directory.



### Docker Installation ##

**Software Tools Requirements**

- Docker version 20.10 or higher
- Docker Compose version 1.28 or higher

#### Docker Environment Preparation

1. **Install Docker**
   Refer to [the official Docker documentatio](https://docs.docker.com/engine/install/ubuntu/) for Docker installation.

2. **Install Docker Compose**
   Refer to [the official Docker Compose documentation](https://docs.docker.com/compose/install/) for Docker Compose installation.

#### Installation Using Docker Compose

1. **Create the docker-compose.yml file**

   ```yaml
   version: '3'
   services:
   
     redis-server:
       image: redis:latest
       container_name: redis-server
       restart: always
       ports:
         - 6379:6379
       volumes:
         - ./redis:/data
       command: redis-server --requirepass <password>
   
     mongodb-server:
       image: mongo:4.4.23
       container_name: mongodb-server
       restart: always
       depends_on:
         - redis-server
       ports:
         - 27017:27017
       environment:
         MONGO_INITDB_ROOT_USERNAME: <username>
         MONGO_INITDB_ROOT_PASSWORD: <password>
       volumes:
         - ./mongo:/data/db
   
     cococat-route:
       image: cococat/cocoroute:v1.0
       container_name: cococat-route
       restart: always
       depends_on:
         - mongodb-server
       ports:
         - 4567:4567
       volumes:
         - ./config.json:/app/config.json
         - ./logs:/app/logs
         - ./cococat_route/pango.conf:/app/cococat_route/pango.conf
         - ./data/cococatroute/userkey.json:/app/cococat_route/userkey.json  #userkey.json needs to be pre-created as an empty file on the host
         - ./data/cococatroute/data:/app/cococat_route/data
         - ./data/cococatroute/pangomail:/app/cococat_route/pangomail
   ```


2. **Configuration Files**

   Mainly configure `config.json` and `pango.conf`. For detailed configuration, see [Configuration Parameters] (#Configuration Parameters).

3. **Start the Services**

   ```bash
   docker-compose up -d
   ```

4. **Stop the Services**

   ```bash
   docker-compose down
   ```

5. **Confirm Successful Startup**

- Use `docker-compose ps` to check the service status:

  ```bash
  docker-compose ps
  ```

  Look for `Service connection completed!` in the startup logs, which can be found in the `logs` directory.



## Configuration Parameters

CocoCat self-service mainly consists of `cococat-web` and `cococat-route`.

### cococat-web

#### config.json

This configuration file includes the following parameters:

```json
{
    "AppTitle": "CocoCat-Web",
    "mongo": {
        "host": "127.0.0.1",
        "port": "27017",
        "username": "admin",
        "password": "",
        "database": "admin",
        "uri": "",
        "dbName": "cococatdb"
    },
    "redis": {
        "host": "127.0.0.1",
        "port": 6379,
        "db": 0,
        "password": ""
    },
    "bodyParser": {
        "urlencoded": {
            "limit": "1mb"
        },
        "json": {
            "limit": "1mb"
        }
    },
    "port": "4567",
    "remoteService": "http://127.0.0.1:8088",
    "queue": "redis"
}
```


##### Parameter Descriptions

+ **`AppTitle`**: Name, default is `CocoCat-Web`.
+ **`mongo`**:
    - **`host`**: MongoDB host address, default is `127.0.0.1`.
    - **`port`**: MongoDB port number, default is `27017`.
    - **`username`**: MongoDB username.
    - **`password`**: MongoDB password.
    - **`database`**: MongoDB authentication database, default is `admin`.
    - **`uri`**: MongoDB connection URI, if set, it takes precedence.
    - **`dbName`**: MongoDB database name, default is `cococatdb`.
+ **`redis`**:
    - **`host`**: Redis host address, default is `127.0.0.1`.
    - **`port`**: Redis port number, default is `6379`.
    - **`db`**: Redis database index, default is `0`.
    - **`password`**: Redis password, default is empty.
+ **`bodyParser`**:
    - **`urlencoded.limit`**: Maximum size limit for URL-encoded request bodies, default is `1mb`.
    - **`json.limit`**: Maximum size limit for JSON request bodies, default is `1mb`.
+ **`port`**: Port number that `cococat-web` listens on, default is `4567`.
+ **`remoteService`**: Backend service address, format is `http://{IP}:{port}`, default is `http://127.0.0.1:8080`.
+ **`queue`**: Queue component type, default is `redis`.

**Note**: Ensure that the MongoDB and Redis connection information is correct and provide appropriate values for all required parameters to ensure the proper operation of the `cococat-web` service.

### cococat-route

#### `pango.conf`

This configuration file includes the following parameters：

```c
[global]
localsrvport=4568
t2trepeater=14ACRC3zwcqcsytNYhc6xT4hUtzhbGAVcK
nodserverurl=http://127.0.0.1:4567
defaultpeers=ODAuMjUxLjIxNS4yMjg7MTA0LjEyOC45MC4yNzsxMDQuMjI0LjE3Ni4xNTE7ODAuMjUxLjIxNi4zMzs2NS40OS4yMDMuNTY7NjUuNDkuMjA2Ljk0OzY1LjQ5LjIxOC4xMDg=
```

##### Parameter Descriptions

+ **`localsrvport`**：Port number for `cococat-route` service, default is `4568`.
+ **`t2trepeater`**：BTC address of the relay service.
+ **`nodserverurl`**：URL of the `cococat-web` service, default is `http://127.0.0.1:4567`.
+ **`defaultpeers`**：List of default nodes.


## CocoApp Self-Service Configuration

According to the `CocoApp` development guide, ensure that the development environment is ready。

### Obtain the Service Key for Self-Service

To integrate self-service, you need to obtain a unique `service-key`。This can be achieved by calling the `getServiceKey` interface。Use the following command to get the `service-key` from the local server：

```bash
curl http://127.0.0.1:4567/getServiceKey
```

#### Example of a successful response：

```json
{
    "code": 200,
    "data": "eyJhdXRoc2lnbiI6Ik1FVUNJUURhOFlFZytNUU5ONXZpNEhHZDlRMDk4dVdlZzBZcDA0bGdoK2FSMlRSUWJ3SWdRM3RqQ1ZGMi9UQm9jRURGL0NrTGF5bjFqc2UrS1JHTjRUSy9RWnhHZmdFeCIsImhvc3QiOiIxNEFDUkMzendjcWNzeXROWWhjNnhUNGhVdHpoYkdBVmNLIiwibWFqb3JrZXkiOiJBeG9NamR3b0FadGRRVlRNNzNIc0ZkOVB1S005OEEyRDQ1bE43MlhST3lnUyIsIm1pbm9rS2V5IjoiQXRKM0Zxd1pkK3duTXhDRVd1VXc1Ymd4a1JycEZvdjdCbE1PaFhiVFYrVjIiLCJwcmlrZXkiOiJJQmFLTDZsY2RwWitUYSt5VjBITG1pN0l6ZVpQTkxlcDZnWldXaGt6QkFVPSJ9"
}
```

### Use the Service Key

After successfully obtaining the `service-key`, use it in the `registerService` method of `CocoApp` to register the self-service functionality。This is a key step in integrating self-service with `CocoApp`。

### Develop Self-Service Functionality

After obtaining the `service-key` and completing the registration, you can start implementing the self-service communication functionality, enabling the self-service to interact with other services for data exchange and message transmission.

Ensure to configure and call the relevant APIs correctly according to the development documentation to ensure stable operation and a good user experience of the service.
