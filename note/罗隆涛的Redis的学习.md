# Redis 学习笔记

---

## 一、初识 Redis

### 1. 认识 NoSQL

NoSQL（Not Only SQL），泛指非关系型数据库。

**SQL vs NoSQL 对比**

| 对比维度 | SQL（关系型） | NoSQL（非关系型） |
| --- | --- | --- |
| 数据结构 | 结构化，基于表/行/列，有严格约束 | 约束较弱，格式灵活 |
| 数据关联 | 表与表通过外键关联，关联强 | 多采用 JSON / KV 格式，关联弱 |
| 查询语言 | 统一的 SQL 语法 | 各数据库语法不同（Redis、MongoDB 各异） |
| 事务特性 | 支持 ACID 强事务 | 遵循 BASE 理论（基本可用、软状态、最终一致） |
| 存储位置 | 主要存磁盘，IO 速度较慢 | 主要存内存，读写速度快 |
| 扩展性 | 纵向扩展为主，横向扩展困难 | 天然支持分布式，横向扩展容易 |

**NoSQL 四大分类**

| 分类 | 特点 | 代表产品 |
| --- | --- | --- |
| 键值型（KV） | 基于 key-value，读写极快 | Redis、Memcached |
| 文档型 | 存储 JSON/BSON 文档，结构灵活 | MongoDB、CouchDB |
| 列存储型 | 按列存储，适合海量数据分布式 | HBase、Cassandra |
| 图数据库 | 存储节点和关系，适合社交/推荐 | Neo4j、JanusGraph |

---

### 2. 认识 Redis

- **全称**：Remote Dictionary Server（远程词典服务器）
- **本质**：基于内存的键值型（key-value）NoSQL 数据库

**核心特征**

| 特征 | 说明 |
| --- | --- |
| 键值型存储 | key 一般为字符串，value 支持多种数据类型 |
| 单线程模型 | 每个命令具有原子性（6.0 后多线程仅用于网络 IO，命令执行仍单线程） |
| 低延迟、速度快 | 基于内存 + IO 多路复用 |
| 持久化 | 支持将内存数据持久化到磁盘（RDB / AOF） |
| 高可用 | 支持主从复制、哨兵、集群（后续学习） |

**典型应用场景**：缓存、分布式锁、计数器、排行榜、会话存储、消息队列等。

---

### 3. 安装 Redis（Linux 源码编译）

Redis 基于 C 语言编写，需要 gcc 编译环境。

**步骤一：安装依赖**

```
dnf -y install gcc tcl
```

**步骤二：下载并解压**

```
# 下载地址：https://download.redis.io/releases/redis-7.4.1.tar.gz
# Windows 下载后拖拽到 /usr/local/src，然后：
cd /usr/local/src
tar -zxvf redis-7.4.1.tar.gz
```

**步骤三：编译安装**

```
cd redis-7.4.1
make && make install
```

安装完成后，可执行文件位于 `/usr/local/bin/`：

- `redis-server`：服务端启动程序
- `redis-cli`：命令行客户端

**步骤四：修改配置文件**

```
cp redis.conf /etc/redis.conf   # 备份并放到 /etc 下
vim /etc/redis.conf
```

关键配置项：

| 配置项 | 推荐值 | 说明 |
| --- | --- | --- |
| `bind` | `0.0.0.0` | 允许所有 IP 访问（默认仅 127.0.0.1） |
| `daemonize` | `yes` | 后台守护进程方式运行 |
| `requirepass` | 你的密码 | 设置访问密码 |
| `logfile` | 日志文件路径 | 指定日志输出位置 |
| `dir` | 数据目录 | 持久化文件存放目录 |

> 
> vim 小技巧：命令模式下按 `/` 输入关键词可快速搜索定位。

**步骤五：配置 systemd 服务（开机自启）**

```
vi /etc/systemd/system/redis.service
```

写入：

```
[Unit]
Description=Redis Server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /etc/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
```

启动并设置开机自启：

