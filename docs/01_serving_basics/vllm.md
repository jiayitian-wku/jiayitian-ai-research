# vLLM

> 状态：第一轮官方资料整理。实现细节主要参考 vLLM V1 介绍和当前文档，后续阅读源码时需要重新核对版本。

## 什么是 LLM Serving

**个人理解：** LLM inference 是执行模型生成结果；LLM Serving 是把 inference 变成可持续接收多个请求的系统。除模型 forward 外，它通常还包含：

- API 与输入处理；
- 请求排队；
- scheduler 与 batch 构造；
- KV Cache 和 GPU memory 管理；
- 模型执行、采样、流式输出和停止条件；
- 延迟、吞吐量、容量和可靠性管理。

因此，本项目研究的 scheduler 位于“请求已经进入服务系统”和“GPU 执行模型”之间。

## vLLM 的定位

**资料事实：** vLLM 官方将其定位为高吞吐量的 LLM inference and serving engine，可以用于 offline inference，也可以提供 OpenAI-compatible server。PagedAttention 是其核心 KV Cache 管理机制之一。

一个简化的 V1 请求路径是：

```text
raw request
  ↓ validation / tokenization
EngineCoreRequest
  ↓
scheduler waiting queue
  ↓ schedule + KV block allocation
running requests / batch
  ↓
model executor forward + sampling
  ↓
postprocess / stream / finish / free KV blocks
```

## Scheduler 中当前可确认的事实

根据 vLLM 官方 V1 介绍：

- scheduler 决定哪些请求进入下一次 engine step；
- scheduler 维护 `waiting` 和 `running` queues；
- KV cache manager 是 scheduler 的组成部分之一；
- 引用版本中，scheduler 先处理 running queue 中的 Decode 请求，再考虑 waiting queue 中的 Prefill 请求；
- KV blocks 不足时，可能无法调度请求，某些情况下会触发 recompute preemption；
- 请求结束后会释放 KV blocks；
- continuous batching 使新旧请求可以在每个 step 被重新考虑。

当前 engine arguments 文档公开的基础 scheduling policy 包括 `fcfs` 和 `priority`，并允许指定自定义 scheduler class。本文没有发现“vLLM 默认使用输出长度预测进行调度”的官方说明。

## PagedAttention 的作用

**资料事实：** PagedAttention 把请求的 KV Cache 分成 blocks，通过 block table 映射到不连续的物理内存，并随生成按需分配。这减少了连续内存预留与碎片问题，使有限显存可以容纳更多活跃序列。

PagedAttention 解决的是“KV Cache 如何更灵活地存放和管理”，不是“下一个应该调度哪个请求”。两者相关，但不是同一层问题。

## 与本研究的关系

**个人理解：** vLLM 给本项目提供了一个真实系统约束：

```text
预测器不能只输出一个数字
→ 还要明确这个数字交给 scheduler 后改变什么
→ priority？admission？batch composition？KV safety margin？preemption？
```

由于 vLLM 已经按需分配 KV blocks，不能直接假设长度预测一定要用于“一次性预留完整输出的 KV Cache”。长度信息也可能用于请求优先级、未来资源压力估计或抢占风险控制；其收益需要设计并验证。

## 版本边界

- 2023 年官方介绍适合建立 PagedAttention 的核心直觉。
- “Inside vLLM”文章基于 2025-08-09 的一个具体 commit，类名和具体顺序可能随 V1 演进。
- 当前 engine arguments 文档可能继续变化，未来实现实验时必须固定 vLLM 版本和 commit。

## 当前仍需确认

- 自定义长度感知 scheduler 最适合从 vLLM 内部 scheduler class 介入，还是通过外部 priority/router 介入？
- 当前版本对 running request 的 preemption、KV 分配和 mixed prefill/decode 的准确行为是什么？
- 哪些 metrics 能直接从 vLLM 获取，哪些需要自行埋点？

## 主设计者审阅问题

1. 你是否同意把 PagedAttention 与 scheduling policy 分成“内存机制”和“决策策略”两层？
2. 你希望未来的研究原型尽量少改 vLLM 内核，还是接受修改 scheduler 内部？这会影响工程路线，暂不立即决定。
3. 对长度预测的第一候选介入点，你更倾向 waiting queue 排序还是 KV/admission 决策？请只给设计偏好，不需要现在定结论。

## 来源

- [vLLM: Easy, Fast, and Cheap LLM Serving with PagedAttention](https://vllm.ai/blog/2023-06-20-vllm)
- [Inside vLLM: Anatomy of a High-Throughput LLM Inference System](https://vllm.ai/blog/2025-09-05-anatomy-of-vllm)
- [vLLM Engine Arguments](https://docs.vllm.ai/en/latest/configuration/engine_args/)
- [vLLM Benchmark CLI](https://docs.vllm.ai/en/latest/benchmarking/cli/)
