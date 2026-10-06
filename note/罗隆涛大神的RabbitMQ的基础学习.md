# RabbitMQ 学习笔记

## 一、MQ 基础概念

### 1. 一句话定义

**RabbitMQ** 是一个**基于 AMQP 协议、用 Erlang 编写的高性能消息中间件**，本质是「异步通讯组件」——消息发送方和接收方不用同时在线、不用互相等待。

### 2. 同步 vs 异步

| 维度 | 同步通讯（微信语音通话） | 异步通讯（微信文字消息） |
|---|---|---|
| 双方状态 | 必须同时在线 | 不需要，对方不在线也能送达 |
| 发送方体验 | 发起请求后阻塞等待对方回应 | 发完即走，不等待 |
| 耦合度 | 高，A 依赖 B 的即时响应 | 低，收发双方互不认识 |
| 高并发表现 | 大量请求同时阻塞，压力大 | 消息先堆积，系统按节奏处理 |

**同步调用的优势**：时效性强，得到结果才返回（立刻要结果的学习/查询场景用同步）。

**同步调用的问题**：扩展性差、性能下降、级联失败。

**异步调用的常见角色**：
- 消息发出者 → **Producer 生产者**
- 消息代理 → **Broker**
- 消息接受者 → **Consumer 消费者**

**异步的优势**：解除耦合、扩展性强；无需等待、性能好；故障隔离、缓存消息、流量削峰填谷。

**异步的问题**：不能立即得到结果；不能确定下游业务是否执行成功；业务安全依赖于 Broker 的可靠性。

### 3. RabbitMQ 的 5 个核心角色（必背）

| 角色 | 说明 | 类比 |
|---|---|---|
| **Producer 生产者** | 发送消息的一方 | 微信发消息的你 |
| **Exchange 交换机** | 收下消息，按「路由键」决定转发到哪个队列 | 邮局分拣台 |
| **Queue 队列** | 真正存消息的地方，先进先出（FIFO） | 邮箱 |
| **Consumer 消费者** | 从队列取消息处理的一方 | 收消息的好友 |
| **Binding 绑定** | 交换机和队列之间的绑定关系 | 分拣规则 |

**消息流转**：`生产者 → 交换机 →（按路由键匹配绑定）→ 队列 → 消费者`

### 4. 为什么用 RabbitMQ（三大价值）

1. **解耦**：生产者消费者互不感知，改一方不影响另一方
2. **异步**：请求发完就返回，用户体验和系统吞吐都提升
3. **削峰**：突发流量先堆积在队列，系统慢慢消费，防止被冲垮

---

## 二、四大消息中间件选型

| 维度 | RabbitMQ | ActiveMQ | RocketMQ | Kafka |
|---|---|---|---|---|
| 公司/社区 | Rabbit | Apache | 阿里 | Apache |
| 开发语言 | Erlang | Java | Java | Scala & Java |
| 协议支持 | AMQP、XMPP、SMTP、STOMP | OpenWire、STOMP、REST、XMPP、AMQP | 自定义协议 | 自定义协议 |
| 可用性 | 高 | 一般 | 高 | 高 |
| 单机吞吐量 | 一般 | 差 | 高 | 非常高 |
| 消息延迟 | 微秒级 | 毫秒级 | 毫秒级 | 毫秒以内 |
| 消息可靠性 | 高 | 一般 | 高 | 一般 |

**三句话抓住这张表：**

1. **RabbitMQ（正在学）**：可靠性和可用性都"高"、延迟微秒级、协议最标准（AMQP），功能均衡 + 易用，适合中小规模和高可靠性场景，也是**新手学习首选**——概念最正统，学会它再学别的 MQ 是降维打击。
2. **Kafka**：吞吐"非常高"、延迟"毫秒以内"，**海量日志/流数据之王**；代价是可靠性"一般"（默认配置可能丢消息，要靠副本和 ack 机制兜底）。
3. **RocketMQ**：阿里出品，**吞吐高 + 可靠性高**两头都硬，还支持事务消息，电商大厂用得最多。
4. **ActiveMQ**：老牌 Java 中间件，各维度"一般/差"，社区活跃度下降，**新项目基本不选**，只在老系统维护时遇到。

---

## 三、环境部署（Docker）

