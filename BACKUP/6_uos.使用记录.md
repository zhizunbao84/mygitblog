# [uos 使用记录](https://github.com/zhizunbao84/mygitblog/issues/6)

使用的是uos 20 专业版（1030）

## 定时提醒休息
### notify-send
notify-send 是 Linux 下发送通知到桌面的命令行程序。Ubuntu16.04 会在桌面右上角出现一个通知提醒框。

基本用法：

- 发送一条通知内容

notify-send "这是通知的内容"

- 发送一条有标题的通知
notify-send "标题" "这是通知的内容"

- 发送一条有图片的通知
notify-send -i /home/tip.png "标题" "这是通知的内容"

### crontab
crontab 是 Linux 下的定时任务，服务端一般用于定时重启某些服务、定时清理日志信息等。

执行命令`sudo crontab -l`查看定时任务列表，如果有的话，将会列出来

执行命令`sudo crontab -e`进入编辑 crontab 的模式下。

语法是这样的：

minute hour day month week command

minute 表示 分钟，0到59
hour 表示小时，0到23
day 表示日期，1到31
month 表示月份，1到12
week 表示星期，0到7，这里的0或7代表星期日
command 表示要执行的命令，命令行命令，或者外部脚本文件

例子：

1. 每分钟都执行一次 command

    `* * * * * command`

2.每小时的 2 和 3 分钟时执行一次 command
   `2,3 * * * * command`

3.10 点到 18 点之间每个小时的 2 和 3 分钟都执行一次 command
     `2,3 10-18 * * * command`

4.晚上 11 点到早上 7 点之间，每隔 2 小时重启smb
    `* 23-7/2 * * * /etc/init.d/smb restart`

```
# 定时锁屏
30 9-18 * * * export DISPLAY=:0.0; XDG_RUNTIME_DIR=/run/user/$(id -u) notify-send -i ~/ti_xing.webp "休息一下" "该休息休息眼睛了！！"
31 9-18 * * * export DISPLAY=:0.0; XDG_RUNTIME_DIR=/run/user/$(id -u) deepin-screensaver -s
# 定时switchhosts
* 9-18/2 * * * echo "111111" | sed -S -i "/# GitHub520 Host Start/Q" /etc/hosts && echo "111111" | sudo -S sh -c 'echo "111111" | sudo -S curl https://raw.hellogithub.com/hosts >> /etc/hosts'
```

## 修复中文输入法
突然某一天中文输入法无法启动了，在终端输入fcitx提示：
```
(WARN-7441 addon.c:298) 禁用插件 fcitx-xkbdbus，依赖 fcitx-xkb,fcitx-dbus 无法满足。
user@user-PC:~/Desktop$ (ERROR-7441 ime.c:303) 输入法: 打开/usr/lib/mips64el-linux-gnuabi64/fcitx/fcitx-chineseime.so 失败 /lib/mips64el-linux-gnuabi64/libcurl.so.4: symbol GMTLSv1_1_client_method version OPENSSL_1_1_0 not defined in file libssl.so.1.1 with link time reference

```
看错误提示可知，是 libssl.so.1.1 这个文件出问题了，这个文件是opensll软件的
可以`sudo apt install openssl --reinstall`重新安装一下，问题解决。

---

## 任务栏卡死
使用中经常会出现，突然一下任务栏就没反应了，点击没反应，甚至时间显示不走了。
可以`ctrl+alt+del`，然后选择“系统监视器”，相当于任务管理器，在“所有进程”中选择“dde-dock”，强制结束进程。

## 常见命令
1.dde-dock（提供任务栏功能）
2.dde-launcher（提供应用启动器功能）
3.dde-control-center（提供桌面设置功能）
4.dde-desktop（提供桌面壁纸及桌面目录管理功能）
5.dde-lock（提供用户锁屏功能）
6.dde-shutdown（用户电源和会话操作功能）
7.dde-file-manager（提供文件管理功能）
8.dde-polkit-agent（提供用户鉴权对话框功能）
9.deepin-screensaver （屏保程序）


## python使用
### 安装Scipy
在python虚拟环境中配置安装Scipy时提示：
```
To build Scipy from sources, BLAS & LAPACK libraries need to be installed.
```
解决：
```
sudo apt install libblas-dev liblapack-dev
```
### 安装pillow
提示：
```
      The headers or library files could not be found for zlib,
      a required dependency when compiling Pillow from source.
```
在 https://github.com/python-pillow/Pillow/issues/6471  中提到,是由于setuptools版本太高的原因,降级至62.2.0解决.
在uos中提供了编译好的包，执行`sudo apt install python-pil`进行安装，会安装在`/usr/lib/python3/dist-packages`,直接拷贝到你的python虚拟环境的`lib/python3.7/site-packages`中即可,但是版本太低.