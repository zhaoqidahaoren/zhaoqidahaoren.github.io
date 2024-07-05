---
title: Deploy
category: Self Service
order: 2
permalink: /china/Deploy/
ulOrder: 2
clickable: true
---

# CocoCat 自服务部署指南

本文档主要提供 CocoCat 自服务的部署指南，旨在确保自服务顺利部署并正常运行，为开发团队提供高效、稳定的服务基础设施。包括主服务、MongoDB 和 Redis 的详细安装与配置，以及对项目 CocoApp 的开发自服务指导。



## 目录

1. [安装和配置](#安装和配置)
    - 1.1 [最低硬件配置](#最低硬件配置)
    - 1.2 [源码安装](#源码安装)
        - 1.2.1 [Ubuntu 系统](#ubuntu-系统)
        - 1.2.2 [其他操作系统](#其他操作系统)
    - 1.3 [Docker 安装](#docker-安装)
2. [配置参数](#配置参数)
3. [CocoAPP自服务配置](#CocoAPP自服务配置)
4. [故障排除](#故障排除)

---



## 安装和配置

### 最低硬件配置

- CPU：2 核心
- 内存：4GB

### 源码安装

#### Ubuntu 系统

**操作系统要求**

- Ubuntu 22.04 或更高版本

**软件工具要求**

- Node.js 版本 16 或更高版本

1. **下载安装文件** 从 [项目官网下载地址](https://example.com/cococatserver.zip) 下载并解压安装文件：

   ``` bash
   wget https://cococat.com/cococatserver.zip
   unzip cococatserver.zip
   cd cococatserver
   ```

2. **安装项目依赖**

   ```bash
   npm install
   ```

3. **安装与配置 MongoDB** 请参考 [官方 MongoDB 安装指南](https://www.mongodb.com/docs/manual/installation/) 并按以下概览步骤操作：

    1. **安装 MongoDB**

   ```bash
   wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
   echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
   sudo apt update
   sudo apt install -y mongodb-org
   ```

    2. **启动 MongoDB 服务**

   ```bash
   sudo systemctl start mongod
   sudo systemctl enable mongod
   ```

    3. **配置 MongoDB 用户**

   ```bash
   mongo
   use admin
   db.createUser({
     user: "admin",
     pwd: "adminpassword",
     roles: [{ role: "userAdminAnyDatabase", db: "admin" }]
   })
   ```

4. **安装与配置 Redis** 请参考 [官方 Redis 安装指南](https://redis.io/docs/getting-started/installation/) 并按以下概览步骤操作：

    1. **安装 Redis**

   ```bash
   sudo apt update
   sudo apt install -y redis-server
   ```

    2. **配置 Redis 密码** 编辑 `/etc/redis/redis.conf` 文件：

   ```conf
   requirepass yourredispassword
   ```

5. **启动 Redis 服务**

   ```bash
   sudo systemctl restart redis.service
   sudo systemctl enable redis.service
   ```

6. **启动自服务**

   配置 `config.conf` 后启动自服务，具体配置详见[配置参数](#配置参数)。

   ```bash
   node cococat.js # 建议放在后台运行
   ```

   进入 `cococat-route` 目录，配置 `pango.conf`，然后启动服务。具体配置详见[配置参数](#配置参数)。

   ```bash
   cd cococat-route
   chmod +x cococatroute
   ./cococatroute # 建议放在后台运行
   ```

7. **确认启动成功**

   在启动日志中查找是否有 `Service connection completed!`，日志可在 `logs` 目录下查看。



### Docker 安装

**软件工具要求**

- Docker 版本 20.10 或更高版本
- Docker Compose 版本 1.28 或更高版本

#### Docker 环境准备

1. **安装 Docker**
   请参考 [官方 Docker 文档](https://docs.docker.com/engine/install/ubuntu/) 进行 Docker 安装。

2. **安装 Docker Compose**
   请参考 [官方 Docker Compose 文档](https://docs.docker.com/compose/install/) 进行 Docker Compose 安装。

#### 使用 Docker Compose 安装

1. **创建docker-compose.yml文件**

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
         - ./data/cococatroute/userkey.json:/app/cococat_route/userkey.json  #userkey.json需要在宿主机上提前创建好空文件
         - ./data/cococatroute/data:/app/cococat_route/data
         - ./data/cococatroute/pangomail:/app/cococat_route/pangomail
   ```

2. **配置文件**

   主要配置`config.json`、`pango.conf`，具体配置详见[配置参数](#配置参数)。

3. **启动服务**

   ```bash
   docker-compose up -d
   ```

   **停止服务**

   ```bash
   docker-compose down
   ```

4. **确认启动成功**

    - 使用 `docker-compose ps` 确认服务状态：

      ```bash
      docker-compose ps
      ```

    - 在启动日志中查找是否有 `Service connection completed!`，日志可在 `logs` 目录下查看。



## 配置参数

CocoCat 自服务主要由 `cococat-web` 和 `cococat-route` 组成。

### cococat-web

#### config.json

该配置文件包含以下参数：

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

##### 参数说明

+ **`AppTitle`**：名称，默认为 `CocoCat-Web`。
+ **`mongo`**：
    - **`host`**：MongoDB 的主机地址，默认为 `127.0.0.1`。
    - **`port`**：MongoDB 的端口号，默认为 `27017`。
    - **`username`**：MongoDB 用户名。
    - **`password`**：MongoDB 密码。
    - **`database`**：MongoDB 验证数据库，默认为 `admin`。
    - **`uri`**：MongoDB 连接 URI，若设置，则优先使用。
    - **`dbName`**：MongoDB 数据库名称，默认为 `cococatdb`。
+ **`redis`**：
    - **`host`**：Redis 的主机地址，默认为 `127.0.0.1`。
    - **`port`**：Redis 的端口号，默认为 `6379`。
    - **`db`**：Redis 的数据库索引，默认为 `0`。
    - **`password`**：Redis 密码，默认为空。
+ **`bodyParser`**：
    - **`urlencoded.limit`**：URL 编码请求体的最大大小限制，默认为 `1mb`。
    - **`json.limit`**：JSON 请求体的最大大小限制，默认为 `1mb`。
+ **`port`**：`cococat-web` 监听的端口号，默认为 `4567`。
+ **`remoteService`**：应用后端服务地址，格式为 `http://{IP}:{port}`，默认为 `http://127.0.0.1:8080`。
+ **`queue`**：队列组件类型，默认为 `redis`。

**注意**：确保 MongoDB 和 Redis 的连接信息正确无误，并为所有必需参数提供适当的值，以确保 `cococat-web` 服务正常运行。



### cococat-route

#### `pango.conf`

该配置文件包含以下参数：

```c
[global]
localsrvport=4568
t2trepeater=14ACRC3zwcqcsytNYhc6xT4hUtzhbGAVcK
nodserverurl=http://127.0.0.1:4567
defaultpeers=ODAuMjUxLjIxNS4yMjg7MTA0LjEyOC45MC4yNzsxMDQuMjI0LjE3Ni4xNTE7ODAuMjUxLjIxNi4zMzs2NS40OS4yMDMuNTY7NjUuNDkuMjA2Ljk0OzY1LjQ5LjIxOC4xMDg=
```

##### 参数说明

+ **`localsrvport`**：`cococat-route` 服务的端口号，默认为 `4568`。
+ **`t2trepeater`**：中继服务的 BTC 地址。
+ **`nodserverurl`**：`cococat-web` 服务的 URL，默认为 `http://127.0.0.1:4567`。
+ **`defaultpeers`**：默认节点的列表。



## CocoApp 自服务配置

根据 `CocoApp` 开发指南，首先确保开发环境已经准备就绪。

### 获取自服务的 Service Key

要对自服务进行集成，需要获取一个唯一的 `service-key`。这可以通过调用 `getServiceKey` 接口实现。使用以下命令从本地服务器获取 `service-key`：

```bash
curl http://127.0.0.1:4567/getServiceKey
```

#### 成功响应示例：

```json
{
    "code": 200,
    "data": "eyJhdXRoc2lnbiI6Ik1FVUNJUURhOFlFZytNUU5ONXZpNEhHZDlRMDk4dVdlZzBZcDA0bGdoK2FSMlRSUWJ3SWdRM3RqQ1ZGMi9UQm9jRURGL0NrTGF5bjFqc2UrS1JHTjRUSy9RWnhHZmdFeCIsImhvc3QiOiIxNEFDUkMzendjcWNzeXROWWhjNnhUNGhVdHpoYkdBVmNLIiwibWFqb3JrZXkiOiJBeG9NamR3b0FadGRRVlRNNzNIc0ZkOVB1S005OEEyRDQ1bE43MlhST3lnUyIsIm1pbm9rS2V5IjoiQXRKM0Zxd1pkK3duTXhDRVd1VXc1Ymd4a1JycEZvdjdCbE1PaFhiVFYrVjIiLCJwcmlrZXkiOiJJQmFLTDZsY2RwWitUYSt5VjBITG1pN0l6ZVpQTkxlcDZnWldXaGt6QkFVPSJ9"
}
```

### 使用 Service Key

成功获取 `service-key` 后，将其用于 `CocoApp` 中注册自服务功能的 `registerService` 方法中。这是将自服务与 `CocoApp` 集成的关键步骤。

### 开发自服务功能

获取 `service-key` 并完成注册后，您可以开始实现自服务的通信功能，使得自服务能够与其他服务进行数据交互和消息传输。

确保按照开发文档正确配置并调用相关 API，以保证服务的稳定运行和良好的用户体验。