在虚拟机里使用 Docker 拉取镜像并运行：

```bash
docker pull rabbitmq:4.3-management

docker network create hmall        # 如果这个网络还没创建，先执行这行

docker run -d \
  -e RABBITMQ_DEFAULT_USER=pomian \
  -e RABBITMQ_DEFAULT_PASS=20070408llT \
  -v mq-plugins:/plugins \
  --name mq \
  --hostname mq \
  -p 15672:15672 \
  -p 5672:5672 \
  --network hmall \
  rabbitmq:4.3-management
```

| 项 | 说明 |
|---|---|
| 端口 5672 | 程序通讯（AMQP 协议） |
| 端口 15672 | 后台管理界面，浏览器访问 `http://虚拟机IP:15672` |

**VirtualHost（虚拟主机）**：因为 RabbitMQ 吞吐量大，可能多个服务共用一个 RabbitMQ。VirtualHost 包含一套业务独立的 exchange 和 queue，起到**数据隔离**的作用（本笔记使用 vhost `/pomian`）。

**核心认知**：
- 交换机负责路由转发，**本身不存储消息**；必须把交换机和队列**绑定（Binding）**起来
- AMQP（Advanced Message Queuing Protocol）→ **Spring AMQP** 就是 Java 客户端，引入依赖即可

---

## 四、Spring AMQP 基础使用

### 4.1 引入依赖

```xml
<!--AMQP依赖，包含RabbitMQ-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

### 4.2 配置文件（producer / consumer 各模块）

```yaml
spring:
  rabbitmq:
    host: 192.168.228.128
    port: 5672
    virtual-host: /pomian
    username: pomian
    password: 20070408llT
```

### 4.3 发送消息（RabbitTemplate）

```java
@SpringBootTest
public class SpringAMQPTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void testSend() {
        String queueName = "queue1";
        String msg = "Hello Spring MQ!";
        rabbitTemplate.convertAndSend(queueName, msg); // 走默认交换机，直接进队列
    }
}
```

> `convertAndSend(queueName, msg)` 两个参数时没有经过交换机路由（发到默认交换机，routingKey=队列名）；三个参数 `convertAndSend(exchange, routingKey, msg)` 才是显式指定交换机。

### 4.4 消费者监听

```java
@Component          // 必须交给 Spring 管理
public class Mqlistener {
    @RabbitListener(queues = "queue1")   // 消费者启动类需要 @EnableRabbit
    public void listenerQueue1(String msg) {
        System.out.println("消费者收到了queue1的消息:" + msg);
    }
}
```

> ⚠️ **实战坑**：消费者收不到消息，先检查 ①监听类有没有 `@Component` ②启动类有没有 `@EnableRabbit`。

### 4.5 工作队列（Work Queue）模式

多个消费者监听**同一个队列**：

- 可以加快消息的处理速度
- **同一个消息只会被一个消费者处理**（竞争消费）
- `prefetch`（预取数量）：默认 **250**，谁先注册谁先整批端走 250 条；设为 **1** 实现「能者多劳」——谁空闲谁拿下一条

```yaml
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1   # 默认 250
```

> 单消费者严格 FIFO；多消费者并行时，System.out 与 System.err 混排会造成"假乱序"。

---

## 五、交换机（Exchange）

| 类型 | 路由规则 | 场景 |
|---|---|---|
| **Fanout** | 广播：收到消息发给**所有**绑定的队列 | 广播通知 |
| **Direct** | 定向：`RoutingKey` 与队列的 `BindingKey` **完全相等**才投递 | 定向路由 |
| **Topic** | 模糊匹配：RoutingKey 是 `word.word` 形式，支持通配符 | 按主题订阅 |

**Topic 通配符**：
- `#`：0 个或多个单词（`china.#` 匹配 `china.weather`、`china`）
- `*`：1 个单词（`china.*` 匹配 `china.weather`，不匹配 `china.weather.hot`）

**Direct 细节**：
- 每个队列可以绑定多个 `BindingKey`
- 多个队列绑定了同一个 Key 时，效果就相当于 Fanout

**生产者发送示例：**

```java
// Fanout：routingKey 传 null 即可
rabbitTemplate.convertAndSend("pomian.fanout", null, "hello fanout!");

// Direct：routingKey 精确匹配
rabbitTemplate.convertAndSend("pomian.direct", "blue", "hello direct!");

// Topic：支持通配符
rabbitTemplate.convertAndSend("pomian.topic", "china.weather", "hello topic!");
```

