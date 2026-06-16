# 📅 复习计划与进度 (Study Todo List - Detailed)

> **当前进度**：Day 40 (03-25)  
> **复习重点**：Java 基础、并发、JVM、MySQL、Redis、计网、OS、算法、项目、**面试 STAR 法则**
> **今日完成**：9大主题面试问答 + summarize 技能测试

---

## Day 02 (01-31)

### 🧠 思维导图
- [x] **JVM 篇**：[JVM虚拟机_思维导图.pdf](./CS_Notes/mindMap/JVM虚拟机_思维导图.pdf)

### ✅ 今日任务

- [x] **八股文 (Java/JVM/MySQL)**

  - **MySQL**
    - **binlog**：逻辑日志，记录 SQL 语句或行变更，用于主从复制和数据恢复（Point-in-Time Recovery）。
    - **redo log**：物理日志（InnoDB 特有），记录页的修改，循环写（Write Ahead Log, WAL），保证 Crash-safe（宕机恢复）。
    - **undo log**：逻辑日志，记录相反操作（Insert → Delete），用于事务回滚（原子性）和 MVCC（多版本并发控制）。
    - **MVCC**：
      - **快照读**（Snapshot Read）：简单的 `select`，不加锁，读取历史版本。
      - **当前读**（Current Read）：`select for update` / `insert` / `update` / `delete`，加锁，读取最新版本。
      - **实现**：基于 Undo Log 版本链 + Read View（读视图）。
    - **场景题**
      - **大批量插入**：使用 JDBC `RewriteBatchedStatements=true` + 事务提交（每 1000-2000 条 commit 一次），关闭自动提交。
      - **B+树**：非叶子节点仅存索引（key），叶子节点存完整数据（key+data）且通过双向链表连接。相比 B 树，层级更低（IO次数少），范围查询更优。

  - **JVM GC**
    - **分代模型**：新生代（Eden:S0:S1 = 8:1:1） / 老年代。
    - **CMS (Concurrent Mark Sweep)**：
      - 标记-清除算法。
      - 阶段：初始标记（STW）→ 并发标记（三色标记：黑/灰/白）→ 重新标记（STW，修正并发期间变动）→ 并发清除。
    - **G1 (Garbage First)**：
      - 物理上不分代（Region），逻辑分代。
      - 并发标记-整理算法（无内存碎片）。
      - 核心优势：**可预测停顿时间模型**（Pause Prediction Model）。
    - **ZGC**：
      - 基于**颜色指针**（Colored Pointers）和**读屏障**（Load Barriers）。
      - STW 极短（<10ms），吞吐量极高，支持 TB 级堆内存。

  - **对象 & 内存**
    - **对象内存布局**：
      - **对象头**（Header）：Mark Word（哈希码、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID）、类型指针（Klass Pointer）、数组长度（仅数组）。
      - **实例数据**（Instance Data）：字段值。
      - **对齐填充**（Padding）：保证对象大小是 8 字节倍数。
    - **对象创建流程**：类加载检查 → 分配内存（指针碰撞 / 空闲列表） → 初始化零值 → 设置对象头 → 执行 `<init>` 方法。

  - **GC 机制**
    - **Minor GC**：Eden 区满触发。存活对象年龄 +1。
    - **晋升老年代**：
      1. 年龄达到阈值（默认 15，CMS 默认 6）。
      2. **动态年龄判定**：Survivor 区中相同年龄所有对象大小总和 > Survivor 空间的一半，则年龄大于等于该年龄的对象直接进入老年代。
      3. 大对象直接进入老年代。

  - **类加载**
    - 流程：**加载**（Loading）→ **链接**（验证 Verify / 准备 Prepare / 解析 Resolve）→ **初始化**（Initialization）。
    - **双亲委派模型**：
      - 机制：自底向上检查是否加载，自顶向下尝试加载。
      - 作用：保证核心类库安全（防篡改 `java.lang.String`），避免重复加载。

  - **Java 基础**
    - `new String("abc")`：
      - 若字符串常量池无 "abc"：创建 2 个对象（常量池 1 个，堆中 1 个）。
      - 若有：创建 1 个对象（堆中 1 个）。

- [x] **算法**
  - **反转链表**：双指针（pre, cur, next）。
  - **最大子数组和**：DP `dp[i] = max(nums[i], dp[i-1] + nums[i])`。
  - **除自身以外数组的乘积**：
    - 方法：左乘积数组 `L[i]` × 右乘积数组 `R[i]`。
    - 优化：空间 O(1)，用输出数组存左乘积，再反向遍历乘右乘积。
  - **合并区间**：按左端点排序，遍历更新右边界 `max(end, interval.end)`。

- [x] **项目/实战 (技术派)**
  - **事务使用与注意事项**：`@Transactional`（默认只回滚 RuntimeException），失效场景（同类调用、非 public、catch 异常未抛出）。
  - **定时任务**：Spring Task (`@Scheduled`) / Quartz / XXL-JOB（分布式）。
  - **邮件服务**：`JavaMailSender`。
    - **图片上传**：本地存储 vs OSS（阿里云/七牛云）。
  - **MapStruct**：基于编译期注解处理器生成代码，性能远超反射实现的 BeanUtils。

- [x] **其他**
  - 导师邮件回复
  - 课内课程
  
> **⏱️ 学习时长**：7.5h

---

## Day 03 (02-01)

### 🧠 思维导图
- [x] **并发编程**：[JUC并发编程_思维导图.pdf](./CS_Notes/mindMap/JUC并发编程_思维导图.pdf)
- [x] **集合框架**：[Java集合_思维导图.pdf](./CS_Notes/mindMap/Java集合_思维导图.pdf)

### ✅ 今日任务

- [x] **复习昨日内容** (20min)

- [x] **八股文 (JUC/多线程/Spring)**

  - **Spring 核心**
    - **IOC (控制反转)**：将对象创建和依赖管理权交给容器，解耦。
    - **AOP (面向切面)**：
      - **JDK 动态代理**：基于接口（Proxy.newProxyInstance），核心是 `InvocationHandler`。
      - **CGLIB**：基于继承（ASM 字节码生成子类），核心是 `MethodInterceptor`。
      - **限制**：final 类（无法继承）、private 方法（无法重写）、内部调用（`this.method()` 不走代理）。

  - **JUC 基础**
    - **进程 vs 线程 vs 协程**：
      - **进程**：OS 资源分配最小单位，独立内存空间。
      - **线程**：CPU 调度最小单位，共享进程堆/方法区，独享栈/PC。
      - **协程**：用户态轻量级线程（Go goroutine / Kotlin coroutines），避免内核态上下文切换开销。
  - **JMM (Java 内存模型)**
    - 线程间通信：通过**共享内存**（主内存）完成，每个线程有私有的**工作内存**（Local Memory）。
    - 关键特性：原子性、可见性、有序性。
  - **线程创建**
    - `Thread`（单继承局限）
    - `Runnable`（接口实现，推荐）
    - `Callable` + `FutureTask`（有返回值，可抛异常，阻塞获取结果）
    - 线程池（推荐）
  - **线程状态**：New → Runnable (Ready/Running) → Blocked / Waiting / Timed_Waiting → Terminated。

  - **锁机制 & 关键字**
    - **volatile**：
      - 保证**可见性**（Lock 前缀指令触发缓存一致性协议 MESI）。
      - 禁止**指令重排**（读写内存屏障 Memory Barrier）。
      - **不保证原子性**（如 `i++`）。
    - **ThreadLocal**：
      - 线程私有变量，实现线程隔离。
      - 原理：每个 Thread 维护一个 `ThreadLocalMap`，Key 是 `ThreadLocal` 实例（**弱引用**），Value 是值（**强引用**）。
      - **内存泄漏**：Key 被回收后 Value 依然存在且无法访问。**解决**：使用完必须调用 `remove()`。
      - Hash 冲突：**线性探测法**（开放寻址），不同于 HashMap 的拉链法。
      - 限制：子线程不可访问父线程（需用 `InheritableThreadLocal`）。

    - **Synchronized vs ReentrantLock**：
      - **Synchronized**：JVM 层级（Monitor），自动释放，非公平，不可中断，锁升级（偏向 → 轻量 → 重量）。
      - **ReentrantLock**：API 层级（AQS），需手动 `unlock`，支持公平/非公平，可中断，支持 `Condition` 分组唤醒。

    - **公平 / 非公平锁**：
      - 公平：按等待队列 FIFO 获取锁（性能低，无饥饿）。
      - 非公平：尝试插队（CAS），失败再入队（性能高，可能饥饿）。

    - **CAS (Compare And Swap)**：
      - 乐观锁基石（无锁编程）。
      - 包含：内存值 V，预期值 A，新值 B。当 V==A 时更新为 B。
      - 问题：ABA 问题（加版本号解决）、循环时间长开销大（自旋）、只能保证一个变量原子性。

    - **锁升级（Synchronized）**：
      1. **无锁**。
      2. **偏向锁**：无竞争，Mark Word 记录线程 ID（JDK15 默认废弃）。
      3. **轻量级锁**：有竞争，在栈帧创建 Lock Record，通过 CAS 抢占锁（自旋）。
      4. **重量级锁**：自旋失败/竞争激烈，膨胀为重量级锁，申请 OS Monitor，线程挂起进入 EntryList（用户态↔内核态切换，性能差）。

    - **死锁条件**：互斥 + 占有并等待 + 不可强占 + 循环等待。
    - **CountDownLatch vs CyclicBarrier**：
      - `CountDownLatch`（减法）：一个等多个（主线程等子线程初始化）。不可重用。
      - `CyclicBarrier`（加法）：多个互相等（所有线程到达屏障点才继续）。可重用。

  - **并发集合**
    - **ConcurrentHashMap**：
      - **JDK7**：Segment 分段锁（ReentrantLock）+ HashEntry。锁粒度是 Segment。
      - **JDK8**：Node 数组 + 链表 + 红黑树。**CAS + Synchronized**（锁粒度细化到数组头节点）。
      - **弃用 ReentrantLock 原因**：JVM 对 Synchronized 做了大量优化（锁消除/粗化/偏向等），且粒度更细，内存开销更小。
    - **CopyOnWriteArrayList**：
      - 写时复制（Arrays.copyOf），读写分离。
      - 写加锁（ReentrantLock），读无锁。
      - 缺点：内存占用倍增，数据**最终一致性**（无法读到实时写入）。

  - **线程池**
    - **7 大参数**：`corePoolSize`, `maximumPoolSize`, `keepAliveTime`, `unit`, `workQueue`（阻塞队列）, `threadFactory`, `handler`（拒绝策略）。
    - **执行流程**：
      1. 线程数 < corePoolSize：创建核心线程。
      2. 线程数 >= corePoolSize：放入 workQueue。
      3. 队列满 & 线程数 < maxPoolSize：创建非核心线程。
      4. 队列满 & 线程数 >= maxPoolSize：执行拒绝策略。
    - **拒绝策略**：
      - `AbortPolicy`（抛异常，默认）。
      - `CallerRunsPolicy`（调用者所在线程执行，既不丢任务也能减缓提交速度）。
      - `DiscardPolicy`（直接丢弃）。
      - `DiscardOldestPolicy`（丢弃队列中最老的）。

  > **⏱️ 耗时**：2h 学习 + 0.5h 总结

- [x] **算法**
  - **环形链表**：快慢指针（相遇即有环）。
  - **合并有序链表**：哨兵节点（dummy head）简化边界处理。
  - **回文链表**：快慢指针找中点 → 反转后半部分 → 双指针比较 → 还原链表（可选）。
  - **两数相加**：模拟加法，注意进位（carry）。
  > **⏱️ 耗时**：1.5h（偏简单）

- [x] **项目/实战 (技术派)**
  - **全局异常处理**：`@ControllerAdvice` + `@ExceptionHandler` 统一返回 JSON。
  - **多数据源**：`AbstractRoutingDataSource` 动态切换。
  - **Excel 导出优化**：EasyExcel（流式写入防 OOM）+ CountDownLatch（多线程分批查询合并）。
  - **数据库表自动初始化**：Flyway / Liquibase 或自定义启动脚本。
  - **日志**：Lombok `@Slf4j` + Logback（异步日志 AsyncAppender 提升性能）。

- [x] **其他**
   课内课程

---

## Day 04 (02-02)

### 🧠 思维导图
- [x] **MySQL**：[MySQL数据库_思维导图.pdf](./CS_Notes/mindMap/MySQL数据库_思维导图.pdf)

### ✅ 今日任务

- [x] **复习昨日内容** (20min)

- [x] **八股文 (MySQL & 集合高频)**

  - **慢 SQL 优化**
    - 识别：`slow_query_log`，`long_query_time`。
    - 优化手段：
      - **分页优化**：`limit 1000000, 10` 改为 `where id > 1000000 limit 10`（书签法）或延迟关联（先查 ID 再 join）。
      - **避免 `select *`**：减少网络传输，增加**覆盖索引**（Covering Index）机会。
      - **索引失效**：最左前缀原则、列运算/函数、类型隐式转换、`or` 条件一侧无索引、`like '%abc'`。
      - **结构优化**：**小表驱动大表**（连接查询时小结果集驱动大结果集）。
      - **ICP (Index Condition Pushdown)**：索引下推，存储引擎层过滤数据，减少回表。

  - **索引详解**
    - **聚簇索引**（Clustered Index）：
      - 主键索引。叶子节点存**整行数据**。
      - 必须有，且仅有一个（主键 -> 唯一非空 -> 隐式 RowID）。
    - **非聚簇索引**（Secondary Index）：
      - 二级索引。叶子节点存**主键值**。
      - 需**回表**（先查二级索引拿到主键，再查聚簇索引拿到数据）。覆盖索引可免回表。
    - **结构对比**：
      - **B+Tree**：矮胖（3 层可存 2000w+），IO 少；叶子节点有序双向链表，支持**范围查询**。
      - **Hash**：O(1) 查询，不支持范围、排序、模糊查询；冲突。

  - **事务 (ACID)**
    - **Atomicity**（原子性）：Undo Log（回滚日志）。
    - **Consistency**（一致性）：代码逻辑 + 约束 + AID。
    - **Isolation**（隔离性）：Lock + MVCC。
    - **Durability**（持久性）：Redo Log（WAL）。双写缓冲（Doublewrite Buffer）防页损坏。

  - **隔离级别**
    - **RU**：脏读。
    - **RC**：不可重复读。每次 `select` 生成新 Read View。
    - **RR**（默认）：幻读。首次 `select` 生成 Read View，后续复用。解决幻读靠 **Next-Key Lock**（临键锁 = Record Lock + Gap Lock）。
    
  - **主从复制**
    - 原理：Master 写入 Binlog → Slave I/O 线程拉取写入 Relay Log → Slave SQL 线程重放。
    - 模式：异步复制（默认，可能丢数据）、**半同步复制**（Semisync，至少一个从库 Ack 才返回）、全同步复制。
    - Binlog 格式：Statement（可能不一致）、Row（文件大，严谨）、Mixed。

  - **集合框架**
    - **ArrayList**：动态数组，扩容 1.5 倍（位运算 `old + old >> 1`），尾插 O(1)，中间插 O(n)。
    - **LinkedList**：双向链表，插删 O(1)（需先定位），不支持随机访问。
    - **HashMap (JDK8)**：
      - **底层**：数组 + 链表 + 红黑树。
      - **扩容**：`resize()`，容量翻倍。JDK8 优化：无需重算 Hash，仅看高位是 0 还是 1（0留在原位，1移动到 `oldIndex + oldCap`）。
      - **树化**：链表长度 > 8 且数组 >= 64。
      - **线程不安全**：JDK7 死循环（头插法），JDK8 数据覆盖/丢数据（尾插法）。
      - **HashSet**：底层就是 HashMap，Value 存固定的 `PRESENT` 对象。

  > **⏱️ 耗时**：2h

- [ ] **算法**
  - 删除链表倒数第 N 个节点（双指针，快指针先走 N 步）。
  - 岛屿数量（DFS / BFS，网格遍历，访问过置为 '0'）。
  - 全排列（回溯 `backtrack`）。
  - 课程表（拓扑排序：入度表 + 队列 / DFS 检测环）。
  > **⏱️ 预计耗时**：1.5h

- [x] **项目/实战 (技术派)**
  - **跨域**：浏览器同源策略限制。解决：CORS（后端设置 Header `Access-Control-Allow-Origin`）、Nginx 反向代理。
  - **JWT vs Session**：
    - Session：服务端存储，客户端存 Cookie(SessionID)，有状态，水平扩展难。
    - JWT：客户端存储（Self-contained），无状态，无法主动注销（需配合 Redis 黑名单）。
  - **消息队列**：RabbitMQ / Kafka 基础概念（解耦、削峰、异步）。

  > **⏱️ 总有效时长**：5.5h

---

## Day 05 (02-03)

### 🧠 思维导图
- [x] **Redis**：[Redis缓存_思维导图.pdf](./CS_Notes/mindMap/Redis缓存_思维导图.pdf)

### ✅ 今日任务

- [x] **复习昨日内容** (20min)

- [x] **八股文 (Redis)**

  - **Redis vs MySQL**
    
    - **Redis**：基于内存，Key-Value 结构，QPS 可达 10w+。非关系型（NoSQL）。
    - **MySQL**：基于磁盘，关系型（Table），支持复杂 SQL 和事务 ACID。
    - **配合**：Redis 存高频热点数据，降低 DB 压力。
    
  - **核心特性 & 为什么快**
    - **纯内存操作**：寻址速度纳秒级，远快于磁盘 IO。
    - **IO 多路复用 (Epoll)**：单线程利用 reactor 模式处理大量并发连接（非阻塞 IO）。
    - **单线程执行模型**（Redis 6.0 前）：避免了上下文切换、锁竞争、死锁问题。
    - **Redis 6.0+**：引入**多线程处理网络 IO**（读写 Socket），但命令执行依然是单线程（保证线程安全）。
    - **高效数据结构**：SDS、SkipList、Dict (渐进式 Rehash)。

  - **持久化 (Persistence)**
    
    - **RDB (Redis Database)**：
      - **原理**：指定时间间隔生成内存快照（Fork 子进程，利用 **COW 写时复制**技术）。
      - **优点**：文件紧凑，恢复速度快，适合备份。
      - **缺点**：实时性差，宕机可能丢失最后一次快照后的数据。
    - **AOF (Append Only File)**：
      - **原理**：记录每次写命令到日志文件。
      - **刷盘策略**：
        - `appendfsync always`：每次写都刷盘（慢，安全）。
        - `appendfsync everysec`：每秒刷盘（**推荐**，兼顾性能与安全，最多丢 1s）。
        - `appendfsync no`：由 OS 决定。
      - **重写 (Rewrite)**：文件过大时，根据当前内存数据生成新的 AOF，去除冗余指令（如 `set a 1`, `set a 2` -> `set a 2`）。
    - **混合持久化**（Redis 4.0+）：
      - AOF 重写时，前半部分用 RDB 格式（快/小），后半部分用 AOF 增量数据。
    
  - **主从复制 & 哨兵**
    - **主从复制**：
      - **作用**：读写分离（主写从读），数据热备。
      - **流程**：建立连接 -> 全量同步（RDB） -> 增量同步（Replication Buffer 指令传播）。
      - **断点续传**：基于 `replication id` 和 `offset`。
    - **哨兵 (Sentinel)**：
      - **监控**：周期性 Ping 节点。
      - **故障转移**（Failover）：主挂掉 -> 选举新主（Raft 算法） -> 通知客户端。

  - **缓存异常场景 (必考)**
    - **缓存击穿**（Hot Key Expired）：热点 Key 过期，大量请求瞬间打入 DB。
      - **解决**：
        1. **互斥锁**（Mutex）：`set nx`，抢到锁查 DB 回填，没抢到 sleep 重试。
        2. **逻辑过期**：Value 中包含过期时间，不设 TTL。发现逻辑过期 -> 返回旧值 + 异步起线程去更新。
    - **缓存穿透**（Data Not Exist）：查根本不存在的数据（如 ID = -1），绕过缓存猛击 DB。
      - **解决**：
        1. **缓存空对象**（Null Value）：简单，但浪费内存。
        2. **布隆过滤器**（Bloom Filter）：位图 + 多重 Hash。请求前先判是否存在。
         - 特性：**说存在不一定存在（误判），说不存在一定不存在**。
    - **缓存雪崩**（Avalanche）：大量 Key 同时过期 或 Redis 宕机。
      - **解决**：
        1. **随机 TTL**：给过期时间加随机值（如 1-5 分钟），分散失效。
        2. **高可用**：Redis Cluster / Sentinel。
        3. **限流降级**：Sentinel / Hystrix。

  - **缓存一致性**
    - **Cache Aside Pattern**（旁路缓存）：**先更新 DB，再删除缓存**。
    - **为什么不是更新缓存？**：并发写会导致脏数据；更新操作可能涉及复杂计算（浪费性能）。
    - **为什么不是先删缓存？**：A 删 -> B 查(旧) -> B 写缓存(旧) -> A 写 DB(新)。导致缓存脏数据。
    - **解决方案**：
      1. **延时双删**：删 -> 写 DB -> sleep(N) -> 再删。解决先删缓存带来的脏数据，但 N 难确定。
      2. **消息队列重试**：删失败 -> 发 MQ -> 消费者重试删。
      3. **Canal 订阅 Binlog**：更新 DB -> Canal 监听 Binlog -> 发 MsQ -> 消费者更新/删除 Redis。

  - **数据类型应用**
    - `String`：Session、计数器、分布式锁、JSON 对象。
    - `Hash`：购物车、用户属性（部分修改）。
    - `List`：消息队列（lpush/rpop）、朋友圈时间线（Timeline）。
    - `Set`：抽奖（spop）、点赞/关注（去重）、共同好友（sinter）。
    - `ZSet`：排行榜（score 排序）、延时队列（score 为时间戳）。
    - `BitMap`：用户签到（日活统计）。
    - `HyperLogLog`：UV 统计（基数估算，省内存）。
    - `Geo`：附近的人。

- [x] - 预计耗时：5h

---

## Day 06 (02-05)

### 🧠 思维导图
- [x] **Spring 全家桶**：[Spring全家桶_思维导图.pdf](./CS_Notes/mindMap/Spring全家桶_思维导图.pdf)
- [x] **Java 基础**：[JavaSE基础_思维导图.pdf](./CS_Notes/mindMap/JavaSE基础_思维导图.pdf)

### ✅ 今日任务

- [x] **复习昨日内容** (20min)

- [x] **八股文 (Spring & Java)**

  - **Java SE 核心**
    - **值传递**：Java **只有值传递**。基本类型传值，引用类型传地址的副本。
    - **Static**：属于类，类加载时初始化。Static 方法不能访问非 Static 成员（无 this）。
    - **String**：
      - `final` 修饰，不可变，线程安全。
      - **StringBuilder**：可变，非线程安全，效率高。
      - **StringBuffer**：可变，Synchronized 线程安全，效率低。
    - **Integer 缓存池**：`-128` 到 `127` 之间的 Integer 对象被缓存（享元模式），`==` 比较为 true。超出范围则 new 新对象。
    - **反射 (Reflection)**：
      - 运行期动态获取类信息（属性/方法）并调用。
      - 应用：Spring IOC、AOP、MyBatis Mapper。
      - 缺点：性能开销（解释执行）、破坏封装性（可访问 private）、安全隐患。

  - **Spring 核心**
    - **Bean 生命周期 (必背)**：
      1. **实例化** (Instantiation)：`createBeanInstance`，调用构造方法。
      2. **属性赋值** (Populate)：`populateBean`，依赖注入（DI）。
      3. **初始化** (Initialization)：
         - `Aware` 接口回调（BeanNameAware, BeanFactoryAware）。
         - `BeanPostProcessor.postProcessBeforeInitialization`。
         - `InitializingBean.afterPropertiesSet`。
         - `init-method` (XML/注解指定)。
         - `BeanPostProcessor.postProcessAfterInitialization` (**AOP 代理在此处生成**)。
      4. **销毁** (Destruction)：`DisposableBean` / `destroy-method`。

    - **循环依赖 (Circular Dependency)**
      - **解决前提**：单例（Singleton） + Setter 注入。构造器注入无法解决（死锁）。
      - **三级缓存原理**：
        - 一级缓存 `singletonObjects`：存放**完整**的 Bean。
        - 二级缓存 `earlySingletonObjects`：存放**早期** Bean（已实例化、未填充属性），用于被其他 Bean 引用。
        - 三级缓存 `singletonFactories`：存放 **ObjectFactory**（函数式接口），用于提前生成 AOP 代理。
      - **流程**：A 依赖 B -> A 实例化 -> 放入三级缓存 -> 注入 B -> B 实例化 -> 注入 A -> 从三级缓存拿 A 的工厂 -> 生成 A 的早期引用放入二级 -> B 完成 -> A 完成。

    - **SpringBoot**
      - **自动装配原理**：
        - `@SpringBootApplication` = `@SpringBootConfiguration` + `@ComponentScan` + `@EnableAutoConfiguration`。
        - `@EnableAutoConfiguration` 引入 `AutoConfigurationImportSelector`。
        - 读取 `META-INF/spring.factories` (或 `spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` in 2.7+)。
        - 筛选：根据 `@ConditionalOnClass`, `@ConditionalOnMissingBean` 等条件判断是否加载配置类。
      - **启动流程**：
        - 创建 `SpringApplication` -> 运行 `run()` -> 创建环境 `Environment` -> 创建上下文 `ApplicationContext` -> 刷新上下文 `refresh()` (启动 Tomcat) -> 运行 Runners。

  - **耗时统计**：学习 **2.5h** ｜整理 **10min** ｜模拟面试 **20min**

- [x] **算法**
  - **二叉树遍历**：
    - 前中后序（递归简单，迭代需掌握 Stack 写法）。
    - 层序遍历（Queue 队列）。
  - **最大深度**：`max(left, right) + 1`。
  - **翻转二叉树**：递归交换左右节点。
  - **对称二叉树**：递归比较 `left.left vs right.right` 和 `left.right vs right.left`。
  - **耗时**：1h

- [x] **项目/实战 (技术派)**
  - **Redis 分布式锁实战**：
    - **问题**：业务执行时间 > 锁过期时间。导致锁自动释放，其他线程进入，引发并发问题。
    - **解决**：**Redisson WatchDog**（看门狗）。
      - 获取锁成功后，启动后台线程（TimerTask）。
      - 每隔 `lockWatchdogTimeout / 3` (默认 10s) 检查锁是否存在。
      - 若存在，重置过期时间为 30s。
      - 业务完成手动 unlock，看门狗停止。
  - **Canal 数据同步**：
    - 原理：伪装成 MySQL Slave，向 Master 发送 dump 请求。Master 推送 Binlog，Canal 解析为 JSON 发送给 MQ/ES。
  - **ES 搜索**：
    - 倒排索引（Inverted Index）：Term (关键词) -> Posting List (文档 ID 列表)。
  - **耗时**：1h

---

## Day 07 (02-06)

### 🧠 思维导图
- [x] **计算机网络**：[计算机网络_思维导图.pdf](./CS_Notes/mindMap/计算机网络_思维导图.pdf)

### ✅ 今日任务

- [x] **复习昨日内容**

