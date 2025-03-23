
**包装类型方法的区别**

```
Double.parseDouble() 返回 double 类型
Double.valueOf() 返回 Double 类型
```

**日期字符串精确到毫秒**

```
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");
String time = sdf.format(new Date());  

```

