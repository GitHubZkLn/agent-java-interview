# Java 集合、反射、JUC 并发编程
# 深度面试题库 · 面试口述版

---

## 第一部分：Java 集合框架

---

### 问题 1：ArrayList 和 LinkedList 的核心区别是什么？各自适用什么场景？

**面试口述版：**

> 面试官好，ArrayList 和 LinkedList 的核心区别在于底层数据结构的取舍不同。
>
> **ArrayList** 用的是动态数组。优点是随机访问快——因为数组是连续内存，直接通过索引就能算出地址，时间复杂度 O(1)。缺点是插入删除慢——往中间插一个元素，需要把后面的元素全部往后搬移，最坏 O(n)。
>
> **LinkedList** 用的是双向链表。优点是插入删除快——已知位置的情况下，只需要修改前后两个指针，时间复杂度 O(1)。缺点是随机访问慢——每次访问都要从头或从尾遍历，O(n)。
>
> **实际选型建议：** 大多数业务场景用 ArrayList。因为实际业务中随机访问远比中间插入删除更常见。而且 ArrayList 有 CPU 缓存友好的优势——连续内存，预读取机制让访问更快。LinkedList 适合做队列（头尾操作频繁）或者需要大量中间插入删除的场景。
>
> **补充一点：** LinkedList 同时实现了 Queue 和 Deque 接口，可以直接当双端队列用。在 JDK 1.8 之后，LinkedList 作为队列使用时，底层是通过 Node 节点组成双向链表实现的。

---

### 问题 2：HashMap 的底层数据结构是什么？JDK 1.7 和 JDK 1.8 有什么区别？

**面试口述版：**

> HashMap 是 Java 面试的必考题，我分两个版本来讲。
>
> **JDK 1.7 的实现：** 底层是数组 + 链表，使用头插法。头插法有个严重问题——并发下 resize 时，链表节点会被反转，形成环形链表。多线程遍历时会死循环，CPU 打满。这就是 HashMap 线程安全问题的根源。
>
> **JDK 1.8 的改进：** 底层变成数组 + 链表 + 红黑树。当链表长度超过 8 且数组长度超过 64 时，链表转成红黑树，查询效率从 O(n) 降到 O(log n)。同时改用尾插法，避免了环形链表问题。
>
> **扰动函数的变化：** JDK 1.7 用 4 次位运算 + 5 次异或，JDK 1.8 简化成 1 次位运算 + 1 次异或。原因是 JDK 1.8 引入了红黑树，长链表的查询效率已经大幅提升，不需要过度依赖扰动函数来分散 hash。
>
> **面试加分点：** 可以提到 JDK 1.7 头插法还有另一个问题——resize 时可能导致数据丢失（两个线程同时插入，某个节点的 next 指针被覆盖）。并发场景下统一推荐使用 ConcurrentHashMap。

---

### 问题 3：HashMap 线程不安全体现在哪些方面？ConcurrentHashMap 是如何解决这个问题的？

**面试口述版：**

> HashMap 线程不安全主要体现在三个场景。
>
> **第一，数据丢失。** 并发 put 时，两个线程同时写入同一个桶，后一个线程的插入会覆盖前一个线程的插入。
>
> **第二，环形链表死循环。** JDK 1.7 的头插法 + resize 并发执行时，链表可能形成环形。这个问题是面试中最常被追问的。
>
> **第三，扩容时数据覆盖。** 扩容过程中，数组迁移时节点顺序被打乱，并发下可能丢失数据。
>
> **ConcurrentHashMap 的解决方案：**
>
> JDK 1.7 用的是分段锁——把整个 HashMap 分成 16 个 Segment，每个 Segment 各自加锁，并发度是 16。优点是锁粒度细了；缺点是锁粒度还是太粗，而且占内存。
>
> JDK 1.8 放弃了分段锁，改用 CAS + synchronized。CAS 处理空桶的插入，synchronized 处理哈希冲突（链表/红黑树）的插入。这样锁粒度细化到每个桶，并发度大大提高。
>
> **核心区别：** JDK 1.7 是"给整个 HashMap 分段"，JDK 1.8 是"给每个桶加锁"。JDK 1.8 的方案锁粒度更细，内存占用更少。

---

### 问题 4：HashMap 的容量为什么必须是 2 的幂次？HashMap 的扩容机制是什么？

**面试口述版：**

> HashMap 要求容量是 2 的幂次，核心原因是为了让哈希分散更均匀，从而减少哈希冲突。
>
> HashMap 计算索引用的是 `(n - 1) & hash`，其中 n 是容量。当 n 是 2 的幂次时，`n - 1` 的二进制形式是全 1（比如 16 - 1 = 15 = 0b1111）。`&` 运算相当于取 hash 的低几位，和取模运算效果相同，但 `&` 比 `%` 快得多。
>
> 如果 n 不是 2 的幂次，比如 n = 15，则 `n - 1 = 14 = 0b1110`，这时候 `&` 运算的分布就不均匀了——某些低位永远为 0，会导致大量哈希冲突。
>
> **扩容机制：** HashMap 负载因子默认是 0.75。当元素数量超过 `capacity × 0.75` 时，触发扩容，容量翻倍。扩容时需要重新计算每个元素的索引位置（rehash），这是 HashMap 扩容性能开销大的原因。JDK 1.8 对 rehash 做了优化——如果 hash & oldCap == 0，则新索引等于旧索引；否则新索引 = 旧索引 + oldCap。这样可以避免完整的 rehash。
>
> **面试加分点：** 提到 JDK 1.8 的扩容优化——元素低位是 0 还是 1，决定了扩容后留在原位还是移动到"原位置 + 旧容量"的位置。这大大减少了 rehash 的计算量。

---

### 问题 5：HashMap 和 Hashtable 的区别是什么？为什么推荐用 HashMap 而不是 Hashtable？

**面试口述版：**

> HashMap 和 Hashtable 的核心区别有三个。
>
> **第一，线程安全。** Hashtable 所有方法都加了 synchronized，是线程安全的；HashMap 不是线程安全的，需要外部同步。
>
> **第二，性能。** Hashtable 由于每个操作都要竞争同一把锁，高并发下性能很差。HashMap 虽然不是线程安全的，但可以通过 Collections.synchronizedMap() 转成线程安全的，或者直接用 ConcurrentHashMap。
>
> **第三，null 支持。** HashMap 允许 key 和 value 为 null；Hashtable 不允许任何 null 值，否则抛 NullPointerException。
>
> **为什么推荐 HashMap？** 因为 Hashtable 的同步开销太大了，在高并发场景下性能极差。而且 Hashtable 是遗留类，设计比较陈旧。实际生产环境推荐用 ConcurrentHashMap。
>
> **补充一点：** Hashtable 的初始容量是 11，负载因子是 0.75；HashMap 的初始容量是 16，负载因子也是 0.75。HashMap 的设计更合理。

---

### 问题 6：HashMap 的 key 有什么要求？为什么 String 和 Integer 适合做 key？

**面试口述版：**

> HashMap 的 key 需要满足两个要求。
>
> **第一，key 必须实现 equals 和 hashCode 方法。** 因为 HashMap 通过 hashCode() 计算索引位置，通过 equals() 判断 key 是否相等。如果这两个方法实现不合理，会导致哈希冲突加剧，甚至找不到已存在的 key。
>
> **第二，key 最好是不可变的。** 因为 key 的 hashCode 在 put 时计算并缓存，如果放入 Map 后 key 的属性变了，hashCode 就变了，下次 get 的时候就会找不到。
>
> **String 和 Integer 为什么适合做 key？**
>
> String 是最常用的 key 类型，因为 String 是不可变的——创建后内容不能改变，hashCode 不会变化。Integer 同理，也是不可变的。这两种类型作为 key 既安全又高效。
>
> **实际项目中的坑：** 如果用自定义对象做 key，一定要保证对象是不可变的——要么用 final class，要么把需要参与 equals 和 hashCode 计算的字段都设成 final。否则就会出现"明明 Map 里有这个元素，但就是 get 不到"的奇怪问题。

---

### 问题 7：ConcurrentHashMap 的 get 操作不需要加锁是如何实现的？

**面试口述版：**

> 这是 ConcurrentHashMap 设计的一个精妙之处。get 操作不需要加锁，靠的是 volatile 的可见性保证。
>
> JDK 1.8 的 ConcurrentHashMap，Node 数组（table）是用 volatile 修饰的： `transient volatile Node<K,V>[] table;`
>
> 这意味着对 table 的所有读写都是对主内存的操作，每次读取都能看到最新的值。get 操作只需要按索引找到桶，然后遍历链表或红黑树，找到对应的 key 即可，不需要任何锁。
>
> **但有一个边界情况需要考虑：** 如果 get 正在遍历链表时，另一个线程正在 resize 并往链表头部插入节点，会不会看到不完整的数据？答案是不会。因为 JDK 1.8 的插入用的是尾插法——新节点插在链表尾部，不影响正在遍历的链表尾部。
>
> **和 JDK 1.7 的区别：** JDK 1.7 的 Segment 分段锁实现中，get 操作也不需要加锁，同样靠 volatile 的可见性保证。但因为每个 Segment 各自独立，所以并发度比 JDK 1.8 低很多。
>
> **面试加分点：** 可以提到，ConcurrentHashMap 的 get 体现了"读多写少"场景下的高性能设计哲学——不需要所有操作都加锁，volatile 的可见性保证可以让读操作完全无锁化。

---

### 问题 8：HashMap 扩容时，JDK 1.8 是如何优化 rehash 的？

**面试口述版：**

> JDK 1.8 对扩容时的 rehash 做了精妙的优化，不需要完整重新计算每个元素的 hash 值。
>
> **优化原理：** 在扩容时，如果 `hash & oldCap == 0`，则新索引等于旧索引；如果 `hash & oldCap != 0`，则新索引 = 旧索引 + oldCap。
>
> 以容量从 16 扩容到 32 为例：
>
> - 假设某个 key 的 hash 值是 20，`20 & 16 = 0`，则该 key 扩容后仍在原位置（桶 4）
> - 假设某个 key 的 hash 值是 36，`36 & 16 = 4`，则该 key 扩容后移动到新位置（桶 4 + 16 = 桶 20）
>
> **为什么这样算？** 因为 oldCap 是 2 的幂次，比如 16 = 0b10000。`hash & 16` 实际上就是看 hash 的第 5 位（从 0 开始）是 0 还是 1。扩容后新的容量是 32 = 0b100000，所以原来第 5 位的信息会体现在新索引中——第 5 位是 0 留在原位，第 5 位是 1 则移动 oldCap 个位置。
>
> **性能收益：** 这意味着每个元素扩容时只需要判断一个 `&` 运算，不需要重新调用 hashCode() 和取模，大幅减少了扩容的计算开销。这也是 JDK 1.8 扩容比 JDK 1.7 快的原因之一。

---

## 第二部分：Java 反射机制

---

### 问题 9：什么是 Java 反射？它的工作原理是什么？

**面试口述版：**

