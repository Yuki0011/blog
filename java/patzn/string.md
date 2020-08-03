**String带小数参数转换为Long型,直接转会报错**

```
String a = "81185.6";
Long b = Long.parseLong(a);
log(b);
java.lang.NumberFormatException: For input string: "81185.6"
at java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
at java.lang.Long.parseLong(Long.java:589)
at java.lang.Long.parseLong(Long.java:631)
at  com.iss.oa.service.ExpenseReimService.realCost(ExpenseReimService.java:1056)

解决方法：
String a = “100.01”;
long b = new Double(Double.parseDouble(a)).longValue();
long c = Double.valueOf(a).longValue();
```


**区别**

```
Double.parseDouble() 返回 double 类型
Double.valueOf() 返回 Double 类型
```

**精确到毫秒的字符串**

```
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");
String time = sdf.format(new Date());  

```

**集合，字符串互相转换**

```
String s = StringUtils.join(longs.stream().map(Object::toString).toArray(String[]::new), ",")

List<Long> listIds = Arrays.asList(ids.split(",")).stream().map(s -> Long.parseLong(s.trim())).collect(Collectors.toList());

```