---

## 六、在 Java 中声明队列和交换机

### 方式一：@Bean 声明（Config 类，正式项目推荐）

```java
@Configuration
public class DirectConfiguration {
    @Bean
    public DirectExchange directExchange() {
        return new DirectExchange("pomian.direct");
    }

    @Bean
    public Queue directQueue1() {
        return new Queue("direct.queue1", true);
    }

    @Bean
    public Binding bindingRed() {
        return BindingBuilder.bind(directQueue1()).to(directExchange()).with("red");
    }
}
```

> 要绑定多个 RoutingKey 就要写多个 Binding Bean，比较麻烦。

### 方式二：注解声明（在 @RabbitListener 里，快速演示）

```java
@RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "direct.queue1", durable = "true"),
        exchange = @Exchange(name = "pomian.direct", type = ExchangeTypes.DIRECT),
        key = {"red", "blue"}   // 一个队列绑定多个 key
))
public void listenerDirectQueue1(String msg) {
    System.out.println("消费者1 收到了direct.queue1的消息:" + msg);
}
```

---

## 七、消息转换器（Jackson 3）

引入依赖，用 JSON 序列化替代默认的 JDK 序列化：

```xml
<!--Jackson 3，Spring Boot 4 主推版本（原 Jackson2 已弃用）-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jackson</artifactId>
</dependency>
```

> ⚠️ **版本差异**：Spring AMQP 4.0 起 `Jackson2JsonMessageConverter`（Jackson 2 时代）已弃用，改用 **Jackson 3 的 `JacksonJsonMessageConverter`**。

配置（放到 Config 类，而不是启动类）：

```java
@Configuration
public class MqJacksonConfig {
    @Bean
    public MessageConverter messageConverter() {
        return new JacksonJsonMessageConverter();
    }
}
```

> producer / consumer **两端都要配置**同一个转换器，否则发的 JSON 消费者解析不了。依赖树里应出现 `tools.jackson.core:jackson-databind:3.x`（不是 `com.fasterxml.jackson`）。

**发送对象消息：**

```java
@Test
public void testSendObject() {
    Map<String, Object> msg = new HashMap<>();
    msg.put("name", "jack");
    msg.put("age", 18);
    rabbitTemplate.convertAndSend("object.queue", msg);
}
```

> ⚠️ **实战坑**：监听方法签名写成 `Map<String, Objects>`（java.util.Objects 工具类）会导致反序列化失败、消息被 reject 丢弃；必须写 `Map<String, Object>`。

---

## 八、消息可靠性总览

消息可能丢的三个环节：

1. **消息发送的时候丢了** → 生产者可靠性（重连 + 确认）
2. **MQ 把消息弄丢了** → MQ 可靠性（持久化）
3. **消费者把消息弄丢了** → 消费者可靠性（确认 + 重试）

另外还有：**延迟消息**。

---

## 九、生产者可靠性

### 9.1 生产者重连（网络不好时）

```yaml
spring:
  rabbitmq:
    connect-timeout: 1s   # 设置连接超时
    template:
      retry:
        enabled: true          # 开启超时重试机制
        initial-interval: 1000ms  # 失败后的初始等待时间
        multiplier: 1            # 失败后下次等待时长的倍数
        max-attempts: 3
```

> ⚠️ 重连是**阻塞式**的，对业务性能有要求的场景**要禁用重试机制**。

### 9.2 生产者确认（Publisher Confirm / Return）

MQ 有两种确认机制：**producer confirm** 和 **producer return**。

| 机制 | 触发时机 | 结论 |
|---|---|---|
| **confirm** | 消息到达交换机 | 异步回调，`ack=true` 到达，`ack=false` 拒收 |
| **return** | 消息到达交换机但**路由失败**（找不到匹配队列） | 返回路由异常；一般不写，出问题靠 confirm 兜底 |

**各种情况对应的确认结果：**

| 情况 | 返回 |
|---|---|
| 消息投递到 MQ 且路由成功 | ACK |
| 临时消息入队成功 | ACK |
| 持久消息入队完成持久化 | ACK |
| 路由失败（return 兜底） | 先返回 return，再 ACK |
| 其他情况 | NACK |

