
## curl提交数据
```sh
curl -X POST -H "Content-Type: application/json" -d '{"username": "test2", "password": "123456"}' http://192.168.48.129:3000/api/my/login

```

## docker运行mysql

```
1、先将.sql文件copy到docker容器里 docker ps //找到容器的短ID或者指定的name. docker inspect  -f '{{.Id}}' id or name 得到指定容器的全  ID docker cp 本地文件路径 ID全称:容器路径[docker cp mysql.sql 12345:/tmp/] 
2、将文件copy进容器之后,进入docke 容器 docker exec -it mysql bash 进入容器 mysql -u root -p 进入mysql服务
```

```sh
# 复制shop.sql
docker cp shop.sql mysql:/

# 进入容器bash
docker exec -it mysql bash 

# 进入容器 
mysql -uroot -p123456 
-Dshop< shop.sql

# 创建数据库
CREATE DATABASE shop;

# source命令调用sql文件
mysql –uroot –p123456 
source /shop.sql

```

## 创建 server 镜像并运行 server 容器

```sh

docker rm server -f
docker rmi server -f
docker build -t server .
docker run --name server -p 3000:3000 -d server
docker ps -a

curl -X POST -H "Content-Type: application/json" -d '{"username": "admin", "password": "1234567"}' http://192.168.48.129:3000/api/my/loginadmin

curl -X POST -H "Content-Type: application/json" -d '{"username": "wjj", "password": "123456"}' http://192.168.48.129:3000/api/my/register

```

## 创建 shop 镜像并运行shop容器

```sh

docker rm shop -f
docker rmi shop -f
docker build -t shop .
docker run --name shop -p 80:80 -d shop
docker ps -a

curl  http://192.168.48.129/

curl http://192.168.48.129/admin/

curl -X POST -H "Content-Type: application/json" -d '{"username": "admin", "password": "1234567"}' http://192.168.48.129/api/my/loginadmin

```

## mysql 连接错误解决方式

* 错误提示
```
Error: ER_NOT_SUPPORTED_AUTH_MODE: Client does not support
authentication protocol requested by server; consider upgrading
MySQL client
```

### 解决方法
* 进入容器

```sh

docker exec -it mysql /bin/bash
mysql -h localhost -u root -p123456

```

* 依次输入

```sql

ALTER USER 'root'@'localhost' IDENTIFIED BY '123456' PASSWORD EXPIRE NEVER;

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';

```

* 超级用户账户 ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456'; 
* 刷新权限 FLUSH PRIVILEGES; 

## 基于腾讯云 Serverless 集群部署

```sh
# 上传 shop 镜像到腾讯云
docker login --username=100031291486 -p Tx123456 ccr.ccs.tencentyun.com
docker tag shop ccr.ccs.tencentyun.com/agentwoo/shop:latest
docker push ccr.ccs.tencentyun.com/agentwoo/shop:latest

docker tag shop ccr.ccs.tencentyun.com/agentwoo/shop:v1
docker push ccr.ccs.tencentyun.com/agentwoo/shop:v1


# 上传 server 镜像到腾讯云
docker login --username=100031291486 -p Tx123456 ccr.ccs.tencentyun.com
docker tag server ccr.ccs.tencentyun.com/agentwoo/server:latest
docker push ccr.ccs.tencentyun.com/agentwoo/server:latest

# 打包并上传 mydb 镜像到腾讯云
docker build -t mydb .
docker tag mydb ccr.ccs.tencentyun.com/agentwoo/mydb:latest
docker push ccr.ccs.tencentyun.com/agentwoo/mydb:latest

```

## 通过 docker 安装 mysql

```sh
# 删除目录及容器
# rm -rf /root/mysql
# docker rm -f mysql

# 创建 mysql 容器并运行
docker run -it \
    --name mysql \
    --privileged=true \
    --restart unless-stopped \
    -p 3306:3306 \
    -m 500m \
    -v /root/mysql/data:/var/lib/mysql \
    -v /root/mysql/conf:/etc/mysql/conf.d \
    -e MYSQL_ROOT_PASSWORD=123456 \
    -e TZ=Asia/Shanghai \
    -d mysql:8.0.33 \
    --lower_case_table_names=1

docker ps -a        # 查看容器列表
docker logs mysql   # 查看日志

# 进入容器 bash
docker exec -it mysql /bin/bash
    mysql -h localhost -u root -p123456
        mysql> show databases;
        mysql> exit;
    exit
```

## 云数据库部署

```sql
-- 登录远程数据库
mysql -uroot -p123456 
source init.sql
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456'; 
FLUSH PRIVILEGES; 

```