- [x] **八股文 (计算机网络)**

  - **网络体系结构**
    - **OSI 七层模型**：
      1. **应用层** (Application)：HTTP, FTP, SMTP, DNS。
      2. **表示层** (Presentation)：数据格式转换、加密解密 (TLS/SSL)、压缩。
      3. **会话层** (Session)：建立、管理、终止会话。
      4. **传输层** (Transport)：TCP, UDP。端到端传输，流量控制，差错检测。
      5. **网络层** (Network)：IP, ICMP, ARP。路由选择，分组转发。
      6. **数据链路层** (Data Link)：MAC, VLAN。帧传输，差错检测。
      7. **物理层** (Physical)：比特流传输 (光纤/双绞线)。
    - **TCP/IP 四层模型**：应用层、传输层、网络层、网络接口层。
    - **数据封装**：
      - 应用数据 -> **报文** (Message)
      - 传输层 -> **段** (Segment, TCP)
      - 网络层 -> **包** (Packet, IP)
      - 链路层 -> **帧** (Frame)
      - 物理层 -> **比特** (Bit)

  - **HTTP 协议**
    - **状态码 (Status Code)**
      - 2xx：成功 (200 OK, 204 No Content, 206 Partial Content)
      - 3xx：重定向 (301 Moved Permanently, 302 Found, 304 Not Modified)
      - 4xx：客户端错误 (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found)
      - 5xx：服务端错误 (500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable)
    - **GET vs POST**
      - **GET**：获取资源，幂等，参数放 URL (长度限制)，无 Body (一般)，会被浏览器缓存。
      - **POST**：提交资源，非幂等，参数放 Body (无限制)，更安全 (相对)。
    - **HTTP 版本演进**
      - **1.0**：短连接 (每请求需 TCP 握手)。
      - **1.1**：长连接 (Connection: keep-alive)，管线化 (Pipelining，但不常用)。
      - **2.0**：
        - **多路复用** (Multiplexing)：单连接并发处理多个请求 (Stream ID)。
        - **头部压缩** (HPACK)：减少重复 Header 传输。
        - **二进制分帧**：传输二进制而非文本。
        - **服务端推送** (Server Push)。
      - **3.0**：**QUIC** (基于 UDP)，解决 TCP 队头阻塞，连接迁移 (Connection ID)，0-RTT 建连。

  - **HTTPS (HTTP over SSL/TLS)**
    - **原理**：HTTP + SSL/TLS 加密层。
    - **加密方式**：
      - **非对称加密** (RSA/ECC)：用于握手阶段协商密钥 (Session Key)。
      - **对称加密** (AES/ChaCha20)：用于后续传输数据 (速度快)。
    - **握手流程 (简化)**：
      1. Client Hello (支持的加密套件, Random1)。
      2. Server Hello (选定套件, Random2, **数字证书**)。
      3. Client 验证证书 (CA 签名) -> 生成 Pre-master Secret (Random3) -> 用 Server 公钥加密发送。
      4. Server 用私钥解密 Pre-master。
      5. 双方利用 Random1+2+3 生成最终 **Session Key**。
      6. Finished (加密握手结束)。

  - **TCP 协议 (Transmission Control Protocol)**
    
    - **特性**：面向连接、可靠传输、字节流、全双工。
    - **三次握手 (Three-way Handshake)**
      1. SYN (seq=x) -> Listen
      2. SYN+ACK (seq=y, ack=x+1) -> Syn-Sent
      3. ACK (seq=x+1, ack=y+1) -> Established
      - **为什么三次？**：防止旧的重复连接初始化；同步双方序列号；确认双向收发能力。
    - **四次挥手 (Four-way Wave)**
      1. FIN (seq=u) -> Fin-Wait-1
      2. ACK (ack=u+1) -> Close-Wait (Server 处理剩余数据)
      3. FIN (seq=w) -> Last-Ack
      4. ACK (ack=w+1) -> Time-Wait -> Closed
      - **为什么四次？**：TCP 全双工，Server 收到 FIN 后可能还有数据要发，先回 ACK，发完再回 FIN。
    - **TIME_WAIT**
      - **时长**：2MSL (Maximum Segment Lifetime)。
      - **作用**：
        1. 保证最后一个 ACK 能到达 Server (若丢包 Server 会重发 FIN)。
        2. 防止旧连接的报文混入新连接 (让网络中残留报文消失)。
    - **可靠性机制**
      - **确认应答 (ACK)** & **序列号 (Seq)**。
      - **超时重传 (RTO)**。
      - **流量控制**：滑动窗口 (Sliding Window)，接收方通告窗口大小 (rwnd)。
      - **拥塞控制**：慢启动 (Slow Start, 指数增长) -> 拥塞避免 (Congestion Avoidance, 线性增长) -> 拥塞发生 (超时/3ACK) -> 快重传 (Fast Retransmit) -> 快恢复 (Fast Recovery)。
    
  - **UDP 协议**
    
    - **特性**：无连接、不可靠、面向报文、无拥塞控制。
    - **应用**：DNS, DHCP, TFTP, SNMP, 实时音视频 (RTP), QUIC。
    
  - **IP 协议**
    - **ARP (Address Resolution Protocol)**：IP 地址 -> MAC 地址。广播请求，单播响应。
    - **ICMP**：Ping 原理 (Echo Request / Reply)，Traceroute 原理 (TTL 超时)。
    - **NAT**：网络地址转换，解决 IPv4 不足。

  - **浏览器输入 URL 全过程**
    1. **DNS 解析** (浏览器缓存 -> OS 缓存 -> Hosts -> Local DNS -> Root DNS -> TLD DNS -> Authoritative DNS)。
    2. **TCP 建连** (三次握手)。
    3. **TLS 握手** (HTTPS)。
    4. **发送 HTTP 请求**。
    5. **后端处理** (LB -> Gateway -> Service -> DB)。
    6. **接收响应**。
    7. **浏览器渲染** (HTML -> DOM, CSS -> CSSOM, 合并 -> Render Tree, 布局 -> Layout, 绘制 -> Paint)。
    8. **TCP 断连** (四次挥手)。

  - **耗时统计**：学习 **3h** (计网内容多，优先高频考点)

- [x] **算法**
  - 二叉树中序遍历、层序遍历
  - 最大深度
  - 翻转二叉树
  - 对称二叉树
  - **耗时**：1h

总有效时长**5h30**

---

## Day 08 (02-08)

### 🧠 思维导图
- [x] **操作系统**：[操作系统_思维导图.pdf](./CS_Notes/mindMap/操作系统_思维导图.pdf)

### ✅ 今日任务

- [x] **复习昨日内容** (20min)
- [x] **八股文 (操作系统)**

  - **基础概念**
    - **内核态 vs 用户态**：
      - **内核态** (Kernel Mode)：Ring 0，可执行特权指令，访问所有硬件/内存。
      - **用户态** (User Mode)：Ring 3，受限访问。
      - **切换方式**：系统调用 (System Call, 如 read/write)、异常 (Exception, 如缺页)、中断 (Interrupt, 如时钟/IO)。
      - **开销**：保存/恢复寄存器上下文，TLB 失效，CPU 缓存失效。
  - **进程管理**
    - **进程 vs 线程**
      - **进程** (Process)：资源分配的基本单位。拥有独立的地址空间 (Code, Data, Heap, Stack)。
      - **线程** (Thread)：CPU 调度的基本单位。共享进程的资源 (Heap, Code, Data, 文件描述符)，独享 Stack, PC, 寄存器。
      - **切换开销**：进程切换 >> 线程切换 (需切页表，刷新 TLB)。
    - **进程通信 (IPC)**
      - **管道** (Pipe)：匿名管道 (父子进程)，命名管道 (FIFO)。半双工。
      - **消息队列** (Message Queue)：链表，拷贝开销。
      - **共享内存** (Shared Memory)：映射同一物理内存，最快，需配合信号量同步。
      - **信号量** (Semaphore)：PV 操作，用于同步互斥。
      - **Socket**：跨网络通信。
      - **信号** (Signal)：kill, ctrl+c。
    - **调度算法**
      - **FCFS** (先来先服务)：不利于短作业。
      - **SJF** (短作业优先)：长作业饥饿。 调度算法：
      - 时间片轮转（公平）
      - 多级反馈队列（多队列 + 不同时间片 + 抢占）
      - 优先级调度
    - 进程状态：**创建 → 就绪 → 执行中 → 阻塞 → 销毁**
      - 僵尸进程：子进程结束，父进程未 `wait`，PCB 未释放
      - 孤儿进程：父进程退出，子进程被 `init` 接管（无危害）
  - **进程 / 线程**：
    - 进程通信（IPC）：socket｜管道（效率低）｜信号｜消息队列（长度限制）｜共享内存（最快）｜信号量
    - 线程：一个进程可有多个线程；**共享进程内存**，仅独立寄存器和栈  
      - 进程是**资源分配单位**，线程是 **CPU 调度单位**
      - 同进程线程切换 < 跨进程切换
    - 线程模型：
      - **ULT（用户级线程）**：切换快，阻塞系统调用会阻塞整个进程
      - **KLT（内核级线程）**：OS 调度，阻塞互不影响，切换开销大
      - **混合模型**：M:N
  - **同步与并发问题**：
    - 互斥：访问共享资源需加锁；**信号量（P/V）**
    - 活锁：互相让步但无进展
    - 饥饿：长期得不到 CPU
  - **文件管理**：
    - 软链接：引用指向，类似快捷方式，可跨文件系统
    - 硬链接：多个目录项指向同一 inode；**不可跨文件系统 / 不可链接目录**
  - **内存管理**：
    - 虚拟内存：虚拟地址 → 物理地址映射；**隔离进程 + 扩展内存**
    - 分段：段号 + 基址 + 偏移（逻辑单位，用户可见）
    - 分页：虚拟页 → 物理页；页表映射  
      - 多级页表：解决页表过大
      - 快表（TLB）：缓存常用页表项，利用局部性原理，**减少内存访问**
    - 分段 vs 分页：逻辑单位 vs 物理单位
    - 页面置换：内存不足 → 页面换出到磁盘
      - FIFO / LRU / LFU / Clock / OPT（理论最优，难实现）
  - **IO**：
    - 同步 IO：
      - 阻塞 IO：等待数据
      - 非阻塞 IO：轮询
      - IO 多路复用：一个线程管理多个 socket
    - 异步 IO：提交后立即返回，完成由 OS 通知
    - 零拷贝：`mmap` / `sendfile`，减少用户态 ↔ 内核态切换  
      **应用：Kafka**
  - **耗时统计**：学习 **1.5h**
- [x] **Claude Code 入门**
  - agent 初体验，不得不感慨ai发展之速   **耗时：1.5h**
- [x] **算法**
- 验证二叉搜索树 二叉树第k小树 二叉树右视图 二叉树展开链表
- **耗时**：1h
- [x] **技术派教程** 30min

总时长 5h

---

## Day 09 (02-09)

- [x] **校内事务**
  - 论文处理 (1.5h)

- [x] **八股自测 (Mock Interview)**
  - **内容**：高频八股 (Java/MySQL/Redis)。
  - **反思**：
    - **问题**：看懂 != 会讲。回答磕磕绊绊，逻辑不清。
    - **对策**：
      1. **STAR 法则**：Situation (背景), Task (任务), Action (行动), Result (结果)。
      2. **录音复盘**：自己讲一遍录下来，听哪里卡壳。
      3. **画图辅助**：边讲边画架构图/流程图 (白板面试)。
  - **耗时**：4h

总时长 5h

---

## Day 10 (02-10)

- [x] **八股复习**
  - 针对昨日自测薄弱点 (计网/OS) 查漏补缺。
  - **耗时**：1.5h

- [x] **算法 (LeetCode)**
  - **LRU 缓存机制 (Hard)**
    - **思路**：哈希表 (查找 O(1)) + 双向链表 (插删 O(1))。
    - **结构**：
      - Node: key, value, prev, next
      - Map: <key, Node>
      - Dummy Head/Tail (哨兵节点)
    - **get(key)**：若存在，移到头部 (moveToHead)。
    - **put(key, value)**：若存在，更新 value 并移到头部；若不存在，新建节点插入头部。若容量满，删除尾部节点 (removeTail) 并移除 Map 中对应 key。
  - **K 个一组翻转链表 (Hard)**
    - **思路**：迭代法。
      - 1. 检查剩余长度是否 >= k。
      - 2. 翻转前 k 个节点 (pre, cur, next)。
      - 3. 连接翻转后的子链表 (preGroup.next = newHead, oldHead.next = nextGroup)。
      - 4. 更新 preGroup 指针，继续下一组。
  - **耗时**：1h

- [x] **RAG 项目 (检索增强生成)**
  - **模块**：文件上传解析。
  - **难点**：
    - **文本分块 (Chunking)**：固定字符数 / 语义分割 / 递归分割 (RecursiveCharacterTextSplitter)。太小丢失上下文，太大干扰检索。
    - **Embedding**：OpenAI / HuggingFace 模型向量化。
  - **耗时**：1h

总时长 3.5h

---

## Day 11 (02-11)

- [x] **八股**
  - 阅读 JavaGuide / 牛客 面经。
  - **耗时**：1h

- [x] **算法**
  - **随机链表的复制 (Medium)**
    - **方法 1**：Map<Node, Node> 映射旧节点到新节点。
    - **方法 2** (O(1) 空间)：原地复制 A->A'->B->B'。1. 复制节点；2. 复制 random 指针 (curr.next.random = curr.random.next)；3. 拆分链表。
  - **排序链表 (Medium)**
    - **思路**：归并排序 (Merge Sort)。
    - 1. 快慢指针找中点 (cut)。
    - 2. 递归排序左右两半。
    - 3. 合并两个有序链表 (merge)。
  - **合并 K 个升序链表 (Hard)**
    - **方法 1**：优先队列 (Min Heap)。O(N log K)。
    - **方法 2**：分治归并 (两两合并)。
  - **耗时**：2h

- [x] **RAG 项目**
  - **异步解耦**：引入 **Kafka** 处理文件解析任务。
    - Upload Controller -> Kafka Producer -> Topic -> Kafka Consumer (Python/Java) -> Vector DB。
    - **重试机制**：消费失败放入死信队列 (DLQ) 或延迟队列重试。
  - **鉴权**：**Spring Security + OAuth2**。
    - **Access Token**：短效，用于访问资源。
    - **Refresh Token**：长效，用于刷新 Access Token (减少用户登录频率，同时保证安全)。
  - **耗时**：2h

总时长 5h

---

## Day 12 (02-13)

- [x] **八股**
  - 牛客笔试模拟 (1h)：算法 OK，选择题 (设计模式/正则/Linux命令) 偏难。
  - JavaGuide 高频题复习 (1h)。

- [x] **算法**
  - **手写快排 (Quick Sort)**
    - **核心**：Partition (分区)。
      - 选 pivot (通常选第一个或随机)。
      - 双指针 swap，左边 < pivot，右边 > pivot。
      - 递归 left, right。
  - **课程表 (Topology Sort)**
    - **思路**：入度表 (Indegree) + 邻接表 (Adjacency List) + 队列 (Queue)。
    - 1. 统计所有课程先修课数量 (入度)。
    - 2. 入度为 0 入队。
    - 3. 出队 count++，将后续课程入度 -1，若为 0 入队。
    - 4. 若 count == numCourses，无环。
  - **接雨水 (Hard)**
    - **方法**：双指针 (Two Pointers)。
      - 维护 maxLeft, maxRight。
      - 左右指针向中间收缩。
      - `water += min(maxLeft, maxRight) - height[i]`。
  - **耗时**：2h

- [x] **求职准备**
  - 修改简历 (0.5h)。

总时长 4.5h

-----

## Day 13 (02-23)

> **📝 状态**：复工回归，沉淀心情。

- [x] **八股**
  - **多线程交替打印 (ABCABC)**
    - **Wait/Notify**：synchronized + flag 标记。
    - **Lock/Condition**：await/signal。
    - **Semaphore**：Permits 传递 (A=1, B=0, C=0)。
  - **项目难点梳理**
  
- [x] **算法**
  - **下一个排列 (Next Permutation)**
    - 1. 从后向前找第一个升序对 (i, i+1)。
    - 2. 从后向前找第一个 > nums[i] 的数 j。
    - 3. 交换 nums[i], nums[j]。
    - 4. 反转 i+1 后的所有元素 (变为升序)。
  - **双指针应用**：找较大较小值。

- [x] **环境配置**
  - **心得**：AI (ChatGPT/Claude) 虽强，但必须自己理解需求 (Prompt Engineering) 才能解决复杂环境问题。

总时长 4.5h

---

## Day 14 (02-24)

### 🧠 思维导图
- [x] **八股文复习**：结合项目深挖与思维导图查漏补缺。

### ✅ 今日任务

- [x] **八股文 (项目/基础)**
  - **RAG 项目拷打总结 (Q1, Q2)**
    - **Q1. 大文件切分与 ES 索引优化**
      - **大文件分片**：采用 5MB 固定分片，MD5 + MinIO compose + Kafka 异步处理。MinIO 合并是原子性操作。向量化仍基于 5MB 块，ES 作为向量存储，平衡了轻量化和功能。
      - **代码文件切分**：未来会采用**结构化拆分**（根据文件后缀判断），将代码索引为 `content` 类型走 BM25（用于关键词检索）。
      - **ES Mapping 原理 (关键学习点)**：
        - `Mapping`：定义文档结构与字段类型，影响索引和检索性能。
        - `text` 类型：用于全文检索（BM25），会被分词器处理并构建倒排索引。
        - `keyword` 类型：用于精确匹配、排序、聚合，作为完整字符串索引，**不会分词**，**并非加密**。
        - `dense_vector` 类型：存储浮点数数组，构建 ANN 索引 (HNSW) 进行向量相似度检索。
      - **ES 性能优化**：项目并发量不高暂未实施优化。对 `Filter-then-Search` 担忧其损失召回率（正确）。
        - **其他优化手段**：HNSW 参数调优、索引分片优化、硬件升级、Pre-filtering (需谨慎确保不损失召回)。
    - **Q2. 混合检索合并、去重与多样性、模糊查询处理**
      - **混合检索精排**：KNN 向量检索 (`topK*30` 粗排) -> ES `rescore` (BM25 权重 5x，向量权重 0.2)。通过 A/B 测试优化权重。
      - **冗余过滤与多样性 (未来设计思路)**：
        - **思路**：设定相似度阈值过滤高重叠片段；优先从不同文档选取片段；考虑 MMR (Maximal Marginal Relevance) 算法，平衡相关性和多样性。
      - **模糊查询处理 (未来设计思路)**：
        - **思路**：同义词扩展/查询重写；Fallback 机制（扩大召回范围或降级到纯 BM25）；利用 LLM 重写用户查询。
  
- [x] **算法**
  - **数组中重复的数字 (Easy)**
    - **方法**：原地交换 (In-place Swap)。
    - **思路**：遍历数组，若 `nums[i] != i`，则将其交换到索引 `nums[i]` 处。若目标位置 `nums[nums[i]]` 已有正确值，则发现重复。
    - **复杂度**：时间 O(N)，空间 O(1)。
  - **编辑距离 (Hard)**
    - **DP 定义**：`dp[i][j]` 表示将 word1 的前 i 个字符转换成 word2 的前 j 个字符所需的最小操作数。
    - **状态转移**：
      - 若 `word1[i-1] == word2[j-1]`：`dp[i][j] = dp[i-1][j-1]`（无需操作）。
      - 若 `word1[i-1] != word2[j-1]`：`dp[i][j] = min(...) + 1`
        - **插入**：`dp[i][j-1]`（word2 匹配了，word1 滞后）。
        - **删除**：`dp[i-1][j]`（word1 多余字符）。
        - **替换**：`dp[i-1][j-1]`（对应位置字符变身）。
    - **初始化**：`dp[i][0] = i` (删 i 次), `dp[0][j] = j` (插 j 次)。

- [x] **AI 工具实践 (OpenClaw)**
  - **体验与反思**：
    - **环境配置**：配置 OpenClaw 时遇到的环境问题提醒我，解决复杂问题需先明确需求与架构。
    - **方法论**：
      1. **明确需求**：What & Why。
      2. **理解架构**：How it works。
      3. **分步实施**：Prerequisites (前提) -> Action (执行) -> Expectation (预期) -> Validation (验证)。
      4. **稳扎稳打**：每一步验证无误再进行下一步，避免混乱。

> **⏱️ 总时长**：5.5h

## Day 15 (02-25)

### ✅ 今日任务

- [x] **八股文 (项目/基础)**
  - **RAG 项目拷打总结 (Q1, Q2)**
    - **Q1. 大文件切分与 ES 索引优化**
      - **大文件分片**：采用 5MB 固定分片，MD5 + MinIO compose + Kafka 异步处理。MinIO 合并是原子性操作。向量化仍基于 5MB 块，ES 作为向量存储，平衡了轻量化和功能。
      - **代码文件切分**：未来会采用**结构化拆分**（根据文件后缀判断），将代码索引为 `content` 类型走 BM25（用于关键词检索）。
      - **ES Mapping 原理 (关键学习点)**：
        - `Mapping`：定义文档结构与字段类型，影响索引和检索性能。
        - `text` 类型：用于全文检索（BM25），会被分词器处理并构建倒排索引。
        - `keyword` 类型：用于精确匹配、排序、聚合，作为完整字符串索引，**不会分词**，**并非加密**。
        - `dense_vector` 类型：存储浮点数数组，构建 ANN 索引 (HNSW) 进行向量相似度检索。
      - **ES 性能优化**：项目并发量不高暂未实施优化。对 `Filter-then-Search` 担忧其损失召回率（正确）。
        - **其他优化手段**：HNSW 参数调优、索引分片优化、硬件升级、Pre-filtering (需谨慎确保不损失召回)。
    - **Q2. 混合检索合并、去重与多样性、模糊查询处理**
      - **混合检索精排**：KNN 向量检索 (`topK*30` 粗排) -> ES `rescore` (BM25 权重 5x，向量权重 0.2)。通过 A/B 测试优化权重。
      - **冗余过滤与多样性 (未来设计思路)**：
        - **思路**：设定相似度阈值过滤高重叠片段；优先从不同文档选取片段；考虑 MMR (Maximal Marginal Relevance) 算法，平衡相关性和多样性。
      - **模糊查询处理 (未来设计思路)**：
        - **思路**：同义词扩展/查询重写；Fallback 机制（扩大召回范围或降级到纯 BM25）；利用 LLM 重写用户查询。

  - **技术社区平台项目拷打总结 (Q3, Q4)**
    - **Q3. 高并发/缓存 (Redisson 看门狗 / 降级熔断)**
      - **缓存雪崩**：采用随机 TTL 避免大规模失效。
      - **Key 击穿**：通过**逻辑过期** + **Redisson 分布式锁**（互斥保证刷新安全）解决。
      - **限流**：考虑使用 Redis + Lua 脚本 + 令牌桶算法实现。
      - **降级熔断**：未深入实现，但理解其必要性。
        - **未来设计思路**：
          - **判断标准**：Redis 连接失败 3 次且指数退避后确认。`Cache-Aside Pattern`。
          - **熔断机制**：从"关闭" (Closed) -> "开启" (Open)，直接跳过 Redis 到 MySQL；"半开" (Half-Open) 周期性探测 Redis 恢复情况。
          - **协同工作**：熔断器作为更高层防护，包裹缓存降级逻辑。
    - **Q4. 数据一致性与搜索能力 (ES 降级 MySQL Like)**
      - **BM25 关键词搜索**：项目主要依赖此。上传时幂等性校验确保 MySQL 数据最新。
      - **ES 降级 MySQL Like 的影响 (权衡)**：
        - **性能**：MySQL Like 性能差，但可通过优化慢 SQL (小表驱动大表、索引下推) 缓解。**未来考虑引入 `FULLTEXT` 索引** 或轻量级全文搜索引擎 (如 Sphinx)。
        - **用户体验**：响应慢则返回"待会重试"。
        - **数据一致性**：MySQL 保证是最新数据。ES 降级后，用户仍能搜到最新数据，但**搜索能力会严重受限** (无分词、高亮、模糊匹配)。
        - **未来设计思路**：
          - **用户反馈**：降级时明确告知用户"搜索功能受限，请尝试更精确关键词"。
          - **重试机制**：用户手动点击重试，或后端异步重试并通知。
          - **查询意图识别**：根据查询类型（强关键词/强语义）动态调整 BM25 和向量召回权重。

- [x] **算法**
  - 复习了一下深搜的几道题 因为之前打比赛深搜写过太多了 看一眼 想了一下思路没问题就过了 然后写了个分割回文串 写的还不错
  - 二分最基础的写一道 两分钟ac 
  - 栈方面写了个字符串编码 思路是对的 但是string api写的头晕 没写出来
  - 1.5h
- [x] 其他：投了一下简历 刷了会小红书的简历 和ai相关

> **⏱️ 总时长**：5h

## Day 16 (02-26)

### 🧠 思维导图
- [ ] 今日暂无新的思维导图，重点回顾与巩固。

### ✅ 今日任务

