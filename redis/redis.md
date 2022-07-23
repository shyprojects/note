# linux环境下安装redis

* 将redis-6.2.1.tar.gz上传至/opt/

* 安装环境需要gcc

* 解压

* 进入文件redis-6.2.1

* make（编译）

* make install（安装）

* 默认安装在/usr/local/bin 

* bin下的几个文件：

  * redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何
  * redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲
  * redis-check-dump：修复有问题的dump.rdb文件
  * redis-sentinel：Redis集群使用
  * redis-server：Redis服务器启动命令
  * redis-cli：客户端，操作入口

* 启动redis

  * 前台启动：redis-server（不推荐使用，启动后此会话不能进行其他操作，ctrl+c停止）

  ![image-20220501120547908](redis.assets/image-20220501120547908.png)

  * 后台启动：

    * 修改redis.config(daemonize no改为yes)
    * 启动redis：redis-server /usr/local/.../redis.conf
    * 查看相关信息：ps -ef | grep redis

    ![image-20220501125047601](redis.assets/image-20220501125047601.png)

    * 用客户端访问：redis-cli

    ![image-20220501125306269](redis.assets/image-20220501125306269.png)

    * 测试验证：ping

    ![image-20220501125406304](redis.assets/image-20220501125406304.png)

* 关闭redis：

  * 在客户端访问：shutdown 
  * 在客户端访问：exit                  +             ps -ef | grep redis（查看进程）+ kill 进程号
  
* 设置开机自启动

  * 创建文件

  ```
  vi /etc/systemd/system/redis.service
  ```

  * 内容如下

  ```
  [Unit]
  Description=redis-server
  After=network.target
  
  [Service]
  Type=forking
  ExecStart=/usr/local/bin/redis-server /usr/local/src/redis-6.2.6/redis.conf
  PrivateTmp=true
  
  [Install]
  WantedBy=multi-user.target
  ```

  * 重载服务

  ```
  systemctl daemon-reload
  ```

  * 使用下面的命令进行操作

  ```
  # 启动
  systemctl start redis
  # 停止
  systemctl stop redis
  # 重启
  systemctl restart redis
  # 查看状态
  systemctl status redis
  # 执行下面的命令，可以让redis开机自启：
  systemctl enable redis
  ```

# 相关基础知识

* redis的默认端口号为6379
* redis默认有16个数据库，从下标0开始，默认处于0号数据库。
  * 通过select 1 切换数据库
  * 所有的数据库密码相同
* redis是单线程+多路io复用

![image-20220511172510271](redis.assets/image-20220511172510271.png)

## redis命令行客户端

* redis安装完成后就自带了命令行客户端：redis-cli，使用方式如下

```
redis-cli [options] [commonds]
```

* 常用的options：
  * -h 连接地址（127.0.0.1）
  * -p 端口号
  * -a 密码
* 直接使用-a 密码比较危险，可以先进入redis客户端再输入   AUTH 密码 登录。
  * 使用ping与redis服务器做心跳测试

## 常用命令

* keys *0：查看0号库所有的key，不加0为当前库
* select 1：切换数据库
* exists k1：查看当前库是否存在k1
* type k1：查看k1的类型
* del k1：删除k1
* unlink k1：删除k1
* expire k1 10：给k1设置过期时间10s
* ttl k1：查看k1还有多长时间过期，-1代表永不过期，-2代表已经过期
* dbsize：查看当前库的key个数
* flushdb：清空当前库
* flushall：清空所有库

# 常用数据类型

## redis中key的格式

* redis的key允许有多个单词形成层级结构，多个单词之间用:隔开，如下：
  * 项目名：业务名：类型：id
* 类似于java中的包层级关系
* 这个格式非固定，也可以根据自己的需求来删除或添加词条
  * set com:shy:user:1 tom
  * set com:shy:product:1 p1
* 在redis的图形化界面中可以清楚的观察出层级关系

## String

* String类型是二进制安全的，意味着可以包含任何数据类型，比如jpg，序列化的对象
* 一个String的value最多可以是512M
* String类可以是普通字符串，也可以是int或者float，字符串与数字之间只是存储方式不同，使用上没有任何区别

### 常用命令

