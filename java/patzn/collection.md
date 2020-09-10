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

**2个集合的操作**

```
        // 交集
        List<String> intersection = list1.stream().filter(item -> list2.contains(item)).collect(toList());
        System.out.println("---交集 intersection---");
        intersection.parallelStream().forEach(System.out :: println);
 
        // 差集 (list1 - list2)
        List<String> reduce1 = list1.stream().filter(item -> !list2.contains(item)).collect(toList());
        System.out.println("---差集 reduce1 (list1 - list2)---");
        reduce1.parallelStream().forEach(System.out :: println);
 
        // 差集 (list2 - list1)
        List<String> reduce2 = list2.stream().filter(item -> !list1.contains(item)).collect(toList());
        System.out.println("---差集 reduce2 (list2 - list1)---");
        reduce2.parallelStream().forEach(System.out :: println);
 
        // 并集
        List<String> listAll = list1.parallelStream().collect(toList());
        List<String> listAll2 = list2.parallelStream().collect(toList());
        listAll.addAll(listAll2);
        System.out.println("---并集 listAll---");
        listAll.parallelStream().forEachOrdered(System.out :: println);
 
        // 去重并集
        List<String> listAllDistinct = listAll.stream().distinct().collect(toList());
        System.out.println("---得到去重并集 listAllDistinct---");
        listAllDistinct.parallelStream().forEachOrdered(System.out :: println);
 
        System.out.println("---原来的List1---");
        list1.parallelStream().forEachOrdered(System.out :: println);
        System.out.println("---原来的List2---");
        list2.parallelStream().forEachOrdered(System.out :: println);

作者：lizhiyong
链接：https://juejin.im/post/6844903833726894093
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```