```
systemctl daemon-reload    # 重新加载服务配置
systemctl start redis      # 启动 Redis
systemctl status redis     # 查看运行状态
systemctl enable redis     # 设置开机自启
```

**步骤六：放行防火墙端口**

```
firewall-cmd --zone=public --add-port=6379/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-ports   # 查看已放行端口
```

> 
> Redis 默认端口号：**6379**

---

### 4. Redis 客户端

**客户端类型**

| 类型 | 说明 | 代表 |
| --- | --- | --- |
| 命令行客户端 | 终端操作 | redis-cli |
| 图形化客户端 | 可视化界面 | RedisDesktopManager（RDM）、Another Redis Desktop Manager |
| 编程客户端 | 程序中通过 API 操作 | Jedis、Lettuce、Redisson |

**命令行客户端 redis-cli**

```
redis-cli [options]
```

| 参数 | 说明 | 默认值 |
| --- | --- | --- |
| `-h` | 指定服务器 IP | 127.0.0.1 |
| `-p` | 指定端口号 | 6379 |
| `-a` | 指定密码 | 无 |

示例：

```
redis-cli -h 192.168.1.100 -p 6379 -a yourpassword
```

**图形化客户端下载**：`https://github.com/lework/RedisDesktopManager-Windows/releases`

---

### 5. Redis 数据结构

#### 5.1 通用命令（key 操作）

通过 `help @generic` 可查看所有通用命令。

| 命令 | 语法 | 说明 |
| --- | --- | --- |
| `keys` | `keys pattern` | 查看符合模式的所有 key（**生产环境慎用，会阻塞主线程**） |
| `del` | `del key [key ...]` | 删除指定 key，支持批量 |
| `exists` | `exists key` | 判断 key 是否存在，返回 1/0 |
| `expire` | `expire key seconds` | 设置 key 存活时间（秒） |
| `ttl` | `ttl key` | 查看剩余有效期，-1 表示永久，-2 表示不存在 |
| `type` | `type key` | 查看 key 对应 value 的类型 |

> 
> 小技巧：`help [command]` 可查看某个命令的具体用法。

#### 5.2 key 的层级结构

Redis 的 key 支持多级结构，用 `:` 分隔，形成类似目录的层级：

```
项目名:业务名:id
例如：yangli:user:1
```

便于在图形化客户端中按目录树展示和管理。

#### 5.3 String 类型

最基础的类型，value 为字符串。

| 命令 | 语法 | 说明 |
| --- | --- | --- |
| `set` | `set key value` | 存或修改一个键值对 |
| `get` | `get key` | 获取 key 对应的 value |
| `mset` | `mset k1 v1 k2 v2 ...` | 批量添加 |
| `mget` | `mget k1 k2 ...` | 批量获取 |
| `incr` | `incr key` | 整数 value 自增 1 |
| `incrby` | `incrby key 步长` | 整数 value 按步长自增 |
| `incrbyfloat` | `incrbyfloat key 增量` | 浮点型 value 按增量增长 |
| `setnx` | `setnx key value` | key 不存在时才新增（等价 `set key value nx`） |
| `setex` | `setex key seconds value` | 设值同时设过期时间（等价 `set key value ex seconds`） |

**应用场景**：缓存对象、计数器、分布式锁、存储 session。

#### 5.4 Hash 类型

类似 Java 的 HashMap，value 内部又是一组 field-value 键值对。

| 命令 | 语法 | 说明 |
| --- | --- | --- |
| `hset` | `hset key field value` | 存或修改 field |
| `hget` | `hget key field` | 获取指定 field 的值 |
| `hmset` | `hmset key f1 v1 f2 v2 ...` | 批量设置 field |
| `hmget` | `hmget key f1 f2 ...` | 批量获取 field |
| `hgetall` | `hgetall key` | 获取所有 field-value |
| `hkeys` | `hkeys key` | 获取所有 field |
| `hvals` | `hvals key` | 获取所有 value |
| `hincrby` | `hincrby key field 步长` | 指定 field 自增 |
| `hsetnx` | `hsetnx key field value` | field 不存在时才设置 |