* set k1 值：设置键值对 k1 值
* get k1：获取键k1的值，设置已经存在的键会覆盖
* mset k1 v1 k2 v2：批量设置多个
* mget k1 k2 k3：批量获取
* incr k1：k1++
* decr k1：k1--
* incrby k1 10：k1+10
* decrby k1 10：k1-10
* append k1 值：在已经存在的k1上追加值
* strlen k1：得到k1的长度
* setnx k1 值：若k1存在，不发生改变，若不存在，设置k1的值
* msetnx k1 v1 k2 v2：k1 k2都不存在才能成功
* getrange k1 0 3：获取字符串k1前四个位置的值
* setrange k1 3 abc：从3号位置开始覆盖为abc
* setex age 20 value 30：设置age=30过期时间20s
* getset k1 tom：获取k1，并且设置k1为tom

## Hash

* 是一个键值对集合
* 想要存储java对象可以使用JSON存储，但是该方法在数据库中进行修改格外困难，因此使用Hash进行存储
* 是一个string类的filed和value的映射表，特别适合存储对象。

### 常用命令

* hset k1 f1 v1：向k1中添加键值对 f1 v1
* hget k1 f1：取出k1中f1对应的v1
* hmset k1 f1 v1 f2 v2：批量添加
* hgetall k1：获取k1所有的键值对
* hexists k1 f1：查看k1中f1是否存在
* hkeys k1：查看k1中所有的f
* hvals k1：查看k1中所有的v
* hincrby k1 f1 n：给k1中的f1加上n
* hsetnx k1 f1 v1：存在不添加，不存在添加

## List

* 简单的字符串列表，按照插入顺序排序，可以插入一个数据到头部或者尾部
* 底层是双向链表，对两端容易操作，操作中间元素性能较差

![image-20220512013121500](redis.assets/image-20220512013121500.png)

* 底层原理
  * 如果元素少，多个元素放在一个ziplist中，元素多后，ziplist连起来称为一个quicklist

![image-20220512015218771](redis.assets/image-20220512015218771.png)

### 常用命令

* lpush/rpush： k1 v1 v2 v3：从左边/右边一个一个放v1 v2 v3
* lpop/rpop k1：从左边/右边去除一个值
* rpoplpush k1 k2：从k1的右边取出一个值加到k2的左边
* lrange k1 0 1：查看k1中0-1的值，0 -1代表全部
* lindex k1 1：获取1号位置的元素
* llen k1：获取k1的长度
* linsert k1 before/after v1 v2：在v1之前/之后添加值v2
* lrem k1 3 v1：将k1从左到右删除3个v1
* lset k1 1 v2：修改
* blpop/brpop k1 100：阻塞式的取出，k1如果为空会一直阻塞（lpop会直接返回nil）持续100秒，100秒内有元素添加进来就会取出来

## Set

* Set是String类型的无序集合，底层是value为null的Hash表

### 常用命令

* sadd k1 v1 v2 v3：添加，已经存在的元素会被忽略
* smenber k1：取出k1中的所有元素
* sismenber k1 v1：判断k1中是否含有v1，有返回1，没有返回0
* scard k1：查看集合元素个数
* srem k1 v1 v2：删除k1中的v1 v2
* spop k1：从k1中随机取出一个值
* srandmenber k1 n：从k1中取出n个值，不会删除
* smove k1 k2 v1：将k1中的v1移动到k2
* sinter k1 k2：返回k1k2的交集
* sunion k1 k2：返回k1k2的并集
* sdiff k1 k2：返回k1k2的差集

## Zset/SortedSet

* 与set相似，但是是有序的。

* 每个成员都关联了一个评分，这个评分被用来按照从高到低的方式排序集合中的成员，集合成员是唯一的，但是评分可以是重复的。

* Zset底层使用了Hash和跳跃表：

  * 跳跃表在于给value排序，根据score的范围获取元素列表
  * 从第一层开始找，从上至下

  ![image-20220512133324971](redis.assets/image-20220512133324971.png)

### 常用命令

* zadd k1 s1 v1 s2 v2：向k1中添加v1 v2 评分分别为s1 s2
* zrange topn 0 1：返回排序 0-1的元素（排序按照score从小到大），0 -1代表全部
* zrangebyscore k1 min max （withscores）：返回score介于min-max之间的元素
* zrevrangebyscore k1 max min （withscores）：返回score介于min-max之间的元素倒叙排。
* zincrby k1 50 v1：给k1中的v1的score+50
* zrem k1 v1：删除
* zcount k1 min max：统计个数
* zrank k1 v1：查看v1排名

