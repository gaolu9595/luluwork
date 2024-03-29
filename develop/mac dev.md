# Mac Environment
### 修改环境变量：
vim ～/.bash_profile
	【The use of Vim Editor】
	 Normal mode：将按键解释成命令，按I进入Editor mode
	 Editor mode: 编辑文件内容，按esc进入Normal mode
	  [https://blog.csdn.net/qq_43432935/article/details/92013718](https://blog.csdn.net/qq_43432935/article/details/92013718)  
open -e .bash_profile，在编辑器中打开bash_profile文件
<img src="/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.n9MHCt/Pasted Graphic.png" alt="Pasted Graphic" style="zoom:33%;" />

---
### Home-brew
Homebrew是一款Mac OS平台下的软件包管理工具，拥有安装、卸载、更新、查看、搜索等很多实用的功能。简单的一条指令，就可以实现包管理，而不用你关心各种依赖和文件路径的情况，十分方便快捷。
 [https://brew.sh/index_zh-cn.html](https://brew.sh/index_zh-cn.html)  
 [https://www.zhihu.com/question/27617980](https://www.zhihu.com/question/27617980)  

---

### Home-brew安装mongodb
 [https://www.jianshu.com/p/abccdafb876d](https://www.jianshu.com/p/abccdafb876d)  

<img src="/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.o8JDur/Pasted Graphic.png" alt="Pasted Graphic" style="zoom:33%;" />

### Home-brew安装python2

 [https://www.jianshu.com/p/94ec0e15355c](https://www.jianshu.com/p/94ec0e15355c)  Install以后要配置环境变量
### ant和ivy
 [https://blog.csdn.net/wangxilong1991/article/details/70225053](https://blog.csdn.net/wangxilong1991/article/details/70225053)  
ant是Apache下的一个将软件编译、测试、部署等步骤联系在一起加以自动化的工具，ant运行时需要一个build.xml文件（构建文件）
ivy是一个跟踪管理项目依赖的工具，类似于maven，做jar包管理
咱们项目代码中的协作方式可以理解为：ant+ivy可以实现maven的功能，ivy.xml进行依赖管理，build.xml进行ant构建打包，最终得到一个jar包+包含jar包和依赖包的总压缩包
 [https://blog.csdn.net/gaodebao1/article/details/56830854?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.nonecase](https://blog.csdn.net/gaodebao1/article/details/56830854?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.nonecase) 

---

### 官网安装docker
1. Docker使用教程 [https://www.runoob.com/docker/docker-container-usage.html](https://www.runoob.com/docker/docker-container-usage.html)  
2. lite-stats工程下的docker容器使用方法：
	1. 方法1（例如：mysql-container的启动）
		1. 进入docker/目录，找到所需image的子目录，在该子目录下执行build.sh文件来build a image
		2. 继续执行该子目录中的run.sh文件来up a container
		3. 启动该container完成
	2. 方法2（例如：mongo-container的启动）
		1. 进入docker/non-reg/目录，执行docker-compose up -d 「service名」来build a image并up a container
		2. 启动该container完成
	3. 方法3（例如自行实验启动的amazing_keller container）：只需要run一下就可以同时执行好build和up两步操作，如下图

<img src="/var/folders/qr/zhjlrk5j1cg4d4qz5s7dkk9rz3y26g/T/net.shinyfrog.bear/BearTemp.M6EBd0/Pasted Graphic.png" alt="Pasted Graphic" style="zoom:33%;" />

## MacOs 升级到Big Sur

### Flink运行时找不到/Library/Internet Plugin的问题

手动配置JAVA_HOME也解决不了这个问题，只能暴力删除掉MacOS自带的Java JDK，只留下我们需要用的那一个

```shell
lugao@lugao-mac ~ /usr/libexec/java_home -V
Matching Java Virtual Machines (2):
    1.8.251.08 (x86_64) "Oracle Corporation" - "Java" /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
    1.8.0_251 (x86_64) "Oracle Corporation" - "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_251.jdk/Contents/Home
/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
lugao@lugao-mac ~
lugao@lugao-mac ~
lugao@lugao-mac ~ which java
/Library/Java/JavaVirtualMachines/jdk1.8.0_251.jdk/Contents/Home/bin/java
lugao@lugao-mac ~ cd /Library/Internet Plug-Ins/JavaAppletPlugin.plugin
cd: string not in pwd: /Library/Internet
lugao@lugao-mac ~ cd /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
lugao@lugao-mac /Library/Internet Plug-Ins/JavaAppletPlugin.plugin ls
Contents
lugao@lugao-mac /Library/Internet Plug-Ins/JavaAppletPlugin.plugin cd ..
lugao@lugao-mac /Library/Internet Plug-Ins ls
JavaAppletPlugin.plugin
lugao@lugao-mac /Library/Internet Plug-Ins cd JavaAppletPlugin.plugin/Contents
lugao@lugao-mac /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents ls
Disabled.plist Enabled.plist  Frameworks     Home           Info.plist     MacOS          Plugins        Resources
lugao@lugao-mac /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents cd Home
lugao@lugao-mac /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home ls
COPYRIGHT                          README                             THIRDPARTYLICENSEREADME.txt        bin                                man
LICENSE                            THIRDPARTYLICENSEREADME-JAVAFX.txt Welcome.html                       lib                                release
lugao@lugao-mac /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home cd /Library/Java/JavaVirtualMachines
lugao@lugao-mac /Library/Java/JavaVirtualMachines ls
jdk1.8.0_251.jdk
lugao@lugao-mac /Library/Java/JavaVirtualMachines sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
Password:
lugao@lugao-mac /Library/Java/JavaVirtualMachines ls
jdk1.8.0_251.jdk
lugao@lugao-mac /Library/Java/JavaVirtualMachines /usr/libexec/java_home -V
Matching Java Virtual Machines (1):
    1.8.0_251 (x86_64) "Oracle Corporation" - "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_251.jdk/Contents/Home
/Library/Java/JavaVirtualMachines/jdk1.8.0_251.jdk/Contents/Home
lugao@lugao-mac /Library/Java/JavaVirtualMachines flink --version
Version: 1.11.2, Commit ID: fe36135
```



## application pathes in my MacOS

```shell
lugao@lugao-mac /usr/local/Cellar ls
apache-flink      go                ipython           maven             mysql             openssl@1.1       python@3.8        sqlite            zeromq
elasticsearch     go@1.11           jupyterlab        mercurial         node              pandoc            python@3.9        telnet
gdbm              icu4c             lrzsz             mongodb-community openjdk           protobuf          readline          xz
```