**应用场景**：存储对象（用户信息、商品信息），比 String 序列化更省空间，支持单字段修改。

#### 5.5 List 类型

类似 Java 的 LinkedList，可看作双向链表，支持左右两端操作。

| 命令 | 语法 | 说明 |
| --- | --- | --- |
| `lpush` | `lpush key element [element ...]` | 向左（头部）添加元素 |
| `rpush` | `rpush key element [element ...]` | 向右（尾部）添加元素 |
| `lpop` | `lpop key [count]` | 从左侧弹出元素，有返回值 |
| `rpop` | `rpop key [count]` | 从右侧弹出元素 |
| `lrange` | `lrange key start end` | 查看指定范围元素，下标从 0 开始 |
| `blpop` | `blpop key seconds` | 阻塞式左侧弹出，可实现消息队列 |
| `brpop` | `brpop key seconds` | 阻塞式右侧弹出 |

**应用场景**：消息队列、最新列表、栈/队列结构。

#### 5.6 Set 类型

类似 Java 的 HashSet，无序、不可重复。

**单集合操作**

| 命令 | 语法 | 说明 |
| --- | --- | --- |
| `sadd` | `sadd key member [member ...]` | 添加元素 |
| `srem` | `srem key member [member ...]` | 删除元素 |
| `scard` | `scard key` | 查看元素个数 |
| `sismember` | `sismember key member` | 判断元素是否在集合中 |
| `smembers` | `smembers key` | 获取所有元素 |

**集合运算**

| 命令 | 语法 | 说明 |
| --- | --- | --- |
| `sinter` | `sinter key1 key2` | 求交集 |
| `sdiff` | `sdiff key1 key2` | 求差集（key1 有但 key2 没有） |
| `sunion` | `sunion key1 key2` | 求并集 |

**应用场景**：标签系统、共同好友、抽奖去重、点赞/收藏。

#### 5.7 SortedSet（ZSet）类型

类似 Java 的 TreeSet，但底层是 **SkipList + Hash 表**。每个元素带一个 score 用于排序，默认升序。

| 命令 | 语法 | 说明 |
| --- | --- | --- |
| `zadd` | `zadd key score member [score member ...]` | 添加元素，可批量 |
| `zrem` | `zrem key member [member ...]` | 删除元素，可批量 |
| `zrank` | `zrank key member` | 查看排名（升序，从 0 开始） |
| `zrevrank` | `zrevrank key member` | 查看排名（降序） |
| `zcard` | `zcard key` | 获取元素个数 |
| `zcount` | `zcount key min max` | 统计 score 在范围内的元素个数 |
| `zincrby` | `zincrby key 增量 member` | 指定元素的 score 自增 |
| `zrange` | `zrange key start end` | 按排名升序查看范围元素 |
| `zrevrange` | `zrevrange key start end` | 按排名降序查看 |
| `zrangebyscore` | `zrangebyscore key min max` | 按 score 范围查看元素 |

> 
> 记忆技巧：命令中带 `rev` 的表示 reverse（反转/降序）。

**应用场景**：排行榜（游戏积分、热搜榜）、延时队列、按权重排序。

#### 5.8 五大数据类型总结

| 类型 | 底层结构 | 特点 | 典型应用 |
| --- | --- | --- | --- |
| String | 简单动态字符串（SDS） | 最基础，可存字符串/数字/二进制 | 缓存、计数器、分布式锁 |
| Hash | 哈希表 | field-value 结构，适合存对象 | 用户信息、商品详情 |
| List | 双向链表（quicklist） | 有序、可重复、两端操作快 | 消息队列、最新列表 |
| Set | 哈希表 | 无序、不可重复、支持集合运算 | 标签、共同好友、去重 |
| SortedSet | SkipList + Hash | 有序、不可重复、按 score 排序 | 排行榜、延时队列 |

