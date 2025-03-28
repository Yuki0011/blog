rocketmq 初始化的配置占用内存空间很大，本地测试使用可以调小一些，防止（特别是自己的云服务器）内存太小，导致启动报错 
 
将 runser.sh 和 runbroker.sh 文件中的虚拟机内存设置的小一些

```
JAVA_OPT="${JAVA_OPT} -server -Xms2g -Xmx2g -Xmn1g -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
```