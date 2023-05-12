# Kuboard 安装笔记

## 购买并登录云服务器

* [腾讯云服务器](https://cloud.tencent.com/product/cvm)

## 安装k3s及Kuboard v3

```sh
# 安装k3s
curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | INSTALL_K3S_MIRROR=cn sh -

# 安装Kuboard v3
kubectl apply -f https://addons.kuboard.cn/kuboard/kuboard-v3.yaml

kubectl get node

kubectl get pod -A

# 查看安装状态
kubectl get pods -n kuboard -o wide
```

## 访问Kuboard

```sh
http://云服务器ip:30080

账号：admin
密码：Kuboard123
```

## 创建集群步骤

```sh
1、选择admin并查看概要
2、选择default
3、常用操作=>创建工作负载
4、基本信息:部署=>展示层=>工作负载名称
   容器信息：添加工作容器=>名称=>容器镜像（始终拉取新镜像）
5、服务/应用路由：端口80:80、选择Clientip
6、保存，等待创建
7、创建成功后，添加路由
   ingressclass选择默认、添加路由规则、路径映射（前缀匹配/）
```

## 访问创建的域名
```sh
修改host配置文件（IP地址  域名）
路径: C:\Windows\System32\drivers\host
```