- [x] 📚 **八股文 (项目/基础)**
  - 📌 **Kafka 核心概念与高级特性深入**
    
    **核心架构组件（结合RAG项目理解）**：
    
    - **Broker** - Kafka服务器节点
      - *实际业务*：就像快递分拣中心，多个Broker组成集群，某个挂了其他还能继续工作。
      - *RAG项目应用*：我们部署了3个Broker，文件解析任务分散在不同节点，单机故障不影响整体服务。
    
    - **Topic** - 消息主题/分类
      - *实际业务*：就像快递的不同线路（"北京线"、"上海线"），Producer按目的地投放，Consumer按需订阅。
      - *RAG项目应用*：`file-parse-topic`（文件解析）、`vector-index-topic`（向量索引）、`notification-topic`（通知），不同业务解耦。
    
    - **Partition** - Topic的物理分片
      - *核心特性*：Partition内消息有序（像排队买票），不同Partition间无序（像多个窗口同时卖票）。
      - *实际业务*：一个Topic有3个Partition = 3个并行通道，消费能力提升3倍。
      - *RAG项目应用*：大文件分片后发送到不同Partition并行处理，但同一文件的所有分片必须进同一个Partition（保证顺序）。
    
    - **Producer** - 消息生产者
      - *分区策略*：
        - 轮询（RoundRobin）：负载均衡，适合无顺序要求场景
        - 按键哈希（Key Hashing）：相同Key进同一Partition，保证顺序
        - 自定义分区器：按业务逻辑（如文件ID）路由
      - *RAG项目应用*：按`fileId % partitionNum`路由，确保同一文件的所有块按顺序处理。
    
    - **Consumer & Consumer Group** - 消费者与消费者组
      - *实际业务*：Consumer Group就像一个快递站点，组内多个Consumer是多个快递员，共同分担一个区域的配送（一个Partition只能被一个Consumer消费）。
      - *RAG项目应用*：启动3个Consumer实例组成Group，并行消费文件解析任务，吞吐量提升3倍。
    
    - **ZooKeeper / KRaft** - 集群协调者
      - *作用*：管理Broker注册、Leader选举、元数据存储。
      - *演进*：Kafka 2.8+ 引入KRaft模式，去除ZooKeeper依赖，减少运维复杂度。
    
    ---
    
    **防重复消费（幂等性）**：
    
    - **问题场景（真实案例）**：
      Consumer处理完消息，正准备提交Offset时突然崩溃。重启后Kafka认为这条消息没处理过，又发了一遍。结果同一份文件被解析了两次，向量库里出现重复数据。
    
    - **解决方案详解**：
      
      1. **幂等性设计（推荐）**
         - *原理*：让业务逻辑本身具备"执行多次结果一样"的特性。
         - *实现方式*：
           - 数据库唯一索引：文件解析结果插入时，`fileId + chunkIndex`作为唯一键，重复插入会报错，捕获异常后跳过。
           - Redis SetNX：`SET fileId:chunkIndex processed EX 86400`，处理前检查是否已存在。
           - 状态机控制：文件状态流转（PENDING → PARSING → DONE），只有PENDING才能执行，DONE状态直接返回。
         - *RAG项目应用*：MinIO存储文件时文件名包含MD5，重复上传直接覆盖；ES索引时文档ID用`fileId_chunkIndex`，天然去重。
      
      2. **手动提交Offset（精确控制）**
         - *代码逻辑*：
           ```java
           while (true) {
               ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
               for (ConsumerRecord<String, String> record : records) {
                   try {
                       process(record);           // 先处理业务
                       commitSync(record.offset()); // 成功后再提交
                   } catch (Exception e) {
                       log.error("处理失败", e);
                       // 不提交Offset，下次重试
                   }
               }
           }
           ```
         - *权衡*：保证不丢失，但可能重复（处理成功但提交失败时）。
      
      3. **事务消费（Exactly-Once，最严格）**
         - *原理*：将"消息处理"和"Offset提交"放在同一个事务中，要么都成功，要么都回滚。
         - *代码示例*：
           ```java
           producer.initTransactions();
           while (true) {
               ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
               producer.beginTransaction();
               for (ConsumerRecord<String, String> record : records) {
                   producer.send(process(record)); // 处理并发送结果
               }
               producer.sendOffsetsToTransaction(
                   consumer.position(consumer.assignment()), 
                   consumer.groupMetadata()
               ); // 提交Offset也是事务的一部分
               producer.commitTransaction(); // 原子提交
           }
           ```
         - *适用场景*：金融交易、订单处理等强一致性场景。
         - *性能代价*：吞吐量下降约20-30%，RAG项目一般不需要这么严格。
    
    ---
    
    **防消息丢失（全链路保障）**：
    
    - **Producer端配置（第一道防线）**：
      - `acks=all`：等待所有ISR副本确认（而不仅仅是Leader），确保数据已复制到多个Broker。
      - `retries=3` & `retry.backoff.ms=1000`：网络抖动时自动重试3次，间隔1秒。
      - `enable.idempotence=true`：开启幂等性，即使重试也不会产生重复消息（通过PID和Sequence Number去重）。
      - `buffer.memory=33554432`（32MB）：增大发送缓冲区，避免Producer被阻塞。
      - `compression.type=lz4`：压缩消息，减少网络传输量和存储空间。
    
    - **Broker端配置（第二道防线）**：
      - `min.insync.replicas=2`：要求至少2个副本同步才认为写入成功（配合acks=all）。
      - `unclean.leader.election.enable=false`：禁止非同步副本成为Leader，避免数据丢失。
      - `log.flush.interval.messages=10000`：每10000条消息强制刷盘。
      - `log.flush.interval.ms=1000`：每秒强制刷盘（权衡性能与持久化）。
      - `replication.factor=3`：每个Partition保存3个副本，容忍2个节点故障。
    
    - **Consumer端配置（第三道防线）**：
      - `enable.auto.commit=false`：关闭自动提交，改为手动控制。
      - 处理流程：**拉取消息 → 业务处理 → 持久化结果 → 提交Offset**。
      - 禁止：**拉取消息 → 提交Offset → 业务处理**（此时崩溃会导致消息丢失）。
      - `max.poll.records=500`：批量拉取，减少网络往返，但单次处理时间不能过长（避免触发rebalance）。
    
    ---
    
    **消息堆积处理（应急与预防）**：
    
    - **监控预警（防患于未然）**：
      - *指标*：Consumer Lag（待消费消息数）、消费速率（messages/sec）、处理延迟（latency）。
      - *工具*：Kafka Manager、Prometheus + Grafana、Burrow（专门监控Lag）。
      - *告警策略*：Lag > 10000 触发警告，> 50000 触发紧急告警，自动通知运维。
      - *RAG项目实践*：文件上传高峰期（如批量导入100个文档），Lag可能瞬间飙升，需要提前扩容Consumer。
    
    - **扩容Consumer（立竿见影）**：
      - *原理*：Consumer数量 ≤ Partition数量，增加Consumer可线性提升消费能力。
      - *操作*：Kubernetes HPA（Horizontal Pod Autoscaler）根据CPU/Lag自动扩缩容。
      - *限制*：如果Partition只有3个，Consumer增加到5个，多出来的2个会空闲。
      - *RAG项目经验*：提前根据业务高峰创建足够多的Partition（如12个），平时用3个Consumer，高峰时扩容到12个。
    
    - **优化消费逻辑（挖潜）**：
      - *异步处理*：收到消息后丢到线程池异步执行，主线程立即拉取下一条。
      - *批量处理*：`max.poll.records=1000`，一次性拉取1000条，批量写入数据库（JDBC batch insert）。
      - *减少IO*：合并多次Redis操作为Pipeline，合并多次ES索引为Bulk API。
      - *RAG项目优化*：文件解析结果先攒批，每50个chunk一次性写入ES，吞吐量从100 msg/s提升到2000 msg/s。
    
    - **跳过非关键消息（断尾求生）**：
      - *场景*：日志收集、非关键通知，允许丢失部分数据。
      - *操作*：`consumer.seekToEnd()`直接跳到最新消息，或重置Offset到特定时间点后。
      - *风险*：数据丢失，仅适用于可容忍丢失的场景。
    
    - **分流处理（削峰填谷）**：
      - *方案1*：堆积消息导出到临时Topic `file-parse-topic-backup`，原Consumer只处理实时消息，另起慢速Consumer处理备份Topic。
      - *方案2*：消息暂存Redis List，Worker从Redis取数据异步处理，Kafka快速消费不落库。
      - *RAG项目应用*：大文件解析慢，小文件解析快，拆分两个Topic分别处理，避免大文件阻塞小文件。
    
    ---
    
    **消息顺序消费（分布式难题）**：
    
    - **全局有序 vs 局部有序**：
      - *全局有序*：所有消息按严格时间顺序处理（单Partition + 单Consumer），性能最差但最简单。
      - *局部有序*：同一业务Key的消息有序（如同一用户、同一文件），不同Key之间无序，性能与顺序的平衡。
    
    - **实现局部有序**：
      - *Producer端*：按Key哈希选择Partition，`partition = hash(fileId) % partitionNum`。
      - *Consumer端*：单线程消费保证Partition内顺序；多线程时需按Key路由到同一线程（如`fileId % threadNum`）。
    
    - **RAG项目实战**：
      - *需求*：一个文件分10个chunk，必须按chunk 0-9顺序解析，否则向量入库会乱。
      - *方案*：Producer按`fileId`分区，同一文件的所有chunk进同一Partition；Consumer单线程顺序处理每个Partition。
      - *扩展*：如果有1000个文件并发处理，启动10个Consumer，每个Consumer处理100个文件的顺序流，整体吞吐量提升10倍且单文件内仍有序。

  - 📌 **RAG 文件上传与解析 - 面试标准答案整理（派聪明项目）**
    
    **Q1: 大文件上传的技术方案是什么？**
    
    - **核心方案**：分片上传 + 断点续传
    - **具体流程**：
      1. **前端分片**：大文件切成 5MB 小块，并发上传
      2. **后端存储**：分片存入 MinIO，路径格式 `chunks/{fileMd5}/{chunkIndex}`
      3. **状态记录**：Redis Bitmap 记录每个分片上传状态（0/1）
      4. **断点续传**：网络中断后，前端查询 Redis 状态，跳过已上传分片
      5. **合并文件**：所有分片上传完成后，调用 MinIO `composeObject` 在存储端合并（不占服务器资源）
      6. **异步处理**：发送 Kafka 消息，触发文件解析、文本切片、向量化
    - **关键优势**：上传接口快速响应，耗时任务异步处理，用户体验好
    
    **Q2: 如何识别分片属于哪个文件？**
    
    - **文件标识**：前端计算文件 MD5 哈希值作为唯一标识 `fileMd5`
    - **分片标识**：每个分片携带 `fileMd5` + `chunkIndex`（分片序号）
    - **存储结构**：MinIO 中按 `chunks/{fileMd5}/{chunkIndex}` 组织，确保归属正确
    - **合并顺序**：按 `chunkIndex` 顺序通过 `composeObject` 合并
    
    **Q3: 断点续传的具体实现？**
    
    - **Redis Bitmap 记录状态**：
      - Key: 文件 MD5
      - Value: Bitmap，第 n 位表示第 n 个分片是否上传成功（1=成功，0=未上传）
    - **续传流程**：
      1. 网络恢复后，前端携带 `fileMd5` 查询 Redis
      2. 获取已上传分片列表
      3. 跳过已上传分片，只上传缺失分片
      4. 后端再次核验（防篡改）
    - **内存计算**：100 万个分片仅需约 122KB 内存（1,000,000 bits / 8 / 1024）
    
    **Q4: 为什么选择 Redis 而不是 MySQL 存储分片状态？**
    
    | 维度 | Redis (Bitmap) | MySQL |
    |-----|---------------|-------|
    | **性能** | 内存操作，O(1) 时间复杂度 | 磁盘 IO，慢 |
    | **数据特性** | 临时数据，合并后即失效 | 持久化存储，没必要 |
    | **内存占用** | 100万分片 ≈ 122KB | 每条记录占用大 |
    | **写入压力** | 高频写入无压力 | 高并发写入会成为瓶颈 |
    | **适用性** | 纯状态标记，适合 Bitmap | 关系型数据才适合 |
    
    - **结论**：分片上传是高频、临时的状态标记场景，Redis Bitmap 是最优解
    
    **Q5: 临时分片存储在哪里？为什么选择 MinIO？**
    
    - **存储选择**：MinIO（对象存储）
    - **选择原因**：
      1. **大文件友好**：适合大文件、多分片场景
      2. **高并发读写**：支持多分片并发上传
      3. **S3 协议兼容**：标准接口，易于维护
      4. **composeObject API**：服务端直接合并分片，不占应用服务器 CPU/内存
    
    **Q6: 分片上传与断点续传中 Redis 和 MinIO 的核心角色？**
    
    | 组件 | 核心角色 | 具体职责 |
    |-----|---------|---------|
    | **Redis** | 状态管理器 | Bitmap 记录分片上传状态，支持断点续传查询，O(1) 高性能 |
    | **MinIO** | 存储引擎 | 持久化存储分片，支持高并发读写，提供 composeObject 合并能力 |
    | **配合** | 协同工作 | Redis 管"哪些有了"，MinIO 管"实际存哪了"，两者缺一不可 |
    
    **面试官追问应对（结合之前拷打问题）**：
    
    - **"如果 MinIO 挂了怎么办？"**
      - 部署 MinIO 集群（分布式模式），多副本保证高可用
      - 或者使用云厂商对象存储（阿里云 OSS、AWS S3）作为备选
    
    - **"如果 Redis 挂了，断点续传还能工作吗？"**
      - 不能精准续传，但可以通过 MinIO 列出已上传分片来恢复（兜底方案）
      - 或者使用 Redis Cluster / Sentinel 保证 Redis 高可用
    
    - **"分片上传过程中，用户刷新页面怎么办？"**
      - 文件 MD5 不变，刷新后重新计算 MD5，查询 Redis 状态，自动续传
      - 用户体验："继续上传"而不是"重新上传"

- [x] 💻 **算法**
  - 二叉树公共祖先
  - 矩阵最大面积  其实猜到了是单调栈 但是有点出错 没写出来 菜啊…
  - > **⏱️ 耗时**：1.5h
  
- [x] 📝 **其他**
  - 📧 **求职活动**：投递了2份简历（腾讯音乐、搜狐），刚开就投 有点慌…
  - 🔄 简历整理**：根据JD调整简历措辞，突出高并发和分布式经验

> **⏱️ 总时长**：8h

---

## Day 17 (02-27)

### 🧠 思维导图
- [x] **复习巩固**：回顾 Kafka 核心概念与 RAG 项目架构

### ✅ 今日任务

- [x] **八股文 (Kafka 深度复习 + 分布式理论)**
  
  - **Kafka 副本机制 (Replication)**
    
    - **ISR (In-Sync Replicas)**：与 Leader 保持同步的副本集合。
    - **ACK 机制**：
      - cks=0：Producer 不等待确认，吞吐量最高，可能丢失数据。
      - cks=1：等待 Leader 确认，Leader 挂掉可能丢数据。
      - cks=all：等待所有 ISR 副本确认，数据最安全，延迟最大。
    - **Leader 选举**：ISR 列表为空时，配置 unclean.leader.election.enable 决定是否允许非同步副本成为 Leader（可用性 vs 一致性权衡）。
    
  - **Kafka 高性能原理**
    - **顺序写磁盘**：比随机写内存还快，充分利用磁盘预读。
    - **零拷贝 (Zero-Copy)**：sendfile() 系统调用，数据直接从 PageCache 发送到网卡，减少 2 次用户态/内核态切换。
    - **PageCache 机制**：利用 OS 缓存，消费时直接从缓存读，不读磁盘。
    - **批量处理**：Producer 批量发送 (atch.size)、Consumer 批量拉取 (max.poll.records)。
  
  - **分布式理论基础**
    
    - **CAP 定理**：Consistency (一致性)、Availability (可用性)、Partition Tolerance (分区容错性) 三者不可兼得，最多满足两项。
      - **CP 系统**：ZooKeeper、etcd、Consul（优先保证一致性）。
      - **AP 系统**：Eureka、Cassandra（优先保证可用性）。
    - **BASE 理论**：Basically Available (基本可用)、Soft state (软状态)、Eventually consistent (最终一致性)，是 AP 系统的实践指导。
    
  - **📌 场景复盘：Canal 同步延迟应急方案**
    
    **问题背景**：
    面试官追问："Canal 延迟 5 分钟，用户发布文章后搜不到，怎么办？"
    
    **核心思路**：引入"写后即读"一致性缓存
    
    不要等 Canal 慢慢同步。在文章发布成功的瞬间，利用 Redis 构建一个 "热数据搜索补丁"。
    
    **Step 1：双写热块（Write-to-Cache）**
    在发布文章逻辑中，除了写入 MySQL，同步将文章的 **搜索关键词（Title/Tags）和元数据（ID）** 写入 Redis 的一个 ZSet 中。
    - Key: search:hot_patch:{userId} 或 search:global_hot_patch
    - Score: 时间戳 timestamp
    - TTL: 设置 1 小时（覆盖 Canal 的延迟时间）
    
    **Step 2：聚合查询（Read-Merge）**
    在搜索接口中，代码逻辑改为：
    1. 调用 ES 进行常规搜索，拿到结果列表 List_ES
    2. 根据用户关键词，同步查询 Redis 里的"补丁块"，拿到 List_Redis
    3. 在内存中进行去重合并（如果 ID 相同，以 Redis 为准，因为它是最新的）
    
    **Step 3：降级开关（Circuit Breaker）**
    
    - 前端感知延迟
    - 如果监控发现 Canal 延迟超过阈值，在搜索界面提示："部分新发布内容可能稍后可见"
    - 自动触发上述的 Redis 补丁逻辑
    
    **避坑指南（面试加分点）**：
    
    1. **不要查从库/主库**：绝对禁止在搜索接口里用 LIKE %关键词% 去刷 MySQL，一旦搜索量大，主库瞬间会被查挂，引发全站雪崩
    2. **数据量控制**：Redis 补丁只存最近 1 小时的数据。数据量大了以后，内存成本和聚合排序的 O(N log N) 开销会拖慢接口响应
    3. **版本控制**：最好带上文章的版本号或 update_time，防止 Canal 恢复后，旧数据覆盖了 Redis 里的新状态
    
  - **📚 项目广度拷打总结笔记**
    
    **1. 消息中间件：吞吐量与灵活性的博弈**
    
    | 特性 | Kafka (RAG项目使用) | RabbitMQ (社区项目使用) |
    |-----|-------------------|------------------------|
    | **设计动机** | 针对日志流、大数据量设计，追求极致吞吐 | 针对企业级业务逻辑设计，追求灵活路由 |
    | **底层原理** | 顺序写磁盘、零拷贝、Partition 并发 | 基于 AMQP 协议，支持多种 Exchange 路由模式 |
    | **你的选型理由** | 1GB 文件分片上传，对磁盘 IO 和带宽压力大，Kafka 的 Partition 能有效平衡负载 | 社交行为（点赞/通知）业务复杂，需根据不同行为路由到不同队列，且需要可靠的 ACK 保证不丢 |
    
    **2. 检索架构：ES 是银弹吗？**
    
    - **ES 存在的意义**：解决 MySQL 无法高效处理的"分词搜索"和"相关度评分"
    - **RAG 中的瓶颈**：ES 存储高维向量（如 2048 维）时，底层 HNSW 索引极度消耗内存（Page Cache）
    - **进阶决策**：当数据规模过亿时，应考虑 **ES (元数据/文本) + Milvus (向量分片)** 的组合拳
    
    **3. 二级缓存：数据一致性的"最后公里"**
    
    - **更新策略**：采用 Cache-Aside Pattern（先更 DB，再删缓存）
    - **本地缓存同步**：
      - **现状**：采用了广播机制
      - **局限**：节点多时会造成"广播风暴"
      - **优化**：给本地缓存（Caffeine）增加一个很短的 Random TTL。即使不收到同步信号，它也会自然过期，利用 Redis 的数据进行兜底
    
    **4. 上下文安全：ThreadLocal 的艺术与深坑**
    
    - **核心作用**：解决代码入侵性。不用在 Service 每层方法都传 UserDTO
    - **线程池陷阱**：线程池会复用线程。如果不手动 remove()，线程 A 的用户信息会被线程 B 继承
    - **最佳实践**：结合 AOP + Spring Security。在 finally 块中执行 SecurityContextHolder.clearContext()，这是防止权限穿透的工业级标准做法
  
- [x] **算法 **
  
  - 复习了快排
  
- [x] **其他**
  
  - 课内作业考试

> **⏱️ 总时长**：6.5h

---

## Day 18 (03-02) 内容

- [x] **🎯 求职投递（策略优化版）**
  
  - **已投递**：
    - **携程 (Ctrip)** - 暑期实习 ⏳ 等待反馈
    - **好未来 (TAL)** - 教育科技赛道，Java后端岗位
    - **慧策 (Huice)** - 电商 SaaS 方向，Java开发岗位
  
  - **📊 投递策略复盘（关键认知升级）**：
    - **问题识别**："刚开就投"风险高 - 春招/暑期实习开放初期，简历筛选标准严格，容易成为"炮灰"
    - **优化策略**：
      1. **错峰投递**：关注截止日期，避开高峰期（建议开放后 3-5 天再投）
      2. **简历迭代**：根据挂掉的反馈持续优化项目描述和关键词
      3. **内推优先**：找学长/学姐内推，简历直推部门，通过率更高
      4. **海投+精投结合**：保底岗位海投，心仪岗位针对 JD 精修后投递
      5. **STAR 法则强化**：项目描述突出 Situation-Task-Action-Result
  
  - **⏱️ 耗时**：30min

- [x] **📚 八股文深度整理**
  
  - **🚀 Kafka 分布式理论**
    - **ISR (In-Sync Replicas)**：与 Leader 保持同步的副本集合，权衡可用性与一致性
    - **零拷贝 (Zero-Copy)**：`sendfile()` 技术原理 - PageCache → 网卡，减少 2 次用户态/内核态切换，提升吞吐量
    - **CAP 定理实践**：CP 系统（ZooKeeper，强一致性）vs AP 系统（Eureka，高可用），根据业务场景选型
  
  - **🔍 ES Hybrid Search 深度理解**
    - **召回流程**：
      1. **语义粗排**：KNN 向量检索，召回 `topK * 30` 候选集（扩大召回）
      2. **关键词过滤**：BM25 筛选含关键词文档（减少无相关召回）
      3. **精排 Rescore**：BM25 权重 1.0 + 向量相似度权重 0.2（A/B 测试调优）
    - **准确性评估**：通过 A/B 测试动态调整权重配比，观察 CTR 和用户满意度
  
  - **🔐 RBAC 权限架构（Spring Security + JWT）**
    - **认证流程**：
      1. `UserDetailsService.loadUserByUsername()` 查询用户 + 权限集合
      2. 封装为 `UserDetails`（用户ID、密码、权限列表）
      3. 首次登录：生成 JWT Token（Access + Refresh）返回客户端
      4. 后续请求：Header 携带 Token → 服务端解析 → 加载到 SecurityContext
    - **安全机制**：
      - Token 过期机制（Access 15min，Refresh 7天）
      - `@PreAuthorize` 注解权限校验
      - **分布式方案**：SecurityContext 只作用于单个请求，Redis 中额外存储用户信息（JSON字符串）用于跨请求共享
  
  - **🗄️ MySQL 执行原理与优化**
    - **执行流程**：连接器 → 分析器（词法/语法分析）→ 优化器（选择索引）→ 执行器 → 存储引擎 → 返回结果
    - **Explain 分析四要素**：
      - `key`：实际使用的索引
      - `type`：访问类型（`ref` 非唯一索引✅，`all` 全表扫描❌）
      - `extra`：额外信息（`Using index` 覆盖索引✅，`Using where` 回表❌）
      - `rows`：扫描行数（越少越好）
  
  - **💬 消息队列高频问题（面试准备）**
    - **消息确认机制**：
      - 消费者手动 ACK，处理成功后确认
      - 失败进入死信队列 (DLQ) 或重试
    - **队列挂了怎么办**：
      - Kafka：Partition 多副本机制，Leader 挂了 ISR 重新选举
      - RabbitMQ：镜像队列，主从切换
    - **持久化 vs 吞吐量权衡**：
      - 持久化（刷盘）会降低吞吐量
      - 解决方案：批量刷盘、异步刷盘、或接受部分消息丢失的场景下不持久化
  
  - **⏱️ 耗时**：2h

- [x] **📝 课内作业**
  - 完成学校课程相关作业任务
  - **⏱️ 耗时**：2h

- [x] **💻 算法练习（单调栈专题突破）**
  - **题目**：最大矩形面积（LeetCode 84）
  - **解法**：单调栈（Monotonic Stack）
    - **核心思路**：遍历每个柱子，找左右第一个比它小的边界，计算面积
    - **优化技巧**：哨兵法（数组首尾加 0）简化边界处理，避免特判
    - **复杂度**：时间 O(N)，空间 O(N)
  - **状态**：✅ 已完成（之前未写出，今日彻底掌握！）
  - **⏱️ 耗时**：30min

> **⏱️ 总时长**：7h（已修正）

---
## Day 19 (03-03)

### ✅ 今日任务

- [x] **携程笔试测评** - 完成北森题库测评

- [x] **OpenClaw 配置** - 配置飞书多 Agent 环境

- [x] **RAG 项目拷打准备** (深度)

  **Q1: 1GB 文件优化 (15s → 3s)**
  - **瓶颈定位**: Arthas 追踪 → 70% 单线程解析 + 20% 网络传输
  - **核心优化**:
    - 5MB 分片 + MinIO Multipart Upload (MD5 秒传去重)
    - Kafka 异步流水线解耦四阶段 (上传/解析/向量化/入库)
    - Redis Bitmap 记录分片状态 (O(1) 查询)
  - **拷打点**: 5MB 分片依据? 对比 1/5/10MB 压测，平衡并发与开销

  **Q2: Kafka 选型理由**
  - **对比**: RabbitMQ (万级 TPS，复杂路由) vs Kafka (百万级 TPS，简单模型)
  - **设计细节**:
    - Partition = Consumer × 2，避免热点
    - `acks=all` + `min.insync.replicas=2` 保证不丢
    - 死信队列处理失败消息
  - **拷打点**: Partition 只能增不能减，扩缩容会导致顺序性问题

  **Q3: ES 向量检索降级方案**
  - **熔断策略**: 连续失败 5 次 → Hystrix 切换到纯 BM25
  - **兜底方案**: Redis 缓存热点结果，保证服务可用率 85%
  - **拷打点**: 降级后用户体验? 相关性下降但服务不中断

  **Q4: RBAC 权限控制**
  - **模型**: RBAC + 数据标签 (org_id + visibility)
  - **实现**: JWT + ThreadLocal 存储用户 → ES Filter 强制注入权限条件
  - **拷打点**: ThreadLocal 如何减少 30% 查询? 同请求内存缓存避免重复鉴权

- [x] **算法复习**
  - 单调栈（最大矩形面积）- 哨兵法优化边界处理，时间 O(N)，空间 O(N)

- [x] **暑期实习投递** - 根据 STAR 优化简历描述，投递 2-3 家

> **⏱️ 总时长**：4.5h

## Day 20 (03-04)

### ✅ 今日任务

- [x] **简历微调**
  - 根据 STAR 法则优化项目描述措辞
  - **⏱️ 耗时**：30min
- [x] **算法：最长有效括号 (Hard)**
  - **初始思路错误**：尝试用滑动窗口计数左右括号数量，忽略了嵌套结构的复杂性
  - **正确解法**：单调栈 (Monotonic Stack)
    - **核心思想**：栈底保存最后一个无法匹配的右括号下标作为"基准"
    - **流程**：
      1. 遇到 '(' ：下标入栈
      2. 遇到 ')' ：弹出栈顶；若栈空，当前下标入栈作为新基准；否则计算长度 `i - stack.peek()`
    - **边界处理**：
      - 初始化栈压入 -1（处理从0开始的合法子串）
      - 连续右括号时正确更新基准
    - **复杂度**：时间 O(N)，空间 O(N)
  - **⏱️ 耗时**：30min
- [x] **求职投递**
  - 投递 2 家公司
  - **慧策状态**：简历挂，核查发现可能是暑期实习未正式开放（可能投递的是去年岗位，无 HC）
  - **⏱️ 耗时**：30min
- [x] 课程作业
  - **⏱️ 耗时**：2h
- [x] 八股文
  - 缓存一致性 以及jvm调优实际体验

在图书馆也坐了六个小时但是感觉有效时长其实也不多 最近正反馈太少了也有点迷茫 复盘的时候总感觉也没干什么 争取调整一下状态了 

> **⏱️ 总时长**：4h



## Day 21 (03-05)

### ✅ 今日任务

- [x] **RAG 项目复习** (40min)
  
  - **RBAC 权限流程回顾**
    - 认证流程: UserDetailsService → UserDetails封装 → JWT生成 → 后续请求解析
    - 权限校验: JWT解析 → ThreadLocal存储 → ES Filter注入权限条件
    - 优化点: 同请求内存缓存减少30%重复鉴权查询
  
  - **文件上传解析幂等性**
    - 幂等性保证: MD5哈希去重 + Redis分布式锁
    - 前端多线程: 5MB分片并行上传，MinIO Multipart Upload
    - 异步流水线: Kafka四阶段解耦（上传/解析/向量化/入库）
    - 状态管理: Redis Bitmap O(1) 查询分片状态
  
- [x] **算法: 堆排序**
  - **核心思想**: 完全二叉树结构，父节点 >= 子节点（大顶堆）
  - **流程**: 构建堆 → 交换堆顶与末尾 → 调整堆 → 重复
  - **复杂度**: 时间 O(N log N)，空间 O(1)
  - **适用场景**: Top K 问题、优先队列实现、数据流的中位数

- [x] **八股文温故知新**

  **Q1: 为什么不推荐使用 JDK 的 Executors 创建线程池？**
  
  | 方法 | 问题 |
  |------|------|
  | `newFixedThreadPool` | 使用无界队列 LinkedBlockingQueue，任务堆积导致 OOM |
  | `newCachedThreadPool` | 允许创建 Integer.MAX_VALUE 个线程，线程数暴增导致 OOM |
  | `newSingleThreadExecutor` | 同样使用无界队列，任务堆积 OOM |
  
  **推荐做法**: 使用 `ThreadPoolExecutor` 手动配置，指定有界队列
  
  ---
  
  **Q2: 线程池的核心线程数和最大线程数怎么设定？**
  
  | 任务类型 | 公式 | 说明 |
  |----------|------|------|
  | **CPU密集型** | `N + 1` | N = CPU核数，+1防止页缺失等待 |
  | **IO密集型** | `2N` 或 `N/(1-阻塞系数)` | 阻塞系数 0.8~0.9 |
  
  ---
  
  **Q3: synchronized 的原理实现是什么？**
  
  1. **Java 层面**: `synchronized` 关键字
  2. **字节码层面**: `monitorenter` / `monitorexit` 指令
  3. **JVM 层面**: Monitor 对象（监视器锁）
  4. **OS 层面**: Mutex Lock（互斥锁）
  
  **锁升级过程**: 无锁 → 偏向锁 → 轻量级锁 → 重量级锁
  
  | 锁类型 | 场景 | 原理 |
  |--------|------|------|
  | **偏向锁** | 单线程反复进入 | Mark Word 记录线程ID，无CAS |
  | **轻量级锁** | 低竞争 | CAS 自旋，避免内核态切换 |
  | **重量级锁** | 高竞争 | 内核态 Mutex，线程阻塞唤醒 |

> **⏱️ 总时长**: ~3h
> 
> **状态**: 有效时长偏低，需要调整状态，增加正反馈

---

## Day 22 (03-06)

### ✅ 今日任务

- [x] **🔧 JMeter 压测实操入门** （工具配置 - 简单记录）

  **环境修复**
  - Redis 密码问题排查：实际密码 `PaiSmart2025`，配置文件 `application-dal.yml` 已修复
  - 项目本地启动成功

  **压测配置**
  - 线程数: 10
  - Ramp-up: 5秒
  - 循环次数: 10
  - 目标接口: `http://localhost:8080/` (首页)

  **压测结果**
  | 指标 | 数值 | 说明 |
  |------|------|------|
  | Throughput | 18.8/sec | QPS (每秒处理请求数) |
  | Average | 100ms | 平均响应时间 |
  | Min/Max | 72ms/185ms | 响应时间范围 |
  | Error % | 0% | 无错误 |
  | Samples | 100 | 总请求数 |

  **关键认知**
  - 单机 10 线程压不出 3000 QPS，简历里的 3000+ 是集群压测数据
  - 单机合理范围: 200-400 QPS (有缓存)
  - JMeter 报告位置: 左侧树 → 聚合报告 / 察看结果树

  **面试防御话术**
  > "3000+ 是集群压测数据，4 台 4核8G + Nginx 负载均衡，JMeter 2000 并发跑 5 分钟。单机本地压测大概 200-400，看缓存情况。"

  **⏱️ 耗时**: 1.5h

