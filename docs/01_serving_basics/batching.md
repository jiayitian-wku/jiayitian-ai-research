# Batching

> 状态：第一轮 serving 基础整理。本文区分静态/固定请求批次与 continuous batching，并连接到输出长度未知问题。

## 为什么要 Batching

**个人理解：** 单个 Decode step 的工作量可能不足以充分利用 GPU。把多个请求放在一次模型执行中，可以摊销模型权重读取、kernel launch 和部分系统开销，提高整体吞吐量。不过 batch 越大并不自动代表所有延迟指标越好，它还受显存、token budget、请求长度分布和负载影响。

## “固定 batch”在本文中的含义

这里的固定 batch 特指：**先组成一组请求，并在这组请求整体结束前不动态替换成员。**

它不等同于配置中的“最大 batch size”，也不意味着所有现代系统都采用相同的 padding 实现。

假设一个 batch 有三个请求：

```text
A：较短  ──完成
B：中等  ─────────完成
C：较长  ─────────────────────完成
```

如果必须等 C 完成才组成下一批，那么 A、B 完成后释放出的执行机会无法及时接纳 waiting queue 中的新请求。不同输出长度越明显，这种静态成员关系越不适合在线生成服务。

## Continuous Batching

**资料事实：** vLLM V1 的异步引擎在每个 engine step 后都会同时考虑新旧请求；scheduler 为下一步选择 Decode 和/或 Prefill 请求。请求完成后会被清理并归还 KV blocks，新请求可以在后续 step 被调度。这构成 continuous batching 的核心直觉。

```text
step 1：A B C
step 2：A B C
A 完成并退出
step 3：D B C    ← 新请求 D 补入
B 完成并退出
step 4：D E C    ← 新请求 E 补入
```

这里的“连续”不是指 GPU 永远没有空闲，也不是指任意请求每一步都必然进入 batch；scheduler 仍要受 token budget、KV Cache 容量、优先级和其他约束影响。

## 它解决了什么

- 允许短请求完成后尽快释放位置和 KV 资源；
- 允许在线到达的新请求在后续迭代加入；
- 减少固定请求批次中因完成时间不同造成的空置；
- 为 scheduler 在每个 step 重新组合工作提供机会。

## 它没有自动解决什么

Continuous batching 提供了动态调度的机制，但不会自动决定最佳策略。它仍然留下：

- waiting queue 中先选谁；
- Prefill 与 Decode 如何共享 token budget；
- KV Cache 不足时如何 admission 或 preemption；
- 如何平衡 TTFT、TPOT、throughput、公平性和 tail latency；
- 是否以及如何使用未知的未来输出长度。

SGLang 的官方介绍将 continuous batching 与 RadixAttention、paged attention 视为可兼容技术；SGLang 的 scheduler 还通过将 CPU 上的下一批调度与当前 GPU 计算重叠，减少调度本身造成的 GPU 空隙。这是“降低调度开销”，并不等同于“已经解决长度感知调度策略”。

## 与输出长度调度的关系

**个人理解：**

- 固定 batch 下，输出长度影响一批请求要等待最长请求多久；
- continuous batching 下，请求可以动态离开和加入，但调度器仍不知道每个请求未来还需多少 Decode step；
- 因此研究问题从“如何避免等待整批结束”进一步变成“每一步应该从 waiting/running requests 中选择和组合谁”。

输出长度预测可能帮助选择请求或规划资源，但是否改善整体性能，取决于预测错误、负载、调度目标和底层引擎机制，需要后续论文与实验验证。

## 当前仍需确认

- 在 vLLM 和 SGLang 当前版本中，batch 的具体组成单位是 requests、tokens 还是两者共同约束？
- Prefill 和 Decode 混合批处理会如何改变 TTFT/TPOT 权衡？
- 长度感知策略应该作用于 waiting queue、running set，还是 admission control？

## 主设计者审阅问题

1. 你能否解释 continuous batching 是“机制”，而 FCFS/SJF 是“选择策略”这一差别？
2. 你是否同意：有了 continuous batching，输出长度未知仍然是问题，只是问题形式发生了变化？
3. 在在线服务里，你最想优化平均性能、尾延迟、吞吐量还是公平性？今天可以暂不定案，但需要记录偏好。

## 来源

- [Inside vLLM: Anatomy of a High-Throughput LLM Inference System](https://vllm.ai/blog/2025-09-05-anatomy-of-vllm)
- [SGLang：Fast and Expressive LLM Inference with RadixAttention](https://www.lmsys.org/blog/2024-01-17-sglang/)
- [SGLang v0.4：Zero-Overhead Batch Scheduler](https://www.lmsys.org/blog/2024-12-04-sglang-v0-4/)