> 反射是 Java 在运行状态中动态获取类的信息并操作对象的能力。
>
> **工作原理：** Java 程序编译后会生成 .class 文件，类加载器（ClassLoader）将这些 .class 文件加载到内存的方法区，在堆中生成一个唯一的 Class 对象。这个 Class 对象包含了类的完整结构信息——有哪些字段、哪些方法、哪些构造器、继承了什么接口等。
>
> 通过这个 Class 对象，我们就能在程序运行时"反射"出类的信息，并且动态调用它的方法、访问它的字段。
>
> **常见的反射使用场景：**
> - Spring 的依赖注入（@Autowired）：通过反射找到 set 方法并注入
> - MyBatis 的 ORM 映射：通过反射将 ResultSet 映射到 Java 对象
> - 序列化框架（Jackson/Gson）：通过反射读取和写入对象的字段
> - 注解处理器：通过反射读取类、方法、字段上的注解
>
> **面试加分点：** 可以提到，反射的核心是 Class 对象，它是理解反射的起点。Class 对象是反射的大门，所有反射操作都从获取 Class 对象开始。

---

### 问题 10：Class 对象的获取方式有哪几种？它们有什么区别？

**面试口述版：**

> 获取 Class 对象有三种方式。
>
> **第一种：类名.class。** 比如 `String.class`。这种方式最简单直接，不触发类的静态初始化块，编译时就确定。
>
> **第二种：对象.getClass()。** 比如 `"hello".getClass()`。需要先有一个实例，适用于已有对象的情况下获取其类型。
>
> **第三种：Class.forName("全限定名")。** 比如 `Class.forName("java.util.ArrayList")`。这种方式最灵活，可以动态传入字符串，适合配置文件和框架。最常用于 JDBC 驱动的加载——`Class.forName("com.mysql.cj.jdbc.Driver")`。
>
> **三者的区别：**
>
> - 类名.class：编译时确定，最安全，不会有拼写错误
> - 对象.getClass()：需要对象，不灵活
> - forName()：运行时动态确定，可以传变量，但有 ClassNotFoundException 风险
>
> **框架中的应用：** JDBC 中用 forName() 是因为驱动类名是从配置文件读取的，不是硬编码的。Spring 的组件扫描也大量使用 forName() 动态加载 Bean 类型。

---

### 问题 11：反射操作字段和方法的 API 有哪些？getDeclaredField 和 getField 有什么区别？

**面试口述版：**

> 操作字段的主要 API：
>
> - `getField(name)`：获取指定的 public 字段，包括继承的 public 字段
> - `getDeclaredField(name)`：获取当前类声明的所有字段，不考虑继承关系，包括 private
> - `getFields()`：获取所有 public 字段
> - `getDeclaredFields()`：获取当前类声明的所有字段
>
> 操作方法的主要 API：
>
> - `getMethod(name, paramTypes)`：获取指定的 public 方法
> - `getDeclaredMethod(name, paramTypes)`：获取当前类声明的所有方法，包括 private
> - `getMethods()`：获取所有 public 方法，包括继承的
> - `getDeclaredMethods()`：获取当前类声明的所有方法
>
> **getDeclaredField 和 getField 的核心区别：**
>
> `getField` 只能获取 public 字段，且包括父类继承的 public 字段。如果要访问 private 字段，必须用 `getDeclaredField`，然后调用 `field.setAccessible(true)` 关闭访问权限检查。
>
> **注意：** setAccessible(true) 是一个安全开关，JVM 有安全管理器时会检查这个操作。反射破坏了 Java 的封装性，这是反射最大的争议点。
>
> **面试加分点：** 可以提到，在 JDK 9 之后，setAccessible 受到了 Module 系统的限制——如果目标类是模块系统中的模块化类，可能无法关闭访问检查。这也是 Java 9 之后反射的一个变化。

---

### 问题 12：反射为什么慢？有哪些优化手段？

**面试口述版：**

> 反射的性能开销主要来自四个方面。
>
> **第一，JIT 优化失效。** 正常的方法调用，JIT 编译器在运行时会做内联、常量折叠等优化。但反射调用的方法在编译时无法确定，JIT 无法优化，只能解释执行，性能差 2~3 倍。
>
> **第二，访问权限检查。** 每次反射调用都要检查访问权限，即使我们已经调用了 setAccessible(true)，JVM 内部仍然有安全检查的开销。
>
> **第三，装箱和拆箱。** 反射调用方法时，基本类型参数必须装箱成包装类（Integer、Long 等），返回值也经常需要拆箱，这有额外的对象创建开销。
>
> **第四，字节码指令复杂。** 反射调用的字节码生成比直接调用复杂得多，涉及 MethodAccessor 的 Native 调用或 Java 方式的动态生成。
>
> **优化手段：**
>
> **第一种：缓存 Method/Field 对象。** 不要每次调用都重新获取，一次获取，多次使用。
>
> **第二种：setAccessible(true)。** 在有把握的情况下关闭访问检查，减少 JVM 的安全检查开销。
>
> **第三种：高频调用切换。** JDK 的 ReflectASM 会在反射调用超过 15 次后，从 Native 方式切换到 Java 方式（字节码生成），减少 JNI 开销。
>
> **第四种：用其他方案替代反射：**
> - 注解处理代替反射调用
> - 动态代理
> - 代码生成工具（MapStruct 在编译期生成映射代码，而不是运行时反射）
>
> **面试加分点：** 可以提到，实际项目中如果对性能要求极高（比如高频交易系统），会尽量避免反射，用预编译生成的代码替代。但大多数业务系统，反射的性能影响是可以接受的。

---

### 问题 13：反射的典型应用场景是什么？Spring 中反射是如何实现依赖注入的？

**面试口述版：**

> 反射在 Java 框架中无处不在，说几个最典型的应用场景。
>
> **Spring 的依赖注入（@Autowired）：** Spring 启动时，通过反射扫描所有 Bean，找出标注了 @Autowired 的字段和 set 方法，然后根据类型匹配找到对应的 Bean 实例，调用 setAccessible(true) 后注入。整个过程完全靠反射实现，业务代码只需要加注解，不需要手动 new 对象。
>
> **MyBatis 的 ORM 映射：** MyBatis 读取 XML 映射文件和注解配置，通过反射创建 Mapper 代理对象；查询结果从 ResultSet 映射到 Java 对象时，也是通过反射读取字段名，然后调用 setter 方法填充数据。
>
> **Jackson/Gson 的 JSON 序列化：** 框架通过反射遍历对象的字段，动态获取字段名和值，然后序列化到 JSON 字符串。反序列化时，通过反射创建对象并调用 setter 填充数据。
>
> **JDBC 的驱动加载：** `Class.forName("com.mysql.cj.jdbc.Driver")` 通过反射加载驱动类，驱动类在被加载时会执行静态代码块，向 DriverManager 注册自己。
>
> **面试加分点：** 可以提到，所有主流框架的核心都是反射——理解了反射，就理解了这些框架为什么能"零配置"工作。这本质上是"约定优于配置"理念的技术实现。

---

## 第三部分：synchronized 底层原理

---

### 问题 14：synchronized 的底层原理是什么？它是如何加锁的？

**面试口述版：**

> synchronized 的底层原理，面试官可以分两层来理解。
>
> **第一层：编译层面。** Java 编译器会在 synchronized 修饰的代码块前后分别插入 `monitorenter` 和 `monitorexit` 字节码指令。同步方法则是通过方法表中的 `ACC_SYNCHRONIZED` 标志隐式实现的。
>
> **第二层：JVM 层面。** monitorenter 指令在执行时，会尝试获取对象的 monitor（监视器锁）。每个 Java 对象在内存中都关联了一个 Monitor，Monitor 内部维护了一个计数器——计数器为 0 表示未被占用，线程获取后计数器加 1；重入时计数器再加 1；退出同步块时计数器减 1，减到 0 则释放锁。
>
> **Monitor 是什么？** Monitor 是基于 C++ 实现的 ObjectMonitor，包含了三个核心字段：
> - `_owner`：指向持有锁的线程
> - `_WaitSet`：等待队列，调用 wait() 的线程在这里排队
> - `_EntryList`：阻塞队列，未抢到锁的线程在这里等待
>
> **线程竞争 Monitor 的流程：** 线程执行到 synchronized 代码块时，先去 _EntryList 排队抢锁；抢到的线程成为 _owner；如果线程调用 wait() 就进入 _WaitSet 等待，被 notify 唤醒后重新进入 _EntryList 排队。
>
> **面试加分点：** 可以提到，synchronized 的锁是对象级别的，不是方法级别的。一个 synchronized 实例方法，锁的是 this 对象；一个 synchronized static 方法，锁的是 Class 对象。

---

### 问题 15：synchronized 的锁升级机制是什么？为什么说是不可逆的？

**面试口述版：**

> synchronized 在 JDK 1.6 之后引入了锁升级机制，从轻到重一共四级，**不可逆**。
>
> **第一级：无锁状态。** 对象没有任何线程持有，Mark Word 后三位是 001。
>
> **第二级：偏向锁。** 当第一个线程进入同步块时，用 CAS 将自己的线程 ID 写入 Mark Word。之后这个线程再进入同步块，只需要比较线程 ID 是否匹配，不需要任何原子操作。偏向锁解决的是单线程重复加锁的性能损耗。
>
> **第三级：轻量级锁。** 有其他线程来竞争时，偏向锁升级为轻量级锁。线程在栈帧中创建锁记录（Lock Record），包含 Mark Word 副本和对象指针，然后用 CAS 将对象头的 Mark Word 替换为指向锁记录的指针。如果 CAS 失败，就自旋重试（默认自旋 10 次左右）。
>
> **第四级：重量级锁。** 自旋超过阈值，或者竞争加剧时，轻量级锁膨胀为重量级锁。重量级锁依赖操作系统的 Mutex（互斥量），未获取锁的线程被直接阻塞，不再消耗 CPU。不再像轻量级锁那样空转自旋浪费 CPU。
>
> **为什么不可逆？** 因为 Mark Word 的空间是有限的（64 bit），锁状态信息存放在 Mark Word 中。一旦膨胀到重量级锁，Mark Word 中存的是指向 Monitor 的指针，无法反向降级。
>
> **面试加分点：** 可以提到 JDK 15 之后移除了偏向锁（`--disable-exper imental-always-prevalent`），原因是偏向锁在多线程竞争场景下撤销开销大，现代应用基本没有单线程重复加锁的场景了。

---

### 问题 16：synchronized 的四种锁状态，在 Mark Word 中是如何存储的？

**面试口述版：**

> Mark Word 是对象头的核心组成部分，64 bit 的空间在不同锁状态下存储不同的内容。
>
> **无锁状态：** 存储对象的哈希码（identity hash code）和 GC 分代年龄。哈希码是对象第一次调用 System.identityHashCode() 时计算的，计算后存储在 Mark Word 中并长期保留。
>
> **偏向锁状态：** 存储偏向线程的 ID（54 bit）和时间戳（Epoch）。不需要存储哈希码了，因为偏向锁的对象通常不需要调用 hashCode。
>
> **轻量级锁状态：** 存储指向线程栈中锁记录（Lock Record）的指针。锁记录是线程栈帧的一部分，包含 Mark Word 副本和对象指针。
>
> **重量级锁状态：** 存储指向 Monitor 对象的指针。Monitor 是 C++ 实现的对象，包含了 _owner、_WaitSet、_EntryList 等信息。
>
> **GC 状态：** 如果对象正在被 GC，Mark Word 会存储一个特殊的 GC 标记。
>
> **面试加分点：** 可以提到，这个设计体现了 JVM 的空间优化思想——同一块内存，在不同状态下复用，存储不同的信息。Mark Word 只有 64 bit，空间极其宝贵，每一个 bit 都有用。

