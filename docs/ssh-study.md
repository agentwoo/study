# vscode配置远程ssh免密登录


## 相关资源

* [VSCode配置远程ssh免密登录-知乎](https://zhuanlan.zhihu.com/p/597650361)
* [VScode实现SSH免密登录-csdn](https://blog.csdn.net/qq_41636999/article/details/130067726)

## 配置步骤

* vscode安装remote-ssh插件并连接远程主机

* 配置远程文件信息

```sh
1、复制本地id_rsa.pub内容
   目录：C:\Users\Administrator\.ssh

   如果本地没有id_rsa.pub文件，则执行以下命令，默认一直回车
   ssh-keygen

2、连接远程主机创建.ssh目录
   mkdir .ssh

3、在.ssh目录下创建authorized_keys文件
   cd .ssh
   touch authorized_keys
   
4、将id_rsa.pub内容粘贴到authorized_keys中

5、执行以下命令
   sudo chmod 600 authorized_keys
   service sshd restart

6、关闭重新打开vscode远程连接
```