**开启确认配置：**

```yaml
spring:
  rabbitmq:
    publisher-confirm-type: correlated   # 异步回调方式，看消息有没有到达交换机
    publisher-returns: true              # 开启路由失败退回
```

> `confirm ack=true` **只代表到达交换机，不代表到达队列**——路由失败要 return 兜底。

**4.x 全局回调写法（放 Config 类，一次注册所有消息共用）：**

```java
@Slf4j
@Configuration
public class MqConfirmConfig implements ApplicationContextAware {
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        RabbitTemplate rabbitmq = applicationContext.getBean(RabbitTemplate.class);
        rabbitmq.setConfirmCallback(new RabbitTemplate.ConfirmCallback() {
            @Override
            public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                if (ack) {
                    log.info("消息确认到达交换机, id={}", correlationData.getId());
                } else {
                    log.error("消息被拒收, id={}, 原因: {}", correlationData.getId(), cause);
                }
            }
        });
    }
}
```

**Return 回调（路由失败时）：**

```java
@Slf4j
@Configuration
public class MqReturnConfig implements ApplicationContextAware {
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        RabbitTemplate rabbitmq = applicationContext.getBean(RabbitTemplate.class);
        rabbitmq.setReturnsCallback(new RabbitTemplate.ReturnsCallback() {
            @Override
            public void returnedMessage(ReturnedMessage returned) {
                log.info("return callback, exchange:{}, key:{}, code:{}, text:{}",
                        returned.getExchange(), returned.getRoutingKey(),
                        returned.getReplyCode(), returned.getReplyText());
            }
        });
    }
}
```

**测试时发送（只传 cd，回调全局生效）：**

```java
@Test
public void testConfirmCallback() throws InterruptedException {
    CorrelationData cd = new CorrelationData(UUID.randomUUID().toString());
    rabbitTemplate.convertAndSend("pomian.direct", "red", "hello direct!", cd);
    Thread.sleep(2000); // 等异步回调完成
}
```

> ⚠️ **4.x API 差异**：`CorrelationData.getFuture()` 返回 `CompletableFuture<Confirm>`，旧版 `addCallback` 已移除，用 `whenComplete`；`Confirm` 从类改为 Record，用 `ack()` / `reason()`（`isAck()` / `getReason()` 已弃用）。
> **全局 vs 单条**：配置类里注册的是全局回调（所有消息共用）；`cd.getFuture().whenComplete(...)` 是单条回调，只能写在"创建 cd 的地方"（测试方法/业务代码），因为配置类执行时还没有 cd。
> 追求性能可以不启用确认，会降低发送消息的性能。

---

## 十、MQ 可靠性（持久化）

### 10.1 为什么需要持久化

- MQ 宕机时内存中的消息会丢失
- 内存有限，消费者处理过慢会引发 MQ 阻塞
- 内存满、数据从内存转到磁盘时 MQ 是阻塞的

### 10.2 三个持久（都要持久才行）

| 层 | 持久化开关 | 说明 |
|---|---|---|
| 交换机 | `durable=true` | 重启后交换机还在 |
| 队列 | `durable=true` | 重启后队列还在 |
| 消息 | `deliveryMode=2`（PERSISTENT） | 重启后消息从磁盘恢复 |

**Spring AMQP 默认都开了**：
- 队列：`@RabbitListener` / `QueueBuilder` 声明时默认 `durable=true`
- 消息：`MessageProperties.DEFAULT_DELIVERY_MODE = PERSISTENT`（每条消息默认 deliveryMode=2）

> 所以用 Java 发消息，重启 RabbitMQ 后队列和消息都还在——不需要额外配置。

### 10.3 RabbitMQ 4.x 存储机制（与旧版的关键差异）

| | RabbitMQ 3.x | RabbitMQ 4.x（本项目 4.3） |
|---|---|---|
| transient（非持久）消息 | 只在内存，内存压力才写盘 | **正常也写盘**（存储方式和持久化消息一样） |
| 重启后 transient 消息 | 被丢弃 | **依然被丢弃** |
| 重启后 persistent 消息 | 恢复 | 恢复 |
| lazy queue 概念 | 存在（3.12 前） | **已移除**（所有 classic queue 默认磁盘优先） |

