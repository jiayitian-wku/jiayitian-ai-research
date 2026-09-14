# SGLang

> 状态：第一轮官方资料整理。本文只建立 serving runtime、KV Cache 与 scheduler 的高层联系，不展开高级部署功能。

## SGLang 的定位

**资料事实：** SGLang 官方将其定位为面向大语言模型和多模态模型的高性能 serving framework，目标包括低延迟和高吞吐量，支持从单 GPU 到分布式集群，并提供 OpenAI-compatible APIs。

从本项目视角，可以先把它理解为：

```text
客户端请求
  ↓
API / 输入处理
  ↓
SGLang serving runtime 与 scheduler
  ↓
batch、KV Cache、model worker
  ↓
生成与流式返回
```

这只是高层心智模型，不等于当前 SGLang 全部进程和模块的精确源码结构。

## 当前需要掌握的三个机制

### 1. Continuous Batching

SGLang 支持在线 LLM serving 所需的 continuous batching，使请求能够在生成过程中动态加入和退出批次。它提供动态执行基础，但具体选择谁、如何平衡资源仍属于 scheduler policy 问题。

### 2. RadixAttention / Prefix Cache

**资料事实：** SGLang 的 RadixAttention 使用 radix tree 管理 token 序列到 KV Cache tensors 的映射，自动发现不同请求或不同生成调用之间可复用的 prefix KV Cache；GPU 容量有限时，系统需要 eviction policy。官方早期介绍采用递归 LRU 叶节点淘汰，并说明它与 continuous batching 和 paged attention 兼容。

RadixAttention 主要回答“哪些已经计算过的前缀 KV 可以复用”，而输出长度预测主要回答“请求未来可能还要生成多久”。两者都涉及 KV Cache，但时间方向不同：一个面向已出现的 prefix，一个估计未来的 output。

### 3. Scheduler 与 GPU 执行重叠

**资料事实：** SGLang v0.4 的官方介绍指出，CPU 还要完成 batch scheduling、memory allocation 和 prefix matching。其 overlap scheduler 在 GPU 执行当前 batch 时，让 CPU 准备下一批所需 metadata，以减少 GPU 等待 CPU 调度产生的空隙。

这项机制优化的是 scheduler 自身的执行开销，不等于已经决定了最优的请求优先级。

## Prefill 与 Decode

SGLang 的官方 PD disaggregation 说明将 LLM inference 分为：

- Prefill：处理输入序列，计算密集；
- Decode：迭代生成 token，并管理/访问 KV Cache，通常更偏内存密集。

统一引擎中 Prefill 与 Decode 可能相互干扰，因此 SGLang 也支持把两阶段分离到不同服务实例。这个高级架构今天只需知道存在，不需要作为本项目当前方案。

## 与本研究的关系

**个人理解：** SGLang 提醒我们，真实 scheduler 的工作不只是 FCFS/SJF 排序，还可能同时涉及：

- Prefill/Decode batch 构造；
- KV Cache 分配、复用和淘汰；
- CPU 调度与 GPU 执行的流水；
- 多 worker 或分离式服务中的路由。

因此，输出长度预测要有价值，必须明确它影响哪一个具体决策，并确认额外预测和排序开销不会抵消收益。

可能的介入点包括 waiting queue policy、admission、batch composition 或资源安全边界；这些目前只是设计候选，不是 SGLang 已有默认行为，也不是本项目已经确定的方案。

## 与 vLLM 的第一层比较

| 维度 | vLLM | SGLang |
|---|---|---|
| 共同定位 | 高性能 LLM inference/serving 系统 | 高性能 LLM/多模态 serving framework |
| 当前重点机制 | PagedAttention、continuous batching、scheduler | RadixAttention/prefix caching、continuous batching、overlap scheduler 等 |
| 对本项目的意义 | 提供按 block 管理 KV 与真实 scheduler 入口 | 提供缓存复用、batch 调度和更复杂 serving runtime 的另一参考实现 |

该表只用于建立第一层定位，不是性能优劣比较。

## 版本边界

- SGLang 已从早期“structured generation language + runtime”持续演进为更完整的生产 serving framework。
- RadixAttention 说明主要来自 2024 年官方介绍，overlap scheduler 来自 v0.4 介绍；实现实验前必须重新核对所选版本源码和文档。

## 当前仍需确认

- 当前主线 SGLang runtime 的 queue、batch 和 KV 管理数据结构是什么？
- 它公开哪些 scheduling policy 或扩展接口？
- 长度感知调度更适合修改单实例 scheduler，还是外部 router？
- Radix prefix cache 命中会怎样改变用输入/输出长度估计资源的方式？

## 主设计者审阅问题

1. 你是否能区分 prefix cache reuse 与输出生成过程中 KV Cache 增长？
2. 你是否同意“zero-overhead scheduler”描述调度开销优化，而不是长度感知策略？
3. 在 vLLM 与 SGLang 之间，你希望先选一个作为主要实现候选，还是先完成 S3/TIE 阅读后再选？我建议后者。

## 来源

- [SGLang 官方网站](https://www.sglang.io/)
- [SGLang 官方文档首页](https://docs.sglang.io/)
- [Fast and Expressive LLM Inference with RadixAttention and SGLang](https://www.lmsys.org/blog/2024-01-17-sglang/)
- [SGLang v0.4: Zero-Overhead Batch Scheduler](https://www.lmsys.org/blog/2024-12-04-sglang-v0-4/)
- [SGLang：Prefill and Decode Disaggregation](https://www.lmsys.org/blog/2025-05-05-large-scale-ep/)