# 配置文件

* 单位的表示方式，不支持1k对应1000bytes

![image-20220512200617119](redis.assets/image-20220512200617119.png)

* 包含的其他文件

![image-20220512200744809](redis.assets/image-20220512200744809.png)

* bind
  * 设置只允许本机访问
  * 注释掉后可以通过其他地址远程访问

![image-20220512190540935](redis.assets/image-20220512190540935.png)

* protected-mode
  * 保护模式改为no
  * 此时即可支持其他地址访问

![image-20220512190702028](redis.assets/image-20220512190702028.png)

* tcp-backlog
  * backlog是一个连接队列， ===  未完成三次握手队列+已经完成三次握手队列（默认511）

![image-20220512191202057](redis.assets/image-20220512191202057.png)

* timeout（默认为0永不超时）
  * 设置一个超时时间，长时间没有数据交互超时后，连接断开，需要重新连接redis

![image-20220512192139747](redis.assets/image-20220512192139747.png)

* tcp-keepalive

  * 心跳时间，默认300，每隔300秒检测一次，连接是否存活，如果存活继续服务，不存活，释放服务

  ![image-20220512193218507](redis.assets/image-20220512193218507.png)

* daemonize

  * 是否支持后台启动

  ![image-20220512193416326](redis.assets/image-20220512193416326.png)

* pidfile
  
  * redis每次启动时设置进程号

![image-20220512193529744](redis.assets/image-20220512193529744.png)

* loglevel（日志级别）
  * debug：最详细的信息
  * verbose：较为有用的信息
  * notice：生产环境中使用
  * warning：一些最重要的信息

![image-20220512193630381](redis.assets/image-20220512193630381.png)

* logfile

  * 设置日志的输出路径（默认为空）

  ![image-20220512193851931](redis.assets/image-20220512193851931.png)

* databases

  * 默认的数据库个数

  ![image-20220512193951585](redis.assets/image-20220512193951585.png)

* requirepass（设置密码）
  * 打开注释，密码设置在后面
  * 也可以通过指令设置

![image-20220512195345621](redis.assets/image-20220512195345621.png)

* maxclients
  * 设置最大客户端连接个数

![image-20220512195503679](redis.assets/image-20220512195503679.png)

* 设置最大内存

![image-20220512195655923](redis.assets/image-20220512195655923.png)

# redis的发布与订阅

* redis的发布与订阅（pub/sub）是一种消息通信模式：发布者（pub）发送消息，订阅者（sub）接收消息

* redis客户端可以订阅任意数量的频道

* 操作演示：

  * 打开一个客户端，订阅一个频道channel1

    * subscribe channel1

    ![image-20220513123728397](redis.assets/image-20220513123728397.png)

  * 打开另外一个客户端，向频道channel1发送消息hello

    * publish channel1 hello

    ![image-20220513123821887](redis.assets/image-20220513123821887.png)

  * 订阅的客户端可以接收到消息

  ![image-20220513123925670](redis.assets/image-20220513123925670.png)

# redis中的新数据类型

## Bitmaps

* 该数据类型可以实现位操作。
* 其本身不是一种数据类型，实际上是字符串，但是他可以对字符串的位进行操作。
* 可以把bitmaps想象成一个以位为单位的数组，数组每个单元只能放0和1，数组的下标叫做偏移量

![image-20220513125558774](redis.assets/image-20220513125558774.png)

### 常用命令

* setbit k1 0 1：第一个数组元素设置为1
* getbit k1 0：获取k1偏移量为0的元素的值
* bitcount k1：统计k1中1的值
* bitcount k1 start end：

![image-20220513132109280](redis.assets/image-20220513132109280.png)

* bittop operation destkey key1 key2
  * operation可以取：
    * and 交集
    * or 并集
    * not 非
    * xor 异或
  * 以and为例
    * key1：00000101
    * key2：10100110
    * 会生成一个destkey：00000100

### bitmap的优点与不足

![image-20220513133548286](redis.assets/image-20220513133548286.png)

![image-20220513133706031](redis.assets/image-20220513133706031.png)

![image-20220513133804229](redis.assets/image-20220513133804229.png)

## HyperLogLog

* HyperLogLog来处理重复的元素问题。
* 只要解决基数问题

### 常用命令

