# H

## 设计表

```
{
    "name": "songsong",
    "friends": ["bingbing" , "lili"] ,       //列表Array, 
    "children": {                      //键值Map,
        "xiao song": 18 ,
        "xiaoxiao song": 19
    }
    "address": {                      //结构Struct,
        "street": "hui long guan" ,
        "city": "beijing" 
    }
}
```

#



```

```





































## 数据类型

- 原始数据类型
  - int tinyint smallint bigint
  - float double
  - string varchar char
  - boolean 
  - date timestamp
- 组合数据类型
  - array<>
  - map<,>
  - struct<name:>

## 表的操作

### 创建表

- ```
  CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name 
  [(col_name data_type [COMMENT col_comment], ...)] 
  [COMMENT table_comment] 
  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] 
  [CLUSTERED BY (col_name, col_name, ...) 
  [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] 
  [ROW FORMAT row_format] 
  [STORED AS file_format]
  [LOCATION hdfs_path]
  
  CREATE 创建的关键字
  [EXTERNAL] 如果添加这个关键字，那么将创建一个外部表
  TABLE 关键字
  [IF NOT EXISTS] 如果不存在就创建这张表,如果重复创建会报错
  [(col_name data_type [COMMENT col_comment], ...)]  设置表的属性 
  	列名 数据类型,列名 数据类型
  [COMMENT table_comment] 设置表的注释信息
  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]  数据分区
  	列名 数据类型,列名 数据类型
  [CLUSTERED BY (col_name, col_name, ...) 数据分桶
  [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] 数据分桶
  [ROW FORMAT row_format]  设置数据切分格式
  [STORED AS file_format] 以什么样的格式存储数据
  [LOCATION hdfs_path] 指定数据存放的位置（外部表时候使用）
  ```

- ```
  {
      "name": "songsong",
      "friends": ["bingbing" , "lili"] ,       //列表Array, 
      "children": {                      //键值Map,
          "xiao song": 18 ,
          "xiaoxiao song": 19
      }
      "address": {                      //结构Struct,
          "street": "hui long guan" ,
          "city": "beijing" 
      }
  }
  songsong,bingbing-lili,xiaosong:18-xiaoxiaosong:16,huilongguan-beijing
  
  create table t_user(
  name string,
  friends array<string>,
  children map<string,int>,
  address struct<street:string,city:string>
  )
  row format delimited 
  fields terminated by ',' 
  collection items terminated by '-'
  map keys terminated by ':'
  lines terminated by '\n';
  
  load data local inpath '/root/t_user_001' into table t_user;
  
  drop table t_user;
  ```

- 表与文件夹与文件

  - 当我们创建好表的时候，会在当前表所属的库中创建一个文件夹
  - 当我们设置表路径的时候，可以直接指向一个已有的路径,也可以直接去使用文件夹中的数据
  - 当我们load数据的时候，就会将数据文件存放到表对应的文件夹中
  - 而且数据一旦被load，就不能被修改
  - 我们查询数据也是查询文件中的文件,这些数据最终都会存放到HDFS
  - 当我们删除表的时候，表对应的文件夹会被删除，同时数据也会被删除

### 显示修改表结构

- 显示表
  - show tables;
  - show tables like '*u*';
- 表的详情
  - desc person;
- 删除表
  - drop table if exists t_user;
- 修改表名称
  - 内部表（同时修改文件目录）
  - 外部表（因为目录是共享的，所以不会修改目录名称）
  - alter table person rename to t_person;
- 修改列
  - alter table t_person change column name pname string;
- 添加列
  - alter table t_person add columns ( passwd string );

### 内-外部表

- 与之对应的概念是内部表，如果创建表的时候不加 external

- 当删除外部表的时候，并不会级联的删除对应的文件夹

- 所以我们经常将外部表设置Location，指向一个项目组共享的一个资源

- 外部表：hive外面的表，也就是说hive并没有完全的拥有这份数据

- 删除这张表并不会去删除这个数据，不过描述这张表的元数据信息将会被删除。

- 可以减少数据的传输和硬盘的占用

- ```sql
  create external table t_user(
  name string,
  friends array<string>,
  children map<string,int>,
  address struct<street:string,city:string>
  )
  row format delimited 
  fields terminated by ',' 
  collection items terminated by '-'
  map keys terminated by ':'
  lines terminated by '\n'
  location '/shsxt/t_user/';
  
  load data local inpath '/root/t_user_001' into table t_user;
  
  drop table t_user;
  ```

- 内部表-外部表相互转换

  - alter table t_user set tblproperties('EXTERNAL'='TRUE');

## 数据载入

### load

- [ ] 加载数据的方式

