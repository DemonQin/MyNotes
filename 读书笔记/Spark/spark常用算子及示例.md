# spark常用算子及示例

## 触发算子

一个application应用程序中有几个Action类算子执行，就有几个job运行

### 1、count

返回数据集中的元素数。会在结果计算完成后回收到Driver端

### 2、countByKe

 作用到K,V格式的RDD上，根据Key计数相同Key的数据集元素。

### 3、countByValue

根据数据集每个元素相同的内容来计数。返回相同内容的元素对应的条数。

### 4、reduce

根据聚合逻辑聚合数据集中的每个元素。

### 5、collect

```java
/**
 * collect 
 * 将计算的结果作为集合拉回到driver端，
 * 一般在使用过滤算子或者一些能返回少量数据集的算子后，
 * 将结果回收到Driver端打印显示。
 */
数据：
hello xixi
good  job
hello sxt
haha  lala
job xixi
huhu meimei
hello xixi
good  job
hello xixi
good  job
hello xixi
good  job
   
代码：
JavaRDD resultRDD = lines.filter(x -> x.contains("job"));
List<String> collect = resultRDD.collect();
        System.out.println(collect);

结果：
[good  job, job xixi, good  job, good  job, good  job]
```

### 6、foreach

循环遍历数据集中的每个元素，运行相应的逻辑。

### 7、foreachPartition

遍历分区

### 8、take(n)

返回一个包含数据集前n个元素的集合

### 9、first

first=take(1),返回数据集中的第一个元素





## 转换算子

Transformations算子是延迟执行，也叫懒加载执行。

### 1、cogroup

```
两个RDD
当调用类型（K，V）和（K，W）的数据集时，返回一个数据集（K，（Iterable <V>，Iterable <W>））元组
// cogroup 与 join不同！
// 相当于，一个key join上所有value，都给放到一个Iterable里面去！
```

### 2、map

将一个RDD中的每个数据项，通过map中的函数映射变为一个新的元素。

特点：输入一条，输出一条数据

```
通过传入的函数处理每个元素，返回新的数据集。
特点：输入一条，输出一条。
split切割后的数据是toString没有重写的对象
```

### 3、groupByKey

```
一个RDD
当调用类型（K，V）和（K，W）的数据集时，返回一个数据集（K，（Iterable <V>，Iterable <W>））元组
```

### 4、flatMap

先map后flat。与map类似，每个输入项可以映射为0到多个输出项。

```
输入一条数据，输出0到多条数据。
```

### 5、 mapPartitionWithIndex

类似于mapPartitions,除此之外还会携带分区的索引值。

```java
 public Iterator<String> call(Integer index, Iterator<String> iter)
```

### 6、 repartition

增加或减少分区。会产生shuffle。

### 7、Coalesce

coalesce常用来减少分区，第二个参数是减少分区的过程中是否产生shuffle。

true为产生shuffle，false不产生shuffle。默认是false。

如果coalesce设置的分区数比原来的RDD的分区数还多的话，第二个参数设置为false不会起作用，如果设置成true，效果和repartition一样。即repartition(numPartitions) = coalesce(numPartitions,true)

### 8、 groupByKey

作用在K，V格式的RDD上。根据Key进行分组。作用在（K，V），返回（K，Iterable <V>）。

### 9、 zip

将两个RDD中的元素（KV格式/非KV格式）变成一个KV格式的RDD,两个RDD的个数必须相同。

```java
err:Can only zip RDDs with same number of elements in each partition
```

```
数据：
nameRDD		("zhangsan","lisi","wangwu")
scoreRDD		(100,200,300)

代码：
nameRDD.zip(scoreRDD)

结果：
(zhangsan,100)
(lisi,200)
(wangwu,300)
```



### 10、 zipWithIndex

该函数将RDD中的元素和这个元素在RDD中的索引号（从0开始）组合成（K,V）对。

```java
JavaRDD<String> nameRDD = sc.parallelize(Arrays.asList("zhangsan","lisi","wangwu"),2);

JavaPairRDD<String, Long> zipWithIndex = nameRDD.zipWithIndex();

//结果
//(zhangsan,0)
//(lisi,1)
//(wangwu,2)
```



### 11、join,leftOuterJoin,rightOuterJoin,fullOuterJoin

作用在K,V格式的RDD上。根据K进行连接，对（K,V）join(K,W)返回（K,(V,W)）

join后的分区数与父RDD分区数多的那一个相同。

```java
/**
 * nameRDD
 *Tuple2<>(0, "aa")
 * Tuple2<>(1, "a")
 * Tuple2<>(1, "a")
 * Tuple2<>(2, "b")
 * Tuple2<>(3, "c")
 */

//nameRDD.join(scoreRDD)

/**
 * scoreRDD
 *(1, 100)
 * (2, 200)
 * (3, 300)
 * (3, 300)
 * (4, 400)
 */

/**
 * resultRDD
 * (1,(a,100))
 * (1,(a,100))
 * (3,(c,300))
 * (3,(c,300))
 * (2,(b,200))
 */
```



