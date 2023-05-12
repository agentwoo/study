# docker 学习笔记

## 准备工作

* 安装 vmware
* 下载 CentOS-7 镜像: [CentOS-7-x86_64-Minimal-2009.iso](https://mirrors.aliyun.com/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-2009.iso?spm=a2c6h.25603864.0.0.74092d1cKCOuxM)
* 安装 CentOS-7 虚拟机
    * 网络连接: NAT 模式
    * 登录密码: root / 123456

```sh
# 查看虚拟机 ip 地址
ip addr
#  ens33: inet 192.168.184.131/24
```

## docker 安装

```sh
# 安装 docker 并启动服务
yum install -y docker
systemctl enable docker
systemctl start docker

# 如果太慢的话可以改用网易镜像源
# echo '{ "registry-mirrors": ["http://hub-mirror.c.163.com"] }' > /etc/docker/daemon.json
# 清空镜像源
# echo '{}' > /etc/docker/daemon.json
# 修改后重启服务
# systemctl daemon-reload
# systemctl restart docker

# 查看信息
docker info
```

## 通过 docker 安装 openresty

```sh
# 查找并拉取 openresty 镜像
docker search openresty
docker pull openresty/openresty:latest
docker images  # 查看镜像列表

# 删除容器
# docker rm -f myweb

# 创建 openresty 容器并运行
docker run --name=myweb -d -p 80:80 openresty/openresty

# 访问网站首页
curl http://127.0.0.1           # 只能在虚拟机内访问
curl http://192.168.184.131     # 可在宿主机访问

docker ps                       # 查看运行中的容器列表
docker ps -a                    # 查看全部容器列表
docker inspect  myweb           # 查看该容器的详细信息
docker logs     myweb           # 查看日志输出
docker logs -f  myweb           # 跟踪日志输出
docker start    myweb           # 启动
docker restart  myweb           # 重启
docker pause    myweb           # 暂停
docker unpause  myweb           # 恢复
docker stop     myweb           # 优雅退出
docker kill     myweb           # 强制退出
docker rm       myweb           # 删除已停止的容器
docker rm -f    myweb           # 强制删除

# 进入容器 bash
docker exec -it myweb /bin/bash
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

## 通过 docker 安装 phpmyadmin

```sh
# 删除容器
# docker rm -f myadmin

# 创建 phpmyadmin 容器并运行
docker run --name myadmin -d -e PMA_ARBITRARY=1 -p 8080:80 phpmyadmin

# 每 1 秒钟刷新容器列表
watch -n 1 'docker ps -a'

docker logs myadmin # 查看日志
docker exec -it myadmin /bin/bash

# 访问网站首页
curl http://127.0.0.1:8080          # 只能在虚拟机内访问
curl http://192.168.184.131:8080    # 可在宿主机访问

# 服务器: 172.17.0.4:3306 或 192.168.184.131:3306
# 账  号: root
# 密  码: 123456
```

## IP地址及防火墙设置

```sh
ip addr  # 查看虚拟机 ip 地址

docker network ls                   # 查看网络列表
docker network inspect bridge       # 查看全部网络信息

# 查看全部容器的 ip 地址
docker inspect -f='{{.Name}} {{.NetworkSettings.IPAddress}} {{.HostConfig.PortBindings}}' $(docker ps -aq)

# 查看 mysql 容器 ip
docker inspect mysql  --format '{{.NetworkSettings.IPAddress}}'
docker exec mysql cat /etc/hosts

# mysql 3306 端口防火墙默认不开放

firewall-cmd --zone=public --add-port=3306/tcp --permanent      # 开放 3306 端口
firewall-cmd --zone=public --remove-port=3306/tcp --permanent   # 关闭 3306 端口
firewall-cmd --reload   # 配置立即生效

# 查看防火墙所有开放的端口
firewall-cmd --zone=public --list-ports

# 关闭防火墙
systemctl stop firewalld.service
```

## Docker Hub 镜像服务器

* 注册 docker.io 账号: https://hub.docker.com/
* 生成 Access Token: dckr_pat_lg6pXL0c4al-sor2k3L3id-0bss

```sh
# 登录 docker.io
docker login -u killsen -p dckr_pat_lg6pXL0c4al-sor2k3L3id-0bss

# 注销
docker logout
```