- [ ] ```
  load data [local] inpath '/opt/module/datas/student.txt' overwrite | into table student [partition (partcol1=val1,…)];
  
  
  load data 加载数据
  [local] 本地，不加Local就是从HDFS,如果是HDFS，将会删除掉原来的数据
  inpath 数据的路径
  '/opt/module/datas/student.txt' 具体的路径，要参考本地还是HDFS
  overwrite 覆盖
  into table 加入到表
  student 表的名字
  [partition (partcol1=val1,…)] 分区
  
  
  --Linux本地
  load data local inpath '/root/t_user_001' into table t_user;
  --HDFS
  load data inpath '/shsxt/t_user/t_user_001' into table t_user;
  --HDFS 覆盖
  load data inpath '/shsxt/t_user_001' overwrite into table t_user;
  load data local inpath '/root/t_user_001' overwrite into table t_user;
  ```

### Insert查询结果

```
--创建一张表
create table t_user1(
name string
)
row format delimited fields terminated by ','  lines terminated by '\n';

create table t_user2(
friends array<string>
)
row format delimited fields terminated by ',' 
collection items terminated by '-'  
lines terminated by '\n';


--插入别人的查询结果
insert overwrite table t_user1 select name from t_user;
insert overwrite table t_user2 select friends from t_user;

--一次查询但是可以将数据插入到多张表(多插入模式不能插入相同的表)
from t_user 
insert overwrite table t_user1 select name
insert overwrite table t_user2 select friends;
```

### Insert原始数据

```
--特殊数据类型插入
insert into t_user values('songxiaobao',{'zs','ls'},'xb:13-xxb:11','tl-hlj');

--不推荐使用，每次只能插入一条记录，而且会启用mapreduce
insert into t_user1 values('songxiaobao');
```

## 数据备份

- 将表中的数据备份

```
--将查询结果存放到本地
insert overwrite local directory '/root/user' 
select * from t_user;

--按照指定的方式将数据输出到本地
insert overwrite local directory '/root/user'
ROW FORMAT DELIMITED fields terminated by ',' collection items terminated by '-'
map keys terminated by ':' lines terminated by '\n'
select * from t_user;

--将查询结果输出到HDFS 
insert overwrite directory '/shsxt/user'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
select * from t_user;
--直接使用HDFS命令保存表对应的文件夹
hdfs dfs -get Hive Linux
```

- 将表结构和数据同时备份

```
--将数据导出到HDFS
export table t_user to '/shsxt/t_user_backup';
--删除表结构
drop table t_user;
--恢复表结构和数据
import from '/shsxt/t_user_backup';
```

## 分区表

- 原因

  - 当我们load数据到表中的时候，会将数据存放到表对应的文件夹中
  - 但是随着时间的增长，表中的数据也会越来越多
  - 每次查询都会做全遍历的话，那么查询的时间也会越来越长
  - 我们可以根据常用属性建立子文件夹，将对应的数据存放到里面去
  - 这样我们查询数据直接去子文件夹中查询即可

- 分区表分类

  - 分区的层次

    - 单分区

    - 多分区

      - ```
        xiaoming1,g1,bj1
        xiaoming2,g1,bj1
        xiaoming3,g1,bj2
        xiaoming4,g1,bj2
        xiaohong1,g2,bj1
        xiaohong2,g2,bj1
        xiaohong3,g2,bj2
        xiaohong4,g2,bj2
        
        
        --创建学生表
        create table t_student(
        name string
        )
        partitioned by(
        grade string,
        clazz string
        ) 
        row format delimited 
        fields terminated by ',' 
        lines terminated by '\n';
        
        --动态分区
        create table t_student_tmp(
        name string,
        grade string,
        clazz string
        ) 
        row format delimited 
        fields terminated by ',' 
        lines terminated by '\n';
        
        --向临时表中载入数据
        load data local inpath '/root/student' into table t_student_tmp;
        --开始向分区表插入数据
        insert overwrite table t_student partition (grade,clazz) 
        select name,grade,clazz from t_student_tmp;
        ```

  - 数据加载的方式

    - 静态分区

      - 将数据指定到那个分区，对应的分区属性就是指定值

      - 要求我们绝对不能放错数据

      - ```
        --创建角色表
        create table t_role(
        name string
        )
        partitioned by(
        author string
        ) 
        row format delimited 
        fields terminated by ',' 
        lines terminated by '\n';
        
        --向分区表中加载数据
        load data local inpath '/root/novel_gl' into table t_role partition(author='gl');
        load data local inpath '/root/novel_jy' into table t_role partition(author='jy');
        
        --向分区中加载数据（同时有两个不同分区的数据）
        load data local inpath '/root/novel_gj' into table t_role partition(author='gl');
        ```

    - 动态分区

      - 就是现将数据存放到临时表

      - 等我们将数据插入到分区的时候，查询临时表

      - ```
        --设置参数开启动态分区
        set hive.exec.dynamic.partition=true;
        set hive.exec.dynamic.partition.mode=nonstrict;
        --创建角色表
        create table t_role(
        name string
        )
        partitioned by(
        author string
        ) 
        row format delimited 
        fields terminated by ',' 
        lines terminated by '\n';
        --创建动态分区的临时表
        create table t_role_tmp(
        name string,
        author string
        ) 
        row format delimited 
        fields terminated by ',' 
        lines terminated by '\n';
        --向临时表中载入数据
        load data local inpath '/root/novel' into table t_role_tmp;
        --动态插入数据
        insert overwrite table t_role partition (author) select name from t_role_tmp;
        ```