- [x] **📚 简历数据拷打准备** （八股文/面试 - 详细展开）

  ### 6 个量化数据逐个拆解

  | 数据 | 拷问方向 | 回答要点 |
  |------|---------|---------|
  | **15s→3s** | 异步处理时间优化 | 分片并行+批量向量生成+ES bulk |
  | **30% 鉴权优化** | AOP 统计验证 | ThreadLocal 复用，3.2→2.2 次查询 |
  | **QPS 3000+** | 压测口径澄清 | 集群压测：4台+Nginx，单机200-400 |
  | **4s→1s** | 服务端 RT 优化 | Caffeine+Redis 二级缓存 |
  | **60 倍导出优化** | 多线程方案 | FastExcel+8线程+分页1000 |
  | **280ms→50ms** | 异步解耦设计 | MQ 发消息即返回，消费端批量处理 |

  ### 诚实边界话术

  > "这个数据是去年项目里的，具体数字我可能记不太清，大概是 3000 左右。但我清楚当时的架构和优化手段，比如多级缓存怎么设计的、数据库连接池怎么调的，这些我可以详细讲。"

  ### 模拟面试 Q&A 整理

  #### Q1: IP 首部结构

  ```
  | 版本(4) | 首部长度(4) | 服务类型(8) | 总长度(16) |
  | 标识(16) | 标志(3) | 片偏移(13) |
  | TTL(8) | 协议(8) | 首部校验和(16) |
  | 源 IP 地址(32) |
  | 目的 IP 地址(32) |
  | 可选字段 + 填充 |
  ```

  - **版本**: IPv4/IPv6
  - **首部长度**: 单位是 4 字节，最小 5 (20字节)
  - **总长度**: IP 包总长度，最大 65535 字节
  - **TTL**: 生存时间，每经过一跳路由器减 1
  - **协议**: 上层协议类型 (TCP=6, UDP=17, ICMP=1)

  #### Q2: Java IO 流体系

  | 分类维度 | 类型 | 代表类 |
  |---------|------|--------|
  | **方向** | 输入流 / 输出流 | InputStream / OutputStream |
  | **数据单位** | 字节流 / 字符流 | FileInputStream / FileReader |
  | **功能** | 节点流 / 处理流 | FileInputStream / BufferedInputStream |

  **关键区别**: 

  - 字节流: 操作二进制数据，继承自 InputStream/OutputStream
  - 字符流: 操作字符数据，自动处理编码，继承自 Reader/Writer
  - 处理流: 包装节点流，提供缓冲、转换等功能

  #### Q3: GDB 常见命令

  | 命令 | 作用 |
  |------|------|
  | `gdb <program>` | 启动调试 |
  | `break <line>` / `b` | 设置断点 |
  | `run` / `r` | 运行程序 |
  | `next` / `n` | 单步执行（不进入函数）|
  | `step` / `s` | 单步执行（进入函数）|
  | `continue` / `c` | 继续运行 |
  | `print <var>` / `p` | 打印变量值 |
  | `backtrace` / `bt` | 查看调用栈 |
  | `quit` / `q` | 退出 GDB |

  #### Q4: 二分查找最左边界

  ```java
  // 找第一个 >= target 的位置
  int left = 0, right = nums.length - 1;
  while (left <= right) {
      int mid = left + (right - left) / 2;
      if (nums[mid] < target) {
          left = mid + 1;
      } else {
          right = mid - 1;  // nums[mid] >= target，继续往左
      }
  }
  return left;  // 最左边界
  ```

  **关键**: `<=` 时继续往左收缩，最终 `left` 指向第一个 >= target 的位置

  #### Q5: 模拟字符串相加

  ```java
  StringBuilder sb = new StringBuilder();
  int i = num1.length() - 1, j = num2.length() - 1, carry = 0;
  while (i >= 0 || j >= 0 || carry != 0) {
      int sum = carry;
      if (i >= 0) sum += num1.charAt(i--) - '0';
      if (j >= 0) sum += num2.charAt(j--) - '0';
      sb.append(sum % 10);
      carry = sum / 10;
  }
  return sb.reverse().toString();
  ```

  **优化**: StringBuilder 可直接 append，最后 reverse，避免头部插入的 O(N) 开销

  #### Q6: Redis 大 Key 判断与解决

  **判断方法**:
  1. `redis-cli --bigkeys` : 扫描全库找出大 key
  2. `redis-cli --memkeys` : 分析内存占用
  3. `DEBUG OBJECT <key>` : 查看对象编码和序列化长度

  **解决方案**:
  | 方案 | 适用场景 | 操作 |
  |------|---------|------|
  | **拆分** | Hash / ZSet / Set | 按业务维度拆成多个小 key |
  | **压缩** | String 类型大 value | 使用 snappy / gzip 压缩后再存储 |
  | **本地缓存** | 热点大 key | Caffeine/Guava Cache 本地缓存，减少 Redis 读取 |
  | **异步删除** | 需要删除大 key | 使用 `UNLINK` 替代 `DEL`，后台异步删除避免阻塞 |

  **⏱️ 耗时**: 1h

- [x] **📋 其他事务** （日常事务 - 简单记录）
  
  - 课内事务处理
  - **⏱️ 耗时**: 1h

> **⏱️ 总时长**: ~3.5h
> 
> **状态**: JMeter 实操完成，简历数据拷打准备就绪
> **明日计划**: 继续投递简历，准备面试 STAR 回答

---

## Day 23 (03-09)

### ✅ 今日任务

- [x] **🎯 求职投递大爆发**

  - **已投递**（8家）：
    - **米哈游** - 初筛通过 ⏳ 待发测评（需认真准备）
    - **OPPO** - Java后端 ⏳ 等待反馈
    - **网易** - 后端开发 ⏳ 等待反馈
    - **百度** - 后端开发 ⏳ 等待反馈
    - **美团** - Java后端 ⏳ 等待反馈
    - **众安** - 后端开发 ⏳ 等待反馈
    - **360** - 后端开发 ⏳ 等待反馈
    - **京东** - ✅ 测评已完成（耗时1h）
    - **拼多多** - 📝 简历完善中

  - **暂缓投递**：
    - **腾讯**、**蚂蚁** - 信心不足，Shell/Linux 需补强后再投

  - **💡 关键发现**：JD 普遍要求 **Shell/Linux 命令**，技能缺口需补强

  - **⏱️ 耗时**：1h

- [x] **📝 京东测评完成**
  - 北森题库，耗时约1小时
  - 行测 + 性格测试组合
  - **状态**：已完成，等待面试通知

- [x] **📚 技能缺口识别**
  - **Shell/Linux 命令** - 高频出现在 JD 要求中
  - **待补内容**：常用命令、脚本编写、系统管理
  - **优先级**：高（影响腾讯/蚂蚁投递信心）

> **⏱️ 总时长**: ~2h
> 
> **状态**: 投递量大，需跟进测评 + 补 Shell/Linux
> **明日计划**: 准备米哈游测评，学习 Shell 基础命令

---

## Day 24 (03-10)

### 📌 关键事项
- **字节HR约面** - 已约面试，进入备战状态
- **拼多多笔试** - 3月15日，倒计时5天
- **注意TL** - 把握好时间线

---

### ✅ 今日任务

#### 1️⃣ Java 并发进阶
- [ ] **CompletableFuture**
  - 异步编程模型、组合操作（thenCompose/thenCombine）、异常处理（exceptionally/handle）
  - 与Future/Callable的区别，非阻塞回调优势
  - **耗时：0.5h**

- [ ] **CountDownLatch**
  - 倒计时门闩原理，await()等待count归零
  - 应用场景：主线程等待多个子线程完成（如多数据源并行加载）
  - **耗时：0.5h**

#### 2️⃣ Linux 基础补强 ⭐ 【高优先级】
- [ ] **常用命令**
  - 文件操作：`ls/cd/cp/mv/rm/find/chmod`
  - 进程管理：`ps/top/kill/nohup/jobs`
  - 网络诊断：`netstat/lsof/ping/curl/telnet`
  - 文本处理：`grep/awk/sed/wc/sort/uniq`
  - **耗时：1.5h**

- [ ] **Shell 脚本基础**
  - 变量定义、条件判断、循环结构、函数定义
  - 常用场景：日志切割、定时备份、服务启停脚本
  - **耗时：1h**

#### 3️⃣ MyBatis 核心
- [ ] **#{} vs ${}**
  - `#{}`：预编译占位符，防SQL注入，自动加引号
  - `${}`：直接字符串替换，有注入风险，用于动态表名/列名
  - **耗时：0.5h**

#### 4️⃣ 设计模式

- [ ] **状态机**
  - 实现方式：枚举定义状态 + 上下文类管理流转
  - 应用场景：订单状态流转（待支付→已支付→已发货→已完成）
  - **耗时：0.5h**

#### 5️⃣ 分布式基础
- [ ] **注册中心对比**

| 特性 | Nacos | ZooKeeper |
|-----|-------|-----------|
| 一致性协议 | Raft/AP模型 | ZAB/CP模型 |
| 健康检查 | 心跳+临时实例 | 临时节点 |
| 功能扩展 | 配置中心一体化 | 纯注册中心 |
| 适用场景 | 微服务云原生 | 大数据生态（Dubbo） |

- **耗时：0.5h**

#### 6️⃣ Redis 深度
- [ ] **常见数据结构及应用场景**

| 数据结构 | 应用场景 |
|---------|---------|
| String | 缓存、计数器、分布式锁、Session |
| Hash | 购物车、用户属性存储 |
| List | 消息队列、时间线、最新N条 |
| Set | 点赞/关注（去重）、共同好友、抽奖 |
| ZSet | 排行榜、延时队列、滑动窗口限流 |
| Bitmap | 用户签到、日活统计 |
| HyperLogLog | UV统计（基数估算）|
| Geo | 附近的人、地理位置计算 |

- [ ] **场景题：一亿用户排行榜实时排名更新**
  - 方案：ZSet（score=积分，member=用户ID）
  - 分区策略：按分数段分片（如0-1000分一个ZSet），减少单Key大小
  - 优化：本地缓存Top N，异步批量更新Redis
  - **耗时：1h**

#### 7️⃣ MQ 消息队列专题
- [ ] **消息丢失处理**
  - Producer端：`acks=all` + 失败重试 + 本地日志兜底
  - Broker端：多副本同步 + 刷盘策略优化
  - Consumer端：手动ACK，处理成功后才确认
  - **耗时：0.5h**

- [ ] **发送/消费失败处理**
  - 重试策略：指数退避（1s→2s→4s→8s），最大重试3-5次
  - 幂等性：唯一ID去重、数据库唯一索引、状态机控制
  - **耗时：0.5h**

- [ ] **死信队列（DLQ）**
  - 进入条件：重试耗尽、消息过期、队列满
  - 注意：不是失败一次就进，是重试耗尽后才进入
  - 处理：人工介入、告警通知、异步补偿
  - **耗时：0.5h**

#### 8️⃣ 多线程线程安全
- [ ] **线程安全实现方式**
  - `synchronized`：JVM层Monitor锁，锁升级机制
  - `Lock`：API层，支持公平锁/可中断/Condition分组
  - 原子类：`AtomicInteger`（CAS无锁）、`LongAdder`（高并发分段）
  - 不可变对象：`String`、final修饰
  - **耗时：0.5h**

- [ ] **线程池参数设置**
  - CPU密集型：`corePoolSize = N + 1`
  - IO密集型：`corePoolSize = 2N` 或 `N/(1-阻塞系数)`
  - **耗时：0.5h**

- [ ] **其他线程隔离方案**
  - `InheritableThreadLocal`：父子线程传递
  - `TransmittableThreadLocal`（TTL）：线程池场景传递
  - 方法参数显式传递：最安全但代码侵入
  - **耗时：0.5h**

#### 9️⃣ 定时任务优化
- [ ] **SpringTask定时不准解决方案**
  - 问题：单节点调度、任务阻塞导致延迟、无持久化
  - 方案：引入 **Redis ZSet 延迟队列**，用时间戳作为score
  - 流程：任务入ZSet → 轮询获取score≤当前时间的任务 → 执行 → 删除
  - **耗时：0.5h**

---

### ⏱️ 时长统计

| 模块 | 预估耗时 |
|-----|---------|
| Java并发进阶 | 1h |
| Linux基础补强 | 2.5h |
| MyBatis核心 | 0.5h |
| 设计模式 | 0.5h |
| 分布式基础 | 0.5h |
| Redis深度 | 1h |
| MQ消息队列 | 1.5h |
| 多线程线程安全 | 1.5h |
| 定时任务优化 | 0.5h |

**⏱️ 总时长：约 6-7h**

---

### 🎯 优先级

**Linux 命令 > CompletableFuture > MQ 专题 > Redis 场景题**

### 📤 产出目标
每个知识点能用自己的话讲清楚，能举项目中的实际例子


---

## Day 26 (03-11)

### 📌 关键事项
- [ ] **携程笔试** - 3-12 北京时间 19:00（英国时间 11:00）
- [ ] **米哈游笔试** - 03-13 14:00（英国时间 06:00）
- [ ] **拼多多笔试** - 03-15 15:00（英国时间 07:00-09:00）

---

### ✅ 今日任务

#### 1️⃣ 八股文复习 - 系统大纲梳理

**📚 Java 核心知识点回顾**

| 模块 | 核心考点 | 掌握状态 |
|------|----------|----------|
| **JVM** | GC算法(CMS/G1/ZGC)、类加载机制、内存模型 | 🟢 熟练 |
| **并发** | AQS、线程池、锁升级、volatile/CAS | 🟡 需巩固 |
| **MySQL** | 索引优化、事务隔离、MVCC、Binlog/RedoLog | 🟢 熟练 |
| **Redis** | 数据结构、持久化、缓存异常、分布式锁 | 🟡 需巩固 |
| **Kafka** | ISR/ACK机制、零拷贝、消息不丢失 | 🟢 熟练 |
| **ES** | 倒排索引、Hybrid Search、Rescoring | 🟢 熟练 |

**重点复习内容**：
- **GC算法对比**：
  - CMS：标记-清除，低停顿但碎片多
  - G1：Region分区，可预测停顿
  - ZGC：颜色指针，亚毫秒级停顿
- **事务隔离级别**：RU/RC/RR/Serializable，RR解决幻读靠Next-Key Lock
- **缓存异常**：击穿(互斥锁/逻辑过期)、穿透(布隆过滤器)、雪崩(随机TTL)

**⏱️ 耗时：1h**

---

#### 2️⃣ 算法练习 - 牛客ACM模式适应

**🎯 练习目标**：熟悉笔试环境输入输出

**Scanner vs BufferedReader**：
| 方式 | 代码示例 | 适用场景 |
|------|----------|----------|
| **Scanner** | `Scanner sc = new Scanner(System.in); int n = sc.nextInt();` | 简单输入，代码简洁 |
| **BufferedReader** | `BufferedReader br = new BufferedReader(...); String[] s = br.readLine().split(" ");` | 大数据量，效率高 |

**注意点**：
- 笔试一般4道题，时间90分钟
- 第1、2题要快速AC（20分钟内）
- 注意处理多组输入 `while (scanner.hasNext())`

**⏱️ 耗时：30min**

---

#### 3️⃣ 携程往年笔试练习

**📊 题型规律总结**

| 题号 | 难度 | 题型 | 策略 |
|------|------|------|------|
| 第1题 | Easy | 暴力模拟/签到题 | 5分钟内AC |
| 第2题 | Easy-Med | 简单数论/数学 | 15分钟内AC |
| 第3题 | Medium | 二分/双指针/滑动窗口 | 30分钟内争取AC |
| 第4题 | Hard | DP/图论/高级数据结构 | 能做多少做多少 |

**今日完成**：2套往年笔试题
- ✅ 第一套：3/4题（第4题DP没做出来）
- ✅ 第二套：3/4题（第4题图论部分分）

**结论**：海笔难度适中，但需要快速AC前两题，为后面难题留时间。

**⏱️ 耗时：1.5h**

---

#### 4️⃣ Java 并发进阶 - 可重入锁深度

**🔒 ReentrantLock 原理**

```java
// AQS 核心字段
private volatile int state;  // 同步状态：0=未锁定，>0=重入次数

// 获取锁
final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        // 无锁状态，CAS尝试获取
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        // 重入：state++
        int nextc = c + acquires;
        if (nextc < 0) throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}

// 释放锁
protected final boolean tryRelease(int releases) {
    int c = getState() - releases;  // state--
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    if (c == 0) {  // 减到0才真正释放
        free = true;
        setExclusiveOwnerThread(null);
    }
    setState(c);
    return free;
}
```

**关键点**：
- `state` 用 `volatile` 修饰，保证可见性
- 重入时无需竞争，直接 `state++`
- 释放时 `state--`，到0才真正释放锁

**⏱️ 耗时：30min**

---

#### 5️⃣ 项目拷打准备 - 面试话术打磨

**🎯 核心问题**：介绍最有挑战性的项目

**STAR 法则准备**：

| 维度 | 内容 |
|------|------|
| **Situation** | RAG企业知识库项目，需要支持1GB+大文件上传解析 |
| **Task** | 优化上传解析流程，要求15s内完成1GB文件处理 |
| **Action** | 1) 5MB分片+MD5秒传去重 2) MinIO Multipart Upload 3) Kafka异步流水线解耦 4) ES批量写入优化 |
| **Result** | 从15s优化到3s，提升5倍；支持并发上传不阻塞 |

**技术难点与解决方案**：

| 难点 | 解决方案 |
|------|----------|
| 大文件切分与ES索引优化 | 5MB固定分片 → MinIO原子合并 → Kafka异步向量化 |
| 混合检索精排 | KNN粗排(topK*30) → BM25过滤 → Rescoring精排(权重调优) |
| RBAC权限控制 | JWT + ThreadLocal存储 → ES Filter注入权限条件 |

**延伸问题准备**：
- MySQL → ES 同步：Canal订阅Binlog → Kafka → ES更新
- MySQL CPU高排查：慢查询日志 → EXPLAIN分析 → 索引优化/连接数调整
- MQ消息堆积：监控Lag → 扩容Consumer → 批量消费优化

**⏱️ 耗时：40min**

---

#### 6️⃣ 未完成事项记录

**⏸️ 秒杀系统复习**（未开始）
- 库存扣减方案：Redis 预减 + MQ 异步下单 + MySQL 最终一致性
- 限流：令牌桶/漏桶算法
- 防超卖：数据库唯一索引/乐观锁/Redis Lua原子扣减

**⏸️ 订单一致性**（未开始）
- 本地消息表方案
- 事务消息（RocketMQ/Kafka事务）
- 最大努力通知
- TCC 事务

---

### 💭 今日反思

**有效时长**：约 4h（偏低）

**主要问题**：
1. 笔试准备占用了大量时间（2h+）
2. 八股复习深度不够，多为大纲梳理
3. 秒杀系统和分布式事务未完成

**调整方向**：
1. 米哈游笔试前再突击一下算法（重点：DP、图论）
2. 拼多多笔试前重点复习Java并发和Redis
3. 字节面试前必须完成项目拷打Q5-Q9
4. 明天开始早起，适应北京时间考试节奏

---

> **⏱️ 总时长：4h**  
> **状态**：笔试周，调整作息适应北京时间考试  
> **关键节点**：携程笔试倒计时1天（明天！）





---

## Day 27 (03-12)

### ✅ 今日任务

- [x] **笔试** ⏳ 等待结果
  
  **考试概况**:
  | 项目 | 内容 |
  |------|------|
  | 题型 | 4道编程题（ACM模式） |
  | 用时 | 2h |
  | 成绩 | A了2.2题 |
  
  **问题复盘与改进**:
  
  | 题号 | 难度 | 问题 | 改进方向 |
  |------|------|------|----------|
  | 第1题 | Easy | ✅ AC | 签到题，快速通过 |
  | 第2题 | Easy-Med | ⚠️ 数据范围不熟，边界处理卡壳 | 赛前复习`int/long`范围，提前准备溢出判断模板 |
  | 第3题 | Medium | ⚠️ DFS会写但时间不够，只写暴力 | 提高基础题速度，为难题留时间 |
  | 第4题 | Hard | ❌ 位运算，完全没思路 | 补充位运算专题：异或、与、或、移位技巧 |
  
  **核心教训**:
  1. **时间分配失衡**：前两题用了60分钟，后两题只剩30分钟
  2. **数据范围意识薄弱**：第2题应该是`long`却用了`int`导致溢出
  3. **位运算盲区**：需要专项突破（常用技巧：异或去重、lowbit取最右1、状态压缩）
  
  **⏱️ 耗时：2h**

- [x] **八股文复习**
  
  #### MySQL 索引与优化
  
  **1. 最左前缀索引**
  - **概念**：复合索引`(a,b,c)`，查询条件只要包含`a`就能用上索引
  - **顺序问题**：MySQL 5.7+ 优化器会自动调整条件顺序，**不需要严格按索引列顺序写WHERE**
  - **示例**：索引`(name,age,score)`
    - ✅ `WHERE age=20 AND name='hx'` → 能用索引（优化器调整顺序）
    - ✅ `WHERE name='hx'` → 能用索引（前缀匹配）
    - ❌ `WHERE age=20` → 不能用索引（缺少最左列）
  
  **2. 回表（Lookup）**
  - **定义**：通过二级索引查到主键，再通过主键查聚簇索引获取完整行数据
  - **图示**：
    ```
    二级索引(name) → 找到主键(id=100) → 聚簇索引 → 拿到完整行数据
    ```
  - **避免回表**：使用**覆盖索引**（查询列全在索引里）
    ```sql
    -- 索引(name,age)，查询只涉及这两列
    SELECT name, age FROM user WHERE name = 'hx';  -- 无需回表
    ```
  
  **3. 慢SQL排查四要素**
  
  | EXPLAIN字段 | 含义 | 优化目标 |
  |------------|------|----------|
  | `type` | 访问类型 | `system > const > eq_ref > ref > range > index > ALL`，避免`ALL` |
  | `key` | 实际使用的索引 | 确保不是`NULL` |
  | `rows` | 扫描行数 | 越小越好 |
  | `Extra` | 额外信息 | 避免`Using filesort`、`Using temporary`，追求`Using index` |
  
  #### CPU 100% 排查思路
  
  **排查流程**：
  ```
  1. top → 找到PID（如Java进程占CPU 99%）
  2. top -Hp <PID> → 找到TID（具体线程）
  3. printf "%x\n" <TID> → 转16进制（如 0x4a3b）
  4. jstack <PID> > thread.dump → 导出线程栈
  5. grep -A 20 '4a3b' thread.dump → 定位具体代码
  ```
  
  **常见原因**：
  | 场景 | 特征 | 解决 |
  |------|------|------|
  | 死循环 | 某线程一直RUNNABLE | 修复循环条件 |
  | 频繁GC | GC线程占用高 | 调堆内存/优化对象创建 |
  | 正则回溯 | Pattern.match()卡顿 | 优化正则表达式 |
  | 线程竞争 | 大量BLOCKED状态 | 减少锁粒度/用并发容器 |
  
  **⏱️ 耗时：1h**

> **⏱️ 总时长：3h**  
> **状态**: 笔试完成，位运算需补强，明天米哈游笔试加油 🎉

---

## Day 28 (03-13)

### ✅ 今日任务

- [ ] **米哈游笔试** - 03-13 14:00（英国时间 06:00）
  - 状态：等待结果
  - **⏱️ 耗时：2h**

- [ ] **艾宾浩斯复习** - 遗忘曲线复习计划执行
  - 复习内容：Day 27/26/23/20/12 (1/2/4/7/15天前)
  - **⏱️ 耗时：1h**

> **⏱️ 总时长：3h**  
> **状态**: 米哈游笔试日，位运算专项补强

---

## Day 29 (03-14)

### ✅ 今日任务

- [x] 笔试 1.5h
- [x] **八股面经** - 模拟面试形式，查漏补缺
  - **⏱️ 耗时：2h**
- [x] **艾宾浩斯复习** - 遗忘曲线复习计划执行
  - 复习内容：Day 28/27/24/21/13 (1/2/4/7/15天前)
  - **⏱️ 耗时：1h**

> **⏱️ 总时长：3h**  
> **状态**: 笔试复盘日，重点补充JVM调优和MQ细节

---

### 📝 笔试自问自答（自我拷打）

#### 1. String 为什么设计为 final？

| 原因 | 说明 |
|------|------|
| **安全性** | 防止恶意篡改（如网络连接、文件路径等敏感操作依赖String） |
| **不可变性** | 创建后不可修改，天然线程安全，无需同步 |
| **哈希缓存** | hashCode()可缓存，作为HashMap/HashSet key时性能极高 |
| **字符串常量池** | 相同字符串共享同一对象，节省内存 |

> ✅ 回答正确，补充了哈希缓存的细节

---

#### 2. 生产者消费者模型的实现方式

**方式一：synchronized + wait/notify**
```java
synchronized(queue) {
    while(queue.isFull()) queue.wait();  // 生产者等待
    queue.put(item);
    queue.notifyAll();  // 唤醒消费者
}
```

**方式二：阻塞队列（推荐）**
```java
BlockingQueue<Item> queue = new ArrayBlockingQueue<>(100);
queue.put(item);  // 队列满时自动阻塞
queue.take();     // 队列空时自动阻塞
```

**常见阻塞队列对比**：

| 队列类型 | 特点 | 适用场景 |
|----------|------|----------|
| **ArrayBlockingQueue** | 有界数组，FIFO，需指定容量 | 固定大小的生产者消费者 |
| **LinkedBlockingQueue** | 链表实现，默认无界(Integer.MAX_VALUE) | 任务量不确定时 |
| **SynchronousQueue** | 不存储元素，直接传递 | 高效线程间通信 |
| **PriorityBlockingQueue** | 优先级排序 | 带优先级的任务调度 |
| **DelayQueue** | 延迟获取元素 | 定时任务调度 |

> ⚠️ **补充**：原回答只提到概念，补充了代码示例和5种阻塞队列的对比

---

#### 3. 单例模式实现要点

```java
public class Singleton {
    private static volatile Singleton instance;  // volatile禁止指令重排
    
    private Singleton() {}  // private构造器
    
    public static Singleton getInstance() {
        if (instance == null) {           // 第一次检查（无锁）
            synchronized(Singleton.class) {
                if (instance == null) {   // 第二次检查（有锁）
                    instance = new Singleton();  // 非原子操作，需volatile
                }
            }
        }
        return instance;
    }
}
```

**关键点**：

- `private` 构造器防止外部实例化
- `volatile` 禁止指令重排，避免返回未初始化对象
- **双重检查锁（DCL）**：减少锁粒度，提高性能

> ⚠️ **补充**：原回答过于简略，补充了完整代码和DCL解释

---

#### 4. CPU 100% 排查完整流程

```bash
# 1. 找到高CPU进程
$ top
#  PID USER  PR NI VIRT  RES  SHR S %CPU %MEM TIME+  COMMAND
# 1234 root  20  0 2.5g 1.2g  15m R 99.9  7.5 12:34 java

# 2. 找到具体线程（-H显示线程）
$ top -Hp 1234
#  PID  %CPU
# 5678 98.5

# 3. 线程ID转16进制（jstack中线程ID是16进制表示！）
$ printf "%x\n" 5678
# 162e

# 4. 导出线程栈并定位
$ jstack 1234 > thread.dump
$ grep -A 20 '162e' thread.dump
```

> 🔧 **纠正**：`printf "%x\n" <TID>` 的作用是将**10进制线程ID转为16进制**，因为jstack输出的线程ID是16进制格式（如`nid=0x162e`）。这是原回答中"不知道有什么用"的解答。

**常见原因**：
| 场景 | 特征 | 解决 |
|------|------|------|
| 死循环 | 某线程一直RUNNABLE | 修复循环条件 |
| 频繁GC | GC线程占用高 | 调堆内存/优化对象创建 |
| 正则回溯 | Pattern.match()卡顿 | 优化正则表达式 |
| 线程竞争 | 大量BLOCKED状态 | 减少锁粒度/用并发容器 |

> ✅ 常见原因回答正确

---

#### 5. OOM 排查完整攻略

**排查流程**：
```bash
# 1. 开启OOM时自动生成堆dump
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/path/to/dump.hprof

# 2. OOM发生后分析
$ jmap -dump:format=b,file=heap.hprof <pid>

# 3. 使用工具分析
- **VisualVM**：图形界面，适合初学者
- **Eclipse MAT**：专业分析工具，可定位内存泄漏
- **jhat**：JDK自带命令行工具
```

**MAT分析关键指标**：
| 指标 | 含义 | 关注点 |
|------|------|--------|
| **Dominator Tree** | 对象支配树 | 找出占用内存最大的对象 |
| **Histogram** | 类实例统计 | 哪些类实例数异常 |
| **Leak Suspects** | 泄漏嫌疑报告 | 自动分析可能的泄漏点 |

