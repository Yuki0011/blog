linux 密令
```
uname -r 查看内核发行版本
cat /etc/os-release  查看系统版本
```

将文件从mac 拷贝到 centos上

```
scp /Users/xiao/Documents/Image/favicon.ico root@192.168.191.32:~
```

解压 tar.gz

```
tar -zxvf ×××.tar.gz
```

vim中删除多行

```
在ESC模式下，输入
:7,19d
```


安装.rpm 软件管理包

```
rpm -ivh jenkins-2.164.1-1.1.noarch.rpm
```

centos 安装jdk

```
yum list java-1.8*
yum install java-1.8.0-openjdk* -y
```
配置环境变量

```
JDK安装在/usr/lib/jvm/文件夹下的/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.141-2.6.10.1.el7_3.x86_64/lib中。

cd ..

pwd

vim /etc/profile

export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.141-2.6.10.1.el7_3.x86_64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

```

查看nginx配置文件的位置

```
locate nginx.conf
```

查看端口是否被占用

```
lsof -i:
```

查看是否启动

```

```