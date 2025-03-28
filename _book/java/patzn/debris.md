
```java
获取resource下的文件
InputStream inputStream = getClass().getClassLoader().getResourceAsStream("templates/2019register.docx”);
```
服务注册不上排查
http://localhost:8002/actuator/health
检查服务健康状态
![avatar](/Users/xutao/guangyan/picture/9007578A-BABE-4E67-8C74-213D0B36083E.png)
将处于down状态的服务的健康检查关闭掉，如redis不健康
management:
  health:
    redis:
      enabled: false
      
```
.replaceAll("<[.[^<]]*>", "")    去掉富文本的P标签
```



时间操作
```
推迟一周示例： 
Calendar curr = Calendar.getInstance(); //获取当前时间
curr.set(Calendar.DAY_OF_MONTH,curr.get(Calendar.DAY_OF_MONTH)+7); //给当前时间增加一周
Date date=curr.getTime();//获取新的时间
推迟一个月示例： 
Calendar curr = Calendar.getInstance(); 
curr.set(Calendar.MONTH,curr.get(Calendar.MONTH)+1); //增加一月
Date date=curr.getTime();
推迟一年示例： 
Calendar curr = Calendar.getInstance(); 
curr.set(Calendar.YEAR,curr.get(Calendar.YEAR)+1); //增加一年
Date date=curr.getTime();
```