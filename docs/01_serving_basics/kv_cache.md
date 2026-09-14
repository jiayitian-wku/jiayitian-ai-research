# KV Cache

> 状态：第一轮 serving 基础整理。本文重点关注 KV Cache 与输出长度、并发和调度的关系。

## KV Cache 保存什么

**资料事实：** Transformer 自回归生成新 token 时，需要让当前 token 关注此前 token。系统保存各层此前 token 的 key/value 张量，避免每个 Decode step 都重新计算整段历史的 K/V；这些张量称为 KV Cache。

KV Cache 不是生成文本本身，也不是模型权重。它是某个请求在当前上下文上的中间状态。

## 为什么它会增长

```text
Prefill 完成：KV(prompt tokens)
Decode 第 1 步：KV(prompt tokens + 新 token 1)
Decode 第 2 步：KV(prompt tokens + 新 token 1 + 新 token 2)
...
请求结束：释放或转入可复用缓存策略
```

**资料事实：** vLLM 的说明将 KV Cache 描述为 large、dynamic，其大小随序列长度变化；PagedAttention 将每个请求的 KV Cache 划分为固定 token 数的 blocks，并按需分配物理 blocks。

**个人理解：** 对普通 Transformer 而言，单请求 KV Cache 大小大体随当前已缓存 token 数线性增长，还受到层数、KV head 数、head dimension 和数据类型影响。具体计算不能只套一个通用公式，因为 MHA、GQA、MQA、MLA、滑动窗口等结构不同。

## PagedAttention 的第一层直觉

传统连续内存思路容易产生预留浪费和碎片。vLLM 的 PagedAttention 借鉴操作系统分页：

- 逻辑上，一个请求的 token 顺序连续；
- 物理上，对应 KV blocks 不必连续；
- block table 负责从逻辑 block 映射到物理 block；
- 新 token 到来时可以按需增加 block；
- 请求完成后，blocks 可以回到空闲池。

这使 KV Cache 管理更灵活，也让系统有机会同时容纳更多序列。它没有消除显存容量限制，也没有让输出长度变得已知。

## 输出长度未知带来的困难

请求到达时，系统通常知道 prompt token 数和用户给出的 `max_tokens` 上界，但不知道模型会在何时产生 EOS 或满足其他停止条件。因此：

- 不知道请求最终还会新增多少 KV 状态；
- 不知道它会占用 KV blocks 多久；
- 不知道什么时候可以释放资源给后续请求；
- 在显存紧张时，难以提前判断 admission、batch composition 或 preemption 的长期后果。

`max_tokens` 是允许生成的上限，不等于真实输出长度预测。将所有请求都按最大值看待可能过于保守，而完全忽略未来增长又可能导致后续资源压力。

## 与本研究的关系

**个人理解：** 输出长度预测可能向调度器提供两类信息：

1. **预计资源量：** 未来大约需要多少新增 KV Cache；
2. **预计资源持有时间：** 请求大约还会留在 running set 中多久。

现代 vLLM 已经能按 block 动态分配 KV Cache，因此不能预设“预测长度的唯一用途就是一次性精确预留全部显存”。它也可能用于排队顺序、admission、batch 组合、预留安全边界或 preemption 策略；这些都还是待研究设计，不是已采用方案。

## 当前仍需确认

- vLLM 与 SGLang 当前版本分别在什么粒度分配、释放和复用 KV Cache？
- 当 KV blocks 不足时，不同引擎如何选择等待、抢占、重计算或换出？
- 预测过短和预测过长分别会怎样影响实际 serving？
- prefix caching 与单个请求自身不断增长的 KV Cache 应如何区分？

## 主设计者审阅问题

1. 你是否能解释“输出长度影响的不只是计算步数，也包括资源持有时间”？
2. 你是否同意当前不把研究限定为“按预测长度预分配显存”？
3. 对你的设计而言，预计新增 KV 量和预计完成时间，哪一个更应该先成为调度器输入？目前不需要立即决定。

## 来源

- [vLLM: Easy, Fast, and Cheap LLM Serving with PagedAttention](https://vllm.ai/blog/2023-06-20-vllm)
- [Inside vLLM: Anatomy of a High-Throughput LLM Inference System](https://vllm.ai/blog/2025-09-05-anatomy-of-vllm)
- [vLLM Engine Arguments：CacheConfig 与 SchedulerConfig](https://docs.vllm.ai/en/latest/configuration/engine_args/)