> 🔧 **纠正**：原回答"不太清楚具体概念"，补充了完整的排查流程和MAT分析指标

---

#### 6. MQ 选型详解

| 特性 | Kafka | RocketMQ | RabbitMQ |
|------|-------|----------|----------|
| **吞吐量** | 百万级TPS | 十万级TPS | 万级TPS |
| **延迟** | 高吞吐优先，延迟ms级 | 低延迟，ms级 | 微秒级 |
| **功能** | 基础功能 | 延迟队列、事务消息、顺序消息 | 路由灵活、插件丰富 |
| **场景** | 日志采集、大数据、流处理 | 电商交易、金融支付 | 企业集成、复杂路由 |

**Kafka "日志模型"解释**：
> Kafka将消息持久化为**日志文件**，每条消息追加写入磁盘末尾。消费者通过**offset**顺序读取，支持"回溯消费"（重新读取历史消息）。这种设计天然适合：
> - 日志收集（ELK栈）
> - 流式处理（Spark Streaming、Flink）
> - 事件溯源（Event Sourcing）

> 🔧 **纠正**：解释了用户"不太理解"的日志模型概念

---

#### 7. G1 GC 详细流程

**G1（Garbage First）特点**：
- 区域化内存管理（Region，1-32MB）
- 可预测停顿时间（`-XX:MaxGCPauseMillis=200`）
- 标记-整理算法，无内存碎片

**回收流程**：
```
1. 初始标记（Initial Mark）：STW，标记GC Roots直接引用
2. 根区域扫描（Root Region Scan）：并发，扫描Survivor区引用
3. 并发标记（Concurrent Mark）：并发，遍历整个堆
4. 最终标记（Remark）：STW，修正并发期间的变动
5. 筛选回收（Cleanup）：STW，按回收价值排序Region，优先回收垃圾最多的Region
6. 复制/整理（Evacuation）：将存活对象复制到空Region，清空原Region
```

> 🔧 **补充**：原回答"具体流程待补充"，补充了完整6步流程

---

#### 8. JVM 优化策略

| 优化方向 | 具体措施 |
|----------|----------|
| **使用G1** | `-XX:+UseG1GC`，适合大堆内存（>4GB） |
| **调整堆大小** | `-Xms`和`-Xmx`设为相同值，避免运行时扩缩容 |
| **减少对象创建** | 对象池、StringBuilder替代字符串拼接 |
| **减少Full GC** | 避免System.gc()，及时释放大对象引用 |
| **元空间调优** | `-XX:MaxMetaspaceSize`防止元空间溢出 |

> ✅ 回答基本正确

---

#### 9. Bean 生命周期

```
实例化 → 属性赋值（依赖注入） → 
Aware接口回调 → BeanPostProcessor.postProcessBeforeInitialization → 
初始化（@PostConstruct/InitializingBean） → 
BeanPostProcessor.postProcessAfterInitialization → 
使用 → 销毁（@PreDestroy/DisposableBean）
```

**扩展点**：
- **Aware接口**：获取容器资源（ApplicationContextAware、BeanFactoryAware）
- **BeanPostProcessor**：AOP代理在此阶段生成

> ✅ 回答正确，增加了图示化

---

#### 10. 悲观锁的实现机制

| 实现方式 | 机制 | 特点 |
|----------|------|------|
| **synchronized** | JVM层面，Monitor机制，锁升级 | 自动释放，非公平 |
| **ReentrantLock** | AQS框架，CAS+队列 | 需手动unlock，支持公平/非公平、可中断 |
| **数据库悲观锁** | `SELECT ... FOR UPDATE` | 行级锁，事务提交释放 |

**synchronized vs ReentrantLock对比**：
| 特性 | synchronized | ReentrantLock |
|------|--------------|---------------|
| 实现层级 | JVM（Monitor） | API（AQS） |
| 释放方式 | 自动 | 手动unlock() |
| 公平性 | 非公平 | 可配置公平/非公平 |
| 可中断 | 否 | 支持lockInterruptibly() |
| 条件队列 | 一个（wait/notify） | 多个Condition |

> 🔧 **纠正**：原回答只提到synchronized，补充了ReentrantLock和数据库悲观锁

---

#### 11. 线程池使用注意事项

原回答正确，补充两点：

**核心注意事项**：
1. **区分任务类型配置核心线程数**
   - CPU密集型：core = CPU核数 + 1
   - IO密集型：core = CPU核数 * 2 或更大
2. **自定义等待队列策略**：LinkedBlockingQueue无界队列会导致OOM风险，建议使用有界队列
3. **避免CallerRunsPolicy阻塞主线程**：在异步场景中会导致调用线程（可能是主线程）执行任务，失去异步意义
4. **异常处理**：线程池内的异常不会抛出到提交线程，需配合Callable+Future或自定义ThreadFactory捕获

> ✅ 回答正确且有深度

---

#### 12. 如何让主线程感知子线程异常？

**方式一：Callable + Future（推荐）**
```java
ExecutorService pool = Executors.newFixedThreadPool(4);
Future<?> future = pool.submit(() -> {
    // 子线程任务
    throw new RuntimeException("出错了");
});

try {
    future.get();  // 主线程阻塞获取结果，异常会在这里抛出
} catch (ExecutionException e) {
    // 捕获子线程异常
    e.getCause().printStackTrace();
}
```

**方式二：自定义ThreadFactory + UncaughtExceptionHandler**
```java
ThreadFactory factory = r -> {
    Thread t = new Thread(r);
    t.setUncaughtExceptionHandler((thread, ex) -> {
        System.err.println("线程" + thread.getName() + "异常：" + ex);
    });
    return t;
};
```

> 🔧 **纠正**：原回答提到的InheritableThreadLocal是用于父子线程共享变量，不是用于异常感知。这里给出了两种正确的异常感知方式。

---

#### 13. 频繁GC的原因分析

原回答正确，系统化整理：

| 原因 | 排查方法 | 解决 |
|------|----------|------|
| **内存泄漏** | MAT分析，找大对象 | 修复引用未释放问题 |
| **堆内存过小** | 监控GC频率 | 增大-Xmx |
| **对象创建过多** | jstat -gc观察 | 对象池、缓存优化 |
| **大对象进入老年代** | GC日志分析 | 调整大对象阈值 |
| **System.gc()调用** | 代码搜索 | 移除显式调用 |

---

#### 14. Spring AOP 设计模式与代理方式

**设计模式**：
- **代理模式（Proxy Pattern）**：Spring AOP的核心
- **装饰器模式（Decorator）**：增强原有功能而不改变接口

**代理方式**：
| 代理方式 | 基于 | 适用场景 |
|----------|------|----------|
| **JDK动态代理** | 接口（InvocationHandler） | 目标类实现了接口 |
| **CGLIB** | 继承（ASM字节码生成子类） | 目标类无接口 |

**日志/上下文注入注意事项**：
| 问题 | 说明 |
|------|------|
| **内部调用失效** | `this.method()`不走代理，AOP不生效。解决：注入自身或改用AspectJ |
| **final类/方法** | CGLIB无法代理，会报错 |
| **循环依赖** | AOP代理对象可能导致循环依赖问题 |
| **性能损耗** | 每次调用都有代理开销，避免在热点路径过度使用 |

> 🔧 **纠正**：原回答不清楚设计模式，补充了代理模式和装饰器模式；补充了日志/上下文注入的4个注意事项

---

#### 15. MySQL EXPLAIN 分析

原回答正确，系统化整理：

**EXPLAIN关键字段**：

| 字段 | 关注值 | 优化方向 |
|------|--------|----------|
| **type** | system > const > eq_ref > ref > range > index > ALL | 避免ALL（全表扫描） |
| **key** | 实际使用的索引名 | 确保不是NULL |
| **rows** | 扫描行数估算 | 越小越好 |
| **Extra** | Using index（覆盖索引）、Using where、Using filesort（需优化） | 避免filesort/temporary |

**优化示例**：
```sql
-- 原始SQL（type=ALL）
SELECT * FROM user WHERE YEAR(create_time) = 2024;

-- 优化后（type=range，走索引）
SELECT * FROM user WHERE create_time >= '2024-01-01' AND create_time < '2025-01-01';
```

---

#### 16. ES 倒排索引详解

**倒排索引（Inverted Index）结构**：
```
文档集合：
Doc1: "Java is good"
Doc2: "Python is good"
Doc3: "Java and Python"

倒排索引：
Term    | Doc IDs (Posting List)
--------|------------------------
java    | Doc1, Doc3
python  | Doc2, Doc3
good    | Doc1, Doc2
is      | Doc1, Doc2
and     | Doc3
```

**核心组件**：
| 组件 | 作用 |
|------|------|
| **Term Dictionary** | 存储所有词项，使用FST（有限状态转换）压缩，支持快速前缀查找 |
| **Posting List** | 记录包含该词的文档ID列表，使用RoaringBitmap压缩 |
| **Term Index** | 词项索引，加速Term Dictionary查找 |

**倒排索引 vs B+树索引**：
| 场景 | 优势 |
|------|------|
| 全文搜索 | 倒排索引天然适合关键词检索 |
| 精确匹配/范围查询 | B+树更优 |

> 🔧 **补充**：原回答完全空白，补充了倒排索引的完整原理和结构

---

### 📊 今日复盘总结

| 知识点 | 掌握程度 | 薄弱环节 |
|--------|----------|----------|
| String final设计 | ⭐⭐⭐⭐⭐ | 已掌握 |
| 生产者消费者 | ⭐⭐⭐⭐ | 阻塞队列类型需记忆 |
| 单例模式 | ⭐⭐⭐⭐⭐ | 已掌握 |
| CPU排查 | ⭐⭐⭐⭐ | 16进制转换目的（已补充） |
| OOM排查 | ⭐⭐⭐ | MAT工具使用（已补充） |
| MQ选型 | ⭐⭐⭐⭐ | Kafka日志模型（已补充） |
| G1 GC流程 | ⭐⭐⭐⭐ | 详细步骤（已补充） |
| 悲观锁 | ⭐⭐⭐⭐ | ReentrantLock对比（已补充） |
| 线程异常感知 | ⭐⭐⭐ | Future/get异常处理（已补充） |
| Spring AOP | ⭐⭐⭐ | 设计模式+注意事项（已补充） |
| ES倒排索引 | ⭐⭐ | 完整原理（已补充） |

---



## Day 30 (03-16)

### 🎯 今日重点
AI模拟面试复盘（16道八股题深度整理）+ Redis热key与高可用复习

### ⏱️ 学习时长
（待补充）

---

### 🧠 思维导图
（今日无新增）

---

### 📚 八股文（16道深度整理）

---

#### 1. volatile 内存屏障机制

**内存屏障类型**：
| 屏障类型 | 作用 | 插入位置 |
|----------|------|----------|
| LoadLoad | 阻止读-读重排 | 读操作后 |
| StoreStore | 阻止写-写重排 | 写操作后 |
| LoadStore | 阻止读-写重排 | 读操作后 |
| StoreLoad | 阻止写-读重排（全能屏障） | 写操作后 |

**volatile实现原理**：
- **写操作**：在volatile写之后插入StoreStore屏障，确保之前的写都完成；插入StoreLoad屏障，确保之后的读写都在写完成后执行
- **读操作**：在volatile读之前插入LoadLoad屏障，确保之前的读都完成；插入LoadStore屏障，确保之后的写在读完成后执行

**注意点**：
- volatile保证可见性和有序性，但不保证原子性
- i++操作（读取-修改-写入）仍需synchronized或AtomicInteger

---

#### 2. Redis 热key排查与高可用

**热key vs 大key对比**：
| 维度 | 大key | 热key |
|------|-------|-------|
| 定义 | 单个key的value很大 | 单个key访问频率极高 |
| 危害 | 阻塞、慢查询、迁移困难 | 单节点CPU/带宽打满 |
| 排查 | redis-cli --bigkeys | 监控QPS、hotkeys参数 |
| 解决 | 拆分、压缩 | 本地缓存、key分片 |

**热key解决方案详解**：

1. **本地缓存（一级缓存）**
   - 使用Caffeine或Guava Cache
   - 设置短过期时间（如30秒），减少Redis访问压力
   - 注意：数据一致性降低，适合读多写少场景

2. **key分片（拆分）**
   ```
   原key: user:1001:profile
   拆分后: user:1001:profile:00 ~ user:1001:profile:99
   读取时: random(0,99) 或 hash(userId) % 100
   ```
   - 优点：分散压力到多个节点
   - 缺点：批量查询复杂度增加

3. **读写分离**
   
   - 从节点分担读压力
   - 注意：主从延迟可能导致读到旧数据

**Redis Sentinel选举机制详解**：

1. **主观下线（Subjectively Down）**
   - 单个Sentinel认为主节点不可达
   - 默认配置：30秒内无响应（down-after-milliseconds）

2. **客观下线（Objectively Down）**
   - Sentinel向其他Sentinel询问该节点状态
   - 超过quorum数量的Sentinel同意，则确认故障

3. **选举Leader Sentinel**
   - 使用Raft算法（分布式一致性算法）
   - 每个Sentinel向其他节点请求投票
   - 先到先得，获得多数票的Sentinel成为Leader
   - Leader负责执行故障转移

4. **选择新主节点**
   - 过滤：排除已下线、网络断开的从节点
   - 优先级：slave-priority配置值小的优先（默认100）
   - 复制偏移量：复制数据最多的优先（减少数据丢失）
   - Run ID：创建时间早的优先（保证确定性）

5. **故障转移执行**
   - Leader向新主节点发送SLAVEOF NO ONE
   - 向其他从节点发送SLAVEOF NEW_MASTER
   - 更新配置，通知客户端（通过发布订阅）



---

### 🔢 算法
（今日无算法练习，专注八股复盘）

---

### 🛠️ 项目/实战
（今日无项目实战，专注面试准备）

---

### 📝 其他
- 拼多多笔试三题复盘（待补充详细内容）

---

### 📊 今日复盘

| 项目 | 内容 |
|------|------|
| **完成度** | 16道八股题深度整理，每道题扩展为面试级答案 |
| **薄弱环节** | Sentinel选举算法源码、拼多多笔试题 |
| **明日计划** | 1. 深入Redis Sentinel选举源码<br>2. 完成拼多多笔试详细复盘 |

> **⏱️ 总时长**：（待补充）

---

## Day 31 (03-17)

### 🎯 今日重点
美团AI面试准备（截止3月19日）

携程笔试通过 等待约面

### ⏱️ 学习时长
（待补充）

---

### ✅ 今日任务

- [ ] **美团AI面试准备**
  - 截止日期：3月19日前完成
- [ ] rag项目准备：
  - 如何评估搜索策略 可以人工先构建一个评测集 包括人工筛选的一些高相关文档 还有一些干扰因子文档 标注 最后评估召回率等指标
- [ ] linux  git指令学习：
  - [ ] 常见： cpu飙升排查：先top查看进程 在用ps -T grep 线程名查看状态 用jstat 查看 free看内存余量 
  - [ ] 


> **⏱️ 总有效时长**：待补充

---

### 📊 今日复盘

| 项目 | 内容 |
|------|------|
| **完成度** | 待补充 |
| **薄弱环节** | 待补充 |
| **明日计划** | 1. 完成美团AI面试<br>2. 继续八股复习 |

## Day 33 (03-18)

### 🎯 今日重点
美团AI面试完成 + 面试复盘整理

### ⏱️ 学习时长
目标：5h（稳步回升，不过度）

---

### ✅ 今日任务

- [x] **美团AI面试** ⏰ 截止今日
  - ✅ 已完成AI面试录制（约40分钟）
  - 面试内容已转录并整理
  - **⏱️ 耗时：1h**

- [x] **面试复盘与整理**
  - 面试录音转录分析
  - 问题回答质量评估
  - **⏱️ 耗时：1h**

> **⏱️ 总时长：2h（今日以面试为主）**
> 
> **状态**：美团AI面试已完成，需针对薄弱环节补强

---

### 📊 今日复盘 - 美团AI面试深度分析

#### 面试概况
- **时长**：约40分钟
- **题型**：自我介绍 + 8道技术问答 + 1道场景设计题 + 1道经历描述题
- **形式**：AI录制面试，全程录像

#### 面试问题清单

| 序号 | 问题类型 | 具体问题 | 回答质量 |
|------|----------|----------|----------|
| 1 | 自我介绍 | 个人背景+AI工具应用经历 | ⭐⭐⭐⭐ |
| 2 | 数据结构 | 数组与链表的区别 | ⭐⭐⭐ |
| 3 | 数据结构追问 | 实现FIFO队列选择数组还是链表？ | ⭐⭐⭐⭐ |
| 4 | 数据结构追问 | 链表频繁查询第k个元素的表现？ | ⭐⭐⭐⭐ |
| 5 | Java基础 | 双亲委派模型工作原理 | ⭐⭐⭐ |
| 6 | Redis | 几种数据结构及典型应用 | ⭐⭐⭐⭐ |
| 7 | Redis追问 | List结构在具体场景中的作用 | ⭐⭐⭐⭐ |
| 8 | Redis追问 | 消费者处理速度跟不上如何处理？ | ⭐⭐⭐ |
| 9 | 系统设计 | 用户注册登录+验证码功能设计 | ⭐⭐⭐⭐ |
| 10 | 系统设计追问 | 大模型接口设计（请求/返回/流式/错误码） | ⭐⭐ |
| 11 | 经历描述 | 有限资源下解决问题的经历 | ⭐⭐⭐⭐ |

#### 表现亮点 ✅

1. **AI工具应用**：展示了使用OpenClaw自动化学习流程的实际案例，体现技术敏感度
2. **项目经验丰富**：RAG知识库项目、文件上传异步处理等实际业务场景回答流畅
3. **系统设计思路清晰**：用户登录注册系统设计完整，涵盖JWT、Redis、限流等关键点
4. **学习能力展示**：本科紧急完成JSP项目的经历体现了快速学习和问题解决能力

#### 需改进点 ⚠️

| 问题 | 具体表现 | 改进建议 |
|------|----------|----------|
| **口头禅过多** | "然后...然后..."频繁出现 | 改用"首先/其次/最后"结构化表达 |
| **术语不够专业** | "小龙虾"、"OpenClaw" | 统一用"大模型工具"、"AI Agent" |
| **部分概念模糊** | 双亲委派模型表述不够准确 | 重新梳理：自底向上检查，自顶向下加载 |
| **错误码设计经验不足** | 大模型接口错误码设计较生疏 | 参考HTTP状态码+业务码分层设计 |
| **数组链表对比表述混乱** | 访问速度部分表述不清 | 重新整理：数组O(1)随机访问 vs 链表O(n)顺序访问 |

#### 关键表达技巧修正表

| **维度** | **你的现状（需扣分）** | **资深学长建议（正解）** |
|----------|------------------------|--------------------------|
| **口头禅** | "差不多这样子"、"其实是类似的" | **删掉。** 这种词会稀释你的技术自信。 |
| **逻辑连接** | "然后...然后..." | 使用 **"首先、其次、最后"** 或 **"从两个维度来看"**。 |
| **AI称呼** | "小龙虾"、"OpenClaw" | 统一称呼为 **"大模型工具"** 或 **"AI Agent"**，显得专业。 |
| **面对不会的问题** | "这个不太会"、"没有接触过" | **"虽然我目前没有直接的生产实践，但基于我对HTTP/数据结构的底层理解，我会尝试从以下维度设计..."** |

#### 知识点补强清单

**高优先级（面试高频）**：
- [ ] 双亲委派模型：重新梳理完整流程图
- [ ] 数组vs链表：整理对比表格，强化访问速度概念
- [ ] 错误码设计：参考阿里云/腾讯云API设计规范

**中优先级**：
- [ ] Redis List消息队列积压处理：补充扩容Consumer、批量处理等方案
- [ ] 流式返回技术：WebSocket vs SSE vs HTTP Stream区别

---

## Day 34 (03-19)

### 🎯 今日重点
面试复盘补强 + Linux技能缺口补强 + 算法保持手感

### ⏱️ 学习时长
目标：5h

---

### ✅ 今日任务

- [x] **八股文补强** ⭐ 基于昨日面试复盘
  - **双亲委派模型**：重新梳理完整流程（自底向上检查，自顶向下加载）
    - 目的：防止核心类库被恶意篡改
    - 流程图：Bootstrap → Extension → Application ClassLoader
  - **数组 vs 链表对比**：整理标准对比表格
    - 访问速度：数组O(1)随机访问 vs 链表O(n)顺序访问
    - 插入删除：数组O(n)需移动元素 vs 链表O(1)改指针
    - 扩容：数组需重新分配 vs 链表动态扩容
  - **错误码设计规范**：参考阿里云API设计
    - 2xx：成功
    - 4xx：客户端错误（400参数错误、401未授权、403禁止访问、404不存在）
    - 5xx：服务端错误（500内部错误、502网关错误、503服务不可用）
  - **⏱️ 耗时：1.5h**

- [x] **Linux基础** ⭐ JD高频技能缺口
  - **常用命令**：`ls/cd/cp/mv/find/grep/awk/sed`
  - **进程管理**：`ps/top/kill/jstat/jmap`
  - **CPU 100%排查**：`top → top -Hp <PID> → printf "%x\n" <TID> → jstack → grep`
  - **内存排查**：`free/jstat -gc/jmap -heap`
  - **⏱️ 耗时：1.5h**

- [x] **算法练习** - 保持手感
  - 快排手写（10分钟限时）
  - 归并排序（分治思想）
  - 二分查找最左/最右边界（模板巩固）
  - **⏱️ 耗时：1h**

- [x] **项目拷打** - RAG项目Q&A
  - 整理已完成的项目拷打答案（Q1-Q4）
  - 录音练习STAR法则回答（每个项目2分钟版本）
  - **⏱️ 耗时：30min**

- [x] **投递跟进**
  - 查看美团AI面试结果
  - 更新投递状态表
  - 根据JD要求继续投递2-3家
  - **⏱️ 耗时：30min**

> **⏱️ 总时长：5h**
> 
> **状态**：基于昨日面试复盘针对性补强，重点突破Linux技能缺口
> 
> **正反馈目标**：完成Linux基础命令学习，能流利说出CPU排查完整流程

---

### 📊 昨日面试关键教训

| 问题 | 改进行动 |
|------|----------|
| 口头禅"然后..." | 改用"首先/其次/最后" |
| 术语不专业 | 统一用"大模型工具"而非"小龙虾" |
| 双亲委派表述不清 | 今日重新梳理流程图 |
| 错误码设计生疏 | 学习阿里云API规范 |




### 1. IO 多路复用 select/poll/epoll

IO 多路复用是一种高效的 IO 处理机制，核心思想是用一个线程同时管理多个文件描述符（FD），避免为每个连接创建独立线程。

select 是最早的实现，它使用固定大小的 fd_set（默认 1024 个 FD），每次调用时需要把整个 FD 集合从用户态拷贝到内核态，然后遍历所有 FD 检查是否有事件就绪，时间复杂度是 O(n)。由于 FD 数量有限制且每次都要全量拷贝，效率较低。

poll 对 select 做了改进，使用链表存储 FD 解决了数量限制问题，但仍然需要每次调用时全量拷贝到内核并遍历所有 FD，时间复杂度还是 O(n)。

epoll 是 Linux 独有的高性能实现，它通过三个核心函数完成：epoll_create() 创建内核中的红黑树和就绪链表；epoll_ctl() 注册 FD 并绑定回调函数；epoll_wait() 直接返回就绪链表。epoll 高效的核心原因有三点：一是 FD 常驻内核避免了重复拷贝；二是采用事件驱动机制，当 FD 有数据时硬件中断触发回调将 FD 放入就绪链表；三是只返回就绪的 FD 不需要遍历。epoll 还支持 LT（水平触发）和 ET（边缘触发）两种模式，LT 只要有数据每次都通知可以分批处理，ET 只在状态变化时通知一次必须一次性读完，适合高性能场景。

### 2. 双亲委派模型

双亲委派模型是 Java 类加载的核心机制，类加载器有明确的层级关系：Bootstrap ClassLoader（启动类加载器，负责加载 JAVA_HOME/lib 下的核心类库）→ Extension ClassLoader（扩展类加载器，负责加载 JAVA_HOME/lib/ext 下的扩展类）→ Application ClassLoader（应用类加载器，负责加载 classpath 下的类）→ 自定义 ClassLoader。

当收到类加载请求时，流程是"先委派父加载器，父加载不了再自己加载"。具体来说，一个加载器收到请求后，先检查是否已加载过，如果父加载器能完成加载就返回，只有父加载器无法处理时才由自己尝试加载。

双亲委派的核心目的是保证 Java 核心类库的安全性，防止用户自定义的类替换核心类。例如 java.lang.String 只有 Bootstrap ClassLoader 能加载，即使自定义了 String 类也不会生效，这就保证了系统的安全性。

打破双亲委派的典型场景有：Tomcat 每个 WebApp 有独立的类加载器实现应用隔离；JDBC 使用 SPI 机制需要线程上下文类加载器；热部署工具需要重新加载类。

### 3. 类加载机制

Java 类加载分为三个阶段：加载、链接、初始化。

加载阶段完成了三件事：读取字节码文件（.class）、验证字节码格式、根据字节码信息创建 Class 对象。

链接阶段包含验证、准备、解析三个步骤。验证阶段检查字节码是否符合 JVM 规范；准备阶段为类的静态变量分配内存并设置默认值（如 int 默认为 0，对象默认为 null）；解析阶段将符号引用转换为直接引用（通过方法表、字段表直接定位到内存地址）。

初始化阶段是执行 <clinit> 方法的过程，这个方法由静态变量赋值语句和静态代码块组成，按照在类中出现的顺序从上到下执行。

类初始化时机（主动引用）包括：new 对象、getstatic/putstatic 访问静态字段、invokestatic 调用静态方法、反射 Class.forName()、子类初始化时父类未初始化、主类（main 方法所在类）。被动引用不会触发初始化，包括：子类引用父类静态字段（父类初始化，子类不初始化）、数组定义 MyClass[] arr = new MyClass[10]、引用编译期常量（存储在常量池中）。

### 4. MySQL 执行 SQL 语句过程

MySQL 执行一条 SQL 语句需要经过五个阶段：连接器 → 分析器 → 优化器 → 执行器 → 存储引擎。

连接器负责 TCP 握手建立连接、身份认证、获取用户权限。连接建立后会常驻连接池。

分析器进行词法分析将 SQL 字符串分解为 token（如 SELECT、FROM、table name），以及语法分析检查 SQL 语法是否正确并生成抽象语法树（AST）。

优化器是 MySQL 的核心环节，它会分析成本选择最优执行计划：选择使用哪个索引、多表连接的顺序（驱动表和被驱动表）、决定是否使用索引覆盖等。

执行器根据优化器生成的执行计划调用存储引擎的 API，首先检查权限，然后调用 handler 接口读写数据。

存储引擎（如 InnoDB）负责实际的数据读写操作，从 Buffer Pool 中读取数据页，如果页不在 Buffer Pool 中则从磁盘加载。

MySQL 8.0 废弃了查询缓存功能，因为查询缓存失效太频繁——任何对表的 DML 操作（INSERT/UPDATE/DELETE）都会导致该表的所有查询缓存失效，命中率很低。

### 5. OOM 排查思路

线上发生 OOM 的排查流程分为五步：

第一步：保留现场。通过 JVM 参数 -XX:+HeapDumpOnOutOfMemoryError 自动在 OOM 时导出堆转储文件，或者手动执行 jmap -dump:format=b,file=heap.hprof <pid> 导出。

第二步：分析堆转储。使用 MAT（Memory Analyzer）、JProfiler 或 VisualVM 加载 hprof 文件。查看 Histogram 找出占用内存最多的对象，查看 Dominator Tree 找出哪些大对象被 GC Root 直接引用。

第三步：定位 GC Root。GC Root 包括：虚拟机栈中引用的对象（局部变量）、方法区中静态属性引用的对象、方法区中常量引用的对象、本地方法栈中 JNI 引用的对象、活跃的线程对象。通过 Path to GC Roots 可以找到谁在引用这个大对象。

第四步：分析代码定位泄漏点。常见的内存泄漏场景包括：静态集合（如 static Map）不断添加数据没有清理、ThreadLocal 使用后没有 remove() 导致内存泄漏、数据库连接没有正确关闭、监听器或回调注册后没有取消注册。

第五步：修复代码并验证。

### 6. 堆外 OOM 原因

堆外内存（Direct Memory）不受 JVM 堆管理，需要手动释放，常见原因包括：

