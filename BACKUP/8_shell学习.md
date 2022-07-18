# [shell学习](https://github.com/zhizunbao84/gitblog/issues/8)


## 重定向提示权限不够
```
@Ubuntu:/home$ sudo cat test.txt > test2.txt
bash: test2.txt Permission denied
```
解决办法：`sudo sh -c "sudo cat test.txt > test2.txt"`

## 在脚本中免密码使用sudo
- 使用管道，`echo "password"|sudo -S command`
- 使用环境变量。
1. 创建一个密码文件，如_PWD_TEMP_，写入内容：
```
 #! /bin/bash
echo  yourpassword
```
2.在脚本中执行sudo 命令之前引入环境变量SUDO_ASKPASS，`export SUDO_ASKPASS=./_PWD_TEMP_`。
3、执行命令`sudo -A  command`。