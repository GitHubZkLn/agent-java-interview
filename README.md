# Agent Java Interview

面向 Java 后端与 AI Agent 方向面试准备的资料合集。仓库内容以“面试口述版”“两页精要版”“可直接打印版”为主，覆盖 Java 基础、集合与并发、Spring、MySQL、Redis、Elasticsearch、Kafka、云原生，以及 RAG / AI Agent 系统设计与项目面经。

## 适合谁使用

- 准备 Java 后端开发、Java 工程师、后端研发实习/社招面试的人。
- 需要快速复习 Spring、JUC、MySQL、Redis、Kafka、ES 等高频八股与场景题的人。
- 准备 AI Agent、RAG、知识库、智能客服、多智能体、MCP / A2A 等方向面试的人。
- 想把知识点整理成可口述答案、可打印材料、冲刺清单的人。

## 内容结构

```text
.
├── README.md
└── html版本/
    ├── Java / Spring / JUC 面试资料
    ├── MySQL / Redis / ES / Kafka / 云原生资料
    ├── AI Agent / RAG / 知识库 Agent 资料
    └── 字节 AI Lab、网易云音乐等公司面经
```

## 资料导航

### Java 基础与并发

| 文件 | 内容定位 |
| --- | --- |
| [Java基础高频面试题_两页精要版.html](html版本/Java基础高频面试题_两页精要版.html) | Java 基础高频题速查，包含 equals/hashCode、String、关键字、OOP、接口/抽象类、异常、泛型、Java 8、BIO/NIO/AIO 等。 |
| [Java集合反射JUC并发面试题库_面试口述版.md](html版本/Java集合反射JUC并发面试题库_面试口述版.md) | 集合、HashMap、ConcurrentHashMap、反射、synchronized、CAS、AQS、线程池、CompletableFuture 等深度口述答案。 |
| [Java集合反射JUC并发面试题库_可直接打印.html](html版本/Java集合反射JUC并发面试题库_可直接打印.html) | Java 集合、反射、JUC 并发专题的打印版。 |
| [Java高频场景面试题库_面试口述版.html](html版本/Java高频场景面试题库_面试口述版.html) | ThreadLocal、启动初始化、过滤器/拦截器/AOP、大文本检索、综合场景设计、Spring Boot 高级特性等场景题。 |

### Spring / Spring Boot

| 文件 | 内容定位 |
| --- | --- |
| [Spring全家桶面试题库_面试口述版.md](html版本/Spring全家桶面试题库_面试口述版.md) | Spring IOC/AOP、Bean 生命周期、循环依赖三级缓存、Spring Boot 自动配置、Starter、事务、Spring MVC、设计模式等。 |
| [Spring全家桶面试题库_可直接打印.html](html版本/Spring全家桶面试题库_可直接打印.html) | Spring 全家桶核心专题打印版。 |

### 数据库、中间件与云原生

| 文件 | 内容定位 |
| --- | --- |
| [MySQL深度面试题库_面试口述版.html](html版本/MySQL深度面试题库_面试口述版.html) | MySQL 索引、事务隔离级别、MVCC、锁机制、慢 SQL 排查与优化。 |
| [Redis深度面试题库_面试口述版.html](html版本/Redis深度面试题库_面试口述版.html) | Redis 数据结构、高可用、缓存一致性、内存管理、分布式锁、集群与运维。 |
| [ES高频面试题_两页精要版.html](html版本/ES高频面试题_两页精要版.html) | Elasticsearch 集群、分片副本、倒排索引、写入/搜索流程、脑裂、分词器、搜索调优。 |
| [Kafka与运维云原生面试题库.html](html版本/Kafka与运维云原生面试题库.html) | Kafka 核心原理、消息可靠性、Docker、Kubernetes、Prometheus + Grafana 监控体系。 |

### AI Agent / RAG / 知识库