DirectByteBuffer 是最常见的原因。Netty、NIO 使用 DirectByteBuffer 在堆外分配内存，虽然它通过 Cleaner 在 GC 时间接释放，但如果 DirectByteBuffer 对象进入老年代后迟迟没有触发 Full GC，堆外内存会不断增长导致 OOM。排查可以设置 -XX:MaxDirectMemorySize=512m 限制大小。

Metaspace 是方法区的实现，存储类的元数据。类加载过多（如大量使用动态代理、CGLIB、Groovy、JSP）会导致 Metaspace 满。可以设置 -XX:MaxMetaspaceSize=256m 限制。

线程栈每个线程默认占用 1MB 栈空间，如果线程数过多（如线程池配置不当）会导致 OOM。可以设置 -XX:Xss=256k 减小栈大小。

JNI/Native 内存调用本地 C/C++ 库时，如果本地库有内存泄漏也会导致堆外 OOM。可以使用 Native Memory Tracking（-XX:NativeMemoryTracking=summary）排查。

Code Cache 是 JIT 编译代码的缓存区，如果 JIT 编译过多可能占满。可以设置 -XX:ReservedCodeCacheSize=240m。

### 7. MySQL 锁机制 vs Java 锁

MySQL 锁分为多个层次：

全局锁（FLUSH TABLES WITH READ LOCK）会锁住整个数据库用于备份。表锁包括 MDL（元数据锁）在查询时自动加锁保护表结构，以及 LOCK TABLES 显式加锁。行锁是 InnoDB 引擎特有的，包括 Record Lock（行记录锁）、Gap Lock（间隙锁，锁定索引记录之间的区间防止幻读）、Next-Key Lock（Record Lock + Gap Lock 的组合）。

行锁的实现原理是在索引上加锁，如果查询没有使用索引会退化为表锁。例如 SELECT * FROM user WHERE id = 1 FOR UPDATE 只会锁住 id=1 的行，但 SELECT * FROM user WHERE name='tom' 如果 name 没有索引则会锁住整个表。

意向锁（IS/IX）是表级锁，表示事务想在行级加锁，作用是协调行锁和表锁的共存。

Java 锁主要有 synchronized 和 ReentrantLock 两种。synchronized 是 JVM 内置关键字，基于对象头的 Mark Word 实现，支持偏向锁 → 轻量级锁 → 重量级锁的锁升级机制。ReentrantLock 是 JDK 提供的 API，基于 AQS（AbstractQueuedSynchronizer）实现，支持公平锁/非公平锁、可中断等待（tryLock）、可设置超时、多条件变量（Condition）。

两者的核心区别：MySQL 行锁在数据库层面解决并发问题，Java 锁在应用层面解决；MySQL 锁是自动的（事务提交或回滚时释放），Java 锁需要手动加锁释放；MySQL 锁有死锁检测机制会自动回滚，Java 锁需要编程避免死锁。

### 8. 反射在 Spring 中的应用

反射在 Spring 框架中无处不在，核心应用场景包括：

IOC 容器通过反射创建 Bean。Spring 会扫描 classpath 下的类，根据 @Component、@Service 等注解识别需要管理的类，然后通过 Class.forName() 加载类，再用 newInstance() 或 getDeclaredConstructor().newInstance() 创建实例。

依赖注入（DI）通过反射注入属性。Spring 遍历 Bean 的所有字段，如果字段有 @Autowired 注解，就通过反射设置值：field.setAccessible(true) 绕过访问检查，field.set(bean, dependency) 注入依赖对象。

AOP 代理底层也是反射。Spring AOP 有两种代理方式：JDK 动态代理（目标类实现了接口）使用 Proxy.newProxyInstance() 创建代理类；CGLIB 代理（目标类没有实现接口）通过继承目标类并重写方法实现。代理对象的 method.invoke(target, args) 调用原方法，前后可以添加切面逻辑。

注解解析通过反射获取注解信息。Spring 通过 Class.getAnnotation()、Method.getAnnotation() 等方法获取 @Value、@Transactional 等注解信息，然后根据注解配置执行相应逻辑。

反射的优势是解耦和灵活性，配置驱动不需要硬编码；缺点是有性能开销（需要解析方法签名、绕过访问检查）。Spring Boot 使用了编译期注解处理器和 ASM 字节码操作来优化反射性能。

### 9. 缓存预热

缓存预热是指系统启动或缓存失效后，主动将热点数据加载到缓存中，避免大量请求直接打到数据库导致雪崩。

预热方式有四种：

启动时加载：在应用启动时通过 @PostConstruct 或 ApplicationRunner 接口，在系统初始化阶段批量查询热点数据并写入缓存。适合数据量较小且相对固定的场景。

定时刷新：通过 @Scheduled 定时任务，在凌晨业务低峰期定期刷新缓存。适合数据会周期性变化的场景。

消息触发：在数据发生变更时（如商品价格调整），通过消息队列通知缓存服务更新对应缓存。适合对数据实时性要求较高的场景。

脚本预热：运维通过脚本批量导入数据到缓存。适合大数据量的初始化场景。

时间选择原则是避开业务高峰期，通常选择凌晨 2-5 点业务低峰期执行。

避免打挂数据库的措施包括：分批加载（每次查询 100-1000 条，sleep 间隔）、限流（控制并发数）、异步执行（不阻塞主流程）、降级预案（预热失败不影响正常业务）。
### 10. UDP 协议

UDP（用户数据报协议）是不可靠的传输层协议，因为它不提供任何可靠性保障机制。首先，UDP 不建立连接就直接发送数据，没有三次握手过程，发送前不需要双方确认。其次，UDP 不保证数据交付，即使数据包丢失也不会重试，发送方不知道接收方是否收到。第三，UDP 不保证数据顺序，数据包可能以任意顺序到达，甚至可能重复。第四，UDP 没有流量控制，发送方可能以接收方无法处理的速度发送数据导致丢包。第五，UDP 没有拥塞控制，不会在网络拥塞时主动降低发送速度。

UDP 广泛应用于对实时性要求高于可靠性的场景。实时音视频是典型应用，包括视频会议、直播、VoIP 语音通话等，RTMP、WebRTC、RTSP 等协议都基于 UDP，因为在实时通话中偶尔丢几个包影响不大，但延迟高了体验就很差。在线游戏也是典型场景，游戏玩家需要实时交互，延迟比偶尔丢包更关键，游戏客户端通常自己实现可靠传输层来处理丢包问题。DNS 查询使用 UDP，因为 DNS 请求和响应数据量小，速度快更重要，超时未响应客户端会重试。UDP 还支持广播和一对多场景，组播音视频直播、局域网设备发现等只能用 UDP。物联网场景中 MQTT 很多实现基于 UDP 减少网络开销。

选择 UDP 的核心原因是延迟和开销。TCP 为了保证可靠性有三次握手建立连接、确认重传机制、流量控制、拥塞控制等复杂逻辑，这些都会增加延迟。而 UDP 没有任何额外开销，首部只有 8 字节，TCP 有 20 字节，处理速度更快。对于实时性要求高的场景宁可丢包也不要延迟，因为 TCP 超时重传的延迟可能比丢几个包更影响体验。应用层可以根据业务需求自己实现可靠性保障，比如游戏中的可靠 UDP（RUDP）、QUIC 协议等。面试时可以总结：UDP 快但不可靠，适合实时性优先的场景；TCP 可靠但有延迟，适合数据准确性优先的场景。

### 11. CMS/ZGC/G1 垃圾收集器

CMS（Concurrent Mark Sweep）是老年代垃圾收集器，核心目标是低延迟。它采用标记-清除算法，分四个阶段：初始标记（STW）→ 并发标记 → 重新标记（STW）→ 并发清除。CMS 的优点是并发收集停顿时间短，缺点是使用标记-清除会产生内存碎片，CPU 敏感时会争抢资源，无法处理浮动垃圾。适用于对延迟敏感、CPU 资源充足的场景。

G1（Garbage First）是面向服务端的收集器，把堆分成多个大小相等的 Region，每个 Region 可以是 Eden、Survivor 或老年代。G1 年轻代使用复制算法，老年代使用标记-整理算法（实际上是复制）。G1 的核心是可预测停顿时间模型，用户设置 MaxGCPauseMillis，G1 会根据历史数据预测哪些 Region 回收价值最高，优先回收垃圾最多的 Region。G1 适用于大内存、多核 CPU、需要可预测停顿时间的场景。

ZGC 是 JDK 11 引入的低延迟垃圾收集器，停顿时间目标不超过 10ms，甚至可以做到 1ms 以内。ZGC 使用着色指针和读屏障技术，实现了几乎全程并发的标记和整理。ZGC 把堆分成多个 Region，支持大内存（最大 16TB），使用复制算法做整理。ZGC 适用于超大内存、对延迟极其敏感的场景，如金融交易系统。

年轻代和老年代算法选择：Serial 年轻代用复制，老年代用标记-整理；Parallel Scavenge 年轻代复制，Parallel Old 标记-整理；ParNew 年轻代复制，配合 CMS 老年代标记-清除；G1 年轻代复制、老年代复制（整理）；ZGC 全程复制。

### 12. JVM 调优思路

JVM 调优的核心思路是根据业务场景选择合适的收集器和参数。首先是选择收集器：单核小内存用 Serial，多核追求吞吐量用 Parallel，追求低延迟用 CMS/G1，超大内存+超低延迟用 ZGC。然后调整堆大小：-Xms 和 -Xmx 设置相同避免动态扩容，一般设为系统内存的 50-80%。调整年轻代比例：-Xmn 或 -XX:NewRatio 控制新生代大小，对象存活时间短可以加大新生代。调整 Eden 和 Survivor 比例：-XX:SurvivorRatio，默认 8:1:1。调整大对象阈值：-XX:PretenureSizeThreshold 直接进老年代。最后通过 GC 日志分析调优效果，使用 -Xlog:gc* 输出详细日志。

### 13. 逃逸分析与栈上分配

逃逸分析是 JIT 编译器的优化技术，分析对象是否逃逸出方法或线程。如果对象不逃逸，可以进行优化：栈上分配（对象分配在栈上随方法结束自动回收）、标量替换（把对象拆成标量变量）、锁消除（对象不逃逸就不需要锁）。栈上分配是逃逸分析的一种优化，对象不在堆上分配，而是在栈帧中分配，方法结束后自动销毁，不需要 GC 回收，大大降低 GC 压力。

不是所有对象都会分配在堆上。如果 JIT 通过逃逸分析发现对象不逃逸，且开启了标量替换（-XX:+DoEscapeAnalysis，默认开启），对象可能被拆解成标量在栈上分配，或者直接被优化掉。大对象会直接进入老年代（-XX:PretenureSizeThreshold）。TLAB（Thread Local Allocation Buffer）是每个线程在 Eden 区的私有分配区，避免多线程分配对象时的同步开销。

### 14. MCP vs Skill vs A2A

MCP（Model Context Protocol）是 Anthropic 提出的开放协议，定义了 AI 模型与外部工具、数据源之间的标准通信方式。MCP 采用客户端-服务器架构，AI 模型是 MCP Client，工具或数据源是 MCP Server。MCP 提供统一的接口规范，包括资源、提示、工具三类能力，任何符合 MCP 规范的服务都可以被 AI 模型调用。MCP 解决的是 AI 应用与外部系统集成碎片化的问题，一次开发就能被多个 AI 应用接入。

Skill 是 OpenClaw 框架中的能力封装概念，一个 Skill 是一组相关的工具、提示词、配置文件的集合，用于完成特定领域的任务。Skill 通过 SKILL.md 定义元数据和规则，通过 scripts/ 目录存放脚本，通过 references/ 目录存放参考资料。Skill 是面向 Agent 的能力单元，可以被 Agent 动态加载和卸载，支持本地开发和分发。

A2A（Agent-to-Agent）是 Agent 之间通信和协作的协议或机制，定义了多个 AI Agent 如何互相发现、协商、分工、协作完成任务。A2A 可以基于消息传递、共享内存、协议约定等方式实现。典型应用包括多 Agent 协作系统、分布式任务调度、Agent 编排框架。A2A 解决的是 Agent 之间如何高效协作的问题，每个 Agent 可能是独立的模型实例，也可能是不同能力的专用 Agent。

三者的核心区别：MCP 是 AI 与外部工具的连接协议，解决"AI 如何调用工具"；Skill 是 Agent 能力的封装单位，解决"Agent 如何组织能力"；A2A 是 Agent 之间的协作机制，解决"多个 Agent 如何协作"。MCP 是协议层的标准，Skill 是框架层的概念，A2A 是协作层的机制。

### 15. Spring Boot Starter 手写

Spring Boot Starter 的本质是把自动配置类和依赖打包在一起，其他项目引入后自动生效。手写 Starter 分五步：第一，创建 Maven 项目，pom.xml 中引入 spring-boot-autoconfigure 依赖。第二，编写核心功能类，比如 HelloService。第三，编写配置属性类，使用 @ConfigurationProperties(prefix = "hello") 绑定配置。第四，编写自动配置类，使用 @Configuration、@ConditionalOnClass、@EnableConfigurationProperties、@ConditionalOnMissingBean 等注解，根据条件创建 Bean。第五，在 resources/META-INF/spring.factories 或 spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports 文件中注册自动配置类的全类名。其他项目引入依赖后，Spring Boot 会扫描 spring.factories 自动加载配置类，根据条件判断是否创建 Bean。 

### 16. AOP 怎么写

AOP（面向切面编程）用于将横切关注点（如日志、权限、事务）从业务逻辑中分离。Spring AOP 基于 JDK 动态代理或 CGLIB 实现，核心概念包括切面、切点、通知。切面用 @Aspect 注解标记，切点用 @Pointcut 定义匹配规则，通知包括 @Before、@After、@AfterReturning、@AfterThrowing、@Around 五种类型。Around 通知最强大，可以控制方法执行时机和返回值。实际开发中常用 AOP 做接口日志、权限校验、性能监控、事务管理。使用时注意：内部调用不会触发 AOP（因为绕过了代理），private 方法无法被代理，循环依赖问题可以通过 @Lazy 解决。

### 17. Redis Hash 存百万数据

Redis Hash 存储百万用户数据时，内存占用取决于字段数量和值大小，假设每个用户 1KB，100 万用户约占用 1GB 内存。Hash 底层使用 ziplist（小数据量）或 hashtable（大数据量），当 field 数量超过 512 或 value 大小超过 64 字节时转换为 hashtable。存百万数据的风险在于：HGETALL、HKEYS、HVALS 等 O(N) 操作会阻塞 Redis，建议避免使用；Hash 变成 bigkey 后主从同步和持久化都会有性能问题。优化方案包括：按业务分片存储、使用 Hash Tag 控制数据分布、定期清理不活跃用户、监控 bigkey 并拆分。面试时可以强调：Hash 适合存储对象，但要注意 bigkey 问题，大数据量建议分片。

### 18. 最左匹配原则

最左匹配原则是 MySQL 联合索引的工作方式，索引 (a, b, c) 可以支持 a、a,b、a,b,c 三种查询条件，但不能支持 b、c、b,c 等跳过最左列的查询。原因是联合索引按定义顺序构建 B+ 树，先按 a 排序，a 相同再按 b 排序，b 相同再按 c 排序。如果查询条件跳过 a，索引无法定位到 b 的起始位置。判断索引是否生效，使用 EXPLAIN 查看 key 字段，显示实际使用的索引名；type 字段为 ref、range、index 表示索引有效，为 ALL 表示全表扫描；Extra 字段为 Using index 表示覆盖索引。如果索引失效，常见原因包括：索引列参与运算、使用函数、类型隐式转换、使用 != 或 <>、使用 OR 连接非索引列、LIKE 以 % 开头。

### 19. 订单超时自动关闭

订单超时自动关闭的实现方案有三种。第一种是定时任务扫描，下单时创建定时任务，10 分钟后扫描订单状态。优点是实现简单，缺点是时间不精确，依赖任务调度器的精度。第二种是 Redis 延时队列，使用 ZSET 存储订单，score 为过期时间戳，消费者轮询 ZRANGEBYSCORE 获取到期订单。Redisson 框架的 RDelayedQueue 封装了延时队列实现。第三种是消息队列延时消息，RocketMQ 支持延时消息（支持 18 个延时等级），RabbitMQ 通过死信队列 + TTL 实现。延时队列的核心逻辑是：生产者将消息发送到延时队列，设置延时时间；消息在队列中等待直到到期；消费者消费到期消息执行关单逻辑。轮询的实现方式是用定时任务（如每秒一次）执行 ZRANGEBYSCORE key 0 当前时间戳，获取到期的订单 ID 后处理并删除。

### 20. SQL 分页

SQL 分页的核心是 LIMIT 和 OFFSET。MySQL 使用 LIMIT offset, size 或 LIMIT size OFFSET offset 语法。LIMIT 10 OFFSET 0 表示从第 0 条开始取 10 条。深度分页性能问题是 OFFSET 会扫描跳过的所有行，比如 LIMIT 10 OFFSET 1000000 会扫描 100 万行然后丢弃。优化方案有三种：游标分页用 WHERE id > lastId ORDER BY id LIMIT 10，利用索引避免扫描；延迟关联先用子查询查 id 再 JOIN，减少回表次数；业务层限制最大页数避免深度分页。实际项目中推荐游标分页，适合无限滚动场景。

### 21. 线程池使用场景

线程池适用于异步处理、批量任务、并发计算等场景。典型用例包括：Excel 批量导出时拆分成多个子任务并行处理，用 CountDownLatch 等待所有子线程完成再合并结果；消息发送失败重试，提交到线程池异步重试避免阻塞主流程；定时任务调度，ScheduledThreadPoolExecutor 执行周期性任务；HTTP 请求并发，CompletableFuture 并发调用多个接口后聚合结果。CompletableFuture 相比原生线程池的优势在于链式调用和异常处理，thenApply、thenCombine、thenAccept 等方法可以优雅地编排异步流程，exceptionally 统一处理异常。实际项目中要注意：线程池命名便于监控、合理配置核心线程数和队列大小、拒绝策略选择（CallerRunsPolicy、AbortPolicy）、优雅关闭线程池。

### 22. 设计模式实践

设计模式要结合具体业务场景回答，建议通过一个功能模块体现多个模式。以支付模块为例：策略模式用于支持多种支付方式，定义 PaymentStrategy 接口，AlipayPayment、WechatPayment 是具体实现，PaymentContext 根据类型选择策略。工厂模式用于创建支付策略，PaymentFactory 封装对象创建逻辑，调用方不关心具体实现。模板方法模式用于定义支付流程骨架，abstractPay() 定义通用步骤，具体支付方式实现差异部分。责任链模式用于风控校验，AmountCheck、FrequencyCheck、RiskCheck 依次执行，任一环节失败则终止。单例模式用于支付配置管理，PaymentConfig 保证全局唯一。装饰器模式用于增强支付功能，比如添加日志、监控、重试。观察者模式用于支付结果通知，支付成功后通知订单服务、积分服务、消息服务。适配器模式用于对接第三方支付 SDK，统一接口屏蔽差异。通过一个模块讲清楚多个模式的协作关系，面试更有说服力。

### 23. int 取值范围

int 是 32 位有符号整数，存储在 4 个字节中。最高位是符号位，0 表示正数，1 表示负数。正数范围是 0 到 2^31-1（2147483647），负数范围是 -2^31（-2147483648）到 -1。为什么负数比正数多一个？因为 0 的二进制表示是 000...000，占用了一个正数位置，而负数使用补码表示，100...000 表示 -2^31，所以负数多一个。面试时可以补充：long 是 64 位，范围是 -2^63 到 2^63-1；Integer.MIN_VALUE 的绝对值会溢出，因为正数最大只有 2^31-1，没有对应的 2^31。

### 24. MySQL CPU 飙高排查

MySQL CPU 飙高的排查思路分为四步。第一步是定位问题，通过 SHOW PROCESSLIST 或 mysqladmin processlist 查看当前连接和查询状态，找出耗时长的 SQL。第二步是分析慢查询，开启慢查询日志 SET GLOBAL slow_query_log = 'ON' 和 SET GLOBAL long_query_time = 1，然后用 mysqldumpslow 工具分析慢查询日志，找出高频慢 SQL。第三步是用 EXPLAIN 分析执行计划，检查是否走索引、是否有全表扫描、是否需要加索引。第四步是检查常见原因，包括缺少索引导致全表扫描、复杂 JOIN 导致性能差、锁等待（SHOW ENGINE INNODB STATUS）、连接数过多（max_connections 配置）。通过 SHOW STATUS LIKE 'Threads_connected' 可以查看当前连接数。

### 25. Redis CPU 飙高排查

Redis CPU 飙高的排查首先用 redis-cli INFO commandstats 查看命令耗时统计，找出耗时最高的命令。然后用 redis-cli --bigkeys 扫描全库找出大 Key，用 redis-cli --hotkeys 找出热 Key（Redis 4.0+）。查看客户端连接用 redis-cli CLIENT LIST，分析是否有异常连接。常见原因包括：大 Key 操作（HGETALL、KEYS * 等 O(N) 命令）、热 Key 压力（单 Key QPS 过高）、内存不足导致频繁 GC、fork 阻塞（RDB/AOF 时 fork 进程）、Lua 脚本死循环。监控可用 redis-cli --latency-history 实时查看延迟，redis-cli INFO memory 查看内存使用。

### 26. Redis Lua 脚本原理

Redis Lua 脚本是在服务端原子执行多命令的机制。Redis 内置 Lua 5.1 解释器，脚本执行具有原子性，执行 Lua 脚本时不会执行其他客户端命令，直到脚本执行完毕。这解决了并发场景下多个命令需要保证原子性的问题，比如扣减库存场景：传统做法是先 GET 再 SET，但两个命令之间可能被其他客户端插队，而 Lua 脚本可以把 GET 和 SET 放在同一个脚本里原子执行。Lua 脚本的优势在于减少网络开销，客户端只需发送一次脚本请求而不是多次命令往返。脚本会被缓存到 Redis 中，后续执行可以通过 EVALSHA 调用缓存版本提高性能。常用场景包括分布式锁、库存扣减、投票防刷。实现方式是在 Lua 脚本中写 Redis 命令，用 EVAL 或 EVALSHA 执行。

### 27. 热 Key 拆解

热 Key 是访问频率极高的单个 Key，比如爆款商品的详情页缓存。拆解方案有三种。第一种是拆分 Key 的字段，把一个 Key 拆成多个 Key，比如 user:1001:profile 拆成 user:1001:base 和 user:1001:extra，不同字段进不同的 Key，请求时随机或轮询访问不同 Key，这样单个 Key 的 QPS 就降到了原来的 N 分之一。第二种是历史版本加版本号，比如 user:1001:profile:v1、user:1001:profile:v2，读写时带版本号，可以实现灰度发布和热 Key 的平滑迁移。第三种是一致性哈希环，用业务 ID 做哈希后路由到不同的 Redis 节点，比如根据用户 ID % N 确定访问哪个 Key。

### 28. 本地缓存一致性

本地缓存存在与 Redis 的数据一致性问题。第一种方案是设置短 TTL，本地缓存只存活几秒或几十秒，数据最终会过期更新，这种方案简单但无法保证强一致，适合对数据一致性要求不高的场景。第二种方案是消息通知，数据变更时发 MQ，本地缓存监听 MQ 收到消息后 invalidate 对应 Key，这需要引入消息队列，实现相对复杂但一致性更好。第三种方案是给本地缓存加随机 TTL，比如基础 TTL 30 秒再加 0-10 秒随机值，这样不同节点的缓存过期时间分散，避免同时过期导致缓存雪崩。第四种方案是 write-through 或 write-behind，写操作时同步更新本地缓存和 Redis，或者写 Redis 后异步更新本地缓存。实际项目中，Caffeine 设置 30 秒 TTL 加随机 5 秒基本够用。

### 29. 大 Key 处理

大 Key 是单个 Key 的 Value 过大，比如一个 Hash 存了上百万个字段。发现大 Key 可以用 redis-cli --bigkeys 扫描全库找出最大的 Key，或者用 MEMORY USAGE key 精确查看某个 Key 的内存占用。处理大 Key 有几种方式。第一是拆分，将大 Hash 拆成多个小 Hash，比如按用户 ID 哈希后缀分片，user:hash:0、user:hash:1，Set 和 ZSet 可以按 score 或 member 分片。第二是压缩存储，String 类型的大 Value 可以用 snappy 或 gzip 压缩后再存，读的时候解压，虽然压缩解压有 CPU 开销，但省内存，适合 Value 不常访问的场景。第三是异步删除，用 UNLINK 替代 DEL，UNLINK 是后台异步删除不会阻塞 Redis 主线程，如果必须同步删除可以先 SCAN 出所有 field 再分批 HDel。第四是设计层面避免，存之前评估好数据量级，设计合理的 Key 结构，不要把海量数据塞进单个 Key。



---

## Day 35 (03-20) - 今日学习

### 🎯 必做任务（根据当前状态生成）
- [x] **面试复盘改进**：练习"首先/其次/最后"表达结构，对着镜子或录音复述今日面试内容，去掉口头禅
- [x] **项目拷打**：完成 RAG 项目 Q5-Q9 整理，结合支付模块讲清楚多个设计模式
- [x] **投递跟进**：查看美团 AI 面试结果，根据反馈调整简历和话术

### 📚 选做任务（根据薄弱点分配）
- [ ] **位运算专项**：异或/lowbit/状态压缩（笔试高频）
- [ ] **Linux 实战**：用本机或虚拟机练习 cpu/内存排查命令
- [x] **算法保持手感**：每日 1-2 题中等难度

### 📤 投递跟进
- 美团 AI 面试：等待结果
- 携程：等待笔试/面试通知
- 京东：等待测评结果
- 明日计划：投递 2-3 家，关注 JD 要求针对性准备

### 📝 备注
- 今日已完成：八股文补强（双亲委派、数组链表、错误码）、Linux 基础、算法练习
- 今日薄弱点已记录：口头禅"然后..."、术语不专业
- MSc 作业已提交（如已提交可忽略）

### 1. MySQL CPU 飙高排查

MySQL CPU 飙高的排查思路分为四步。第一步是定位问题，通过 SHOW PROCESSLIST 或 mysqladmin processlist 查看当前连接和查询状态，找出耗时长的 SQL。第二步是分析慢查询，开启慢查询日志 SET GLOBAL slow_query_log = 'ON' 和 SET GLOBAL long_query_time = 1，然后用 mysqldumpslow 工具分析慢查询日志，找出高频慢 SQL。第三步是用 EXPLAIN 分析执行计划，检查是否走索引、是否有全表扫描、是否需要加索引。第四步是检查常见原因，包括缺少索引导致全表扫描、复杂 JOIN 导致性能差、锁等待（SHOW ENGINE INNODB STATUS）、连接数过多（max_connections 配置）。通过 SHOW STATUS LIKE 'Threads_connected' 可以查看当前连接数。

### 2. Redis CPU 飙高排查

Redis CPU 飙高的排查首先用 redis-cli INFO commandstats 查看命令耗时统计，找出耗时最高的命令。然后用 redis-cli --bigkeys 扫描全库找出大 Key，用 redis-cli --hotkeys 找出热 Key（Redis 4.0+）。查看客户端连接用 redis-cli CLIENT LIST，分析是否有异常连接。常见原因包括：大 Key 操作（HGETALL、KEYS * 等 O(N) 命令）、热 Key 压力（单 Key QPS 过高）、内存不足导致频繁 GC、fork 阻塞（RDB/AOF 时 fork 进程）、Lua 脚本死循环。监控可用 redis-cli --latency-history 实时查看延迟，redis-cli INFO memory 查看内存使用。

### 3. Redis Lua 脚本原理

Redis Lua 脚本是在服务端原子执行多命令的机制。Redis 内置 Lua 5.1 解释器，脚本执行具有原子性，执行 Lua 脚本时不会执行其他客户端命令，直到脚本执行完毕。这解决了并发场景下多个命令需要保证原子性的问题，比如扣减库存场景：传统做法是先 GET 再 SET，但两个命令之间可能被其他客户端插队，而 Lua 脚本可以把 GET 和 SET 放在同一个脚本里原子执行。Lua 脚本的优势在于减少网络开销，客户端只需发送一次脚本请求而不是多次命令往返。脚本会被缓存到 Redis 中，后续执行可以通过 EVALSHA 调用缓存版本提高性能。常用场景包括分布式锁、库存扣减、投票防刷。实现方式是在 Lua 脚本中写 Redis 命令，用 EVAL 或 EVALSHA 执行。

### 4. 热 Key 拆解

