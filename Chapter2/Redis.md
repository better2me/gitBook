# 第八篇：Redis

### 一、介绍

1. #### 关系型数据库 和 非关系型数据库

   - ##### 关系型数据库：MySQL   Oracle ..

     特点：数据结构严谨（库，表，字段..），插入数据严格符合声明的数据类型。

     ACID：原子性，一致性，独立性，持久性

   - ##### 非关系型数据库：Redis  Hbase ...

     特点：数据结构不严谨、松散，

     CAP：强一致性，可用性，分区容错性

2. #### WEB 1.0  和 WEB 2.0

   - ##### WEB 1.0

     用户大多情况只是浏览数据。

   - ##### WEB 2.0

     用户开始产生数据。这就产生了问题。

     - 高并发：同一时刻多个用户往数据库插入数据。

     - 海量数据：存储大量数据。

     - 高扩展性与可用性：服务器切换方式 与 停机时间 。

3. #### NoSQL

   not only sql

4. #### 特性

   - ##### 多数据库。（默认16个 [ 0~15 ])

     select  库索引  可以选择数据库存数据。

     dbsize  查看当前数据库长度

     flush  删除数据库内容

   - ##### 消息的订阅与发布

     subscribe  订阅关键字 （完全匹配）

     public 发布  键   值 

     psubscribe  订阅关键字（关键字匹配）

### 二、数据类型

1. #### 键约束

   长度不能超过1024字节，见名知意

2. #### 五种数据类型

   - ##### String

     String  （内存缓存）

   - ##### Has

     HashMap  （复杂的内存缓存）

   - ##### List

     LinkedList   （避免数据推送丢失 rpop lpush）

   - ##### Set 

     HashSet   （查看访问用户的数量[去重]）

   - ##### SortedSet 

     TreeSet   （实时排名）

### 三、连接池

1. #### 用法

   ```java
   //1. 设置初始化参数。
   JedisPoolConfig pc = new JedisPoolConfig();
   pc.setMaxTotal(100);//最大链接数
   pc.setMaxIdle(30);//最大空闲连接数
   pc.setMinIdle();//最小空闲连接数
   pc.setMaxWaitMillis();//最大等待时间
   //2. 根据初始化参数创建连接池
   JedisPool pool = new JedisPool(pc,"ip"，6379);
   //3. 从连接池获取链接。
   Jedis jedis = pool.getResource();
   //4. 使用链接。
   jdeis.get(key);
   //5. 存回连接池。
   jedis.close();  //省略了fiinally包起来
   ```

2. #### '池'思想

   - 原理 ：

     创建容器，预先存储若干资源。

     使用资源时从容器中取出现成的。

     使用完毕，放回容器。

   - 特点

     效率高，节约资源。

### 四、持久化

redis 的数据 都是在内存中的，有安全隐患。(关系型数据库备份)

redis 自身实现了持久化：

1. #### RDB (模式官方推荐，默认开启  [Repeat DataBase])

   隔一段时间将 redis 数据 序列化到磁盘。

   - ##### 实现策略

     ```shell
     save    900    1  #15分钟，有1个key 改变，就序列化。
     
     save    300    10   #5分钟，有10个key 改变，就序列化。
     
     save    60      10000  #在1分钟内 有1w个key 改变，就序列化。
     ```

   - ##### 存储位置

     > dbfilename   dump.rdb

2. #### AOF 模式。(Append  Only  File)

   当数据有变化时，就序列化到磁盘

   - 实现策略  

     ```shell
     #redis.conf 配置文件里
     appendonly no    yes   #启用
     #(用哪个就把注释干掉)
     # appendfsync    always
     # appendfsync    everysec
     # appendfsync    no
     ```

   - 存储位置

     > appendonly.aof

     

   