---

### 问题 17：synchronized 和 ReentrantLock 有什么区别？

**面试口述版：**

> synchronized 和 ReentrantLock 本质上都是锁，但设计思路和使用方式完全不同。
>
> **加锁方式：** synchronized 是隐式锁——编译器自动在代码前后插入 monitorenter/monitorexit，程序员不需要关心锁的获取和释放，出了同步块自动释放。ReentrantLock 是显式锁——必须手动调用 lock() 获取锁，finally 中调用 unlock() 释放，不释放就会死锁。
>
> **灵活性：** synchronized 功能单一，无法中断等待中的线程，无法设置超时，无法绑定多个条件（Condition）。ReentrantLock 可以 lockInterruptibly() 中断等待，可以 tryLock() 设置超时，可以 newCondition() 创建多个条件队列。
>
> **公平性：** synchronized 只能是非公平锁。ReentrantLock 可以创建公平锁或非公平锁。公平锁按等待顺序排队，非公平锁允许插队（刚释放的线程立刻重新获取锁）。
>
> **底层实现：** synchronized 依赖对象头的 Mark Word 和 Monitor。ReentrantLock 依赖 AQS（队列同步器），AQS 维护了一个 volatile 的 state 和一个 FIFO 队列。
>
> **实际选择：** 大多数场景用 synchronized 就够了——语法简洁，不容易出错。如果需要可中断、可超时、多条件等高级特性，才用 ReentrantLock。
>
> **面试加分点：** 可以提到 ReentrantLock 的可重入实现——同一个线程再次获取锁时，state 加 1，释放时减 1，减到 0 才真正释放锁。

---

## 第四部分：CAS（Compare-And-Swap）

---

### 问题 18：什么是 CAS？它的底层原理是什么？

**面试口述版：**

> CAS 是 Compare-And-Swap 的缩写，是 CPU 提供的硬件级别原子指令。
>
> **底层原理：** CAS 包含三个参数——内存位置（M）、期望值（E）、新值（N）。CPU 执行时，原子地比较 M 的当前值和 E，如果相等，就把 M 更新为 N，返回 true；如果不等，说明其他线程已经修改了 M，则什么都不做，返回 false。整个比较和交换是一个不可打断的原子操作。
>
> **在 Java 中：** CAS 操作由 sun.misc.Unsafe 类提供，JVM 会将其编译成对应的 CPU 指令。在 x86 架构上是 CMPXCHG 指令，在 ARM 架构上是 LDREX/STREX 指令。
>
> **Java 的 CAS 封装：** JDK 的 atomic 包下所有原子类（AtomicInteger、AtomicLong、AtomicReference 等）都基于 CAS 实现。例如 AtomicInteger.getAndIncrement() 底层就是调用 Unsafe 的 CAS。
>
> **面试加分点：** 可以提到，CAS 是一种乐观锁——假设没有线程冲突，直接尝试更新；如果发现冲突（其他线程修改了），就重试，直到成功。这和 synchronized 的悲观锁思路正好相反。

---

### 问题 19：CAS 有哪些缺点？ABA 问题是什么？

**面试口述版：**

> CAS 有三大缺点，ABA 问题是最常被追问的。
>
> **缺点一：ABA 问题。** 线程 A 读取 M = 1，线程 B 将 M 改成 2 再改回 1，线程 A 继续 CAS 操作时发现 M = 1（期望值），以为没变化就成功了。但实际上 M 已经被其他线程修改过又改回来了。ABA 问题的危害在于：某些场景下，变化过程本身是有意义的，但 CAS 只关心结果、不关心过程。
>
> **ABA 问题的解决方案：** 使用 AtomicStampedReference（带时间戳/版本号的引用），每次修改都附带一个递增的版本号，CAS 时不仅比较值，还比较版本号。
>
> **缺点二：自旋开销。** 竞争激烈时，多个线程同时 CAS 失败，然后都空循环重试，浪费 CPU 资源。这就是 LongAdder 要解决的问题。
>
> **缺点三：只能保证一个变量。** CAS 只能原子地操作一个变量。如果需要同时保证多个变量的原子性，CAS 就无能为力了。解决方案是用 AtomicReference 把多个变量打包成一个对象，然后 CAS 这个对象。
>
> **ABA 问题的实际场景：** 常见的应用是栈的出栈入栈——线程 A 读取栈顶是 A，准备出栈；线程 B 把 A 出栈了，又压入了 B 和 C；线程 A 继续 CAS 栈顶的 A（期望值），发现匹配就成功了，但此时栈顶已经不是原来的 A 了。解决方法是给每个栈节点加版本号。

---

### 问题 20：LongAdder 为什么比 AtomicLong 快？它的分段锁原理是什么？

**面试口述版：**

> LongAdder 解决的是高并发下 CAS 的自旋竞争问题。
>
> **AtomicLong 的问题：** 只有一个 value，所有线程都竞争这一个 value 的 CAS 操作。竞争激烈时，大量化程同时自旋，CPU 空转，效率急剧下降。
>
> **LongAdder 的分段思想：** 把一个 value 的竞争，分散到多个 Cell 上。线程通过 hash 算法分散到不同的 Cell，每个 Cell 独立计数，final 时 sum() 求和。
>
> 具体来说：线程写入时，先尝试 CAS 自己对应的 Cell；如果 CAS 失败（竞争激烈），就换一个 Cell 重试。这样大部分线程可以分散到不同 Cell，各自在自己的 Cell 上 CAS，冲突概率大幅降低。
>
> **为什么 sum() 还能得到正确结果？** 因为 sum() 只是把各个 Cell 的值累加，不涉及 CAS，所以是弱一致性——求和的瞬间可能正在写入，但最终所有写入都会被累加进去。对于计数器场景，弱一致性是可以接受的。
>
> **性能对比：** 在 4 线程下，LongAdder 比 AtomicLong 快约 4 倍；线程越多，LongAdder 的优势越明显。但单线程场景下，LongAdder 因为多了一次 hash 计算，反而比 AtomicLong 慢。
>
> **面试加分点：** 可以提到，LongAdder 还有一个 base 值，用于处理没有 hash 冲突的情况。如果 Cell 数组没有初始化或操作失败，就回退到 base 值做 CAS。

---

### 问题 21：AtomicInteger 和 AtomicReference 的典型使用场景是什么？

**面试口述版：**

> AtomicInteger 主要用于计数器场景。
>
> **典型使用场景：**
> - 统计接口调用次数
> - 生成序列号
> - 实现无锁算法中的计数器
>
> **常见操作：**
> - `incrementAndGet()`：原子加 1
> - `getAndIncrement()`：先取值再自增
> - `addAndGet(delta)`：原子加指定值
> - `compareAndSet(expect, update)`：CAS，期望值匹配才更新
>
> **AtomicReference** 用于原子地替换引用，解决引用赋值不是原子操作的问题。
>
> **典型使用场景：**
> - 实现无锁的栈（Treiber Stack）
> - 实现无锁的队列
> - 实现懒加载的单例模式
>
> **注意：** AtomicReference 的 CAS 只能保证引用赋值是原子的，但如果复合操作的逻辑中有"读取-判断-写入"三步，CAS 本身无法保证这三步的原子性。例如 `if (ref.get() == A) { ref.set(B); }`，这个逻辑用普通的 CAS 是无法保证原子性的——需要在 while 循环中反复 CAS。
>
> **面试加分点：** 可以提到，用 AtomicReference 实现乐观锁时，一定要用 while 循环包裹 CAS：

```java
// 错误写法：复合逻辑不原子
if (atomicRef.compareAndSet(expected, newValue)) {
    // do something
}

// 正确写法：循环 CAS 直到成功
do {
    expected = atomicRef.get();
} while (!atomicRef.compareAndSet(expected, newValue));
```

---

## 第五部分：AQS（AbstractQueuedSynchronizer）

---

### 问题 22：AQS 的核心原理是什么？它的三大核心组件是什么？

**面试口述版：**

> AQS 是 Java 并发包的基石，ReentrantLock、Semaphore、CountDownLatch、CyclicBarrier 的底层都依赖它。
>
> **核心思想：** AQS 维护了一个 volatile 的 state（同步状态）和一个 FIFO 等待队列。加锁时用 CAS 尝试修改 state——成功就拿到锁，失败就加入等待队列排队。
>
> **三大核心组件：**
>
> **第一，state（同步状态）。** 是一个 volatile int 类型的变量，默认为 0。state = 0 表示锁未被占用；state > 0 表示锁被占用，数值等于重入次数。子类通过 tryAcquire 和 tryRelease 自定义 state 的含义。
>
> **第二，FIFO 等待队列。** 当线程获取锁失败后，会被封装成 Node 加入队列尾部。队列是一个双向链表，由 head 和 tail 两个指针维护。队列中的线程通过自旋 + LockSupport.park() 等待。
>
> **第三，CAS 操作。** AQS 大量使用 CAS 来原子地修改 state 和队列操作，是实现无锁算法的关键。
>
> **acquire 的流程：** 先 tryAcquire 尝试获取（子类实现），失败就 addWaiter 把线程封装成 Node 加入队列尾部，然后 acquireQueued 在队列中自旋等待——每次自旋前检查自己是不是 head 的下一个节点，如果是就再 tryAcquire 一次。如果自旋超过阈值，就用 LockSupport.park() 真正阻塞线程。
>
> **面试加分点：** 可以提到，AQS 的等待队列是 CLH 队列的变体。CLH 队列的优势是：无锁、无阻塞、FIFO、公平。

---

### 问题 23：AQS 的独占模式和共享模式有什么区别？

**面试口述版：**

> AQS 支持两种工作模式——独占模式和共享模式。
>
> **独占模式：** 同时只有一个线程能获得锁。其他线程都在队列中排队，等待锁释放后再竞争。ReentrantLock 就是独占模式的典型实现。
>
> - 获取锁：acquire() → tryAcquire() → addWaiter() → acquireQueued()
> - 释放锁：release() → tryRelease()
>
> **共享模式：** 多个线程可以同时获得锁。比如 Semaphore 允许多个线程同时持有许可证，CountDownLatch 允许多个线程同时通过。
>
> - 获取锁：acquireShared() → tryAcquireShared() → doAcquireShared()
> - 释放锁：releaseShared() → tryReleaseShared()
>
> **两者的核心区别：**
>
> 独占模式中，tryAcquire 返回 true 表示成功，false 表示失败。共享模式中，tryAcquireShared 返回一个整数值——正数表示成功（还有剩余资源），0 表示成功但没有剩余资源，负数表示失败。
>
> 共享模式下，锁释放时需要唤醒所有后继节点（因为可能有多个线程等待）。独占模式只需要唤醒 head 的下一个节点。
>
> **Node 节点的区别：** Node.EXCLUSIVE 表示独占模式，Node.SHARED 表示共享模式。队列中，独占节点的后继节点不会自动尝试获取锁，但共享节点的后继节点会自动传播（PROPAGATE 状态）。
>
> **面试加分点：** 可以提到，ReentrantReadWriteLock 中，读锁是共享模式（多个线程同时读），写锁是独占模式（只能一个线程写，写的时候不能读）。

