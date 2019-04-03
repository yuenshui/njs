# 下载安装
作为linux模块安装  
从源码编译  
## 作为linux模块安装 
对于Linux，可以使用njs模块包：

- nginx-module-njs — `njs`动态模块  
- nginx-module-njs-dbg — `nginx-module-njs`的调试包 

## 从源码编译  
下面命令从包含`njs`源码的[代码库](http://hg.nginx.org/njs)克隆一份代码:(需要[Mercurial](https://www.mercurial-scm.org/)客户端）：
```bash
hg clone http://hg.nginx.org/njs
```
然后应使用`--add-module`配置参数编译模块:
```bash
./configure --add-module=path-to-njs/nginx
```
模块也可以构建为动态：
```bash
./configure --add-dynamic-module=path-to-njs/nginx
```
只是编译`njs`命令行程序，从`njs`源码根目录运行./configure和make njs命令完成编译。运行命令行程序`./build/njs`