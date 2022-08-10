# [docker 使用](https://github.com/zhizunbao84/mygitblog/issues/14)

 ## 权限问题Got permission denied while trying to connect to the Docker daemon socket
安装完docker CE后在终端执行docker命令提示权限问题。
### 解决方法1
使用sudo获取管理员权限，运行docker命令

### 解决方法2
docker守护进程启动的时候，会默认赋予名字为docker的用户组读写Unix socket的权限，因此只要创建docker用户组，并将当前用户加入到docker用户组中，那么当前用户就有权限访问Unix socket了，进而也就可以执行docker相关命令。
```
sudo groupadd docker     #添加docker用户组
sudo gpasswd -a $USER docker     #将登陆用户加入到docker用户组中
newgrp docker     #更新用户组
docker ps    #测试docker命令是否可以使用sudo正常使用
```

### 解决方法3
```
sudo chmod 666 /var/run/docker.sock
```