* pfadd k1 v1 v2：添加v1 v2到k1中，重复元素不会添加
* pfcount k1 k2...：计算k1 k2中所有不重复元素的个数
* pfmerge destkey sourcekey1 sourcekey2：将sourcekey1 sourcekey2元素合并到destkey

## Geospatial

* redis3.2中增加了对geo类型的支持，是地理信息的缩写，该类型，就是元素的2维坐标，在地图上就是经纬度，redis基于该类型，提高了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作

### 常用命令

* geoadd k1 经度 维度 v1 经度 维度 v2
  * 已经添加的数据不能再次添加
  * 有效经度从-180 --- +180，有效维度从-85.05112878 --- 85.05112878
* geopos k1 v1
  * 取得指定k1中的v1的经度和维度
* geodist k1 v1 v2 单位
  * 获取k1中v1与v2的距离
  * 单位有：
    * km
    * m
    * mi
    * ft
* georadius k1 经度 维度 距离 单位
  * 从k1中取得，以经度维度为中心，距离为半径，方圆距离以内的所有元素

# Redis的java客户端

## jedis

* 是redis的一个客户端工具，通过java操作redis，类似于jdbc技术

### 测试连接

* 在本机连接虚拟机上的redis时需要有一下要求

  * 打开端口号：6379
  * bind
    * 设置只允许本机访问
    * 注释掉后可以通过其他地址远程访问

  ![image-20220512190540935](redis.assets/image-20220512190540935.png)

  * protected-mode

    * 保护模式改为no
    * 此时即可支持其他地址访问

    ![image-20220512190702028](redis.assets/image-20220512190702028.png)


* firewall-cmd --reload（重新载入生效）
* 创建maven工程，导入jedis坐标

```xml
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.3.0</version>
		</dependency>
```

* 进行测试：

```java
import redis.clients.jedis.Jedis;

public class Main {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.232.128",6379);
        String ping = jedis.ping();
        System.out.println(ping);
        jedis.close();
    }
}
```

* 成功

![image-20220513163930379](redis.assets/image-20220513163930379.png)

### 测试对redis的操作

* keys *

```java
import redis.clients.jedis.Jedis;

import java.util.Set;

public class Main {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.184.128",6379);
        Set<String> keys = jedis.keys("*");
        System.out.println(keys);
        jedis.close();
    }
}
```

* 操作字符串

```java
import redis.clients.jedis.Jedis;

public class Main {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.184.128",6379);
        jedis.set("name","tom");
        String name = jedis.get("name");
        System.out.println(name);
        jedis.close();
    }
}
```

## Jedis的线程池

* jedis本身线程不安全，并且频繁的创建和销毁连接会有性能损耗，因此推荐使用jedis连接池

## 案例：模拟验证码发送

要求：

* 输入手机号，发送后生成6位验证码，2分钟有效
* 输入验证码，返回成功或者失败
* 每个手机每天只能发送三次

```java


import redis.clients.jedis.Jedis;

import java.util.HashMap;
import java.util.Random;

public class Main {
    public static void main(String[] args) {
        sendCode("18220471294");

    }
    //对验证码进行校验
    public static void test(String phoneNumber,String code){
        Jedis jedis = new Jedis("192.168.255.128", 6379);
        String s = jedis.get("code" + phoneNumber);
        if(s.equals(code)){
            System.out.println("成功");
        }else {
            System.out.println("失败");
        }
    }
    //发送验证码
    public static void sendCode(String phoneNumber){
        Jedis jedis = new Jedis("192.168.255.128", 6379);
        String s = jedis.get("code" + phoneNumber);
        String s1 = jedis.get("times" + phoneNumber);
        if(s==null){
            String code = getCode();
            System.out.println("验证码是"+code);
            jedis.setex("code" + phoneNumber,120,code);
            jedis.set("times"+phoneNumber,"1");
        }else if(Integer.parseInt(s1)==1||Integer.parseInt(s1)==2){
            String code = getCode();
            System.out.println("验证码是"+code);
            jedis.setex("code" + phoneNumber,120,code);
            jedis.incr("times" + phoneNumber);
        }else {
            System.out.println("今日发送次数超过三次");
            return;
        }
        jedis.close();
    }
    //生成验证码
    public static String getCode(){
        Random random = new Random();
        String str = "";
        for (int i = 0; i < 6; i++) {
            int i1 = random.nextInt(10);
            str+=i1;
        }
        return str;
    }
}
```

## SpringDataRedis

