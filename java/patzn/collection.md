**集合转数组**

```
String[] ss = listStrings.stream().toArray(String[]::new);
String[] sss = listStrings.toArray(new String[listStrings.size()]);
```

**为什么指定hashmap初始大小**

```
初始化hashmap的时候，指定其默认大小，避免扩容
hashmap源码中，我们知道hashmap不指定大小的时候，初始容量 capacity值为16。负载因子为0.75，当我们put操作的时候，会先看下容量是否达到capacity*3/4的扩容条件。而每次扩容都会进行rehash。每次的 rehash都是需要重建哈希表，很费时间和性能。当我们在已知集合大小的情况下，初始化容量大小那就不会进行rehash，性能提升。 反之就容量就默认就好了
当我们初始化定义容量大小的时候，大小定义多少合适了呢？
就定义为集合大小吗？
当然不行，如果我们定义为集合大小，还是可能出现扩容，rehash。
所以我们需要计算一下，大小多少合适，合理，减少扩容，避免rehash。
initCapacity=已知需要存储元素大小/负载因子+1。

```
**区别**

```
Lists.newArrayList() 和 new ArrayList<>() 的区别
前者可以自动推导集合类型
```