## SQL查询

- 和SQL语句一样去使用

  - select from where group by having order by desc/asc

- 规则也和以前一样

  - ```
    --emp
    1122,scott,3100,10
    3344,smith,3200,20
    5566,james,3300,10
    7788,cat,3400,30
    9900,tom,3500,10
    --dept
    10,java
    20,bd
    30,python
    --创建表
    create table t_emp(
    empno int,
    name string,
    sal int,
    deptno int
    ) 
    row format delimited  fields terminated by ','  lines terminated by '\n';
    
    create table t_dept(
    deptno int,
    dname string
    ) 
    row format delimited  fields terminated by ','  lines terminated by '\n';
    --载入数据
    load data local inpath '/root/emp' into table t_emp;
    load data local inpath '/root/dept' into table t_dept;
    
    --查询每个部门的人数
    select deptno,count(empno) from t_emp group by deptno;
    select e.name,d.dname from t_emp e,t_dept d where e.deptno = d.deptno;
    select d.dname,count(e.empno) ce from t_emp e,t_dept d where e.deptno = d.deptno group by d.dname order by ce;
    --查询谁的薪资大于公司的平均薪资
    select e.name from t_emp e,(select avg(sal) avgsal from t_emp) tt where e.sal >tt.avgsal ;
    --查询平均薪资大于等于3200的部门编号
    select deptno,avg(sal) from t_emp group by deptno having avg(sal)>=3200;
    ```

- 还可以使用非关系型数据库的某些SQL语句

  - split:

    - 切分成数组

  - explode

    - 将数组进行拆分，然后一个数据就是一行

  - Lateral View

    - 首先通过UDTF函数拆分成多行，然后将多行的结果组成一个支持别名的虚拟表。

  - ```
    --IMDB250
    nezha dongman,shenhua,3d
    shanghaibaolei 3d,kehuan,aiqing
    zhumengyanyiquan lizhi,dushi
    fengshen shenhua,3d,lishi,dongman
    --创建表
    create table t_movie(
    name string,
    type string
    ) 
    row format delimited  fields terminated by ' '  lines terminated by '\n';
    --载入数据
    load data local inpath '/root/movie' into table t_movie;
    
    --查询数据
    select * from t_movie;
    select name,split(type,',') from t_movie;
    select explode(split(type,',')) from t_movie;
    --错误deSQL，产生一个对多个的问题
    select name,explode(split(type,',')) from t_movie;
    
    --通过虚拟表合并name和type
    select name,tcol from t_movie LATERAL VIEW explode(split(type,',')) typetable AS tcol;
    
    ```

  ```
  
  ```

- WordCount

  - ```
    The escape of the Brazilian boa constrictor earned Harry his longest-ever punishment. By the time he was allowed out of his cupboard again, the summer holidays had started and Dudley had already broken his new video camera, crashed his remote control airplane, and, first time out on his racing bike, knocked down old Mrs. Figg as she crossed Privet Drive on her crutches.
    
    Harry was glad school was over, but there was no escaping Dudley's gang, who visited the house every single day. Piers, Dennis, Malcolm, and Gordon were all big and stupid, but as Dudley was the biggest and stupidest of the lot, he was the leader. The rest of them were all quite happy to join in Dudley's favorite sport: Harry Hunting.
    
    This was why Harry spent as much time as possible out of the house, wandering around and thinking about the end of the holidays, where he could see a tiny ray of hope. When September came he would be going off to secondary school and, for the first time in his life, he wouldn't be with Dudley. Dudley had been accepted at Uncle Vernon's old private school, Smeltings. Piers Polkiss was going there too. Harry, on the other hand, was going to Stonewall High, the local public school. Dudley thought this was very funny.
    
    “They stuff people's heads down the toilet the first day at Stonewall,” he told Harry. “Want to come upstairs and practice?”
    
    
    --创建表
    create table t_wordcount(
    wordline string
    ) 
    row format delimited  fields terminated by '\n';
    
    
    --载入数据
    load data local inpath '/root/harry' into table t_wordcount;
    
    --单词统计
    select * from t_wordcount;
    
    select split(wordline,' ') from t_wordcount;
    
    select explode(split(wordline,' ')) from t_wordcount;
    
    --方案1
    select word,count(word) from t_wordcount lateral  view explode(split(wordline,' ')) eswtable as word group by word;
    
    --方案2
    select word,count(word) from (select explode(split(wordline,' ')) word from t_wordcount) esw group by word;
    ```

    