热 Key 是访问频率极高的单个 Key，比如爆款商品的详情页缓存。拆解方案有三种。第一种是拆分 Key 的字段，把一个 Key 拆成多个 Key，比如 user:1001:profile 拆成 user:1001:base 和 user:1001:extra，不同字段进不同的 Key，请求时随机或轮询访问不同 Key，这样单个 Key 的 QPS 就降到了原来的 N 分之一。第二种是历史版本加版本号，比如 user:1001:profile:v1、user:1001:profile:v2，读写时带版本号，可以实现灰度发布和热 Key 的平滑迁移。第三种是一致性哈希环，用业务 ID 做哈希后路由到不同的 Redis 节点，比如根据用户 ID % N 确定访问哪个 Key。

### 5. 本地缓存一致性

本地缓存存在与 Redis 的数据一致性问题。第一种方案是设置短 TTL，本地缓存只存活几秒或几十秒，数据最终会过期更新，这种方案简单但无法保证强一致，适合对数据一致性要求不高的场景。第二种方案是消息通知，数据变更时发 MQ，本地缓存监听 MQ 收到消息后 invalidate 对应 Key，这需要引入消息队列，实现相对复杂但一致性更好。第三种方案是给本地缓存加随机 TTL，比如基础 TTL 30 秒再加 0-10 秒随机值，这样不同节点的缓存过期时间分散，避免同时过期导致缓存雪崩。第四种方案是 write-through 或 write-behind，写操作时同步更新本地缓存和 Redis，或者写 Redis 后异步更新本地缓存。实际项目中，Caffeine 设置 30 秒 TTL 加随机 5 秒基本够用。

### 6. 大 Key 处理

大 Key 是单个 Key 的 Value 过大，比如一个 Hash 存了上百万个字段。发现大 Key 可以用 redis-cli --bigkeys 扫描全库找出最大的 Key，或者用 MEMORY USAGE key 精确查看某个 Key 的内存占用。处理大 Key 有几种方式。第一是拆分，将大 Hash 拆成多个小 Hash，比如按用户 ID 哈希后缀分片，user:hash:0、user:hash:1，Set 和 ZSet 可以按 score 或 member 分片。第二是压缩存储，String 类型的大 Value 可以用 snappy 或 gzip 压缩后再存，读的时候解压，虽然压缩解压有 CPU 开销，但省内存，适合 Value 不常访问的场景。第三是异步删除，用 UNLINK 替代 DEL，UNLINK 是后台异步删除不会阻塞 Redis 主线程，如果必须同步删除可以先 SCAN 出所有 field 再分批 HDel。第四是设计层面避免，存之前评估好数据量级，设计合理的 Key 结构，不要把海量数据塞进单个 Key。

---

## Day 36 (03-21) - 今日完成

### ✅ 今日任务

- [x] **项目面试复盘（RAG + 社区平台）**
  
  **RAG 知识库项目 - 面试问答整理**：
  - **Q1 Kafka Pipeline**：前端分块并发上传 → Redis Bitmap 状态管理 → MinIO 分片存储 → 合并 → Kafka 异步解析/向量化。优化点：1GB 文件处理从 15s→3s。
  - **Q2 Redis Bitmap**：O(1) 判断分片状态，内存友好。断点续传：前端重试 → 后端校验 Bitmap → 查 MinIO → 更新状态。
  - **Q3 性能优化**：用 AOP 记录方法耗时，瓶颈在同步等待 CPU + 外部 API，优化手段是改成异步直接返回。
  - **Q4 RBAC + 组织标签**：用户可创建组织标签，父子层级继承权限。表结构：user/role/permission/org/file，查询时 ES 过滤防越权。
  - **Q5 对话上下文**：段落分段 + 10% overlap 防割裂。K 前端传入默认 10，粗排 TopK×30。Prompt 限制兜底输出"不清楚"。
  - **Q6 混合检索**：向量 TopK×30 粗排 → BM25+向量 rescore（权重 1:0.2）→ 人工评估。AB 测试待上线。

  **社区平台项目 - 面试问答整理**：
  - **Q7 缓存一致性**：TTL 30s + 随机 5s 防雪崩。Canal binlog → MQ → 删除 Redis。多节点 Caffeine 用 TTL 兜底，或 MQ 广播失效。
  - **Q8 Canal 同步**：MQ 解耦 + acks=all + 幂等消费 + 多 partition 高可用 + 关闭自动提交 offset。消费失败进死信队列 DLQ。
  - **Q9 海量导出**：线程池参数推导（IO 密集型公式：核心数×11），core=20, max=40, queue=200, CallerRunsPolicy 限流。
  - **Q10 缓存穿透/击穿/雪崩**：
    - 穿透：访问不存在数据 → 布隆过滤器 / 空值缓存
    - 击穿：热 key 失效 → Redisson 分布式锁 / 永不过期
    - 雪崩：大量缓存同时过期 → 随机 TTL + 预热 + 多级缓存

- [x] **synchronized 关键字底层**
  
  **为什么出现？** Java 多线程环境下，需要保证共享变量的可见性、有序性和原子性。

  **底层原理**：
  - **同步代码块**：`monitorenter` 指令获取对象的 monitor（锁），`monitorexit` 释放锁。
  - **同步方法**：ACC_SYNCHRONIZED 标识，方法执行时自动获取 monitor，方法结束释放。
  - **对象头**：`Mark Word` 存储锁状态（无锁/偏向锁/轻量级锁/重量级锁），包含哈希码、GC 年龄、锁标志位、持有锁的线程 ID。

  **锁升级过程（重点！）**：
  - **无锁** → **偏向锁**（第一次获取，线程 ID 写入 Mark Word）→ **轻量级锁**（自旋，CAS 替换 Mark Word）→ **重量级锁**（阻塞，OS 互斥量）。
  - 升级不可逆：偏向锁→轻量级锁→重量级锁。
  - 偏向锁默认延迟 4s 开启，可用 `-XX:BiasedLockingStartupDelay=0` 禁用。

  **synchronized vs Lock**：
  - synchronized：自动获取/释放锁、JVM 层实现、不可中断、非公平锁。
  - Lock：`lock()`/`unlock()` 手动控制、JDK 实现、可中断、公平锁/非公平锁可选、`tryLock()` 尝试获取。

  **使用场景**：
  - 修饰实例方法：锁的是 this 对象。
  - 修饰静态方法：锁的是 Class 对象。
  - 修饰代码块：锁的是指定对象。

- [x] **美团笔试 - 线段树**
  
  **线段树（Segment Tree）**：一种二叉树结构，用于区间查询/更新的 O(logN) 算法。

  **核心操作**：
  - **建树**：`build(node, l, r)`，递归构建，叶子节点存原数组值，内部节点存区间和/最大值等。
  - **区间查询**：`query(node, l, r, ql, qr)`，如果区间完全包含则返回节点值，否则递归左右子树合并。
  - **区间更新**：`update(node, l, r, idx, val)`，找到叶子节点更新后，逐层向上 `pushUp` 合并。

  **代码模板**：
  ```java
  class SegmentTree {
      int[] tree;
      int n;
      
      void build(int[] nums) {
          n = nums.length;
          tree = new int[4 * n];
          build(1, 0, n-1, nums);
      }
      
      void build(int node, int l, int r, int[] nums) {
          if (l == r) {
              tree[node] = nums[l];
              return;
          }
          int mid = (l + r) / 2;
          build(node*2, l, mid, nums);
          build(node*2+1, mid+1, r, nums);
          tree[node] = tree[node*2] + tree[node*2+1]; // 区间和
      }
      
      int query(int node, int l, int r, int ql, int qr) {
          if (ql <= l && r <= qr) return tree[node];
          int mid = (l + r) / 2;
          int sum = 0;
          if (ql <= mid) sum += query(node*2, l, mid, ql, qr);
          if (qr > mid) sum += query(node*2+1, mid+1, r, ql, qr);
          return sum;
      }
      
      void update(int node, int l, int r, int idx, int val) {
          if (l == r) {
              tree[node] = val;
              return;
          }
          int mid = (l + r) / 2;
          if (idx <= mid) update(node*2, l, mid, idx, val);
          else update(node*2+1, mid+1, r, idx, val);
          tree[node] = tree[node*2] + tree[node*2+1];
      }
  }
  ```

  **懒标记（Lazy Propagation）**：区间更新时，不立即下推，标记"待更新"等到查询时再下发。核心：`pushDown(node, l, r)` 传递懒标记到子节点。

  **应用场景**：区间求和/最大值、区间更新（加减）、动态区间查询、主席树前缀。

- [x] **算法练习**
  - [x] 力扣 303 区域和检索（线段树/前缀和）
  - [x] 力扣 307 区域和检索-可变（树状数组/线段树）

> **⏱️ 学习时长**：8h

---

## Day 37 (03-22) - 今日完成

### ✅ 今日完成

- [x] **GC 排查与定位**（Minor/Major/Full GC 触发时机 + jstat/jmap/jstack 命令 + MAT 分析）

- [x] **设计模式深度对比**

  **策略模式 vs 继承**：
  - 继承：编译时绑定，子类强依赖父类，违反开闭原则
  - 策略模式：运行时绑定，通过接口+组合，可在运行时切换不同算法实现
  - 共同点：都用了多态（父类引用指向子类对象），但绑定时机不同

  **模板方法模式**：
  - 核心：父类定义算法骨架（final 方法），子类负责填充具体步骤
  - 适用：流程固定、步骤可变（数据处理：读取→转换→写出）
  - vs 策略：策略是"我想换哪个算法"，模板是"这个流程大家都一样，就中间几步不一样"

  **抽象工厂模式**：
  - 核心：创建一族相关对象（不只是单个对象）
  - 例如：ModernFactory 创建 ModernChair+ModernTable，ClassicalFactory 创建 ClassicalChair+ClassicalTable
  - vs 工厂方法：工厂方法创建单个产品，抽象工厂创建一族产品

  **责任链模式**：
  - 核心：请求沿链传递，每个节点处理自己能处理的，不行就传下一个
  - 典型场景：Spring Security 的 FilterChain
  - vs 策略：策略只选一个执行，责任链是链式传递、消费

  **单例模式**：
  - 核心：整个进程只有一个实例
  - 懒汉式双重检查加 volatile：防止指令重排序（分配内存→构造→赋值，被重排可能导致另一个线程拿到未构造完成的对象）
  - 推荐：枚举式（防反射攻击）

  **发布订阅模式**：
  - 核心：发布者和订阅者通过 Broker 中介解耦，不直接通信
  - vs 观察者：观察者直接通信，发布订阅通过 MQ 中介
  - 典型场景：Kafka，发布消息后多个消费者订阅处理

- [x] **GC 分类澄清 + OOM/GC/CPU 场景题表达**

  **Major GC vs Full GC**：
  - Minor GC：收集新生代，Eden 满触发
  - Major GC：收集老年代，G1/CMS 并发阶段，并发执行无 STW
  - Full GC：清理整个堆（新生代+老年代+Metaspace），STW 时间长
  - 老年代不足时：Minor GC 晋升失败 → Full GC；CMS 并发失败 → Full GC

  **OOM 分类**：
  - OOM: Heap Space → 堆内存耗尽，对象泄漏/大对象
  - OOM: Metaspace → 类加载器泄漏/CGLib/JSP
  - OOM: GC Overhead → GC 开销超过 98%，回收不了

  **面试万能表达模板**：

  1. 描述现象："线上服务出现...，表现为..."
  2. 紧急止血："第一时间止血。OOM → 重启+扩大堆；CPU 100% → kill 高CPU线程"
  3. 分析根因："通过 jstat/jmap/jstack 拿到数据，和业务日志交叉验证，定位到..."
  4. 解决+预防："通过...解决，预防措施是..."

- [x] **CountDownLatch + 60倍性能提升面试表达**

  **CountDownLatch 作用**：让主线程等待所有批次完成后，才开始合并最终文件。把 500 万数据分成 500 个批次（每批 1 万条），16 个线程同时处理。

  **60倍性能提升怎么说**：

  1. 说清对比基准：优化前单线程同步导出 500 万数据需要 60 秒
  2. 说清优化手段：
     - 线程池并行：16 线程处理分页数据
     - FastExcel 批量 API：减少 IO 次数
     - RabbitMQ 异步解耦：查 DB 和写文件不互相阻塞
  3. 给具体数字：60 秒 → 1 秒，[(60-1)/1 ≈ 60x]
  4. 留余地："和服务器配置、数据结构有关，数据量更大时倍数会降低"

- [x] **FastExcel 线程池参数（基于 AMD Ryzen 7 8845H = 8核16线程）**
  - corePoolSize = 8（CPU 核心数，CPU 密集型）
  - maximumPoolSize = 16（2倍核心数，应对突发 IO 等待）
  - keepAliveTime = 60s
  - queue = LinkedBlockingQueue(500)（有界，防止 OOM）
  - rejectedPolicy = CallerRunsPolicy（背压限流，不丢请求）

- [x] **项目细节追问**
  - MinIO 原子性：ComposeObject API，失败则定时任务扫描"合并中"状态重新合并
  - Redis Bitmap key 设计：`userId:fileMd5`，过期时间 1 天
  - 私有库组织标签：递归查询子集合，缓存到 Redis，key = `org:tree:{userId}`

- [x] **面试准备 - 自我介绍与项目介绍**
  - 自我介绍改进版（消除"然后"口头禅，结构化表达：背景→技术→项目→特质）
  - RAG 项目 STAR 法则介绍（情境→任务→行动→结果）
  - 项目难点：文件上传（分片上传、断点续传、状态机、定时补偿）

- [x] **技术选型表达训练**
  - 为什么选 Kafka 而不是 RabbitMQ（吞吐量、顺序性、分区机制）
  - 为什么选 Redis Bitmap 而不是 Hash（内存占用、O(1)查询）
  - 为什么选 ES + 向量混合检索（关键词+语义双引擎）
  - 万能公式：选型技术 + 对比方案 + 核心考量 + 选择理由

- [x] **解决问题表达训练**
  - 性能优化类：问题现象→根因分析→解决方案→量化成果
  - 可靠性类：熔断降级、限流、重试幂等、分布式锁
  - 扩展性类：微服务拆分、分库分表、消息队列解耦

- [x] **面试追问应对**
  - 30% 鉴权优化数据解释（AOP测量 vs 估算，建议说"显著减少"或"20%-40%"）
  - TraceID 构建方案（Snowflake、自定义时间+机器标识+随机数）
  - Redis 内存淘汰策略（allkeys-lru/lfu，过期删除策略）
  - 热点文章列表（ZSet + 滑动窗口，按小时分桶聚合）
  - ES 宕机降级 MySQL（熔断器 Resilience4j，失败率>50%触发降级）

- [x] **Spring Boot 启动流程与 Bean 生命周期**
  - 9 个启动阶段：创建 SpringApplication → 准备环境 → 创建容器 → 刷新容器 → 启动 Tomcat → 回调 Runner
  - Bean 生命周期 12 步：实例化→属性注入→Aware回调→初始化→后置处理→销毁
  - BeanPostProcessor 作用（AOP代理创建、自动装配、属性验证）

- [x] **Caffeine 本地缓存详解（面试版）**
  - **作用**：高性能本地缓存，读写速度比 Redis 快 10-50 倍（本地内存 0.1ms vs 网络 IO 1-5ms）
  - **解决热 Key 击穿**：99% 请求走 Caffeine 本地缓存，只有 1% 穿透到 Redis，Redis 不会被高频请求打挂
  - **二级缓存架构**：Caffeine（L1）→ Redis（L2）→ DB，层层兜底
  - **Redisson 看门狗**：防止缓存击穿，只有一个线程查 DB，其他线程等待
  - **性能提升原理**：
    - 优化前：每次请求 Redis 网络 IO（1-5ms），单节点 QPS 约 300
    - 优化后：99% 请求本地内存（0.1ms），单节点 QPS 3000+，首页加载 4s→1s
  - **面试回答要点**：先查本地→再查 Redis→加锁查 DB，LRU+LFU 淘汰策略，10分钟过期兜底

- [x] **投递情况更新**：
  - 科大讯飞 Java 岗：已投递
  - 滴滴：已投递
  - 快手：已投递  
  - 点点：已投递
  - 欣旺达：已投递
  - 百度：简历挂 ❌
  - 美团 AI：等待面试结果
  - 携程：等待面试通知
  - 京东：等待测评结果

> **⏱️ 学习时长**：8h

---

## Day 38 (03-23) - 今日完成（字节面试）

### ✅ 今日完成

- [x] **字节跳动面试复盘**

  **面试岗位**：后端开发

  **面试问题记录**：
  1. 自我介绍
  2. RAG项目介绍
  3. 为什么选择MySQL存储文件元信息
  4. MySQL中存了哪些文件字段
  5. Redis会话SessionId实现方式
  6. 历史对话携带机制（昨天信息今天是否带上）
  7. ES混合粗排和精排
  8. SQL题：消费者一个月内消费最高的10个
  9. 算法题：合并有序数组

  **问题复盘与改进**：

  **问题1：SQL题表现不佳**
  - 题目：查询消费者一个月内消费最高的10个
  - 我的回答：直接说"写的SQL很少"
  - 问题：给面试官留下基础薄弱印象
  - 改进：即使不会也要展示思路
  - 正确答案：
    ```sql
    SELECT consumer_id, SUM(order_amount) as total_amount
    FROM consumer_order
    WHERE create_time >= DATE_SUB(NOW(), INTERVAL 1 MONTH)
    GROUP BY consumer_id
    ORDER BY total_amount DESC
    LIMIT 10;
    ```
  - 面试话术："这个需求需要按消费者分组汇总，我写的SQL是...实际项目中我更多用MyBatis写动态SQL，这种复杂统计SQL写得少，但我可以快速学习。"

  **问题2：算法题Debug时间过长**
  - 题目：合并有序数组
  - 我的表现：Debug了一会才写出来
  - 问题：双指针基础题不熟练，说明刷题量不够
  - 改进：背熟双指针模板，10分钟内写完
  - 标准模板：
    ```java
    public int[] merge(int[] nums1, int[] nums2) {
        int[] result = new int[nums1.length + nums2.length];
        int i = 0, j = 0, k = 0;
        
        while (i < nums1.length && j < nums2.length) {
            if (nums1[i] <= nums2[j]) {
                result[k++] = nums1[i++];
            } else {
                result[k++] = nums2[j++];
            }
        }
        
        while (i < nums1.length) result[k++] = nums1[i++];
        while (j < nums2.length) result[k++] = nums2[j++];
        
        return result;
    }
    ```

  **问题3：MySQL选型回答太浅**
  - 我的回答：关系型数据库适合做持久化
  - 改进版："文件元信息需要强一致性（状态、权限），且关系复杂（用户-文件-权限多对多），MySQL的事务和ACID特性适合。同时需要支持复杂查询（按用户、按状态、按时间范围），MySQL的索引和SQL灵活性比NoSQL更适合。文件内容本身存MinIO，MySQL只存元信息，分离存储。"

  **问题4：Redis会话描述零散**
  - 我的回答：userId映射会话记录，20条，过期时间
  - 改进版："用户提问时，用userId从Redis查session_key，拿到最近20条历史对话。设置过期时间7天，过期后自动清理。如果没有历史记录，就新建一个session。Redis用Hash结构，field是sessionId，value是JSON数组存对话列表，这样一次查询就能拿到全部历史。"

  **问题5：历史对话机制表述不清**
  - 我的回答：会的，但不是强相关内容会过滤，也可以让大模型指出出处
  - 改进版："会的，默认携带最近20轮对话。但用户可能切换话题，所以我在Prompt里加指令：'如果新问题与历史对话无关，请忽略历史'。同时要求模型回答时引用来源（如'根据文档[1]'），这样用户知道答案来自知识库还是历史对话。"

  **字节面试特点总结**：
  - 算法题：中等难度，要求熟练（20分钟内写完）
  - SQL题：业务场景，考察实际能力
  - 项目深挖：每个细节都可能追问3层

- [x] **面试复盘总结**
  - 暴露问题：SQL基础薄弱、算法题熟练度不够、项目细节表达不够结构化
  - 改进方向：
    1. 每天刷2道算法题（双指针、滑动窗口、二分）
    2. 复习SQL：分组聚合、窗口函数、JOIN
    3. 项目每个细节准备3层追问（是什么→为什么→怎么做）

- [x] **投递状态更新**
  - 字节跳动：已面试，等待结果 ⏳
  - 点点互动：hr初筛 哔哩哔哩 用人部门筛选 360 hr初筛 美团 筛选中

> **⏱️ 学习时长**：6h（含面试40min）
> - 面试参与：**0.7h** (40min)
> - 面试复盘与改进方案整理：**1.5h**
> - 标准面试答案梳理（9道题）：**1.5h**
> - 项目细节深挖（3层追问准备）：**1h**
> - 投递状态更新：**0.3h**
> - 算法模板背诵：**1h**

---

### 📋 标准面试答案（背熟）

**1. 自我介绍**
> 面试官好，我叫薛煌，本科毕业于大连交通大学软件工程专业，GPA 88% 排名学院前 10%，目前在利兹大学攻读计算机科学硕士，预计 2025 年 12 月毕业。竞赛方面，我在大一学年获得蓝桥杯 C++ 组国赛三等奖。技术上，我熟练掌握 Java 后端开发，包括 Spring Boot、MySQL、Redis、Kafka、Elasticsearch 等中间件。项目经历方面，我主导开发了 RAG 企业级知识库平台，实现了大文件分片上传与异步处理流水线，将 1GB 文件处理耗时从 15 秒优化到 3 秒；还开发了技术社区平台，实现了二级缓存架构，单节点 QPS 提升到 3000 以上。希望能加入贵公司，发挥我的技术专长。

**2. RAG项目介绍（STAR法则）**
> **Situation**：企业有大量内部文档，员工需要快速检索和问答，传统搜索无法满足语义理解需求。
> **Task**：我负责后端架构设计，包括大文件上传、权限隔离、对话上下文管理、向量检索架构。
> **Action**：文件上传用分片+Bitmap+MinIO断点续传，1GB文件15s→3s；权限系统用RBAC+组织标签，ThreadLocal减少30%鉴权查询；检索架构用ES+Qwen向量双引擎，KNN+BM25混合重排。
> **Result**：系统支持大文件秒级上传，多维度权限隔离，检索准确率提升40%。

**3. 为什么选择MySQL存储文件元信息**
> 文件元信息需要强一致性（状态、权限），且关系复杂（用户-文件-权限多对多），MySQL的事务和ACID特性适合。同时需要支持复杂查询（按用户、按状态、按时间范围），MySQL的索引和SQL灵活性比NoSQL更适合。文件内容本身存MinIO，MySQL只存元信息，分离存储。

**4. MySQL中存了哪些文件字段**
> 核心字段：file_id（主键）、user_id（上传者）、file_name、file_size、file_md5（秒传校验）、storage_path（MinIO路径）、status（状态：上传中/合并中/已完成）、org_tag（组织标签，权限控制）、create_time、update_time。

**5. Redis会话SessionId实现**
> 用userId作为key，查询最近20条历史对话。Redis用Hash结构，field是sessionId，value是JSON数组存对话列表，一次查询就能拿到全部历史。设置过期时间7天，过期后自动清理。如果没有历史记录，就新建一个session。

**6. 历史对话携带机制**
> 会的，默认携带最近20轮对话。但用户可能切换话题，所以我在Prompt里加指令：'如果新问题与历史对话无关，请忽略历史'。同时要求模型回答时引用来源（如'根据文档[1]'），这样用户知道答案来自知识库还是历史对话。

**7. ES混合粗排精排**
> 先用向量检索返回TopK×30粗排，再用BM25和向量权重融合rescore，BM25权重1，向量权重0.2。这样既能保证语义相关性，又能考虑关键词匹配度。

**8. SQL题：消费者一个月内消费最高的10个**
```sql
SELECT consumer_id, SUM(order_amount) as total_amount
FROM consumer_order
WHERE create_time >= DATE_SUB(NOW(), INTERVAL 1 MONTH)
GROUP BY consumer_id
ORDER BY total_amount DESC
LIMIT 10;
```

**9. 合并有序数组（双指针）**
```java
public int[] merge(int[] nums1, int[] nums2) {
    int[] result = new int[nums1.length + nums2.length];
    int i = 0, j = 0, k = 0;
    while (i < nums1.length && j < nums2.length) {
        if (nums1[i] <= nums2[j]) result[k++] = nums1[i++];
        else result[k++] = nums2[j++];
    }
    while (i < nums1.length) result[k++] = nums1[i++];
    while (j < nums2.length) result[k++] = nums2[j++];
    return result;
}
```

---

## Day 39 (03-24) - 今日完成

### 🎯 求职进展
- **美团**：明日面试准备 ✅

---

### 📚 八股文复习

#### 1. ThreadLocal 底层原理 ⭐⭐⭐⭐⭐

**存储结构**：
- 每个 Thread 维护一个 `ThreadLocalMap`
- 底层是 Entry 数组（线性探测法解决哈希冲突）
- Key：ThreadLocal 对象（弱引用）
- Value：实际存储的值（强引用）

**内存泄漏场景**：
| 场景 | 是否泄漏 | 原因 |
|------|----------|------|
| 单线程 | ❌ 不会 | 线程销毁，ThreadLocal 和 Map 引用断开，GC 回收 |
| 线程池 | ⚠️ 会 | 线程复用不销毁，Value 强引用无法回收，导致内存泄漏 |

**解决方案**：`threadLocal.remove()` 必须在使用后调用

**耗时：1h**

---

#### 2. Java 同步机制分层 ⭐⭐⭐⭐⭐

| 层级 | 实现 | 说明 |
|------|------|------|
| **语言层面** | `synchronized` 关键字 | 语法糖 |
| **字节码层面** | `monitorenter` / `monitorexit` | 进入/退出临界区指令 |
| **JVM逻辑层面** | Monitor 监视器 | Owner + EntryList + WaitSet |
| **操作系统层面** | 用户态→内核态切换 | 申请 mutex 互斥量，重量级锁开销大 |

**Monitor 内部结构**：

- **Owner**：当前持有锁的线程
- **EntryList**：等待锁的阻塞线程队列
- **WaitSet**：调用 `obj.wait()` 后进入等待的线程队列

**加锁流程**：
1. 执行 `monitorenter` → JVM 检查 Monitor
2. Owner 为空 → 成为 Owner；被占用 → 进入 EntryList 阻塞
3. 执行 `monitorexit` → 释放锁，唤醒 EntryList 线程竞争

**耗时：1h**

---

#### 3. Security Filter vs Interceptor ⭐⭐⭐⭐

| 组件 | 位置 | 作用范围 | 目的 |
|------|------|----------|------|
| **Filter** | Servlet 层 | 所有请求（静态资源+Controller） | 过滤请求（认证、鉴权、编码） |
| **Interceptor** | SpringMVC 层 | 仅 Controller 请求 | 拦截业务逻辑（日志、权限校验） |

**执行顺序**：Filter → Servlet → Interceptor → Controller

**耗时：0.5h**

---

#### 4. Spring Boot 自定义线程池 ⭐⭐⭐⭐

**为什么要自定义**：
1. **等待队列**：默认是无界队列 → 容易 OOM
2. **拒绝策略**：可根据业务自定义（如记录日志、降级处理）
3. **监控指标**：队列大小、活跃线程数、CPU 利用率

**核心监控指标**：
- 等待队列数量
- 空闲线程数量
- CPU 利用率

**耗时：0.5h**

---

### 📋 今日新增面试准备（03-24 晚）

#### 1. 深分页优化思路 ⭐⭐⭐⭐

**问题**：`LIMIT 1000000, 10` 需要扫描 1000010 行，性能极差

**优化方案**：
| 方案 | 原理 | 适用场景 |
|------|------|---------|
| **游标分页** | 记录上次查询的最大值，下次从这个值开始 | 实时性要求不高 |
| **覆盖索引** | 只查索引字段，不回表 | 只需要 ID 或索引字段 |
| **ES 搜索** | 用搜索引擎代替数据库分页 | 复杂查询、大数据量 |
| **业务限制** | 限制最大页码（如只能翻到 100 页） | 用户体验可接受 |

**游标分页代码**：
```java
// 第一次查询
List<Article> list = articleMapper.selectByCursor(null, 10);
Long lastId = list.get(list.size() - 1).getId();

// 下次查询，从 lastId 开始
List<Article> nextList = articleMapper.selectByCursor(lastId, 10);
// SQL: SELECT * FROM article WHERE id < #{lastId} ORDER BY id DESC LIMIT 10
```

---

#### 2. ES 使用中的问题及解决方案 ⭐⭐⭐⭐⭐

