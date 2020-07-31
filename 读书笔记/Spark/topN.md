```java
package com.shsxt.java.core.demo;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.PairFunction;
import org.apache.spark.api.java.function.VoidFunction;
import scala.Tuple2;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Iterator;
import java.util.List;

public class TopN {
    public static void main(String[] args) {
        SparkConf conf;
        conf = new SparkConf().setMaster("local").setAppName("a");

        JavaSparkContext sc = new JavaSparkContext(conf);

        //a 80
        //a 78
        JavaRDD<String> linesRDD = sc.textFile("data\\scores.txt");

        /**
         * 将数据转换为kv格式
         */
        JavaPairRDD<String, Integer> pairRDD = linesRDD.mapToPair(new PairFunction<String, String, Integer>() {
            @Override
            public Tuple2<String, Integer> call(String s) throws Exception {
                String[] strings = s.split(" ");
                return new Tuple2<>(strings[0], Integer.valueOf(strings[1]));
            }
        });

        /**
         * 将数据按名字进行分组，通过迭代器筛选出个人分数的前N
         */
        JavaPairRDD<String, Iterable<Integer>> groupRdd = pairRDD.groupByKey();
        groupRdd.foreach(new VoidFunction<Tuple2<String, Iterable<Integer>>>() {
            @Override
            public void call(Tuple2<String, Iterable<Integer>> s) throws Exception {
                String clazzName = s._1;
                Iterator<Integer> iterator = s._2.iterator();

                /**
                 * 创建数组[N]
                 * 遍历迭代器{
                 * 先将数组[N]装满
                 * 当前next()>[i]时，查看[i]是否在[N]中最小，next()替代[N]中最小的值}
                 */
                Integer[] top3 = new Integer[3];
                while (iterator.hasNext()) {
                    Integer score = iterator.next();
                    for (int i = 0; i < top3.length; i++) {
                        if (top3[i] == null) {
                            top3[i] = score;
                            break;
                        } else if (score > top3[i]) {
                            for (int j = 2; j > i; j--) {
                                top3[j] = top3[j - i];
                            }
                            top3[i] = score;
                            break;
                        }
                    }
                }
                for (Integer score : top3) {
                    System.out.println(clazzName + " " + score);
                }
            }
        });

        /**
         * 对结果数据进行排序、输出
         * 
         */
        groupRdd.foreach(new VoidFunction<Tuple2<String, Iterable<Integer>>>() {
            @Override
            public void call(Tuple2<String, Iterable<Integer>> tuple2) throws Exception {
                String key = tuple2._1;
                Iterator<Integer> iterator = tuple2._2.iterator();
                List<Integer> list = new ArrayList<>();
                while (iterator.hasNext()) {
                    list.add(iterator.next());
                }
                Collections.sort(list);
                for (int i = 0; i < Math.min(3, list.size()); i++) {
                    System.out.println(key + " " + list.get(list.size() - i - 1));
                }
            }
        });
        sc.stop();
    }
}
```