## MetaStore

- DBS
  - 和库相关的数据
- TBLS
  - 和表相关的数据
- SDS
  - 和表数据相关的信息





































```
CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name    -- (Note: TEMPORARY available in Hive 0.14.0 and later)
  [(col_name data_type [column_constraint_specification] [COMMENT col_comment], ... [constraint_specification])]
  [COMMENT table_comment]
  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
  [CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
  [SKEWED BY (col_name, col_name, ...)                  -- (Note: Available in Hive 0.10.0 and later)]
     ON ((col_value, col_value, ...), (col_value, col_value, ...), ...)
     [STORED AS DIRECTORIES]
  [
   [ROW FORMAT row_format] 
   [STORED AS file_format]
     | STORED BY 'storage.handler.class.name' [WITH SERDEPROPERTIES (...)]  -- (Note: Available in Hive 0.6.0 and later)
  ]
  [LOCATION hdfs_path]
  [TBLPROPERTIES (property_name=property_value, ...)]   -- (Note: Available in Hive 0.6.0 and later)
  [AS select_statement];   -- (Note: Available in Hive 0.5.0 and later; not supported for external tables)
 
CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
  LIKE existing_table_or_view_name
  [LOCATION hdfs_path];
 
data_type
  : primitive_type
  | array_type
  | map_type
  | struct_type
  | union_type  -- (Note: Available in Hive 0.7.0 and later)
 
primitive_type
  : TINYINT
  | SMALLINT
  | INT
  | BIGINT
  | BOOLEAN
  | FLOAT
  | DOUBLE
  | DOUBLE PRECISION -- (Note: Available in Hive 2.2.0 and later)
  | STRING
  | BINARY      -- (Note: Available in Hive 0.8.0 and later)
  | TIMESTAMP   -- (Note: Available in Hive 0.8.0 and later)
  | DECIMAL     -- (Note: Available in Hive 0.11.0 and later)
  | DECIMAL(precision, scale)  -- (Note: Available in Hive 0.13.0 and later)
  | DATE        -- (Note: Available in Hive 0.12.0 and later)
  | VARCHAR     -- (Note: Available in Hive 0.12.0 and later)
  | CHAR        -- (Note: Available in Hive 0.13.0 and later)
 
array_type
  : ARRAY < data_type >
 
map_type
  : MAP < primitive_type, data_type >
 
struct_type
  : STRUCT < col_name : data_type [COMMENT col_comment], ...>
 
union_type
   : UNIONTYPE < data_type, data_type, ... >  -- (Note: Available in Hive 0.7.0 and later)
 
row_format
  : DELIMITED [FIELDS TERMINATED BY char [ESCAPED BY char]] [COLLECTION ITEMS TERMINATED BY char]
        [MAP KEYS TERMINATED BY char] [LINES TERMINATED BY char]
        [NULL DEFINED AS char]   -- (Note: Available in Hive 0.13 and later)
  | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)]
 
file_format:
  : SEQUENCEFILE
  | TEXTFILE    -- (Default, depending on hive.default.fileformat configuration)
  | RCFILE      -- (Note: Available in Hive 0.6.0 and later)
  | ORC         -- (Note: Available in Hive 0.11.0 and later)
  | PARQUET     -- (Note: Available in Hive 0.13.0 and later)
  | AVRO        -- (Note: Available in Hive 0.14.0 and later)
  | JSONFILE    -- (Note: Available in Hive 4.0.0 and later)
  | INPUTFORMAT input_format_classname OUTPUTFORMAT output_format_classname
 
column_constraint_specification:
  : [ PRIMARY KEY|UNIQUE|NOT NULL|DEFAULT [default_value]|CHECK  [check_expression] ENABLE|DISABLE NOVALIDATE RELY/NORELY ]
 
default_value:
  : [ LITERAL|CURRENT_USER()|CURRENT_DATE()|CURRENT_TIMESTAMP()|NULL ] 
 
constraint_specification:
  : [, PRIMARY KEY (col_name, ...) DISABLE NOVALIDATE RELY/NORELY ]
    [, PRIMARY KEY (col_name, ...) DISABLE NOVALIDATE RELY/NORELY ]
    [, CONSTRAINT constraint_name FOREIGN KEY (col_name, ...) REFERENCES table_name(col_name, ...) DISABLE NOVALIDATE 
    [, CONSTRAINT constraint_name UNIQUE (col_name, ...) DISABLE NOVALIDATE RELY/NORELY ]
    [, CONSTRAINT constraint_name CHECK [check_expression] ENABLE|DISABLE NOVALIDATE RELY/NORELY ]
```







