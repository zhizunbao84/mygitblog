# [linux 命令](https://github.com/zhizunbao84/mygitblog/issues/13)

## 用户和用户组
### 新用户的添加

语法：useradd 选项 用户名
参数说明：
　　-c comment 指定一段注释性描述。
       -d 目录 指定用户主目录，如果此目录不存在，则同时使用-m选项，可以创建主目录。
       -g 用户组 指定用户所属的用户组。
       -G 用户组，用户组 指定用户所属的附加组。
       -s Shell文件 指定用户的登录Shell。
       -u 用户号 指定用户的用户号，如果同时有-o选项，则可以重复使用其他用户的标识号。

useradd -d /home/ztl -m ztl
解释：此命令创建了一个用户sam，其中-d和-m选项用来为登录名sam产生一个主目录/usr/sam（/usr为默认的用户主目录所在的父目录）。

useradd -s /bin/sh -g group -G adm,root gem
解释：此命令新建了一个用户gem，该用户的登录Shell是 /bin/sh，它属于group用户组，同时又属于adm和root用户组，其中group用户组是其主组。

这里可能新建组：#groupadd group:groupadd adm

增加用户账号就是在/etc/passwd文件中为新用户增加一条记录，同时更新其他系统文件如/etc/shadow, /etc/group等。

### 查看用户、用户组
cat /etc/passwd     #可以查看所有用户的列表
w                   #可以查看当前活跃的用户列表
cat /etc/group      #查看用户组

groups   #查看当前登录用户的组内成员
groups test 查看test用户所在的组，以及组内成员
 
whoami   #查看当前登录用户名
id               #查看当前用户所属的用户组
id test      #查看test用户所属的用户组

## 搜索文件内容
搜索、查找文件当中的内容，一般最常用的是grep命令，另外还有egrep, vi命令也能搜索文件里面内容。
搜索的内容如果包含特殊字符时，必须进行转义处理。
搜索某个文件里面是否包含字符串，使用grep "search content" filename1
    grep -rn "hello,world!" *
    * : 表示当前目录所有文件，也可以是某个文件名
    -r 是递归查找
    -n 是显示行号
    -R 查找所有文件包含子目录
    -i 忽略大小写
    -w 完全匹配整个字符，比如一般情况下"fin"会匹配"find"，加上w参数后就不会

如果你想搜索多个文件是否包含某个字符串,可以使用下面方式
grep "search content" filename1 filename2.... filenamen
grep "search content" *.sql


grep –v "被查找的字符串" 文件名
例如查找某些进程时，我们不想显示包含命令grep ora_mmon的进程，如下所示:
```
ps -ef  | grep ora_mmon  
oracle   16675 16220  0 00:09 pts/1    00:00:00 grep ora_mmon
oracle   21412     1  0 Aug22 ?        00:00:07 ora_mmon_gsp
ps -ef  | grep ora_mmon  | grep -v grep
oracle   21412     1  0 Aug22 ?        00:00:07 ora_mmon_gsp
```

如果我们只想获取那些文件包含搜索的内容，那么可以使用下命令
```
grep -H -r "v\$temp_space_header" /u01/app/oracle/product/11.1.0/dbhome_1/rdbms/admin/ | cut -d: -f1`
grep -H -r "v\$temp_space_header" /u01/app/oracle/product/11.1.0/dbhome_1/rdbms/admin/ | cut -d: -f1 | uniq
```
