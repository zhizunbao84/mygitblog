# [docker 安装archlinux](https://github.com/zhizunbao84/mygitblog/issues/3)

docker toolbox下pull archlinux:base-devel 后启动时需要添加-- privileged参数，否则容器内使用pacman会提示权限问题。
docker desktop下则无所谓。

## 基本配置
`pacman -Syy`    将本地数据包与远程数据包同步，否则执行`pacman -S pack_name` 会报数据包找不到的错误(warning: database file for ‘core‘ does not exist ...)
### 添加国内源
在/etc/pacman.d/mirrorlist的头部添加
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.163.com/archlinux/$repo/os/$arch
执行`pacman -Syy`更新软件包缓存。
### 配置开发环境
出现`/usr/lib/libc.so.6: version 'GLIBC_2.34' not found (required by cmake)`  错误，执行`pacman -S glibc lib32-glibc`安装两个包。
出现`gdb: error while loading shared libraries: libicudata.so.71: cannot open shared object file: No such file or directory`错误，执行`pacman -S icu`。
出现`gdb: /usr/lib/libstdc++.so.6: version ‘GLIBCXX_3.4.30' not found (required by gdb)`错误，`pacman -S gcc`升级一下gcc即可。
### 容器内使用systemctl命令
启动容器后如果使用systemctl命令报错：
```
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```
解决办法：
运行容器时添加`--privileged=true`，修改/bin/bash为/sbin/init

### 容器启动时执行脚本
第一种方法：
1. `cp /etc/bash.bashrc /root/.bashrc`
2. 将所需要执行的脚本放在.bashrc中，比如dropbear
3. 在docker run或者docker exec时加上/bin/bash

第二种方法：
1. 执行以下操作：
```
touch /root/mystart.sh
chmod a+x /root/mystart.sh
vi /root/mystart.sh
#!/bin/sh

#添加需要执行的shell脚本

/bin/bash
```
执行脚本时docker run -it …… /bin/bash /root/mystart.sh，docker exec同理。这样就可以执行mystart.sh脚本，并进入容器中，并打开一个终端
最后一行的`/bin/bash`很关键，如果不加，docker run时执行完脚本就退出了，就无法进入容器内终端了。
加了后，docker run时执行完脚本后就又会打开容器内一个终端而不会退出容器。

第三种方法：
1. 执行以下操作：
```
touch /root/mystart.sh
chmod a+x /root/mystart.sh
vi /root/mystart.sh
#!/bin/sh

#添加需要执行的shell脚本
```
2. docker run -itd ……启动容器
3. `docker exec -it container /bin/bash /root/mystart.sh`。此时会执行mystart.sh脚本但是不会进入容器内终端。
4. `docker exec -it container /bin/bash`。进入容器内终端。

5. 或者docker run -it …… /bin/bash -c "/root/mystart.sh && /bin/bash"，-c后面可以多个脚本或命令，用双引号括起。

第五种方法：
用dockfile创建镜像的时候添加并运行脚本，如ENTRYPOINT、CMD等。