| 问题 | 现象 | 解决方案 |
|------|------|---------|
| **脑裂** | 多个节点认为自己是 Master | `discovery.zen.minimum_master_nodes: n/2+1` |
| **分片不均衡** | 某些节点负载过高 | 手动 reroute，或开启自动均衡 |
| **内存溢出** | ES 进程被 OOM | 限制 JVM 堆内存（不超过 32G） |
| **查询慢** | 复杂查询耗时久 | 优化分片数量，使用 filter 缓存 |
| **写入瓶颈** | 写入速度跟不上 | 批量写入（bulk），调整 refresh_interval |

**面试回答**：
> "ES 使用中遇到过分片不均衡和查询慢的问题。分片不均衡通过手动 reroute 解决；查询慢通过优化分片数量（单分片不超过 30G）、使用 filter 缓存、以及预热热点数据解决。"

---

#### 3. Kafka 分区设置与顺序性保证 ⭐⭐⭐⭐⭐

**RAG 项目 Kafka 配置**：
```java
// 生产者配置
props.put("acks", "all");                    // 所有副本确认
props.put("retries", 3);                     // 失败重试
props.put("max.in.flight.requests.per.connection", 1);  // 禁止重试时乱序
props.put("enable.idempotence", "true");     // 幂等性

// 分区策略：按 fileId 分区，保证同一文件的消息顺序
producer.send(new ProducerRecord<>("file.process", fileId, json));
```

**分区设置**：
- **3 个分区**：根据文件 ID 哈希取模，分散负载
- **key=fileId**：保证同一文件的所有消息进入同一分区
- **单分区单线程消费**：保证消息处理顺序

**顺序性保证**：
1. 同一分区内部有序（key 相同进入同一分区）
2. 单线程消费（每个分区一个消费者线程）
3. 幂等性（`enable.idempotence=true`）
4. 手动提交 offset（处理完再 commit）

---

#### 4. 线程池使用场景与参数设置 ⭐⭐⭐⭐⭐

**使用场景**：
1. **FastExcel 批量导出**：16 线程并行处理 500 万数据
2. **Kafka 消息异步处理**：异步处理不阻塞消费
3. **定时任务并行执行**：每天 2 点并行执行任务

**参数设置（AMD 8845H 8核16线程）**：
```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    8,                          // corePoolSize：CPU 核心数
    16,                         // maximumPoolSize：2倍核心数
    60L,                        // keepAliveTime：60s
    TimeUnit.SECONDS,
    new LinkedBlockingQueue<>(500),  // 有界队列，防止 OOM
    new ThreadPoolExecutor.CallerRunsPolicy()  // 拒绝策略
);
```

**参数设置思路**：
- **corePoolSize**：CPU 密集型=核数，IO 密集型=核数×2
- **maximumPoolSize**：核心数×2，应对突发流量
- **workQueue**：有界队列，防止 OOM
- **拒绝策略**：CallerRunsPolicy，调用线程执行，自然限流

---

#### 5. Spring Boot 自动装配与 Bean 生命周期 ⭐⭐⭐⭐⭐

**自动装配原理**：
```
启动读 factories → 检查依赖 → 条件判断 → 自动创建 Bean
```

**记忆口诀**：
```
启动读 factories，
检查依赖有没有，
条件满足就装配，
省得自己写配置。
```

**Bean 生命周期（11步）**：
```
1. 实例化（new）
2. 属性注入（@Autowired）
3. 设置 BeanName
4. 设置 BeanFactory
5. 设置 ApplicationContext
6. 前置处理（BeanPostProcessor）
7. 初始化（@PostConstruct）
8. 后置处理（AOP 代理创建）
9. 使用中（单例缓存）
10. 销毁前（@PreDestroy）
11. 销毁（DisposableBean）
```

**记忆口诀**：
```
出生new，注入@Autowired，
起名认爹知环境，
前置处理要整容，
@PostConstruct初始化，
工作单例缓存中，
@PreDestroy退休，
最后销毁进GC。
```

---

#### 6. TCP 快速重传（三个 ACK）⭐⭐⭐⭐

**原理**：
- 收到 3 个重复 ACK，说明后面的包到了但前面的丢了
- 1-2 个 dup ACK 可能是乱序，3 个大概率真的丢包
- 立即重传丢失的包，不需要等 RTO 超时

**为什么是 3 个？**

- 工程实践中的平衡点（RFC 2581 规定）
- 太少容易误判（乱序触发重传）
- 太多等待太久（延迟高）

---

#### 7. 段内存与页内存 ⭐⭐⭐⭐

**分段（Segmentation）**：
- 按程序逻辑划分（代码段、数据段、堆、栈）
- 优点：便于权限管理、逻辑隔离
- 缺点：外部碎片

**分页（Paging）**：
- 把内存切成固定大小的页（4KB）
- 优点：无外部碎片、离散分配、支持虚拟内存
- 缺点：页表占用内存

**现代 OS（段页式）**：

- 分段用于权限管理
- 分页用于内存分配

---

#### 8. Minor GC 触发时机 ⭐⭐⭐⭐

**触发条件**：**Eden 区满了**

**过程**：
1. Eden + From Survivor 的存活对象 → 复制到 To Survivor
2. 对象年龄 +1
3. 年龄达到 15（默认）→ 晋升老年代
4. Eden 和 From Survivor 清空
5. From 和 To 交换角色

---

#### 9. OOM / 频繁 Full GC / CPU 飙高解决思路 ⭐⭐⭐⭐⭐

**万能解题框架**：
```
确认现象 → 紧急止血 → 定位根因 → 彻底解决 → 预防措施
```

**OOM 定位**：
```bash
jmap -dump:format=b,file=heap.hprof <pid>
# MAT 分析：Histogram → Dominator Tree → Path to GC Roots
OOM 先判断类型。如果是 heap space，用 jmap dump 堆文件，MAT 分析 Histogram 找到占用最大的类，Path to GC Roots 追踪引用链定位代码。如果是 Metaspace，检查是否有动态代理或反射滥用。如果是 GC overhead，说明内存泄漏，对象无法回收。" **防追问：如何定位到代码？** > "MAT 的 Path to GC Roots 可以看到引用链，比如 `UserService` 被 `StaticMap` 持有，`StaticMap` 又被 `ClassLoader` 持有，就能定位到是 UserService 的静态缓存没清理导致泄漏
```

**频繁 Full GC 定位**：
```bash
jstat -gcutil <pid> 1000
# 看 O（老年代）占用和 FGC（Full GC 次数）
```

**CPU 飙高定位**：
```bash
top -c                          # 找高 CPU 进程
ps -mp <pid> -o THREAD,tid      # 找高 CPU 线程
printf "%x\n" <tid>             # 转 16 进制
jstack <pid> | grep <16进制tid> # 查看堆栈
---
CPU 飙高原因 ↓
├─ 业务逻辑复杂
│ → 死循环、无限递归
│ → 大量计算（如复杂正则）
│
├─ 频繁 GC
│ → GC 线程占用 CPU
│
├─ 线程竞争
│ → 锁竞争激烈，线程上下文切换
│
└─ IO 问题 → 虽然 CPU 不高，但线程阻塞，看起来忙
```

**面试回答模板**：
> "我的排查步骤：1. 确认现象（top/ps）；2. 紧急止血（保留现场/重启）；3. 定位根因（jstack/jmap）；4. 解决（修复代码/调优内存/优化并发）；5. 预防（监控/压测/代码审查）。"

---

### ⏱️ 总时长：8h（含面试准备 5h）

**明日重点**：
- 美团面试（已约）
- 继续复习 SQL 和算法
- 背诵今日整理的面试答案

---

## Day 40 (03-25)

### 🎯 美团1面复盘

**面试岗位**：Java后端开发

**面试问题记录**：
1. **算法题**：整数反转（处理溢出问题）
2. **项目介绍**：RAG企业级知识库平台
3. **项目深挖**：设计和实现时间占比？AI代码和自己代码占比？
4. **测试验证**：如何测试和验证RAG系统效果？
5. **召回排序**：召回和排序策略是怎样的？如何优化召回率？
6. **系统性能**：响应耗时和瓶颈分析
7. **Java基础**：基础数据类型、int vs Integer、默认值处理
8. **String比较**：`new String("ABC")`的equals和==比较
9. **集合框架**：ArrayList vs LinkedList、HashMap原理
10. **并发编程**：CAS机制、线程创建方式、线程池使用（结合CountDownLatch）
11. **AI设计题**：支持自然语言预约的用户中心系统设计

---

### 📊 问题复盘与改进

**问题1：算法题边界处理不足 ⭐⭐⭐**
- **题目**：整数反转，需要处理int溢出问题
- **我的表现**：知道要判断溢出，但设置的`Max value`判断没成功，需要面试官提示用`long`类型作为中间变量
- **核心问题**：溢出可能发生在最后一步乘以10或加上个位数**之前**，判断逻辑不完备
- **改进方案**：
  ```java
  public int reverse(int x) {
      long result = 0;  // 用long作为中间变量
      while (x != 0) {
          result = result * 10 + x % 10;
          if (result > Integer.MAX_VALUE || result < Integer.MIN_VALUE) {
              return 0;  // 溢出返回0
          }
          x /= 10;
      }
      return (int) result;
  }
  ```
- **面试话术**："我先用long类型存储中间结果，每次循环后检查是否超出int范围，如果溢出就返回0，最后强转回int。"

**问题2：Java基础不牢固 ⭐⭐⭐**

- **问题1**：变量默认值处理混乱
  - 表现：知道成员变量有默认值，但局部变量没有默认值的情况回答不清晰
  - 正确答案：成员变量（全局）有默认值（int=0, boolean=false），局部变量必须显式初始化，否则编译报错
  
- **问题2**：Integer比较
  - 表现：知道要用equals比较值，但比较大小时没说用compareTo或自动拆箱
  - 正确答案：`a.compareTo(b)` 或 `a > b`（自动拆箱后比较）
  
- **改进方案**：系统复习Java基础，特别是：
  - 基本类型 vs 包装类（自动装箱/拆箱）
  - Integer缓存池（-128~127）
  - 变量作用域和默认值
  - String常量池和new String的区别

**问题3：项目技术深度表达不够 ⭐⭐**
- **问题**：AI代码和自己代码占比？
  - 我的回答：AI写的更多，我是"总纲领设计"
  - 改进版："AI承担约70%代码生成，我负责架构设计、核心模块划分、边界条件定义和代码Review。关键业务逻辑（如权限控制、召回策略）由我设计，AI辅助实现，最后我进行集成测试和优化。"

- **问题**：如何测试验证RAG效果？
  - 我的回答：离线测试，构建测试集
  - 改进版："分两层验证：1）**代码层**：AI生成单元测试+压力测试，设定边界条件；2）**效果层**：离线构建测试集（包含高相关文档+干扰项），输入问题后评估召回准确率，核心指标是recall@k。线上通过用户反馈（点赞/点踩）持续优化。"

**问题4：系统设计思路不够直接 ⭐⭐**

- **问题**：AI设计题中过于关注Prompt工程
- **我的表现**：尝试构建复杂Prompt，被平台限制打断
- **改进方案**：设计题应先宏观后微观：
  1. **需求分析**：核心功能、用户场景、数据规模
  2. **模块划分**：用户模块、预约模块、通知模块、AI解析模块
  3. **数据模型**：预约记录表（时间、状态、用户）、时间段可用性表
  4. **关键逻辑**：时间段冲突检测算法、自然语言→结构化数据解析
  5. **技术选型**：AI用LLM API，存储用MySQL+Redis缓存

---

### ✅ 面试亮点总结

**亮点1：项目经验丰富**
- RAG项目技术栈完整（向量检索、大模型、异步处理）
- 对召回策略（粗排+精排）、BM25+向量分数融合有深入理解
- 性能优化意识（1GB文件15s→3s优化经验）

**亮点2：工程思维强**
- 线程池使用考虑OOM防护（有界队列+CallerRunsPolicy）
- 测试策略分层（代码层+效果层）
- 监控意识（CPU利用率、队列大小动态调整）

**亮点3：沟通能力强**
- 遇到算法困难时主动与面试官交流
- 设计题中主动提出关键问题（是否需要持久化、如何判断时间段可用）
- 能够清晰表达技术选型的权衡

---

### 📝 标准面试答案（背熟）

**1. 整数反转（带溢出检测）**
```java
public int reverse(int x) {
    long result = 0;
    while (x != 0) {
        result = result * 10 + x % 10;
        if (result > Integer.MAX_VALUE || result < Integer.MIN_VALUE) {
            return 0;
        }
        x /= 10;
    }
    return (int) result;
}
```

**2. HashMap原理（精简版）**
> "HashMap底层是数组+链表+红黑树。key的hashCode经过扰动函数（高16位异或低16位）后，用位运算`&(n-1)`定位数组下标。冲突时用拉链法，链表长度>8且数组容量>64时转红黑树，查询从O(n)优化到O(log n)。负载因子0.75，超过阈值扩容为2倍。容量是2的幂次方是为了用位运算代替取模，提高效率。"

**3. CAS机制**
> "CAS是比较并交换的乐观锁机制，包含三个操作数：内存位置V、预期值A、新值B。当V等于A时，才将V更新为B。优点是并发高，没有线程阻塞开销；缺点是可能产生ABA问题（用版本号解决）、自旋消耗CPU。Java中AtomicInteger、ConcurrentHashMap都用CAS实现。"

**4. 线程池+CountDownLatch使用场景**
> "我用线程池+CountDownLatch做过Excel多Sheet并发下载。每个Sheet一个任务提交到线程池，主线程用CountDownLatch等待所有任务完成后再合并文件。线程池参数：核心线程数=CPU核数，最大线程数=CPU核数*2，队列用有界队列（防OOM），拒绝策略用CallerRunsPolicy（让提交线程自己执行，起到限流作用）。"

---

### 🎯 面试官反馈（关键）

> "整体表现都很不错，**基础还是要做扎实一些**。对于边界的考虑不多，多练多思考。可以在加强基础知识的时候形成一些自己的体系，借助AI多把知识面探索开。"

**核心问题**：**基础不牢，边界考虑不足**

---

### 📋 改进计划

| 优先级 | 改进项 | 具体行动 | 时间 |
|--------|--------|----------|------|
| 🔴 P0 | Java基础夯实 | 系统复习数据类型、包装类、String、集合源码 | 每天1h |
| 🔴 P0 | 算法边界处理 | 刷题时重点关注溢出、空值、边界条件 | 每天2题 |
| 🟡 P1 | 项目表达结构化 | 准备STAR法则+3层追问答案 | 每天1个模块 |
| 🟡 P1 | 系统设计能力 | 学习经典设计案例，形成方法论 | 每周2个案例 |
| 🟢 P2 | 知识体系建设 | 用思维导图整理Java知识体系 | 每周更新 |

---

### ⏱️ 学习时长统计

| 项目 | 时长 | 说明 |
|------|------|------|
| 美团1面 | 1h | 实际面试时间 |
| 面试复盘 | 1.5h | 问题整理+改进方案 |
| 标准答案整理 | 1h | 8道题的标准回答 |
| Java基础复习 | 2h | 包装类、集合源码 |
| 算法练习 | 1.5h | 整数反转+边界处理 |
| **总计** | **7h** | - |

---

**明日重点**：
- 继续夯实Java基础（String、集合源码）
- 刷2道算法题（重点关注边界条件）
- 背诵今日整理的标准答案





## Day 41 (03-30)

### 📤 投递进展
- 作业帮、菜鸟、招银、客路已投递

---

### 📚 八股文自问自答

#### 1. 类加载每一步具体做了什么

类加载分为三个阶段：加载、链接、初始化。

**加载阶段**：读取 .class 字节码文件，验证字节码格式是否符合 JVM 规范，根据字节码信息创建 Class 对象存入方法区。

**链接阶段**分三步：
- 验证：检查字节码是否符合 JVM 规范，防止恶意代码
- 准备：为静态变量分配内存并设置默认值（int=0，对象=null），注意这时候还没执行赋值语句
- 解析：把符号引用转成直接引用，比如把方法名转成内存地址

**初始化阶段**：执行 `<clinit>` 方法，这个方法由静态变量赋值和静态代码块组成，按代码顺序从上到下执行。只有主动引用才会触发初始化，比如 new 对象、调用静态方法、反射 Class.forName()。

---

## Day 42 (03-31)

### 📤 投递进展
- 作业帮已发笔试

---

### 📚 八股文自问自答

#### 1. AQS 如何实现公平锁和非公平锁

AQS（抽象队列同步器）底层维护了一个 volatile 修饰的 state 变量和一个 CLH 双向队列。公平锁和非公平锁的区别就在于怎么唤醒等待队列里的线程：公平锁按先进先出顺序唤醒，非公平锁让新来的线程直接尝试抢锁，抢不到再排队。

具体实现上，非公平锁的 lock() 方法一上来就用 CAS 尝试获取锁，不管队列里有没有人等；公平锁会先检查队列里有没有等待的线程，有就老老实实排队。共享锁和排他锁的区别在于 state 的操作：排他锁 state=0 表示空闲，>0 表示被占用且可重入；共享锁 state 表示可用资源数，获取时 state--，释放时 state++。

---

#### 2. MySQL B+树能否换成红黑树？加上双向链表呢？

不能换。MySQL 选 B+树的核心目的是减少磁盘 IO 次数。B+树只有叶子节点存数据，非叶子节点只存索引，这样一页能存更多索引项，树就变得"矮胖"。3 层 B+树就能存 2000 万条数据，意味着查一条数据最多 3 次 IO。

红黑树是二叉树，树高和节点数是 log₂N 的关系，存 2000 万数据树高得几十层，意味着几十次 IO，性能直接崩。而且 B+树叶子节点本身就用双向链表连起来了，天然支持范围查询，红黑树就算加双向链表也得先找到起点再遍历，不如 B+树直接。

---

#### 3. `SELECT * FROM table WHERE a > 0 AND b = 0` 索引怎么加？

这种情况要加联合索引 `(b, a)`。原因是最左前缀原则：b 是等值查询，可以用索引；a 是范围查询，会导致后续索引失效，所以 a 放后面。如果只查部分列，可以考虑覆盖索引，把查询列都放进索引里，避免回表。

如果 a 和 b 都是范围查询，那就只能建单列索引让优化器自己选，或者改写 SQL 拆成多个查询再合并。

---

#### 4. MySQL 为了加速写入速度做了哪些优化？

首先是 WAL（预写式日志）：不直接写数据文件，先写 redo log，顺序写比随机写快很多。redo log 是循环写入的，写满了才刷盘。两阶段提交保证 redo log 和 binlog 一致性：先写 redo log（prepare 状态）→ 写 binlog → 提交 redo log（commit 状态）。

其次是 Buffer Pool：热点数据和索引页缓存在内存里，读写在内存完成，定期刷盘。Change Buffer 专门缓存非唯一二级索引的修改，等空闲时再合并。Doublewrite Buffer 防止页损坏，写数据前先写一份副本，宕机恢复时用副本修复。

刷盘时机：后台线程每秒刷一次、脏页比例超过阈值触发刷盘、redo log 写满触发刷盘、正常关闭时刷盘。

---

#### 5. 如何提升单机系统的并发？

主要用 JUC 包。线程池管理线程生命周期，避免频繁创建销毁的开销；CountDownLatch 让主线程等待多个子任务完成；CyclicBarrier 让多个线程互相等待，到齐了一起执行；CompletableFuture 实现异步编排，thenApply、thenCombine 链式调用。

线程安全方面：用 ConcurrentHashMap 代替 HashMap，分段锁+CAS；CopyOnWriteArrayList 适合读多写少；AtomicInteger 等原子类用 CAS 实现无锁并发。锁优化方面：减小锁粒度（ConcurrentHashMap 分段）、读写分离（ReentrantReadWriteLock）、乐观锁（版本号、CAS）。

---

#### 6. ThreadLocal 和 synchronized 的作用及设计思想？

ThreadLocal 是为了获得线程独立副本。线程之间共享堆内存，但有时候需要线程私有的变量（比如用户上下文、数据库连接）。底层是每个 Thread 维护一个 ThreadLocalMap，Key 是 ThreadLocal 对象（弱引用），Value 是值。内存泄漏问题：Key 被回收后 Value 还在，用完必须手动 remove()。

synchronized 是实现悲观锁的关键。底层是 JVM 内置的 Monitor 监视器锁，每个对象都有一个 Monitor。Monitor 里维护了 Owner（当前持有锁的线程）、EntryList（等待锁的线程队列）、WaitSet（调用了 wait() 的线程队列）。锁升级过程：无锁 → 偏向锁（单线程重入）→ 轻量级锁（CAS 自旋）→ 重量级锁（阻塞等待）。

---

#### 7. 极端场景：数据库、SQL 正常，但峰期接口有毛刺、RT 波动，监控正常，只有线程状态不正常，怎么排查？

这种问题通常在应用层，不在数据库层。

第一步看线程状态：jstack 导出线程栈，大量 BLOCKED 说明锁竞争激烈，大量 WAITING 说明在等外部资源（IO、锁、连接池），大量 RUNNABLE 可能是死循环或 CPU 密集计算。

第二步看 GC：jstat -gcutil 查看 GC 频率，Full GC 会触发 STW 导致接口超时，排查是否有大对象或内存泄漏。

第三步看连接池：数据库连接池耗尽会导致线程等待，检查活跃连接数和最大连接数配置；Redis 连接池同理。

第四步看线程池：队列积压、拒绝策略触发次数、线程池满导致任务排队。

第五步看外部依赖：第三方接口慢、网络抖动、DNS 解析慢。

---

#### 8. 千万级数据表 CURD 效率低，如何优化？

分两个层面：查询优化和架构优化。

**查询优化**：避免 `SELECT *`，只查需要的列；小表驱动大表，JOIN 时小表放左边；EXPLAIN 分析执行计划，看 key（用了哪个索引）、type（访问类型，避免 ALL 全表扫描）、rows（扫描行数）、Extra（是否用了覆盖索引）；索引失效场景要避开，比如函数操作、类型转换、`!=`、`OR`、`LIKE '%xx'`。

**架构优化**：分库分表，垂直拆分（按字段拆，大字段单独表）、水平拆分（按数据量分片，取模、范围、一致性哈希）；读写分离，主库写从库读；冷热分离，历史数据归档到独立表；缓存热点数据到 Redis。

---

#### 9. String 为什么设计为不可变类型？

安全性：String 被大量用于核心 API（数据库连接 URL、文件路径、网络地址），如果可变可能被恶意篡改导致安全问题。

线程安全：final 修饰，创建后不可修改，天然线程安全，多线程并发读无需加锁。

哈希缓存：String 的 hashCode() 会缓存起来，作为 HashMap 的 key 时性能极高，不用每次都计算。

字符串常量池：相同字符串共享同一个对象，节省内存。`String a = "abc"` 直接从常量池取，`new String("abc")` 会在堆上新建对象。

---

#### 10. 如何创建线程池？为什么要线程池？单核 CPU 多线程是否有用？

创建线程池用 `ThreadPoolExecutor` 手动配置参数，不推荐 `Executors` 工厂方法，因为 `FixedThreadPool` 和 `SingleThreadExecutor` 用无界队列容易 OOM，`CachedThreadPool` 线程数无上限也会 OOM。

为什么要用线程池：一是异步处理提高响应速度；二是线程复用，减少创建销毁的开销；三是统一管理，可以监控队列大小、活跃线程数、拒绝策略触发次数；四是限流保护，有界队列 + 拒绝策略防止系统过载。

单核 CPU 多线程：是并发不是并行，CPU 按时间片轮转执行多个线程。适合 IO 密集型任务（等待 IO 时切换到其他线程），不适合 CPU 密集型任务（频繁上下文切换反而降低性能）。

---

#### 11. 单线程 Redis 为什么那么快？

Redis 瓶颈在网络 IO 而不是 CPU 计算，单线程反而避免了上下文切换和锁竞争的开销。基于内存操作，比磁盘快 10 万倍。IO 多路复用（epoll）让单线程能处理大量并发连接。数据结构做了优化：SDS（简单动态字符串）O(1) 获取长度，跳跃表（ZSet）O(logN) 查询，渐进式 rehash（Hash）不阻塞。

项目中用 Redis：用户信息缓存（减少数据库查询）、组织权限缓存（权限校验加速）、会话存储（用户对话历史）、Bitmap（文件分片上传状态）。

---

#### 12. Spring Boot 启动流程

分五步：创建 SpringApplication → 准备环境 → 创建容器 → 刷新容器 → 回调 Runner。

具体来说：new SpringApplication() 构造函数里判断 web 应用类型、加载 Initializer 和 Listener；run() 方法里，先创建 Environment 加载配置，然后创建 ApplicationContext（AnnotationConfigServletWebServerApplicationContext），刷新容器（扫描 Bean、自动装配、Bean 实例化和初始化），启动内嵌 Tomcat，最后执行 ApplicationRunner 和 CommandLineRunner 回调。

自动装配原理：启动时读取 `META-INF/spring.factories`，加载 `EnableAutoConfiguration` 对应的配置类，根据 `@ConditionalOnClass`、`@ConditionalOnMissingBean` 等条件判断是否生效。

---

#### 13. 线程池工作原理

任务提交后，先判断核心线程是否已满，未满就创建核心线程执行任务；已满就加入等待队列；队列满了就创建非核心线程；线程数达到 maximumPoolSize 就执行拒绝策略。

拒绝策略有四种：AbortPolicy（抛异常，默认）、CallerRunsPolicy（调用线程执行，自然限流）、DiscardPolicy（直接丢弃）、DiscardOldestPolicy（丢弃队列最老的任务）。核心线程默认不销毁，非核心线程空闲超过 keepAliveTime 会销毁。

---

#### 14. MySQL 三种日志

**binlog**：逻辑日志，记录 SQL 语句或行变更。三种格式：Statement（SQL 语句，可能主从不一致）、Row（行变更，数据准确但文件大）、Mixed（自动选择）。用途：主从复制、数据备份、增量恢复。

**redo log**：物理日志，记录页的修改。InnoDB 特有，循环写入。用途：宕机恢复（WAL 机制）。两阶段提交保证和 binlog 一致。

**undo log**：逻辑日志，记录相反操作。用途：事务回滚（原子性）、MVCC（多版本并发控制）。读操作通过 undo log 版本链 + Read View 判断可见性。

---

#### 15. 基于 Redis 的分布式锁

基础实现：`SETNX key value` 加锁，`EX` 设置过期时间。问题：SETNX 和 EX 不是原子操作（可用 SET key value NX EX 替代）；锁误删（业务执行时间超过过期时间）。

Redisson：Java 客户端封装，解决了上面的问题。可重入锁：同一个线程可以多次获取同一把锁，state 计数器记录重入次数。看门狗机制：后台线程每 10 秒检查锁是否还存在，存在就续期到 30 秒，防止业务没执行完锁就过期。底层用 Lua 脚本保证原子性。

---

#### 16. 消息队列的优点及消息重复消费/丢失问题

**三大优点**：异步解耦（发消息就返回，不关心后续处理）、削峰填谷（高峰期消息队列缓冲突发流量）、扩展性（增加消费者就能提升处理能力）。

**重复消费**：生产端开启幂等性校验（消息 ID 去重）；消费端用本地消息表记录已处理的消息 ID，消费前查表判断；有状态的任务用状态机控制（已处理就不能再处理）。

**消息丢失**：分三段防护。生产端：acks=all（所有副本确认）、失败重试、开启幂等性。Broker：持久化到磁盘、配置刷盘策略、多副本同步。消费端：关闭自动提交 offset，处理完业务再手动提交。

---

#### 17. FunctionCall 和 MCP 的区别

**Function Call**：大模型调用预定义函数的能力。模型输出函数名和参数，应用层执行函数后把结果返回给模型继续对话。比如 ChatGPT 调用 `get_weather(city)`，应用层执行后返回天气数据。是模型层面的一种输出格式。

**MCP（Model Context Protocol）**：Anthropic 提出的开放协议，定义了 AI 模型与外部工具、数据源之间的标准通信方式。MCP 是协议层的标准，解决"AI 如何统一接入各种工具"的问题。一个 MCP Server 可以暴露资源、提示、工具三种能力，任何支持 MCP 的 AI 客户端都能直接调用。

**区别**：Function Call 是大模型的能力，让模型能输出结构化调用指令；MCP 是协议标准，让工具提供方能用统一格式暴露能力，AI 应用方能用统一方式接入。MCP 解决的是生态碎片化问题，Function Call 解决的是模型输出问题。

---

### ⏱️ 学习时长：6h



y