---

### 问题 24：ConditionObject 是如何实现的？它和 synchronized 的 wait/notify 有什么区别？

**面试口述版：**

> ConditionObject 是 AQS 的内部类，每个 Condition 实例都维护了一个独立等待队列。
>
> **实现原理：** await() 调用时，当前进来的线程会释放锁，并创建 Node 加入 Condition 的等待队列。signal() 调用时，从 Condition 队列中取出一个 Node，转移到 AQS 的同步队列中，让它重新竞争锁。
>
> **核心区别一：队列数量。** synchronized 只有一个 Monitor，只有一个等待队列。所有调用 wait() 的线程都挤在一个队列里。ConditionObject 可以创建多个，每个 Condition 有独立的队列。
>
> **核心区别二：精确唤醒。** 有了多个队列，就可以实现精确唤醒——只唤醒等待某个条件的线程，而不影响其他线程。比如阻塞队列中，put 线程和 take 线程分别等待"队列不满"和"队列不空"，signal 时只会唤醒对应的线程，避免无效唤醒。
>
> **核心区别三：超时和中断支持。** Condition.awaitNanos()、awaitUntil() 支持超时，await(long, TimeUnit) 支持中断响应。synchronized 的 wait() 没有超时选项。
>
> **ReentrantLock + Condition 的典型用法：**
>
> ```java
> // 线程 A 等待条件
> lock.lock();
> try {
>     while (!condition) {
>         condition.await();  // 释放锁并等待
>     }
>     // 条件满足，执行任务
> } finally {
>     lock.unlock();
> }
>
> // 线程 B 触发条件
> lock.lock();
> try {
>     condition.signal();  // 唤醒等待该条件的一个线程
> } finally {
>     lock.unlock();
> }
> ```
>
> **面试加分点：** 可以提到，ArrayBlockingQueue 的实现就用到了 ReentrantLock + Condition——一个 notEmpty 条件，一个 notFull 条件。put 线程在 notFull 上等待，take 线程在 notEmpty 上等待。

---

### 问题 25：能否手写一个基于 AQS 的可重入锁？请说明设计思路。

**面试口述版：**

> 可以，我基于 AQS 手写一个可重入锁，核心是重写 tryAcquire 和 tryRelease 两个方法。
>
> **设计思路：**
>
> **tryAcquire(int arg)：** 先获取 state，如果 state == 0，说明锁未被占用，用 CAS 尝试获取（state 从 0 改成 1），成功则设置独占线程；如果 state != 0，说明锁被占用，检查是否是同一个线程重入，如果是则 state 加 1 并返回成功。
>
> **tryRelease(int arg)：** 检查当前线程是否是持有锁的线程，如果不是抛异常；如果 state 减 arg 后等于 0，说明完全释放了，调用 setExclusiveOwnerThread(null) 并设置 state 为 0；如果还不为 0，说明是重入，只减 state 不清空持有线程。
>
> **为什么需要 isHeldExclusively() 检查？** 这是安全机制，防止其他线程错误释放锁。tryRelease 只有在持有锁的线程调用时才有效。
>
> **重入的体现：** state 的值代表重入次数。每次重入 acquire 时 state 加 1，每次 release 时 state 减 1，减到 0 才真正释放锁。这样同一线程可以递归调用加锁方法而不会死锁。
>
> **面试加分点：** 可以提到，ReentrantLock 的公平版本和非公平版本，区别就在于 tryAcquire 中是否有"检查队列中是否有更早等待的线程"这个逻辑。公平版本多了一个 hasQueuedPredecessor() 检查。

---

## 第六部分：ConcurrentHashMap（JDK 7 vs JDK 8）

---

### 问题 26：ConcurrentHashMap JDK 7 的分段锁是如何实现的？

**面试口述版：**

> JDK 7 的 ConcurrentHashMap 使用的是 Segment 分段锁机制。
>
> **数据结构：** 整个 HashMap 被分成 16 个 Segment，每个 Segment 各自维护一个 HashEntry 数组，和一个独立的可重入锁。Segment 数组在 ConcurrentHashMap 构造时就已经初始化好了。
>
> **并发度：** 理论上最大并发度是 16——同一时间最多 16 个线程同时操作不同的 Segment，互相之间不冲突。
>
> **get 操作：** 完全不需要加锁。因为 HashEntry 的 value 和 next 都是 volatile 的，读取总是看到最新的值。
>
> **put 操作：** 先定位到属于哪个 Segment（通过 hash & (Segment数量 - 1)），然后对这个 Segment 加锁，执行类似 HashMap 的 put 操作。
>
> **size 操作：** 这是最复杂的部分。ConcurrentHashMap 的 size 不是简单的把所有 Segment 的 count 加起来——因为统计 size 的过程中，可能有其他线程正在往 Segment 里写数据。JDK 7 的实现是：先尝试无锁计算（最多重试 3 次），每次计算前记录所有 Segment 的 modCount（修改次数），计算后对比；如果 3 次无锁计算过程中有 Segment 被修改过，就对所有 Segment 加锁再计算。
>
> **为什么 JDK 8 放弃了分段锁？** 因为 16 个 Segment 锁粒度还是太粗，而且每个 Segment 本身是一个独立的 ReentrantLock，占用额外内存。并发度固定为 16，无法动态调整。

---

### 问题 27：ConcurrentHashMap JDK 8 的 CAS + synchronized 是如何实现的？

**面试口述版：**

> JDK 8 放弃了 Segment 分段锁，改用 CAS + synchronized，锁粒度细化到每个桶。
>
> **数据结构：** 和 HashMap 一样，是 Node 数组 + 链表 + 红黑树。Node 数组（table）用 volatile 修饰，保证可见性。
>
> **put 流程：**
>
> **第一步，定位桶。** 通过 `(n - 1) & hash` 计算桶位置。
>
> **第二步，CAS 插入。** 如果桶为空，用 CAS 尝试插入新节点。如果 CAS 成功，说明没有竞争，直接插入完成。
>
> **第三步，synchronized 加锁。** 如果桶不为空（已有节点），说明发生了 hash 冲突。此时对这个桶（桶的第一个节点）加 synchronized 锁，然后按链表或红黑树的逻辑插入或更新节点。
>
> **为什么桶不为空时用 synchronized 而不是 CAS？** 因为多个线程可能同时往同一个桶插入节点，CAS 只能保证一个成功。如果用 CAS 自旋重试，竞争激烈时会退化成大量自旋，浪费 CPU。synchronized 锁住桶的第一个节点，串行化写入，是更高效的选择。
>
> **get 操作：** 全程无锁。只需要按索引找到桶，然后遍历链表或红黑树即可，不需要任何锁。
>
> **和 JDK 7 的对比：** JDK 7 是给整个 Segment 加锁（粗粒度），JDK 8 是给每个桶加锁（细粒度）。JDK 8 的并发度等于桶的数量，理论上比 JDK 7 的固定 16 高很多。
>
> **面试加分点：** 可以提到 JDK 1.8 还引入了 ForwardingNode——在扩容过程中，旧的桶数组中的节点会被替换成 ForwardingNode，hash = -1，表示这些桶已经迁移到新数组了。get 操作遇到 ForwardingNode 时，会跳到新数组继续查找。

---

### 问题 28：ConcurrentHashMap 的扩容机制是什么？多线程是如何协同扩容的？

**面试口述版：**

> ConcurrentHashMap 的扩容是多线程协同的，所有正在执行 put 的线程都有义务帮助扩容。
>
> **触发条件：** 当元素数量超过 `capacity × loadFactor`（负载因子，默认 0.75）时触发。
>
> **JDK 1.8 扩容流程：**
>
> **第一步，创建新数组。** 新数组容量是旧数组的两倍。
>
> **第二步，分配迁移任务。** 每个线程认领一段桶区间（步长 stride，默认是 16），从旧数组的高位往低位迁移。
>
> **第三步，迁移单个桶。** 对旧数组的某个桶加 synchronized 锁，将桶中的节点分成两类：`hash & oldCap == 0` 的节点留在原位置（低位），`hash & oldCap != 0` 的节点移动到"原位置 + oldCap"的位置（高位）。这个分配方式和 HashMap JDK 1.8 的 rehash 优化完全一致。
>
> **第四步，替换桶。** 迁移完成后，在旧桶中插入 ForwardingNode 标记，指向新数组。
>
> **帮助扩容（ConcurrentHashMap 的特色）：** 当某个线程 put 时，发现桶的第一个节点的 hash = -1（ForwardingNode），说明正在扩容。这个线程不是等待，而是跳转到 nextTable（新数组）帮忙迁移。
>
> **扩容完成的标志：** transferIndex <= 0。transferIndex 是剩余未分配的任务索引，减到 0 表示所有桶都迁移完成了。
>
> **面试加分点：** 可以提到，JDK 1.8 的 ConcurrentHashMap 扩容有一个优化——在迁移过程中，如果 get 操作找不到元素，会主动去新数组查找（因为节点可能已经迁移走了）。这个细节体现了"用户线程帮忙干活"的设计哲学。

---

## 第七部分：线程池

---

### 问题 29：线程池的七大核心参数是什么？请解释每个参数的作用。

**面试口述版：**

> 线程池七参数中，corePoolSize、maximumPoolSize、workQueue 是最核心的三个。
>
> **corePoolSize（核心线程数）：** 线程池中始终保持存活的线程数量。即使线程空闲，也不会被销毁（除非设置了 allowCoreThreadTimeOut）。这是线程池的"常驻兵力"。
>
> **maximumPoolSize（最大线程数）：** 线程池允许创建的最大线程数量。当核心线程满了、队列也满了，可以继续创建线程，直到达到这个上限。这是线程池的"最大兵力"。
>
> **keepAliveTime（空闲线程存活时间）：** 非核心线程（超出 corePoolSize 的部分）的空闲存活时间。超过这个时间且无新任务，非核心线程会被销毁。这个参数让线程池有了"弹性"——闲时可以缩编。
>
> **unit（时间单位）：** 配合 keepAliveTime 使用，可选值有秒、毫秒、微秒、纳秒等。
>
> **workQueue（任务队列）：** 存储待执行任务的阻塞队列。常见的有：
> - LinkedBlockingQueue（有界队列，推荐设置合理容量）
> - ArrayBlockingQueue（有界队列）
> - SynchronousQueue（不存储元素，直接提交给线程）
> - 无界队列（危险！可能导致 OOM）
>
> **threadFactory（线程工厂）：** 负责创建线程。可以自定义线程名称、优先级、是否为守护线程。
>
> **handler（拒绝策略）：** 当线程池和队列都满了，新任务来了如何处理。
>
> **面试加分点：** 可以提到，生产环境中最容易出问题的两个参数是 workQueue 和 handler——队列设太大可能 OOM，拒绝策略选错可能导致任务丢失。

---

### 问题 30：线程池的执行流程是什么？请描述一个任务从提交到完成的完整过程。

**面试口述版：**

