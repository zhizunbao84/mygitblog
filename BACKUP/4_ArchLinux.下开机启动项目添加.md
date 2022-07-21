# [ArchLinux 下开机启动项目添加](https://github.com/zhizunbao84/mygitblog/issues/4)

现在有需求，想要开机就运行一些东西，方法有两个，一个是桌面级的启动，一个是系统级的启动。
桌面级别，就是进入桌面后，自动打开一些软件，比如 gnome，启动 gnome-tweak 工具就可以看到开机启动项目，添加进去即可。系统级别就是再没有进入桌面就运行一些东西。
### 创建 systemctl 的 service 脚本
系统级别的，我觉得最简单的方式就是创建 systemctl 的 service 脚本。这个脚本放什么位置呢？
我们运行一个命令就看到了：
```
$ systemctl enable sshd 
Created symlink /etc/systemd/system/multi-user.target.wants/sshd.service → /usr/lib/systemd/system/sshd.service.
```
我们开起来 sshd 服务，显示出来 service 的位置，我们模仿这个做一个开机启动出来。
还记得很早的 linux 中有一个 rc.local 么？有什么需要开机启动的脚本直接丢进去就行了。
升级到了systemd 之后，这个玩意就消失了，我们尝试恢复他。
先建立一个 rc-local.service，
`sudo vim /usr/lib/systemd/system/rc-local.service`
然后，我们模仿其他的 service ，来写一下：
```
[Unit]
Description="/etc/rc.local Compatibility" 
#Wants=sshdgenkeys.service
#After=sshdgenkeys.service
#After=network.target

[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
StandardInput=tty
RemainAfterExit=yes
SysVStartPriority=99

[Install]
WantedBy=multi-user.target
```
然后，我们创建 /etc/rc.local 文件：
```
sudo touch /etc/rc.local
sudo chmod a+x /etc/rc.local
```
然后，添加一些我们希望的开机运行脚本,`sudo vim /etc/rc.local`，写入内容：
```
#!/bin/sh
# /etc/rc.local
if test -d /etc/rc.local.d; then
    for rcscript in /etc/rc.local.d/*.sh; do
        test -r "${rcscript}" && sh ${rcscript}
    done
    unset rcscript
fi
```
最后：`sudo systemctl enable rc-local.service`。

把sh脚本放在/etc/rc.local.d/文件夹中就行，或者直接写在/etc/rc-local里也起作用。

### 将脚本放在.bashrc中
1. 执行`cp /etc/bash.bashrc ~/.bashrc`。
2. 将所需要执行的脚本放在~/.bashrc中。系统启动后会进入bash终端，则会执行相应的脚本。