**必须记住的精确规则**：
- **落盘 ≠ 持久**：transient 消息在 4.x 也落盘，但**跨重启只有 persistent 能恢复**
- 4.x 里 persistent vs transient 的唯一区别是 **publisher confirm 发送时机**（持久=落盘后确认，非持久=入队即确认）
- 消息存储是**磁盘（持久层）+ 内存（缓存层）双层**：正常运行时内存里有副本，磁盘上始终有备份，内存压力时逐出内存副本

**管理后台指标含义：**

| 字段 | 含义 |
|---|---|
| Total / Ready / Unacked | 消息总数 / 就绪 / 未确认 |
| **In memory** | 当前在 **RAM** 里的消息数 |
| **Persistent** | 发布时标记为持久化（deliveryMode=2）的消息数 |
| 在磁盘的条数 | `Total - In memory`（跟 Persistent 无关） |

> ⚠️ **常见误区**：`Persistent` 是消息的**身份属性**（发布时决定的），不是"在磁盘的数量"；持久化消息也可以在内存，transient 消息也可以在磁盘。

**管理后台手动发消息**：Publish message 面板默认 `Delivery mode: 1 - Non-persistent`，用它发的消息**重启后会被丢弃**；改成 `2 - Persistent` 才不丢。这也解释了"后台发的重启没了、Java 发的重启还在"。

> 4.x 里非持久化消息也占磁盘，磁盘清理靠 **TTL（消息过期）** 或**队列长度限制**。

---

## 十一、消费者可靠性

### 11.1 消费者确认机制（Consumer Acknowledgement）

消费者处理完消息后给 RabbitMQ 返回确认：

| 动作 | 含义 | 结果 |
|---|---|---|
| **ack** | 成功处理消息 | 队列删除消息 |
| **nack** | 消息处理失败 | 队列**再次投递**消息（重投） |
| **reject** | 处理失败并拒绝 | 队列删除消息（或进死信） |

**开启确认模式：**

```yaml
spring:
  rabbitmq:
    listener:
      simple:
        acknowledge-mode: auto   # 开启消费者确认模式
```

### 11.2 消息失败处理：重试机制

```yaml
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1
        acknowledge-mode: auto
        retry:
          enabled: true          # 开启重试机制（注意：是 enabled，不是 enable）
          initial-interval: 1000ms
          multiplier: 1
          max-attempts: 2
          stateless: true        # true无状态；false有状态，如果有事务改为false
```

> ⚠️ **YAML 结构坑**：`retry` 必须缩进在 `simple:` **里面**（`spring.rabbitmq.listener.simple.retry.*`），写在外面平级不生效；且属性名是 `enabled` 不是 `enable`。
> ⚠️ **概念区分**：这个 retry 是"消息**消费失败**重试"，不是"连接重连"。连接重连（broker 断线后重连）**默认无限重试、没有最大次数配置**，这是设计行为——消费者就该一直等 broker 恢复。

### 11.3 重试耗尽后的处理（MessageRecoverer）

| 模式 | 行为 |
|---|---|
| **Reject**（默认） | 重试耗尽后直接 reject，丢弃消息 |
| **Immediate** | 重试耗尽后返回 nack，消息重新入队 |
| **Republish** | 重试耗尽后将消息投递到指定的交换机（配合死信） |

> 重试耗尽且没有配置 recoverer 时，抛异常的消息默认被 requeue 回队列，会形成"投递→异常→requeue→再投递"的死循环，日志看起来像"无限重连"。

---

## 十二、业务幂等性

**幂等定义**：`f(x) = f(f(x))`——一个业务执行一次和执行多次，对业务状态的影响一致。

| 操作 | 幂等？ | 说明 |
|---|---|---|
| 查询（SELECT） | ✅ 天生幂等 | 只读不改数据 |
| 按主键删除 | ⚠️ 业务效果幂等 | 重复删结果都是"不存在"，但影响行数不同（1 vs 0） |
| 覆盖式更新（SET name='x'） | ✅ 幂等 | 重复设同一值结果一样 |
| 新增（INSERT） | ❌ 不幂等 | 插两次=两条数据 |
| 计数更新（count=count+1） | ❌ 不幂等 | 重放一次多加一次 |