> 线程池的执行流程可以用一句话概括：**不够用就创建，用不了就排队，排不下就扩容，满了就拒绝。**
>
> **第一步：任务提交。** 用户调用 execute(command) 或 submit(task) 提交任务。
>
> **第二步：判断核心线程。** 线程池检查当前运行的线程数是否小于 corePoolSize。如果是，创建新的核心线程来执行任务。
>
> **第三步：判断队列。** 如果核心线程已满，检查队列是否未满。如果是，将任务放入队列等待。
>
> **第四步：判断最大线程。** 如果队列已满，检查当前线程数是否小于 maximumPoolSize。如果是，创建新的非核心线程来执行任务。
>
> **第五步：执行拒绝策略。** 如果线程数已达到 maximumPoolSize 且队列已满，执行拒绝策略。
>
> **面试加分点：** 可以提到一个常见的误解——很多人以为任务会先排队、满了才创建线程。实际上正确的顺序是：**先创建核心线程 → 核心线程满了才入队 → 队列满了才创建非核心线程**。这个顺序不能颠倒，因为设计者认为：与其让任务在队列里等，不如直接创建线程处理。

---

### 问题 31：线程池的四大拒绝策略分别是什么？在什么场景下应该选择哪种策略？

**面试口述版：**

> 线程池的四大拒绝策略，解决的是"线程池已经扛不住了，新任务来了怎么办"的问题。
>
> **第一种：AbortPolicy（默认，拒绝并抛异常）。** 直接抛 RejectedExecutionException。适用于"不能丢任务"的场景——比如转账、下单、支付等核心业务操作。如果任务被丢弃会导致数据不一致或业务损失，就用这个。
>
> **第二种：CallerRunsPolicy（调用方执行）。** 由提交任务的线程直接执行任务。优点是：任务不会被丢弃，调用线程被拖慢了但整体不会崩溃。缺点是：调用线程被阻塞了，可能影响提交方的性能。适用于"宁愿慢一点也不能丢"的场景——比如压测、异步日志写入。
>
> **第三种：DiscardPolicy（静默丢弃）。** 直接丢弃新任务，不抛异常也不记录。适用于"非核心链路，可以丢任务"的场景——比如埋点数据上报、用户行为日志。丢了也不影响主业务，用户无感知。
>
> **第四种：DiscardOldestPolicy（丢弃最老的）。** 丢弃队列中最旧（排队时间最长）的任务，然后尝试提交新任务。适用于"后来的任务比旧的更有价值"的场景——比如用户资料同步，旧的用户资料没有新的有意义。
>
> **面试加分点：** 可以提到，其实还有第五种策略——自定义拒绝策略，实现 RejectedExecutionHandler 接口。可以根据业务需求设计更复杂的策略，比如把被拒绝的任务写入数据库或消息队列，稍后重试。

---

### 问题 32：线程池参数如何配置？有没有通用的配置公式？

**面试口述版：**

> 线程池参数配置没有万能公式，需要根据业务类型来定。
>
> **通用配置公式：**
>
> `线程数 = N × U × (1 + W/C)`
>
> 其中 N 是 CPU 核心数，U 是目标 CPU 利用率（0 到 1 之间），W 是线程平均等待时间，C 是线程平均计算时间。
>
> **CPU 密集型任务：** 计算为主，IO 很少，CPU 利用率接近 100%，W/C ≈ 0。公式简化为 N + 1（多一个线程是为了某个线程偶尔缺页中断时，其他线程能顶上）。推荐配置：`corePoolSize = CPU核心数 + 1`
>
> **IO 密集型任务：** 大部分时间在等 IO，CPU 利用率不高，W/C > 1。推荐配置：`corePoolSize = CPU核心数 × 2` 或更高（根据等待时间与计算时间的比值计算）。常见的推荐是 CPU核心数 × 2。
>
> **混合型任务：** 两种混合，可以分开线程池处理，或者设一个较大的队列缓冲。
>
> **生产环境的坑：**
>
> - 不要用 `Executors` 创建线程池——`newFixedThreadPool` 的队列是 `Integer.MAX_VALUE` 的无界队列，可能导致 OOM；`newCachedThreadPool` 的 maximumPoolSize 是 `Integer.MAX_VALUE`，也可能导致创建过多线程。
> - 应该用 `new ThreadPoolExecutor()` 手动指定参数，队列用 `LinkedBlockingQueue(capacity)` 或 `ArrayBlockingQueue(capacity)`。
>
> **面试加分点：** 可以提到不同业务场景的参考配置：
> - 外部 API 调用（IO 密集）：core=20, max=100, queue=5000
> - 计算密集任务：core=CPU核数, max=CPU核数+1, queue=100~500

---

## 第八部分：CompletableFuture 异步编排

---

### 问题 33：CompletableFuture 相比传统 Future 有哪些优势？

**面试口述版：**

> CompletableFuture 是 Java 8 引入的，解决了传统 Future 的三个痛点。
>
> **传统 Future 的问题：**
>
> 拿到一个 Future 之后，只能阻塞等待（get()）或者轮询查看状态（isDone()）。这在异步场景下非常痛苦——如果 FutureA 的结果是 FutureB 的输入，FutureC 的输入又依赖 FutureA 和 FutureB 的结果，用传统 Future 就要写一堆嵌套的 get() 调用，代码又臭又长。
>
> **CompletableFuture 的三大优势：**
>
> **第一，支持回调。** 不再阻塞等待，用 thenApply、thenAccept、thenRun 注册回调，任务完成后自动触发回调函数。代码是线性的，易读易维护。
>
> **第二，支持链式编排。** thenCompose 可以处理有依赖关系的异步任务（扁平化），thenCombine 可以合并两个独立的异步任务，allOf/anyOf 可以等待所有/任一任务完成。
>
> **第三，支持异常处理和超时。** exceptionally 可以处理异常，handle 可以同时处理结果和异常，orTimeout 可以设置超时时间。
>
> **CompletableFuture 的链式调用示例：**
>
> 假设要实现：查数据库 → 处理数据 → 发消息。用传统 Future 要写嵌套；用 CompletableFuture 可以链式写：
>
> `CompletableFuture.supplyAsync(() -> queryDB())`
> `.thenApply(data -> process(data))`
> `.thenAccept(result -> sendMessage(result))`
>
> 代码从上到下线性执行，逻辑一目了然。
>
> **面试加分点：** 可以提到 CompletableFuture 默认使用 `ForkJoinPool.commonPool()` 作为执行器。这个公共池的线程数默认是 CPU 核心数减 1（最少为 1）。对于 IO 密集型任务，建议自定义线程池，否则公共池可能被占满影响其他任务。

---

### 问题 34：CompletableFuture 的核心 API 有哪些？请解释 thenApply、thenCompose、thenCombine、allOf 的区别。

**面试口述版：**

> CompletableFuture 的 API 很多，我来逐一解释最核心的几个。
>
> **thenApply（转换）：** 对结果做转换，接收一个函数，返回一个新的 CompletableFuture。比如 `future.thenApply(User::getName)` 把 User 对象转成用户名。
>
> **thenCompose（扁平化链式调用）：** 用于处理有依赖关系的异步任务——上一个任务的结果是下一个任务的输入。比如 `future1.thenCompose(user -> future2(user))`。注意：如果用 thenApply 嵌套，`CompletableFuture<CompletableFuture<R>>`，需要手动解包；用 thenCompose 直接返回 `CompletableFuture<R>`。
>
> **thenCombine（合并两个独立任务）：** 当两个任务都完成后，合并它们的输出。比如 `future1.thenCombine(future2, (r1, r2) -> merge(r1, r2))`。两个任务没有依赖关系，但需要都完成才能做最终处理。
>
> **allOf（等待所有任务）：** 等待多个 CompletableFuture 全部完成，返回一个新的 CompletableFuture<Void>。适用于"查用户信息、查订单、查推荐"等多个独立任务并行执行，最后汇总结果的场景。
>
> **exceptionally（异常处理）：** 当任务失败时，执行 fallback 逻辑。`future.exceptionally(ex -> fallbackResult())`
>
> **orTimeout（超时控制）：** 给任务设置超时时间，超时后自动返回默认值或抛出 TimeoutException。`future.orTimeout(3, TimeUnit.SECONDS)`
>
> **面试加分点：** 可以提到，thenApply/thenAccept/thenRun 的区别——apply 有输入有输出，accept 有输入无输出（用于副作用），run 无输入无输出（用于日志等）。

---

### 问题 35：CompletableFuture 如何处理异常？请举例说明 exceptionally 和 handle 的区别。

**面试口述版：**

> CompletableFuture 提供两种处理异常的方式——exceptionally 和 handle。
>
> **exceptionally：** 只在异常时执行，接收 Throwable，返回一个默认值。正常执行时直接透传结果。
>
> ```java
> CompletableFuture.supplyAsync(() -> queryData())
>     .thenApply(data -> process(data))
>     .exceptionally(ex -> {
>         // 异常时返回默认值
>         log.error("查询失败", ex);
>         return defaultData();
>     });
> ```
>
> **handle：** 不管正常还是异常都会执行，接收两个参数——结果和异常，返回一个新结果。
>
> ```java
> CompletableFuture.supplyAsync(() -> queryData())
>     .thenApply(data -> process(data))
>     .handle((result, ex) -> {
>         if (ex != null) {
>             // 异常处理
>             return fallbackData;
>         } else {
>             // 正常处理
>             return enrich(result);
>         }
>     });
> ```
>
> **两者的核心区别：**
>
> exceptionally 只能返回默认值，无法修改原始结果；handle 可以根据结果和异常决定最终的输出，能力更强。
>
> **高级用法：**
>
> - `whenComplete`：和 handle 类似，但不返回新结果，用于记录日志等副作用
> - `exceptionallyCompose`：异常时执行一个异步操作
> - ` CompletableFuture.allOf(...).join()`：等待所有任务，忽略结果
>
> **面试加分点：** 可以提到，exceptionally 的返回值类型必须和原始 CompletableFuture 一致。还需要注意，如果 exceptionally 中的 fallback 也抛异常，那么这个异常会传播下去，不会被后续处理。

---

### 问题 36：CompletableFuture 的超时控制如何实现？生产环境中如何避免 CompletableFuture 的坑？

**面试口述版：**

