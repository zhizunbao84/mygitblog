# [只通过whl文件安装python第三方库、包](https://github.com/zhizunbao84/mygitblog/issues/15)


通过pip安装第三方python库包的时候，有的库、包会提供编译好的二进制文件whl，可以直接使用。
但是pip安装第三方库、包的时候，处理依赖关系的时候，有的依赖包需要从源码编译，而不选择whl文件，不知道为何。

pipdeptree可以显示所有已安装的包的依赖关系，而pipgrip则可以不仅解析已安装的包的依赖关系，还可以通过whl文件解析出依赖关系。
### 提出初步解决思路
所以有以下想法：
1. 通过`pip download  --no-deps --prefer-binary -d . package`只下载whl文件
2. 通过pipgrip解析whl文件得出相应的依赖关系
3. 循环通过步骤1、2，最终下载好所有的依赖包

由于pipgrip每次都是通过网络下载包源文件后编译成wheel，再解析出依赖关系所以比较慢，所以考虑借鉴pipgrip，自己写python脚本来实现上面的步骤。

### pipgrip的源码分析
通过分析pipgrip的源码可以得出，其解析依赖关系的代码主要在pipper.py文件中。
_extract_metadata函数的作用是提取whl文件的metadata，其中就包含依赖关系；
_get_wheel_requirements函数是通过分析metadata，得出依赖关系。

### 其他参考
```
from importlib.metadata import requires
print(requires('setuptools'))
```
可以获取到setuptools的依赖关系，但是还有很多包含extra信息的包（这些包都不是必须的），且只能解析已安装包的
```
from pip._vendor import pkg_resources
_package_name = 'setuptools'
p = pkg_resources.working_set.by_key[_package_name].requires()
print(p)
```
可以获取到setuptools的依赖关系，但是也只能解析已安装包的