* springdata是spring中数据库的操作模块，包含各种数据库的集成，其中对redis集成的模块就叫做springdataredis
* 提供了不同的java客户端：jedis、lettuce
* 支持基于jdk、json、字符串。spring对象的数据序列化与反序列化

![image-20220610122436245](redis.assets/image-20220610122436245.png)

* 创建springboot项目，导入相关依赖

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
```

* 在springboot配置文件中导入redis相关配置

```properties
#redis服务器地址
spring.redis.host=192.168.
#redis数据库端口号
spring.redis.port=6379
#redis数据库端口号索引
spring.redis.database=0
#连接超时时间
spring.redis.timeout=1800000
#连接池最大连接数（负数表示没有限制）
spring.redis.lettuce.pool.max-active=20
#最大阻塞等待时间（负数表示没有限制）
spring.redis.lettuce.pool.max-wait=-1
#连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle=5
#连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
```

* redis的自动配置类，也可以使用springboot整合的

```java
@EnableCaching
@Configuration
public class RedisConfig extends CachingConfigurerSupport {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setConnectionFactory(factory);
//key序列化方式
        template.setKeySerializer(redisSerializer);
//value序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
//value hashmap序列化
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
//解决查询缓存转换异常的问题
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
// 配置序列化（解决乱码的问题）,过期时间600秒
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(config)
                .build();
        return cacheManager;
    }
}
```

* 创建一个Controller

```java
package com.shy.springbootredis.controllers;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@org.springframework.stereotype.Controller
public class Controller {
    @Autowired
    private RedisTemplate redisTemplate;

    @RequestMapping("/")
    @ResponseBody
    public String redis(){
        redisTemplate.opsForValue().set("k","value");
        Object k = redisTemplate.opsForValue().get("k");
        return (String) k;
    }
}
```

* 测试

```java
package com.shy.springbootredis;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringbootRedisApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootRedisApplication.class, args);
    }
}
```

# 缓存

* 基本使用：

```java
@Service
public class ShopServiceImpl extends ServiceImpl<ShopMapper, Shop> implements IShopService {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public Result queryById(Long id) {
        //1.从redis缓存中查询店铺信息
        String shopKey = CACHE_SHOP_KEY + id;
        String shopJOSN = stringRedisTemplate.opsForValue().get(shopKey);
        //2.如果查到了，就返回
        if(StrUtil.isNotBlank(shopJOSN)){
            Shop shop = JSONUtil.toBean(shopJOSN, Shop.class);
            return Result.ok(shop);
        }
        //3.如果查不到，去mysql中查询，并且写入redis并且返回
        Shop shop = getById(id);
        //如果查不到，说明查询错误
        if(shop == null){
            return Result.fail("店铺不存在");
        }

        stringRedisTemplate.opsForValue().set(shopKey,JSONUtil.toJsonStr(shop));
        return Result.ok(shop);
    }
}
```

## 缓存更新策略

* 策略：
  * 内存淘汰：不用自己维护，利用redis的内存淘汰机制，当内存不足时自动淘汰部分数据，下次查询时更新缓存，虽然几乎没有维护成本，但是数据一致性较差
  * 超时剔除：给缓存添加ttl时间，到期后自动删除缓存，下次查询时更新缓存。有一定的维护成本数据一致性的维持一般
  * 主动更新：编写业务逻辑，在修改数据库的同时更新缓存，虽然维护成本较高但是能非常好的保存数据一致性
* 业务场景：
  * 低一致性业务：采用内存淘汰。例如店铺类型的查询缓存
  * 高一致性需求：主动更新，并且以超时剔除作为兜底方案。例如店铺详情查看
* 主动更新业务实现：
  * 由缓存的调用者，在更新数据库的同时更新缓存
  * 缓存与数据库整合为一个服务，由服务来维护一致性。调用者用该服务，无需关心缓存一致性问题
  * 调用者只操作缓存，由其他线程异步将缓存持久化到数据库，最终保证一致性
* 以第一种主动更新实现：
  * 删除缓存还是更新缓存？
    * 删除缓存
  * 如何保证缓存与数据库的操作同时成功或失败？
    * 单体系统：将缓存与数据库操作放在一个事务中
    * 分布式系统：利用tcc等分布式事务方案
  * 先操作缓存还是数据库？
    * 先操作数据库

* 设置过时时间

```java
stringRedisTemplate.opsForValue().set
    (shopKey,JSONUtil.toJsonStr(shop),30, TimeUnit.MINUTES);