> CompletableFuture 有两种超时控制方式。
>
> **方式一：orTimeout（Java 9+）。** 最简洁：
>
> ```java
> CompletableFuture.supplyAsync(() -> heavyTask())
>     .orTimeout(3, TimeUnit.SECONDS)
>     .exceptionally(ex -> fallbackResult());
> ```
>
> **方式二：completeOnTimeout（Java 9+）。** 超时后返回一个默认值：
>
> ```java
> CompletableFuture.supplyAsync(() -> heavyTask())
>     .completeOnTimeout(fallbackResult, 3, TimeUnit.SECONDS);
> ```
>
> **Java 8 的兼容写法：** 用一个延迟任务主动触发 complete：
>
> ```java
> CompletableFuture<String> future = new CompletableFuture<>();
> 
> ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
> scheduler.schedule(() -> {
>     future.completeExceptionally(new TimeoutException());
> }, 3, TimeUnit.SECONDS);
> 
> future.get(); // 超过3秒会抛TimeoutException
> ```
>
> **生产环境的常见坑：**
>
> **坑一：默认线程池阻塞。** CompletableFuture 默认用 ForkJoinPool.commonPool()，这个池的线程数是 CPU核心数-1。如果提交的是 IO 密集型任务（不消耗 CPU），池中的线程会全部阻塞在 IO 上，导致其他任务无法执行。**解决：** 用自定义线程池。
>
> **坑二：异常被吞掉。** 如果不主动处理 exceptionally，所有异常都会被静默吞掉（默认是unchecked exception会直接抛到父线程，但 thenApply 中的异常不会被感知）。**解决：** always 注册回调，或者用 handle 统一处理。
>
> **坑三：CompletionException 的嵌套。** 多层异步编排时，异常会被包装成 CompletionException。捕获时要记得拆包：
>
> ```java
> try {
>     future.get();
> } catch (Exception e) {
>     Throwable cause = e.getCause();  // 解包
> }
> ```
>
> **面试加分点：** 可以提到，在 Spring Boot 项目中，可以用 `@Async` 注解配合 ThreadPoolTaskExecutor 避免线程池问题。

---

## 核心要点速查表

### HashMap vs ConcurrentHashMap

| 问题 | 答案要点 |
|------|---------|
| HashMap 线程不安全 | 并发 put 覆盖、resize 环形链表死循环（JDK 1.7 头插法） |
| JDK 7 分段锁 | 16 个 Segment，get 无锁，put 锁 Segment，并发度 16 |
| JDK 8 CAS+synced | CAS 空桶，synced 锁冲突桶，锁粒度细化到桶，并发度=桶数 |
| 扩容优化 | `hash & oldCap == 0` 留原位，否则移 oldCap 位 |

### synchronized 锁升级

| 问题 | 答案要点 |
|------|---------|
| 四种状态 | 无锁→偏向锁→轻量级锁→重量级锁，不可逆 |
| 偏向锁 | 单线程重复加锁场景，Mark Word 存线程 ID，无需原子操作 |
| 轻量级锁 | CAS 抢锁，自旋重试，失败超阈值膨胀为重量级锁 |
| 重量级锁 | OS Mutex，线程阻塞，不消耗 CPU，涉及用户态到内核态切换 |

### CAS 与 AQS

| 问题 | 答案要点 |
|------|---------|
| CAS 三大缺点 | ABA 问题（AtomicStampedReference）、自旋开销（LongAdder）、单变量限制 |
| LongAdder | 分段计数思想，Cell 数组 hash 分散，多线程各 CAS 自己的 Cell |
| AQS 三大组件 | state（volatile 同步状态）、FIFO 队列、CAS |
| 独占 vs 共享 | 独占：单线程持锁，tryAcquire 返回 boolean；共享：多线程同时持锁，tryAcquireShared 返回剩余值 |

### 线程池

| 问题 | 答案要点 |
|------|---------|
| 执行流程 | 核心线程→队列→最大线程→拒绝策略 |
| 拒绝策略选择 | 核心链路用 AbortPolicy（不丢），非核心用 DiscardPolicy（可丢），压测用 CallerRunsPolicy |
| 配置公式 | CPU密集 = N+1；IO密集 = N×2×U×(1+W/C) |
| 生产环境坑 | 勿用 Executors，用有界队列，自定义线程池 |

### CompletableFuture

| 问题 | 答案要点 |
|------|---------|
| vs Future | 支持回调、链式编排、异常处理、超时控制 |
| thenCompose | 处理依赖关系（扁平化），返回 CompletableFuture |
| thenCombine | 合并两个独立任务结果 |
| allOf | 等待所有任务完成 |
| 常见坑 | 默认线程池共享、高并发阻塞，异常被吞，CompletionException 嵌套 |

---

> **面试冲刺建议：**
> 1. **HashMap / ConcurrentHashMap** 是集合部分的重中之重，必须掌握 JDK 7 和 JDK 8 的差异。
> 2. **synchronized** 的锁升级机制是高频考点，要能画图描述四种状态的转换。
> 3. **AQS** 是 JUC 并发包的基石，理解了 AQS，ReentrantLock、Semaphore、CountDownLatch 就都通了。
> 4. **线程池** 的执行流程 + 参数配置 + 常见错误是必答题。
> 5. **CompletableFuture** 要能用代码演示异步编排的链式写法。
> 6. 以上问题均配有**面试口述版**，可以直接在面试中口头回答。

---

---

# 追问补充篇：各知识点高频追问与追问回答

> **说明：** 本章节对第一部分中的核心题目进行追问扩展，模拟面试官追问场景，每道追问给出标准回答与面试加分点。追问是区分候选人水平的关键环节——能答对基础题是及格，能答好追问才是优秀。

---

## 追问一：HashMap 相关

### 追问 1-1：HashMap 为什么把链表转红黑树的阈值设为 8？红黑树退化成链表的阈值为什么是 6？

**追问回答：**

> 这个 8 和 6 的设定是经过严格的概率统计的。
>
> HashMap 的设计者统计了哈希碰撞的概率分布，假设哈希函数足够均匀，每个桶中的节点数量遵循**泊松分布**，参数 λ = 0.5（对应负载因子 0.75）。
>
> 在这个分布下，链表长度达到 8 的概率只有约 **0.00000006**，即不到千万分之一。也就是说，正常使用中几乎不可能出现链表长度达到 8 的情况。
>
> **那为什么还要转红黑树？** 这是为了防止**哈希 DoS 攻击**。如果有人故意构造大量 hashCode 相同的 key 来攻击系统，链表会退化成 O(n) 的线性查找，红黑树可以将查询效率稳定在 O(log n)。
>
> **为什么红黑树退化成链表是 6？** 这是一个**留有余地的设计**。如果阈值也是 8，那么当链表长度恰好在 8 附近时，可能反复触发转换和退化，造成性能抖动。设为 6 留了两步的缓冲空间，避免这种震荡。
>
> **面试加分点：** 可以提到，JDK 的注释里明确写了引用了泊松分布的概率计算公式。TreeNode 的内存占用是普通 Node 的约 2 倍，所以只有链表确实很长时（超过 8），承担这个空间成本才是值得的。

---

### 追问 1-2：HashMap 的负载因子为什么默认是 0.75？能否改成其他值？

**追问回答：**

> 0.75 是**时间成本和空间成本的最优平衡点**。
>
> 负载因子决定了 HashMap 在什么时机扩容。当 `size > capacity × loadFactor` 时触发扩容。
>
> **如果设得更小（比如 0.5）：** 扩容触发得更早，hash 冲突更少，查询效率更高，但空间利用率低——数组有一半是空的，浪费内存。
>
> **如果设得更大（比如 0.9）：** 空间利用率高了，但 hash 冲突加剧，链表变长，查询效率下降。当负载超过 0.75 后，哈希碰撞的概率急剧上升，性能反而变差。
>
> **为什么是 0.75？** 设计者通过泊松分布的数学计算，找到在 λ = 0.5 时，负载因子 0.75 是综合最优的平衡点。同时，0.75 让扩容后新的负载因子变为 0.375，恰好落在 [0.375, 0.75] 的最优区间内。
>
> **实际项目中可以改吗？** 可以改。如果你的 key 是 String 这种哈希分布良好的类型，可以适当调高负载因子（比如 0.8）来节省空间。如果你的 key 哈希分布差，容易碰撞，可以调低（比如 0.6）来保证查询效率。
>
> **面试加分点：** 可以提到，HashMap 的容量始终是 2 的幂次，负载因子 0.75 配合 2 的幂次，使得 `(capacity × 0.75)` 在扩容前后都是整数，减少扩容时 rehash 的不均匀。

---

### 追问 1-3：ConcurrentHashMap 的 size() 是如何计算的？为什么说 size() 是弱一致性的？

**追问回答：**

> ConcurrentHashMap 的 size() 计算是一个高频追问，JDK 7 和 JDK 8 的实现完全不同。
>
> **JDK 1.7 的实现：**
> ConcurrentHashMap 的 size 不是简单的把所有 Segment 的 count 加起来——因为统计 size 的过程中，可能有其他线程正在写数据。JDK 1.7 的做法是：先尝试**无锁计算**，最多重试 3 次。每次计算前记录所有 Segment 的 modCount（修改次数），计算后对比——如果 3 次无锁计算过程中有 Segment 被修改过（modCount 变了），就放弃重试，对所有 Segment 加锁再计算。
>
> **JDK 1.8 的实现：**
> JDK 1.8 不再基于 Segment，而是用 `baseCount` + `CounterCell[]` 数组来计数。
>
> - **无竞争时**：直接用 CAS 更新 baseCount
> - **竞争激烈时**：初始化 CounterCell 数组，将计数分散到不同的 Cell 中，每个线程 CAS 自己的 Cell，减少竞争
>
> `size()` 方法实际调用 `sumCount()`，返回 `baseCount + 所有 CounterCell.value 的累加和`。
>
> **为什么说 size() 是弱一致性的？**
>
> 因为 `sumCount()` 在遍历 CounterCell 数组累加的过程中，其他线程可能正在往 Cell 里写数据——有些 Cell 的更新可能来不及被累加进去。同理，在 size() 计算过程中，新插入的元素可能还没来得及更新 baseCount 或 Cell。
>
> 所以 `size()` 返回的值是一个**近似值**，不是精确值。这是并发计数器场景下的一致性和性能之间的权衡。
>
> **面试加分点：** 可以提到，如果需要精确计数，应该用 AtomicLong 单独维护一个计数器（牺牲部分并发性能）。ConcurrentHashMap 的 size() 本质上是一个"快速估算"，不是精确值。

---

## 追问二：synchronized 相关

### 追问 2-1：JVM 对 synchronized 做了哪些优化？你听说过锁消除和锁粗化吗？

**追问回答：**

> JVM 对 synchronized 的优化主要包括**锁消除**和**锁粗化**。
>
> **锁消除（Lock Elimination）：**
>
> JVM 通过**逃逸分析**判断一个锁对象是否只被一个线程访问。如果一个锁对象不会逃逸出当前线程（即没有被其他线程访问的可能），JVM 会直接消除这个锁，连加锁操作都不做。
>
> 典型场景：
>
> ```java
> // 逃逸分析后发现 obj 不会逃逸出方法，可以消除锁
> public void method() {
>     Object obj = new Object();
>     synchronized (obj) {
>         // do something
>     }
> }
> // 编译优化后变成：
> public void method() {
>     Object obj = new Object();
>     // synchronized 被消除了
>     // do something
> }
> ```
>
> **锁粗化（Lock Coarsening）：**
>
> 如果 JVM 检测到**一系列连续的加锁和解锁操作都是针对同一个对象**，就会把这些操作合并成一个更大范围的锁，减少锁操作的次数。
>
> 典型场景：
>
> ```java
> // JVM 会将多次加锁解锁合并为一次
> StringBuffer sb = new StringBuffer();
> sb.append("a");  // append 内部有 synchronized
> sb.append("b");
> sb.append("c");
> // 优化后变成一次性加锁，append 所有内容
> ```
>
> StringBuffer 的 append 方法内部都有 synchronized，在循环中连续调用时，JVM 会做锁粗化优化，减少加锁解锁的次数。
>
> **面试加分点：** 可以提到，锁粗化的反面是**锁粒度过细**——如果把一个完整的业务逻辑拆分成多个小 synchronized 块，JVM 会做锁粗化合并。但如果拆分得不合理，反而会增加开销。这个优化对开发人员是透明的，JVM 会自动判断。

