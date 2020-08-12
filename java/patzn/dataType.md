**Bigdecimal**

```
 BigDecimal z1 = new BigDecimal("0");
 BigDecimal z2 = new BigDecimal("0.0");
 System.out.println(z1.equals(z2));  // false 比较精度 
 System.out.println(z1.compareTo(z2)); // 0 不比较精度

```

**浮点数之间的等值判断，基本数据类型不能用==来比较，包装数据类型不能用 equals
来判断**

```
使用 BigDecimal 来定义值，再进行浮点数的运算操作。
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
BigDecimal c = new BigDecimal("0.8");
BigDecimal x = a.subtract(b);
BigDecimal y = b.subtract(c);
if (x.equals(y)) {
 System.out.println("true");
}
```

**【强制】禁止使用构造方法 BigDecimal(double)的方式把 double 值转化为 BigDecimal 对象。<br>
说明：BigDecimal(double)存在精度损失风险，在精确计算或值比较的场景中可能会导致业务逻辑异常。<br>
正例：优先推荐入参为 String 的构造方法，或使用 BigDecimal 的 valueOf 方法，此方法内部其实执行了
Double 的 toString，而 Double 的 toString 按 double 的实际能表达的精度对尾数进行了截断。**

```
 BigDecimal recommend1 = new BigDecimal("0.1");
 BigDecimal recommend2 = BigDecimal.valueOf(0.1);
```


lamda 计算几何某个字段的总和

```
Integer[] integerArray = {1, 3, 5, 10, 18};

List<Integer> list = new ArrayList<>(Arrays.asList(integerArray));

IntSummaryStatistics summaryStatistics = list.stream().mapToInt((s) -> s).summaryStatistics();
System.out.println("总和：" + summaryStatistics.getSum());
System.out.println("平均数：" + summaryStatistics.getAverage());
System.out.println("总个数：" + summaryStatistics.getCount());
System.out.println("最大值：" + summaryStatistics.getMax());
System.out.println("最小值：" + summaryStatistics.getMin());
```