### 12、 union

合并两个数据集。两个数据集的类型要一致。

¬ 返回新的RDD的分区数是合并RDD分区数的总和。

### 13、 intersection

取两个数据集的交集

### 14、 subtract

取两个数据集的差集

```java
//subtract取差集，两个RDD的类型要一致。
JavaRDD<String> rdd1 = sc.parallelize(Arrays.asList("a", "b", "c"));
        JavaRDD<String> rdd2 = sc.parallelize(Arrays.asList("a", "e", "f"));

        JavaRDD<String> subtract = rdd1.subtract(rdd2);
        JavaRDD<String> subtract1 = rdd2.subtract(rdd1);

        System.out.println(subtract.collect());
        System.out.println(subtract1.collect());
//结果
//[b, c]
//[e, f]
```

### 15、mapPartition

与map类似，遍历的单位是每个partition上的数据。

### 16、 distinct(map+reduceByKey+map)

### 17、 cogroup 

当调用类型（K,V）和（K，W）的数据上时，返回一个数据集（K，（Iterable<V>,Iterable<W>））

### 18、 filter



过滤符合条件的记录数，true保留，false过滤掉。

```java
return !line.contains("shsxt");
//!表示false过滤掉
```

### 19、 sample(是否放回, fraction, seed)

随机抽样算子，根据传进去的小数按比例进行又放回或者无放回的抽样。

- withReplacement：true抽取放回，false抽取不放回。
- fraction：
   1）false抽取不放回的情况下，抽取的概率（0-1）。
   0-全不抽
   1-全抽
   2）true抽取放回的情况下，抽取的次数。
- seed：随机数种子。

### 20、 sortByKey/sortBy

作用在K,V格式的RDD上，对key进行升序或者降序排序。

# 控制算子

### 概念：

控制算子有三种，cache,persist,checkpoint，以上算子都可以将RDD持久化，持久化的单位是partition。cache和persist都是懒执行的。必须有一个action类算子触发执行。checkpoint算子不仅能将RDD持久化到磁盘，还能切断RDD之间的依赖关系。

### cache

默认将RDD的数据持久化到内存中。cache是懒执行。

#### 	注意：chche () = persist()=persist(StorageLevel.Memory_Only)

	测试cache文件：
文件：见“NASA_access_log_Aug95”文件。

#### 测试代码：

 

```java
SparkConf conf = new SparkConf();
 conf.setMaster("local").setAppName("CacheTest");
 JavaSparkContext jsc = new JavaSparkContext(conf);
 JavaRDD<String> lines = jsc.textFile("./NASA_access_log_Aug95");

 lines = lines.cache();
 long startTime = System.currentTimeMillis();
 long count = lines.count();
 long endTime = System.currentTimeMillis();
 System.out.println("共"+count+ "条数据，"+"初始化时间+cache时间+计算时间="+ 
          (endTime-startTime));
		
 long countStartTime = System.currentTimeMillis();
 long countrResult = lines.count();
 long countEndTime = System.currentTimeMillis();
 System.out.println("共"+countrResult+ "条数据，"+"计算时间="+ (countEndTime-
           countStartTime));
		
 jsc.stop();
```

### persist：

可以指定持久化的级别。最常用的是MEMORY_ONLY和MEMORY_AND_DISK。”_2”表示有副本数。
持久化级别如下：

#### cache和persist的注意事项：

- cache和persist都是懒执行，必须有一个action类算子触发执行。
- cache和persist算子的返回值可以赋值给一个变量，在其他job中直接使用这个变量就是使用持久化的数据了。持久化的单位是partition。
- cache和persist算子后不能立即紧跟action算子。
  错误：rdd.cache().count() 返回的不是持久化的RDD，而是一个数值了。
  

###checkpoint
将RDD持久化到磁盘，还可以切断RDD之间的依赖关系。
checkpoint 的执行原理：

1. 当RDD的job执行完毕后，会从finalRDD从后往前回溯。

2. 当回溯到某一个RDD调用了checkpoint方法，会对当前的RDD做一个标记。

3. Spark框架会自动启动一个新的job，重新计算这个RDD的数据，将数据持久化到HDFS上。
   优化：对RDD执行checkpoint之前，最好对这个RDD先执行cache，这样新启动的job只需要将内存中的数据拷贝到HDFS上就可以，省去了重新计算这一步。

   #### 使用：

4. 

   ```
    SparkConf conf = new SparkConf();
    conf.setMaster("local").setAppName("checkpoint");
    JavaSparkContext sc = new JavaSparkContext(conf);
    sc.setCheckpointDir("./checkpoint");
    JavaRDD<Integer> parallelize = sc.parallelize(Arrays.asList(1,2,3));
    parallelize.checkpoint();
    parallelize.count();
    sc.stop();
   ```

   