**非幂等业务示例**：用户下单扣库存、退款恢复余额。

**方案一：给每条消息加唯一 ID**
- 在 `JacksonJsonMessageConverter` 设置 message id
- 业务方保存已处理 id，不存在则接收处理，存在则跳过

**方案二：根据业务本身判断**
- 例如订单状态机：只有"待支付"状态能执行"取消"，已取消则直接跳过

---

## 十三、延迟消息（延迟任务）

适合延迟时间较短的消息。

### 13.1 死信（Dead Letter）

**死信 = 消息无法被正常消费、被"处决"的消息**。出现死信的几种情况：

1. 消费者 `basic.reject` 或 `basic.nack` 且 `requeue=false`
2. 消息过期（超过队列或消息设置的 TTL）且无人消费
3. 队列堆积满，最早的消息可能成为死信

队列通过 `x-dead-letter-exchange` 属性绑定一个交换机，队列中的死信会交到这个交换机——这个交换机就叫**死信交换机（DLX）**。

### 13.2 TTL + DLX 实现延迟消息（不需要插件）

```
producer → exchange → delay.queue(无消费者, 消息TTL超时)
                         ↓ 消息过期
                     dead exchange（死信交换机）
                         ↓ 按路由键转发
                      dead.queue → consumer（真正消费）
```

**每个组件的角色：**

| 组件 | 作用 |
|---|---|
| exchange | 普通交换机，把消息路由进延迟队列 |
| delay.queue | 延迟队列：**没有消费者**，消息进来后开始 TTL 倒计时 |
| dead exchange | 死信交换机：消息过期后转到这里 |
| dead.queue | 死信队列：**消费者真正监听的地方** |

**关键点**：延迟配置只加在 delay.queue 这一个队列上，其他业务队列零改动；TTL 从消息**入队那一刻**开始计时。

### 13.3 Java 绑定死信交换机

**注解式（Spring AMQP 4.x 写法）：**

> ⚠️ 4.x 里 `@Queue` 的 `arguments` 是 **`@Argument[]`**（旧版 `String[]` 的 `"key=value"` 写法已废弃）。

```java
@RabbitListener(bindings = @QueueBinding(
        value = @Queue(
                name = "delay.queue",
                durable = "true",
                arguments = {
                        @Argument(name = "x-message-ttl", value = "10000", type = "java.lang.Integer"), // 10秒过期
                        @Argument(name = "x-dead-letter-exchange", value = "pomian.dead"),            // 死信交换机
                        @Argument(name = "x-dead-letter-routing-key", value = "dead.queue")            // 死信路由键
                }
        ),
        exchange = @Exchange(name = "pomian.normal", type = ExchangeTypes.DIRECT),
        key = "delay.queue"
))
public void listenerDelay(String msg) {
    System.out.println("收到延迟消息: " + msg);
}
```

**Config 类式（更清晰，正式项目推荐）：**

```java
@Configuration
public class DelayConfiguration {
    // 延迟队列：TTL + 死信交换机参数
    @Bean
    public Queue delayQueue() {
        Map<String, Object> args = new HashMap<>();
        args.put("x-message-ttl", 10000);
        args.put("x-dead-letter-exchange", "pomian.dead");
        args.put("x-dead-letter-routing-key", "dead.queue");
        return new Queue("delay.queue", true, false, false, args);
    }

    // 死信交换机 + 死信队列
    @Bean
    public DirectExchange deadExchange() {
        return new DirectExchange("pomian.dead");
    }

    @Bean
    public Queue deadQueue() {
        return new Queue("dead.queue", true);
    }

    @Bean
    public Binding deadBinding() {
        return BindingBuilder.bind(deadQueue()).to(deadExchange()).with("dead.queue");
    }
}
```

**三个参数对应关系：**

| @Argument | 作用 | 注意 |
|---|---|---|
| `x-message-ttl` | 消息存活时间（毫秒） | 数值，需 `type = "java.lang.Integer"` |
| `x-dead-letter-exchange` | 过期后投给哪个交换机 | 字符串 |
| `x-dead-letter-routing-key` | 投递给死信交换机用的路由键 | 必须和死信队列绑定键一致 |

### 13.4 应用场景：订单支付超时自动取消