---

### 6. Redis 的 Java 客户端

#### 6.1 主流客户端对比

| 客户端 | 特点 | 线程安全 | 适用场景 |
| --- | --- | --- | --- |
| **Jedis** | 方法名与 Redis 命令一致，简单直接，上手快 | 线程不安全，多线程需用连接池 | 简单项目、学习使用 |
| **Lettuce** | 底层基于 Netty NIO，非阻塞，支持异步 | 线程安全 | Spring Boot 默认客户端 |
| **Redisson** | 高级分布式工具，主打分布式锁和分布式数据结构 | 线程安全 | 分布式锁、分布式集合、微服务 |

#### 6.2 Jedis 基本使用

1. 引入 Maven 依赖：

```
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>5.x.x</version>
</dependency>
```

2. 建立连接（线程不安全，推荐连接池）：

```
// 直接连接（不推荐多线程使用）
Jedis jedis = new Jedis("127.0.0.1", 6379);
jedis.auth("yourpassword");

// 连接池方式（推荐）
JedisPool pool = new JedisPool("127.0.0.1", 6379);
try (Jedis jedis = pool.getResource()) {
    jedis.auth("yourpassword");
    jedis.set("key", "value");
}
```

> 
> Jedis 官网：[https://github.com/redis/jedis](https://github.com/redis/jedis)

#### 6.3 SpringDataRedis

Spring Data Redis 提供 `RedisTemplate` 统一 API，屏蔽底层客户端差异。

**opsForXxx 方法对应关系**

| API | 返回值类型 | 操作类型 |
| --- | --- | --- |
| `redisTemplate.opsForValue()` | ValueOperations | String 字符串类型 |
| `redisTemplate.opsForHash()` | HashOperations | Hash 哈希类型 |
| `redisTemplate.opsForList()` | ListOperations | List 列表类型 |
| `redisTemplate.opsForSet()` | SetOperations | Set 集合类型 |
| `redisTemplate.opsForZSet()` | ZSetOperations | SortedSet 有序集合 |
| `redisTemplate`（直接调用） | — | 通用命令：expire、delete、hasKey 等 |

**快速开始**

1. 创建 Spring Boot 项目，勾选：
   - Lombok
   - NoSQL → Spring Data Redis（Access + Driver）
2. 引入依赖：

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

3. 配置文件 `application.yml`：

> 
> **重要坑点**：Spring Boot 2.x 用 `spring.redis` 前缀，**Spring Boot 3.x 起改为 `spring.data.redis`**。用错前缀会导致配置完全失效，Lettuce 默认连接 `localhost:6379`。

```
# Spring Boot 3.x 配置
spring:
  data:
    redis:
      host: 127.0.0.1
      port: 6379
      password: yourpassword
      database: 0
      lettuce:
        pool:
          max-active: 8
          max-idle: 8
          min-idle: 0
          max-wait: 100ms
```

**序列化问题与三种方案对比**

| 方案 | 说明 | 优缺点 |
| --- | --- | --- |
| 默认 RedisTemplate | 使用 JDK 序列化 | key/value 变乱码字节，value 多存类名，浪费空间，可读性差 |
| 自定义 RedisTemplate | key 用 String 序列化，value 用 JSON 序列化 | 可读性好，但 value 存对象时带 `@class` 类型信息，仍占额外空间 |
| StringRedisTemplate | key/value 都用 String 序列化，手动用 ObjectMapper 做 JSON 转换 | 最省空间，格式最干净，**推荐使用** |

**StringRedisTemplate 推荐用法**：

```
@Autowired
private StringRedisTemplate stringRedisTemplate;

@Autowired
private ObjectMapper objectMapper;

// 存对象
User user = new User("张三", 20);
String json = objectMapper.writeValueAsString(user);
stringRedisTemplate.opsForValue().set("user:1", json);

// 取对象
String json = stringRedisTemplate.opsForValue().get("user:1");
User user = objectMapper.readValue(json, User.class);
```

