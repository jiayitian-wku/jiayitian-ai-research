# Prefill and Decode

> 状态：第一轮 serving 基础整理。本文只建立与输出长度调度有关的系统直觉，不涉及算子和内核细节。

## 一句话区分

- **Prefill：** 处理请求已有的 prompt，建立 prompt 对应的 KV Cache，并产生第一个输出 token 所需的 logits。
- **Decode：** 复用已有 KV Cache，自回归地继续生成后续 token，并把新 token 的 KV 状态追加到缓存中。

## 资料事实

以下内容来自 vLLM 官方博客对 V1 引擎的说明，以及 SGLang 团队对 Prefill/Decode 的介绍：

| 维度 | Prefill | Decode |
|---|---|---|
| 主要输入 | 当前尚未计算的 prompt tokens | 已有上下文和最新 token |
| 工作方式 | 通常并行处理较多 prompt tokens；长 prompt 也可能被 chunked prefill 拆成多个 engine step | 标准自回归解码通常每个 engine step 为每个活跃请求生成一个新 token；推测解码等技术是例外 |
| KV Cache | 为 prompt tokens 计算并写入 K/V | 读取此前 K/V，并为新 token 追加 K/V |
| 常见性能特征 | 通常更偏 compute-bound，但具体分界依赖硬件、模型和 prompt 长度 | 通常更偏 memory-bandwidth-bound，因为每一步计算 token 很少，却需要读取模型权重和已有 KV Cache |
| 直接相关延迟 | 对 TTFT 影响显著 | 对 ITL/TPOT 和输出完成时间影响显著 |

vLLM 的 V1 engine step 可以调度 Decode，也可以调度 Prefill 或 chunked Prefill。一个 step 大致经历：调度请求、执行 forward、采样和后处理；请求满足停止条件后，系统清理它并释放对应 KV blocks。

## 请求生命周期

```text
请求到达
  ↓
输入处理与 tokenization
  ↓
waiting queue
  ↓
Prefill：处理 prompt，建立初始 KV Cache
  ↓
产生第一个输出 token
  ↓
Decode：生成后续 token，并持续扩展 KV Cache
  ↓
EOS / stop condition / max_tokens
  ↓
请求完成，释放其运行资源
```

上图是高层逻辑。现代引擎可能混合调度 Prefill 和 Decode，也可能把长 Prefill 切块，或在不同设备上分离两个阶段，因此不能把它理解成所有请求都严格串行地先统一 Prefill、再统一 Decode。

## 延迟指标的联系

**资料事实：** vLLM 当前 benchmark 文档将 TTFT 定义为从客户端发送请求到收到第一个流式输出的时间；TPOT 是去掉首 token 后，剩余端到端时间按剩余输出 token 数进行摊销。指标名称在不同工具之间并不完全标准化，比较实验时应核对具体测量点和公式。

**个人理解：**

- TTFT 不等于纯 Prefill kernel 时间，它还可能包括输入处理、排队、调度、通信和首 token 返回。
- TPOT 反映 Decode 阶段的平均节奏，但平均值可能掩盖个别 token 的抖动，因此还需要关注 ITL 分布。

## 与输出长度调度的关系

**个人理解：**

1. 输入长度主要决定请求到达后需要处理多少 prompt tokens，并强烈影响 Prefill 工作量。
2. 输出长度决定请求要持续多少轮自回归生成，并影响它在 running set 中停留多久。
3. 在标准逐 token 生成中，输出越长，通常意味着更多 Decode step，以及更长时间持有并扩展 KV Cache。
4. 请求到达时真实输出长度未知，所以调度器不能直接知道其未来迭代次数、完成时间和资源释放时机。

这解释了为什么本项目强调预测**输出长度**：它试图补充调度器对请求未来 Decode 行为的未知信息，而不是替代已经可以直接观察的输入 token 数。

## 当前仍需确认

- vLLM 和 SGLang 各版本如何在同一个 step 中混合 Prefill 与 Decode？
- 不同模型结构、推测解码和 chunked prefill 会怎样改变上述简化图？
- 输出长度对 TTFT、TPOT 和端到端延迟的影响是否会因负载而改变？

## 主设计者审阅问题

1. 你是否同意将“输入长度主要关联 Prefill，输出长度主要关联持续 Decode”作为第一层直觉，而不是绝对规律？
2. 你能否指出 TTFT 为什么不能简单等同于 Prefill 时间？
3. 在你的研究里，预测器应该在请求进入 waiting queue 前、进入后还是准备进入 running set 时给出预测？这一点目前先不定案。

## 来源

- [Inside vLLM: Anatomy of a High-Throughput LLM Inference System](https://vllm.ai/blog/2025-09-05-anatomy-of-vllm)
- [vLLM Benchmark CLI：Latency Metrics](https://docs.vllm.ai/en/latest/benchmarking/cli/)
- [SGLang：Prefill and Decode Disaggregation](https://www.lmsys.org/blog/2025-05-05-large-scale-ep/)
