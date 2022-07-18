# [manjaro i3使用记录](https://github.com/zhizunbao84/gitblog/issues/5)

manjaro基于archlinux，但更于易用。
i3是一个窗口管理器，不是桌面程序，所以毕竟轻量。
manjaro的i3版本可以在官网找到，一直往下。

## 更换Linux国内源
```
sudo pacman-mirrors -i -c China -m rank //更新镜像排名
// 之后自己选择几个镜像，推荐ustc和tuna的镜像源
sudo vim /etc/pacman.d/mirrorlist //查看选择的源
sudo pacman -Syy  //更新数据源

// 设置Archlinuxcn源
sudo vim /etc/pacman.conf

//在文件末尾添加：
[archlinuxcn]
SigLevel = Optional TrustedOnly
#中科大源
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
#清华源
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch

[antergos]
SigLevel = TrustAll
Server = https://mirrors.ustc.edu.cn/antergos/$repo/$arch
Server = http://mirrors.tuna.tsinghua.edu.cn/antergos/$repo/$arch

[arch4edu]
SigLevel = TrustAll
Server = http://mirrors.tuna.tsinghua.edu.cn/arch4edu/$arch

//导入GPG key
sudo pacman -Syy //更新数据源
sudo pacman -S archlinuxcn-keyring //安装导入GPG key
sudo pacman -S antergos-keyrin

// 更新系统(需要下载更新很多软件，会比较慢)
sudo pacman -Syu
```
我自己测试的时候antergos、arch4edu会有错误，后来干脆删除了。

## 解决桌面中文显示方块问题
```
sudo vim /usr/share/conky/conky_maia
//修改Bitstream Vera字体为anti或者文泉驿
:%s/Bitstream Vera/anti
```
重启生效。

## 打开谷歌浏览器的时候没有选项那个background
~/.config/dmenu-recent/
在该文件夹中有terminal和background，保存了对应模式下打开的软件的名字，进去删掉就可以重新选择了

## 输入法安装