---

### 追问 2-2：偏向锁的撤销条件是什么？为什么说偏向锁在高并发下反而可能成为性能瓶颈？

**追问回答：**

> **偏向锁的撤销条件：**
>
> 当其他线程尝试获取已被偏向的锁时，偏向锁会被撤销，升级为轻量级锁。具体分两种情况：
>
> - **偏向线程仍在同步块中**：JVM 会在**全局安全点（Safe Point）** 将偏向锁**升级为轻量级锁**，原偏向线程继续持有锁（锁升级后，原线程继续执行）。
> - **偏向线程已不在同步块中或已退出**：JVM 直接将对象头改为**无锁状态**，其他线程通过自旋等待获取轻量级锁。
>
> 撤销过程必须在全局安全点执行，因为需要暂停所有线程，否则无法安全修改对象头。
>
> **为什么高并发下偏向锁成为瓶颈？**
>
> 偏向锁的设计初衷是优化**单线程重复加锁**的场景。但现代应用大多是**多线程高并发**场景，偏向锁反而带来额外开销：
>
> - **启动延迟**：JVM 启动后有 4 秒的偏向锁启动延迟（`-XX:BiasedLockingStartupDelay`），因为启动阶段线程竞争激烈。
> - **撤销开销**：每次偏向线程竞争锁时，都需要在安全点暂停所有线程，撤销偏向锁。高并发下这个操作频繁触发，成为瓶颈。
> - **并发退化和锁竞争**：偏向锁在多线程交替执行场景下会不断被撤销和重新偏向，造成大量无效的锁升级操作。
>
> 因此，**JDK 15 移除了偏向锁**（`--add-opens java.base/java.lang=ALL-UNNAMED` 可临时恢复）。现代高并发应用几乎不存在"单线程重复加锁"的有效场景，移除偏向锁反而提升了性能。
>
> **面试加分点：** 可以提到，如果确实需要偏向锁的行为（比如某些批处理任务），可以通过参数 `-XX:BiasedLockingStartupDelay=0` 提前开启偏向锁。

---

### 追问 2-3：synchronized 的 wait/notify 原理是什么？为什么 wait 必须在 synchronized 代码块内调用？

**追问回答：**

> **wait/notify 的原理：**
>
> wait/notify 操作的是对象关联的 Monitor。
>
> - **wait()：** 调用 wait() 的线程会释放当前持有的锁，然后进入 Monitor 的 `_WaitSet` 等待队列中，直到被其他线程调用 notify/notifyAll 唤醒。唤醒后重新进入 `_EntryList` 排队等待获取锁。
> - **notify()：** 从 Monitor 的 `_WaitSet` 中随机唤醒一个线程（notifyAll 则唤醒所有），被唤醒的线程不会立刻获得锁，而是重新进入 `_EntryList` 排队。
>
> **为什么 wait 必须在 synchronized 代码块内？**
>
> 这是 Java 语言层面的强制要求。如果 wait() 不在 synchronized 中，线程调用 wait() 时还没有获取锁，Monitor 关联的对象就无法确定——wait() 不知道该释放哪个对象的锁。
>
> 编译器会在 wait() 前检查当前线程是否持有该对象的锁，如果没有持有，抛出 `IllegalMonitorStateException`。
>
> **本质原因：** wait/notify 解决的是"线程间基于共享条件的协调问题"。条件本身就是被锁保护的——如果不在锁内，条件本身就有竞争风险，wait/notify 也就没有意义了。
>
> **面试加分点：** 可以提到，wait 和 sleep 都会让线程暂停，但本质不同：wait() 会**释放锁**，sleep() 不会释放锁；wait() 必须配合 synchronized 使用，sleep() 不需要。另一个区别是 wait() 可以通过 notify() 唤醒，sleep() 必须等超时。

---

## 追问三：AQS 相关

### 追问 3-1：AQS 的等待队列为什么是双向链表而不是单向链表？

**追问回答：**

> AQS 的等待队列是双向链表，主要是为了支持**高效的节点取消操作**。
>
> **如果用单向链表，取消节点的流程：**
>
> 需要从头节点开始遍历整个链表，找到要取消节点的前驱节点，然后修改前驱的 next 指针指向取消节点的 next。这是一个 O(n) 的操作。
>
> **双向链表只需要两步：**
>
> ```java
> // 取消节点 node
> node.prev.next = node.next;  // 前驱的 next 指向后继
> node.next.prev = node.prev;  // 后继的 prev 指向 前驱
> ```
>
> 只需要 2 步，时间复杂度 O(1)。
>
> **为什么取消节点很重要？**
>
> AQS 的等待队列中，线程可能因为超时或被中断而取消等待。取消操作必须高效，否则一个线程的取消操作本身就会阻塞队列。
>
> **另一个原因：后向查找。**
>
> 当 head 节点的 next 被取消后，单向链表无法继续向后查找。双向链表可以从 tail 向 head 遍历（通过 prev 指针），或者继续向后查找其他有效节点，避免节点丢失问题。
>
> **面试加分点：** 可以提到，CLH 队列（原始的 CLH 锁）其实是单向链表，AQS 对其做了改进——增加了 prev 指针，变成双向链表。AQS 的队列也常被叫做"CLH 队列的变体"。

---

### 追问 3-2：为什么公平锁比非公平锁慢？hasQueuedPredecessor() 是如何保证公平的？

**追问回答：**

> **公平锁比非公平锁慢的原因：**
>
> **非公平锁的 lock 流程：**
> 直接用 CAS 尝试修改 state，如果成功就直接获取锁。"插队"机会有两次——一次是刚启动时，一次是释放时。性能好。
>
> **公平锁的 lock 流程：**
> 无论什么情况，都要先调用 `hasQueuedPredecessors()` 检查队列中是否有更早等待的线程。只有队列中没有任何节点时，才允许尝试获取锁。
>
> 公平锁每次都要遍历队列检查，这带来了两个额外开销：
>
> - 额外的链表遍历操作（即使队列很短）
> - 锁的释放时，刚释放的线程不能立刻重新获取锁，必须重新排队
>
> **hasQueuedPredecessor() 的实现：**
>
> ```java
> public final boolean hasQueuedPredecessors() {
>     Node t = tail;   // 从尾节点开始
>     Node h = head;
>     Node s;
>     return h != t &&
>         ((s = h.next) == null || s.thread != Thread.currentThread());
> }
> ```
>
> 检查三件事：
> - `h != t`：队列是否为空（空则无需排队）
> - `s == null`：head 和 tail 之间是否只有一个 dummy 节点（说明有节点在排队）
> - `s.thread != Thread.currentThread()`：head 的下一个节点是否是自己（排除自己重入的情况）
>
> 只有三件事都不成立，才说明当前线程是最早等待的（或队列为空），才允许获取锁。
>
> **面试加分点：** 可以提到，实际使用中，**99% 的场景用非公平锁**，因为公平锁的排队开销得不偿失。只有在需要严格 FIFO 顺序的场景（比如写多读少的锁）才用公平锁。

---

## 追问四：线程池相关

### 追问 4-1：核心线程会被销毁吗？allowCoreThreadTimeOut 是什么？

**追问回答：**

> **正常情况下，核心线程不会被销毁。**
>
> 即使核心线程空闲，线程池也会保持核心线程存活，直到线程池被 shutdown。这是线程池的"常驻兵力"。
>
> **但有一种方式可以打破这个规则：**
>
> `allowCoreThreadTimeOut(boolean value)` —— 允许核心线程超时销毁。
>
> 一旦调用了这个方法，核心线程也会受到 `keepAliveTime` 的约束。如果核心线程空闲时间超过 keepAliveTime，且没有新任务，就会被销毁。
>
> ```java
> // 允许核心线程超时
> ThreadPoolExecutor executor = new ThreadPoolExecutor(
>     4, 8, 30L, TimeUnit.SECONDS,
>     new LinkedBlockingQueue<>(100),
>     Executors.defaultThreadFactory(),
>     new ThreadPoolExecutor.AbortPolicy()
> );
> executor.allowCoreThreadTimeOut(true);  // 核心线程也会被回收
> ```
>
> **典型使用场景：**
>
> - 任务量有明显的波峰波谷，波谷期希望释放资源
> - 需要最小化线程池的内存占用
>
> **注意：** `keepAliveTime` 的默认时间通常较长（通常 60 秒）。如果开启了 `allowCoreThreadTimeOut`，建议把 `keepAliveTime` 设短一些，否则核心线程会在较长时间后才被回收。
>
> **面试加分点：** 可以提到，线程池的线程复用靠的不是"不让核心线程销毁"，而是**线程从任务队列中持续取任务执行**。线程启动后会不断从 workQueue 中取任务，取到一个执行一个，取不到就阻塞等待（LinkedBlockingQueue 的 take()）。这就是线程复用的本质——线程本身不被销毁，一直在消费队列中的任务。

---

### 追问 4-2：execute() 和 submit() 有什么区别？为什么 submit() 更推荐？

**追问回答：**

> **execute() 和 submit() 的核心区别：**
>
> | 对比维度 | execute() | submit() |
> |---------|-----------|---------|
> | **返回值** | void，无返回值 | Future，可获取执行结果 |
> | **异常处理** | 异常会直接抛出（未捕获则线程终止） | 异常被包装到 Future 中，不会杀死线程 |
> | **重载** | 只能接受 Runnable | 可以接受 Runnable（返回 null）和 Callable（返回具体值） |
> | **使用场景** | 不需要返回结果的任务 | 需要返回结果或捕获异常的任务 |
>
> **为什么 submit() 更推荐？**
>
> **第一，submit() 能捕获异常。**
>
> ```java
> // execute() 的问题：异常会导致线程终止
> executor.execute(() -> {
>     int x = 1 / 0;  // 抛出 ArithmeticException
> });
>
> // submit() 的处理：异常被包装，不会杀死线程
> Future<?> future = executor.submit(() -> {
>     int x = 1 / 0;
> });
> future.get();  // 在这里才会抛出 ExecutionException(包装了 ArithmeticException)
> ```
>
> **第二，submit() 支持返回值。**
>
> 如果异步任务需要返回结果（如查询数据库返回用户信息），必须用 submit(Callable)。
>
> **第三，submit() 适合批量任务管理。**
>
> submit() 返回 Future 后，可以批量调用 `Future.get()` 或者用 CompletableFuture 进一步编排。
>
> **面试加分点：** 可以提到，在 Spring 框架中，`@Async` 注解底层用的就是 `submit()` 而不是 `execute()`，这是 Spring 选择 submit() 的一个重要原因——Spring 需要能够捕获异步方法的异常，否则异常会无声无息地丢失。

---

## 追问五：CompletableFuture 相关

### 追问 5-1：CompletableFuture 的 allOf() 有哪些坑？如何正确处理 allOf 的异常？

**追问回答：**