| 文件 | 内容定位 |
| --- | --- |
| [AI_Agent深度面试题库_面试口述版.html](html版本/AI_Agent深度面试题库_面试口述版.html) | Agent 设计模式、Multi-Agent、Skills / MCP / A2A、Prompt / Context Engineering、Memory、沙箱安全、AI 框架。 |
| [AI_Agent高频场景题_系统设计篇_面试口述版.html](html版本/AI_Agent高频场景题_系统设计篇_面试口述版.html) | Agent 架构、智能客服 Agent、Agent 平台工程化、RAG 知识库、效果评估、性能优化、综合系统设计。 |
| [AI_Agent项目面经_ThinkExecute_RAG_面试口述版.html](html版本/AI_Agent项目面经_ThinkExecute_RAG_面试口述版.html) | Think-Execute / RAG 项目面经，适合准备项目介绍与深挖追问。 |
| [智能客服FAQ知识库Agent_面试口述版.html](html版本/智能客服FAQ知识库Agent_面试口述版.html) | 智能客服 FAQ 知识库 Agent 项目讲解，包含 Agent、RAG、工程化、开放问题与召回率优化清单。 |

### 公司面经

| 文件 | 内容定位 |
| --- | --- |
| [字节AILab二面_面试口述版.html](html版本/字节AILab二面_面试口述版.html) | 字节 AI Lab 二面相关准备，覆盖自我介绍、项目、RAG、LangChain、部署、向量库、知识库、训练与算法。 |
| [网易云音乐一面面试题_面试口述版.html](html版本/网易云音乐一面面试题_面试口述版.html) | 网易云音乐一面题，侧重 Java 基础、并发与数据库。 |

## 推荐复习路线

### Java 后端路线

1. 先读 [Java基础高频面试题_两页精要版.html](html版本/Java基础高频面试题_两页精要版.html)，快速建立基础题框架。
2. 深入 [Java集合反射JUC并发面试题库_面试口述版.md](html版本/Java集合反射JUC并发面试题库_面试口述版.md)，重点掌握 HashMap、ConcurrentHashMap、AQS、线程池、CompletableFuture。
3. 阅读 [Spring全家桶面试题库_面试口述版.md](html版本/Spring全家桶面试题库_面试口述版.md)，补齐 IOC、AOP、循环依赖、事务、MVC 和 Spring Boot 自动配置。
4. 按顺序复习 MySQL、Redis、Kafka、ES，并结合场景题练习“为什么这么设计”和“线上怎么排查”。
5. 最后用 [Java高频场景面试题库_面试口述版.html](html版本/Java高频场景面试题库_面试口述版.html) 做综合串联。

### AI Agent 路线

1. 先读 [AI_Agent深度面试题库_面试口述版.html](html版本/AI_Agent深度面试题库_面试口述版.html)，建立 Agent 核心概念与工程化框架。
2. 再读 [AI_Agent高频场景题_系统设计篇_面试口述版.html](html版本/AI_Agent高频场景题_系统设计篇_面试口述版.html)，练习系统设计与场景拆解。
3. 用 [AI_Agent项目面经_ThinkExecute_RAG_面试口述版.html](html版本/AI_Agent项目面经_ThinkExecute_RAG_面试口述版.html) 和 [智能客服FAQ知识库Agent_面试口述版.html](html版本/智能客服FAQ知识库Agent_面试口述版.html) 准备项目表达。
4. 针对目标公司或岗位，再补充 [字节AILab二面_面试口述版.html](html版本/字节AILab二面_面试口述版.html) 这类公司面经。

## 使用建议

- `.md` 文件适合在 GitHub 直接阅读，也方便继续编辑。
- `.html` 文件适合浏览器打开或打印成 PDF，部分文件已经按 A4 和打印样式排版。
- “面试口述版”适合直接训练回答节奏，建议每个问题用 1 到 3 分钟复述一遍。
- “两页精要版”适合面试前一天或当天快速过关键词。
- “可直接打印版”适合做纸质复习材料，配合手写补充自己的项目经验。

## 仓库说明

本仓库主要用于个人面试复习与知识整理。内容偏向面试表达、知识点串联和场景化回答，不是完整的工程代码项目。

## License

当前仓库暂未添加开源许可证。如需对外分发或二次发布，请先确认内容来源、授权和使用边界。