```
下单业务：开始 → 创建订单 → 发送延迟消息 → 结束

延迟消息处理：
  收到延迟消息 → 查询支付状态 → 是否已支付？
    ├─ 是 → 标记为已支付 → 结束
    └─ 否 → 获取下次延迟时间 → 是否有延迟时间？
          ├─ 是 → 重发延迟消息（进入下一个TTL周期）→ 回到"查询支付状态"
          └─ 否 → 取消订单 → 恢复库存 → 结束
```

核心是**延迟轮询**：不是超时一次就砍单，而是多次延迟检查给用户缓冲（可能正在支付），超时才兜底取消。每次"重发延迟消息"就是消费者自己再往 delay.queue 发一条新的。

### 13.5 延迟消息插件（x-delayed-message 交换机）

**原理**：对交换机做改造，消息发到交换机时在交换机内暂存一段时间，再投递到队列。

- 地址：`https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases`
- 通过延迟插件，交换机类型选 `x-delayed-message`、`delayed` 属性设为 `true`
- 发消息时**不要用 setExpire，要用 setDelay**

> ⚠️ **版本问题（本项目实测）**：插件 4.2.0 只支持 RabbitMQ 4.2.x；broker 是 4.3.6 时启用报 `Plugin doesn't support current server version`。选择：①换 rabbitmq:4.2-management 镜像；②改 .ez 里 .app 的版本声明强制装（不保证）；③**不装插件**——上面的 TTL+DLX 方案完全不需要插件。

> 延迟时间很长时（如 30min），可以拆成多个延迟消息（10ms、10ms、1min...）分段投递，降低对 MQ 的压力。

---

## 十四、开发高频避坑汇总（实战）

### 编译 / 测试
1. IDEA 的 `Ctrl+F9` 增量编译**可能漏编测试类**，跑的是旧产物——优先用命令行：`cd D:\java_code\mq-demo; mvn -pl producer clean test -Dtest=SpringAMQPTest`
2. 命令行 Maven 与 IDEA 抢同一 `target/` 会加剧问题，**固定用一种编译工具**
3. 本项目必须用本地 Maven（`E:\Maven\apache-maven-3.9.16`），IDEA 默认"Maven 包装器"不可用
4. `pom.xml` 的 `java.version` 必须 ≥17（本项目 21），写死 Java 8 会报 `ClassSelector resolution failed`

### Spring AMQP 4.x API 变化（网上旧教程都不适用）
5. `CorrelationData.addCallback()` 已移除 → `getFuture().whenComplete(...)`
6. `Confirm.isAck()/getReason()` 弃用 → `ack()/reason()`
7. `Jackson2JsonMessageConverter` 弃用 → `JacksonJsonMessageConverter`（Jackson 3）
8. `@Queue` 的 `arguments` 从 `String[]`（`"k=v"`）→ **`@Argument[]`**
9. `RabbitProperties` 在 `spring-boot-amqp` 的 `org.springframework.boot.amqp.autoconfigure` 包（包名变了）

### 配置拼写（都是踩过的坑）
10. `acknowledge-mode: auto`（不是 `ackownledge-mode`）
11. `retry.enabled`（不是 `enable`），且必须缩进在 `listener.simple.retry.*`
12. `docker exec mq rabbitmq-plugins enable ...`（是 `plugins`，不是 `pluings`）
13. `spring.rabbitmq.listener.simple.retry.*` 管"消费重试"；连接重连默认无限、无配置项

### 现象排查
14. 消费者收不到消息 → 查 `@Component` + 启动类 `@EnableRabbit`
15. 消费端反序列化失败 → 检查监听方法签名（`Map<String, Object>` 不是 `Map<String, Objects>`），且两端 MessageConverter 一致
16. 多消费者消息被一个全拿走 → 默认 `prefetch=250` 谁先注册谁整批端走；要能者多劳设 `prefetch: 1`
17. 后台手动发消息重启后没了 → 面板默认 `Delivery mode: 1`（非持久）；Java 发消息默认 PERSISTENT，重启还在
18. 浏览器打不开管理后台 → 依次查：`docker ps -a` 看 mq 是否 Up → `docker ps | grep mq` 看端口映射 → `Test-NetConnection 虚拟机IP -Port 15672`
19. 重试耗尽还一直刷异常日志 → 那是消息 requeue 死循环，不是连接重连