> **allOf() 最大的坑：异常短路。**
>
> `allOf(...).join()` 会在**第一个异常出现时立即失败并中断等待**，其他还没完成的任务会被忽略。
>
> ```java
> // 问题代码
> CompletableFuture.allOf(f1, f2, f3, f4)
>     .join();  // 如果 f2 抛出异常，f1/f3/f4 的结果全部丢失
> ```
>
> 这在业务上是不可接受的——我提交了 4 个任务，f2 失败了，但我连 f1/f3/f4 的结果都不知道。
>
> **正确处理方式一：独立 join() + 手动汇总**
>
> ```java
> CompletableFuture<String> f1 = CompletableFuture.supplyAsync(() -> queryUser());
> CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> queryOrder());
> CompletableFuture<String> f3 = CompletableFuture.supplyAsync(() -> queryProduct());
>
> Map<String, Object> results = new HashMap<>();
> List<CompletableFuture<?>> futures = Arrays.asList(f1, f2, f3);
>
> for (CompletableFuture<?> f : futures) {
>     f.handle((result, ex) -> {
>         if (ex == null) {
>             results.put(f.toString(), result);  // 正常结果放入
>         } else {
>             results.put(f.toString(), "FAILED");  // 失败也记录
>         }
>         return null;
>     });
> }
>
> CompletableFuture.allOf(futures.toArray(new CompletableFuture[0])).join();
> ```
>
> **正确处理方式二：用 handle 将异常转为 Result 对象**
>
> ```java
> // 把每个 CompletableFuture 包装成返回 Result 的版本
> CompletableFuture<Result<T>> safeFuture = originalFuture
>     .handle((T result, Throwable ex) -> {
>         if (ex == null) return Result.success(result);
>         else return Result.failure(ex);
>     });
>
> // allOf 的是 safeFuture，全部成功才真正成功
> ```
>
> **面试加分点：** 可以提到，`anyOf()` 也有类似问题——只要有一个完成就返回，但返回的是第一个完成的结果（不管成功还是失败），可能返回异常结果而非成功结果。

---

### 追问 5-2：CompletableFuture 默认使用的 ForkJoinPool 有什么问题？

**追问回答：**

> **ForkJoinPool.commonPool() 的两个致命问题：**
>
> **问题一：线程数固定且有限。**
>
> ForkJoinPool 的线程数默认是 `CPU核心数 - 1`（最少为 1）。如果所有核心都是物理核心，线程数 = 核心数 - 1。
>
> 这对于 IO 密集型任务来说是灾难——假设有 8 个 CPU 核心，公共池只有 7 个线程。如果 7 个线程全部阻塞在 IO 上，第 8 个任务根本进不来。
>
> **问题二：所有 CompletableFuture 共享同一个池。**
>
> 不仅仅是你的代码，JDK 内部的并行流（`Stream.parallel()`）、CompletableFuture 等都默认用这个池。如果你的某个任务把池占满了，会影响其他模块的任务。
>
> **正确做法：**
>
> **每个业务场景使用独立的线程池：**
>
> ```java
> // 为 IO 密集型任务单独建线程池
> ExecutorService ioExecutor = new ThreadPoolExecutor(
>     20, 100, 60L, TimeUnit.SECONDS,
>     new LinkedBlockingQueue<>(5000),
>     new ThreadFactoryBuilder().setNameFormat("io-task-%d").build(),
>     new ThreadPoolExecutor.CallerRunsPolicy()
> );
>
> // 所有 CompletableFuture 明确指定线程池
> CompletableFuture.supplyAsync(() -> queryData(), ioExecutor)
>     .thenApply(data -> process(data))
>     .thenAccept(result -> handle(result));
> ```
>
> **面试加分点：** 可以提到，Spring 框架中的 `@Async` 注解配合 `AsyncConfigurer` 使用自定义线程池，就是解决这个问题的标准做法。

---

## 追问六：volatile 与 happens-before

### 追问 6-1：volatile 的底层原理是什么？什么是 happens-before 规则？

**追问回答：**

> **volatile 的底层原理：**
>
> volatile 依赖 CPU 的**内存屏障（Memory Barrier）**实现。
>
> **写操作（volatile 写）：**
> 在写指令后插入 `Store Barrier`（写屏障），强制将数据写入**主内存**，并让其他 CPU 核心的**高速缓存失效**。
>
> **读操作（volatile 读）：**
> 在读指令前插入 `Load Barrier`（读屏障），强制从**主内存**读取最新数据，清空本地缓存。
>
> **x86 架构的特殊性：**
>
> 在 x86 架构下，volatile 读是免费的（不需要屏障，CPU 本身保证缓存一致性），但 volatile 写需要 `Store Barrier`（开销较大）。这就是为什么 volatile 适合"读多写少"的场景。
>
> **什么是 happens-before 规则？**
>
> happens-before 是 Java 内存模型（JMM）的核心概念，定义了**可见性保证**——如果操作 A happens-before 操作 B，那么 A 的结果对 B 可见。
>
> **核心 happens-before 规则：**
>
> | 规则 | 说明 |
> |------|------|
> | **程序顺序规则** | 单线程中，前面的操作 happens-before 后面的操作 |
> | **volatile 规则** | 对 volatile 变量的写 happens-before 对同一变量的读 |
> | **传递性** | 如果 A happens-before B，B happens-before C，则 A happens-before C |
> | **线程启动规则** | Thread.start() happens-before 被启动线程中的所有操作 |
> | **线程终止规则** | 线程中的所有操作 happens-before 其他线程检测到该线程终止 |
> | **锁规则** | 对锁的解锁 happens-before 后续对同一锁的加锁 |
>
> **面试加分点：** 可以提到，volatile 只能保证**可见性**，不能保证**原子性**。比如 `volatile int i = 0; i++;` 这个操作，volatile 保证 i 的值变化对其他线程可见，但 `i++` 本身不是原子的（包含读取-加1-写入三步），仍然需要 synchronized 或 CAS 来保护。

---

## 追问七：CompletableFuture 所有 API 详解

### 追问 7-1：CompletableFuture 完整 API 速查

**追问回答：**

> **创建异步任务：**
>
> - `supplyAsync(Supplier)` —— 有返回值，使用默认 ForkJoinPool
> - `supplyAsync(Supplier, Executor)` —— 有返回值，使用自定义线程池
> - `runAsync(Runnable)` —— 无返回值，使用默认线程池
> - `runAsync(Runnable, Executor)` —— 无返回值，使用自定义线程池
> - `completedFuture(T)` —— 返回一个已经完成的 Future
>
> **链式转换（处理结果）：**
>
> - `thenApply(Function)` —— 转换结果，返回新值（返回 CompletableFuture）
> - `thenAccept(Consumer)` —— 消费结果，无返回值（返回 CompletableFuture<Void>）
> - `thenRun(Runnable)` —— 执行一个动作，无输入无输出
> - `thenCompose(Function)` —— 扁平化链式调用（处理依赖，返回 CompletableFuture 而非嵌套）
> - `thenCombine(other, BiFunction)` —— 合并两个独立任务结果
> - `thenCombineAsync(other, BiFunction)` —— 在其他线程池执行合并
>
> **异常处理：**
>
> - `exceptionally(Function)` —— 异常时返回默认值
> - `exceptionallyCompose(Function)` —— 异常时执行一个异步恢复操作
> - `handle(BiFunction)` —— 不管成功还是异常都执行
> - `whenComplete(BiConsumer)` —— 结果回调，无返回值，用于日志记录等副作用
>
> **等待与获取结果：**
>
> - `join()` —— 阻塞获取结果，异常不抛检查异常（直接抛 unchecked）
> - `get()` —— 阻塞获取结果，抛检查异常（需 try-catch）
> - `get(timeout, unit)` —— 超时阻塞获取
> - `getNow(T valueIfAbsent)` —— 立即获取，已完成返回结果，未完成返回默认值
>
> **超时控制：**
>
> - `orTimeout(duration, unit)` —— 超时则抛 TimeoutException（Java 9+）
> - `completeOnTimeout(value, duration, unit)` —— 超时则返回默认值（Java 9+）
>
> **任务批量控制：**
>
> - `allOf(CompletableFuture[])` —— 等待所有任务完成，返回 CompletableFuture<Void>
> - `anyOf(CompletableFuture[])` —— 任意一个任务完成即返回
> - `completedStage(U)` —— 返回已完成的 CompletionStage（Java 9+）
> - `copy()` —— 复制一个 CompletableFuture，两者互不影响（Java 9+）
>
> **主动完成控制：**
>
> - `complete(T value)` —— 主动标记为完成，设置结果
> - `completeExceptionally(Throwable ex)` —— 主动标记为失败
> - `cancel(boolean mayInterruptIfRunning)` —— 取消任务（内部调用 completeExceptionally）
>
> **面试加分点：** 可以提到 thenApply / thenAccept / thenRun 的区别——apply 转换结果并返回新值，accept 消费结果但不返回，run 执行动作且不关心输入。实际使用时，最常用的是 thenApply 和 thenAccept。

---

## 高频追问速查表

| 原题 | 追问 | 核心答案 |
|------|------|---------|
| Q2 HashMap | 为什么链表转红黑树是8？ | 泊松分布概率统计，正常使用几乎不到千万分之一，主要是防哈希 DoS 攻击 |
| Q2 HashMap | 负载因子为什么是0.75？ | 时间成本和空间成本的最优平衡点，配合2的幂次扩容 |
| Q3 ConcurrentHashMap | size() 怎么算的？ | JDK7 modCount重试3次 / JDK8 baseCount+CounterCell[]，弱一致性 |
| Q14 synchronized | 什么是锁消除和锁粗化？ | 锁消除：逃逸分析后消除无竞争锁；锁粗化：合并连续针对同一对象的加解锁 |
| Q15 synchronized | 偏向锁撤销条件和瓶颈？ | 竞争时安全点撤销，JDK15后移除（高并发撤销开销大） |
| Q15 synchronized | wait为什么必须在synchronized内？ | 编译器强制检查，wait需要知道释放哪个对象的锁，锁内保证条件无竞争 |
| Q22 AQS | CLH队列为什么用双向链表？ | 高效取消节点（O(1)两步操作），支持后向查找避免节点丢失 |
| Q23 AQS | 为什么公平锁比非公平锁慢？ | 公平锁每次都要检查队列+不能插队，非公平锁有两次插队机会 |
| Q29 线程池 | 核心线程会被销毁吗？ | 正常不会；allowCoreThreadTimeOut(true)后受keepAliveTime约束 |
| Q29 线程池 | execute vs submit？ | submit能捕获异常+支持返回值，推荐使用submit |
| Q33 CompletableFuture | allOf()有什么坑？ | join()会因首个异常立即失败并忽略其他任务，应用handle独立处理每个异常 |
| Q33 CompletableFuture | ForkJoinPool有什么问题？ | 线程数=CUP核心数-1且全局共享，IO密集型任务会阻塞池，建议自定义线程池 |
| volatile | volatile底层原理？ | CPU内存屏障，volatile写Store Barrier，volatile读Load Barrier，x86写开销大 |
| volatile | happens-before是什么？ | JMM可见性保证，volatile规则/程序顺序规则/锁规则等8条核心规则 |
| Q34 CompletableFuture | thenApply/thenAccept/thenRun区别？ | apply转换返回新值，accept消费不返回，run不关心输入输出 |