```

* 修改数据库删除缓存并且保证事务的原子性

```java
@Override
@Transactional
public Result update(Shop shop) {
    Long id = shop.getId();
    if (id == null){
        return Result.fail("店铺id不能为null");
    }
    updateById(shop);
    stringRedisTemplate.delete(CACHE_SHOP_KEY + id);
    return Result.ok();
}
```

## 缓存穿透

* 缓存穿透是指客户端请求的数据在缓存中和数据库中都不存在，这样缓存永远不会生效，这些请求都会打到数据库
* 常见解决方案：
  * 缓存空对象：实现简单，维护方便，但可能造成额外的内存消耗，可能造成短期的不一致
  * 布隆过滤：内存占用较少，没有多余key，但是实现较为复杂，存在误判可能
* 使用缓存空对象的方法解决缓存穿透问题：

```java
    @Override
    public Result queryById(Long id) {
        String shopKey = CACHE_SHOP_KEY + id;
        String shopJOSN = stringRedisTemplate.opsForValue().get(shopKey);
        if(StrUtil.isNotBlank(shopJOSN)){
            Shop shop = JSONUtil.toBean(shopJOSN, Shop.class);
            return Result.ok(shop);
        }
        if(shopJOSN != null){
            return Result.fail("店铺信息不存在");
        }//如果是空值，解决缓存穿透问题,直接返回错误信息
        Shop shop = getById(id);
        if(shop == null){
            //查询不到在缓存中设置空值
            stringRedisTemplate.opsForValue().set(shopKey,"",5,TimeUnit.MINUTES);
            return Result.fail("店铺不存在");
        }

        stringRedisTemplate.opsForValue().set(shopKey,JSONUtil.toJsonStr(shop),30, TimeUnit.MINUTES);
        return Result.ok(shop);
    }
```

## 缓存雪崩

* 缓存雪崩是指在同一时间段有大量缓存的key同时失效或者redis宕机，导致大量请求到达数据库，带来巨大压力。
* 解决方案：
  * 给不同的key的ttl添加随机值
  * 利用redis集群提高服务的可用性
  * 给缓存业务添加降级限流策略
  * 给业务添加多级缓存

## 缓存击穿

* 缓存击穿问题也称为热点key问题，是一个被高并发访问并且缓存重建业务较复杂的key突然失效了，无数的请求访问会在瞬间给数据库带来巨大的冲击

* 常见解决方案：

  * 互斥锁

  * 逻辑过期

  ![image-20220613180031644](redis.assets/image-20220613180031644.png)

![image-20220613180129320](redis.assets/image-20220613180129320.png)

![image-20220613180143875](redis.assets/image-20220613180143875.png)

### 利用互斥锁解决缓存击穿

![image-20220614143328913](redis.assets/image-20220614143328913.png)

```java
@Override
public Result queryById(Long id) {
    String shopKey = CACHE_SHOP_KEY + id;
    String shopJOSN = stringRedisTemplate.opsForValue().get(shopKey);
    if(StrUtil.isNotBlank(shopJOSN)){
        Shop shop = JSONUtil.toBean(shopJOSN, Shop.class);
        return Result.ok(shop);
    }
    if(shopJOSN != null){
        return Result.fail("店铺信息不存在");
    }
    Shop shop = null;
    try {
        //实现缓存重建，解决缓存击穿问题
        //1.获取互斥锁
        boolean lock = getLock("lock:shop" + id);
        //2.判断是否获取成功
        if(!lock){
            //获取失败，休眠一段时间之后重试
            Thread.sleep(50);
            return queryById(id);
        }
        //获取成功，查询mysql
        shop = getById(id);
        if(shop == null) {
            stringRedisTemplate.opsForValue().set(shopKey, "", 5, TimeUnit.MINUTES);
            return Result.fail("店铺不存在");
        }
        stringRedisTemplate.opsForValue().set(shopKey,JSONUtil.toJsonStr(shop),30, TimeUnit.MINUTES);
    } catch (InterruptedException e) {
        e.printStackTrace();
    } finally {
        //释放互斥锁
        unLock("lock:shop" + id);
    }
    return Result.ok(shop);
}

public boolean getLock(String key){
    Boolean result = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
    return BooleanUtil.isTrue(result);
}
public void unLock(String key){
    stringRedisTemplate.delete(key);
}
```

### 利用逻辑过期方式解决缓存击穿问题

# redis事务

* redis事务是一个单独的隔离操作，事务的所有命令都会序列化，按顺序执行，事务在执行过程中，不会被其他客户端发来的命令请求所打断。

## Multi、Exec、discard

* 使用Multi后，开启组队阶段，命令会依次派对。
* 使用Exec后，派对号的命令会依次执行，事务即会结束。
* 上述过程中Exec之前如果想终止，可以使用discard命令。

![image-20220514115054508](redis.assets/image-20220514115054508.png)

## 事务的错误处理

* Multi中某个命令出现错误，在Exec时，所有的命令都会被取消

![image-20220514115800616](redis.assets/image-20220514115800616.png)

![image-20220514120344172](redis.assets/image-20220514120344172.png)

* 在Mulit时，所有命令正常，在Exec时，某个命令出现错误，只有出现错误的命令不会执行，其他命令正常执行，不会回滚

![image-20220514115956453](redis.assets/image-20220514115956453.png)

![image-20220514120310946](redis.assets/image-20220514120310946.png)

## 事务的冲突问题

![image-20220514121727522](redis.assets/image-20220514121727522.png)

### 悲观锁

* 悲观锁：每次去拿数据的时候都认为别人会对数据进行修改，因此在每次修改数据之前都会上锁，此时其他人不能再拿到数据，直到拿到数据的人修改后，锁会进行释放。
* 一次只能有一个人操作数据，效率低

![image-20220514122037848](redis.assets/image-20220514122037848.png)

### 乐观锁

* 乐观锁：每次有人拿数据时认为别人不会对进行数据修改，因此不会上锁，但是如果有数据修改，会判断在此期间其他人有没有去更新数据，可以使用版本号等控制。
* 多人可以同时操作数据，效率高

![image-20220514122446564](redis.assets/image-20220514122446564.png)

## WATCH key

* 在执行multi之前，先执行watch key1 key2 ... 可以监视一个或多个key，如果在事务执行之前这个或这些key被其他命令所改动，那么事务将被打断

![image-20220514132359496](redis.assets/image-20220514132359496.png)

![image-20220514132345782](redis.assets/image-20220514132345782.png)

* unwatch key1 key2...：取消对key1 key2...的监视

## redis中事务的三个特性

* 单独的隔离操作：事务中所有的命令都会序列化、按顺序地执行，事务在执行过程中，不会被其他客户端发送过来的请求所打断
* 没有隔离级别概念：队列中的命令没有提交之前都不会实际执行，因为事务提交前任何指令都不会被实际执行
* 不保证原子性：事务中有一条命令执行失败，其后的命令仍然会被执行，没有回滚

# 持久化操作

## RDB

* RDB：在指定的时间内将内存中的数据集快照写入磁盘

* RDB底层原理：

  * Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到 一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。 整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。***\*RDB的缺点是\*******\*最后一次持久化后的数据可能丢失\****。

  * **\*Fork\***

    Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等） 数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程

    在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，Linux中引入了“***\*写时复制技术\****”

    ***\*一般情况父进程和子进程会共用同一段物理内存\****，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。

* ***\*RDB持久化流程\****

![image-20220514163105718](redis.assets/image-20220514163105718.png)

### 配置文件相关内容

* ***\*dump.rdb文件\****

  ![image-20220514163612828](redis.assets/image-20220514163612828.png)

* stop-writes-on-bgsave-error yes

  ![image-20220514163803663](redis.assets/image-20220514163803663.png)

* ***\*rdbcompression\**** :yes，对redis储存到磁盘的快照进行压缩（lzf算法）

  ![image-20220514164059431](redis.assets/image-20220514164059431.png)

* ***\*rdbchecksum\**** ***\*检查完整性\****

  ![img](redis.assets/wps1.jpg) 

  在存储快照后，还可以让redis使用CRC64算法来进行数据校验，

  但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能

  推荐yes.

* save：
  * 以：save 20 3   为例
  * 在第一个20秒内，进行了三次set，第三次set时进行一次持久化操作，进行第四次操作、第五次操作都会进行持久化操作；第二个20秒开始后又从1开始计数，第三个set进行持久化操作.......

![image-20220514170137762](redis.assets/image-20220514170137762.png)

![image-20220514170954144](redis.assets/image-20220514170954144.png)