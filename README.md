<div align=center>

<img src=assets/banner.png width=100% alt=Speculative_inference_from_exact_sampling_to_agent_execution>

# Awesome Speculative Inference

**Racing Against Time: Speculative Inference in the Era of Long Contexts and Long-Horizon Agents**

A curated map of speculative inference across exact sampling, long contexts,<br/>
evolving interactions, agent execution, and resource-aware serving.

[![Curated papers](https://img.shields.io/badge/Curated_Papers-123-7C3AED?style=flat-square)](https://github.com/Robot2050/Awesome-Speculative-Inference#table-of-contents)
[![Research focus](https://img.shields.io/badge/Focus-Long_Contexts_and_Agents-2563EB?style=flat-square)](https://github.com/Robot2050/Awesome-Speculative-Inference#why-speculation-needs-a-task-level-view)
[![License](https://img.shields.io/badge/License-Apache_2.0-22C55E?style=flat-square)](LICENSE)

[Overview](#why-speculation-needs-a-task-level-view) •
[Foundations](#foundations-of-speculative-inference) •
[Drafting](#drafting-and-verification) •
[Long Contexts](#long-context-speculative-decoding) •
[Agents](#speculative-execution-for-agents) •
[Evaluation](#performance-benchmarks-and-evaluation) •
[Research Directions](#research-directions)

<sub>Chapter aligned with the survey · Continuously updated · Community contributions are welcome</sub>

</div>

## News

- **[2026/11]** Released the initial chapter aligned collection covering 123 papers from exact token sampling to speculative agent execution.
- **[2026/11]** Added a unified view of verification contracts, runtime costs, state validity, and task level evaluation.
- **[2026/11]** Contributions are welcome. See the [contribution guide](CONTRIBUTING.md) for scope and entry conventions.

## Why Speculation Needs a Task Level View

Speculative inference is built on a useful asymmetry. A prediction can be cheap and imperfect when verification can efficiently decide which work to keep. That idea began with exact token sampling, but its importance grows as contexts lengthen and agents act over many dependent steps.

For long context models, a small drafter may still pay a large cost to read history. For agents, faster decoding can simply expose the next tool wait. The research problem is therefore broader than acceptance rate. It concerns how to produce useful candidates, confirm them under an explicit contract, and reduce the time required to finish real work.

> [!IMPORTANT]
> The goal is not to speculate more. It is to convert prediction into verified progress on the critical path, with a clear account of compute, memory, state, and recovery costs.

This survey connects four shifts that are often studied separately.

| Shift | Established focus | Broader question in this survey |
|:---|:---|:---|
| From token latency to task latency | Accepted tokens and decoding speed | Does the method shorten a complete request or task |
| From draft accuracy to verified progress | Token level prediction quality | How many candidates survive verification, and at what execution cost |
| From static prompts to evolving state | A fixed input context | Which text, caches, actions, and observations remain reusable after change |
| From isolated decoding to shared execution | Single sequence acceleration | How speculation interacts with batching, tools, device placement, and deadlines |

<div align=center>
<img src=assets/timeline.png width=96% alt=Timeline_of_speculative_inference_research>
<br/>
<sub><b>Research timeline.</b> Representative methods and evaluation studies, from early parallel decoding to long context and agent execution.</sub>
</div>

### A Common Lens Across Levels

The collection follows the organization of the survey. Each work is placed under its primary contribution, while the same analytical lens applies across tokens, contexts, and actions.

| Design dimension | Central question | Representative choices |
|:---|:---|:---|
| Predictive information | What can the proposal mechanism know, and what does that information cost | Independent models, target features, sparse histories, retrieval, trajectory feedback |
| Candidate organization | Which dependencies and alternatives are represented | Chains, trees, parallel positions, argument slots, action segments |
| Verification contract | What must hold before prepared work can be retained | Distribution preservation, greedy agreement, state consistency, observation agreement, semantic acceptance |
| Execution cost | Which work lies on the critical path | Drafting, verification, cache maintenance, synchronization, tools, rollback |
| Adaptation and scheduling | Where should the next unit of speculative effort go | Candidate budgets, context selection, draft updates, request scheduling, action selection |

This perspective also separates concepts that are easy to conflate. Exact token verification preserves a specified target distribution. Approximate verification changes the quality and cost operating point. A predicted tool call is text until it is confirmed by the target. A predicted action additionally requires valid arguments, dependencies, and contained effects before its result can be committed.

## Table of Contents

<details open>
<summary><b>Explore the survey and literature map</b></summary>

- [Why Speculation Needs a Task Level View](#why-speculation-needs-a-task-level-view)
- [Related Surveys](#related-surveys)
- [Foundations of Speculative Inference](#foundations-of-speculative-inference)
- [Drafting and Verification](#drafting-and-verification)
  - [Draft Information Sources](#draft-information-sources)
  - [Parallel and Recurrent Drafting](#parallel-and-recurrent-drafting)
  - [Candidate Trees and Verification](#candidate-trees-and-verification)
  - [Training for Verification](#training-for-verification)
- [Long Context Speculative Decoding](#long-context-speculative-decoding)
  - [Sparse Draft Histories](#sparse-draft-histories)
  - [Shared and Bounded Draft State](#shared-and-bounded-draft-state)
  - [Approximate Verification and Cache Compression](#approximate-verification-and-cache-compression)
  - [Long Inputs, Long Outputs, and Context Diagnostics](#long-inputs-long-outputs-and-context-diagnostics)
- [Context Reuse and Draft Adaptation](#context-reuse-and-draft-adaptation)
- [Speculative Execution for Agents](#speculative-execution-for-agents)
  - [Structured Tool Call Generation](#structured-tool-call-generation)
  - [Speculative Actions](#speculative-actions)
  - [Observation Prediction and Execution Isolation](#observation-prediction-and-execution-isolation)
  - [Trajectory Verification and Certified Execution](#trajectory-verification-and-certified-execution)
- [Scheduling and Resource Allocation](#scheduling-and-resource-allocation)
- [Performance, Benchmarks, and Evaluation](#performance-benchmarks-and-evaluation)
- [Research Directions](#research-directions)
- [Contributing](#contributing)
- [Citation](#citation)

</details>

## Related Surveys

Existing surveys provide broad accounts of proposal generation, verification, and systems design. This repository builds on that foundation and focuses on the connection between long context decoding, evolving histories, and long horizon execution.

- **`arXiv 2024`** Unlocking Efficiency in Large Language Model Inference: A Comprehensive Survey of Speculative Decoding. [[paper](https://arxiv.org/abs/2401.07851)]
- **`arXiv 2025`** Speculative Decoding and Beyond: An In-Depth Survey of Techniques. [[paper](https://arxiv.org/abs/2502.19732)]
- **`IEEE Access 2026`** From Draft-Then-Verify to Systems Co-Design: A Survey of Speculative Decoding for Autoregressive Large Language Models. [[paper](https://doi.org/10.1109/ACCESS.2026.3739598)]

## Foundations of Speculative Inference

Speculative sampling lets a target model check several proposed positions in one pass. With an appropriate acceptance and correction rule, an imperfect drafter can accelerate sampling without changing the specified target distribution. The foundations below establish the acceptance rule, the survival of accepted prefixes, and the distinction between candidate coverage and realized execution cost.

### Exact Sampling and Verification Theory

- **`arXiv 2018`** Blockwise Parallel Decoding for Deep Autoregressive Models. [[paper](https://arxiv.org/abs/1811.03115)]
- **`arXiv 2022`** Fast Inference from Transformers via Speculative Decoding. [[paper](https://arxiv.org/abs/2211.17192)]
- **`arXiv 2023`** Accelerating Large Language Model Decoding with Speculative Sampling. [[paper](https://arxiv.org/abs/2302.01318)]
- **`arXiv 2023`** SpecTr: Fast Speculative Decoding via Optimal Transport. [[paper](https://arxiv.org/abs/2310.15141)]
- **`arXiv 2024`** Block Verification Accelerates Speculative Decoding. [[paper](https://arxiv.org/abs/2403.10444)]
- **`arXiv 2024`** Multi-Draft Speculative Sampling: Canonical Decomposition and Theoretical Limits. [[paper](https://arxiv.org/abs/2410.18234)]

### Verification Contracts Across Predicted Objects

The predicted object determines what successful verification means. Keeping these contracts explicit prevents results obtained under one guarantee from being interpreted as evidence for another.

| Predicted object | Criterion | What must be specified |
|:---|:---|:---|
| Token continuation | Distribution preservation | Proposal probabilities, residual correction, target context, sampling transforms |
| Token continuation | Greedy agreement | Path conditioning, tie handling, numerical behavior |
| Tool action | Action and state consistency | Tool and arguments, state dependencies, isolation, ordered commit |
| Observation | Observation agreement | The exact or task relevant condition for matching the real result |
| Action segment | Semantic acceptance | The verifier, acceptable behavior, and its error profile |
| Completed task | Task quality retention | Success, latency, cost, and the resource budget |

## Drafting and Verification

The central algorithmic problem is to provide enough useful candidates within a limited verification budget. Draft information, conditional dependencies, tree structure, and training objectives are complementary choices rather than mutually exclusive families.

### Draft Information Sources

Independent models offer a clean interface but duplicate weights and historical state. Self speculative methods reuse target computation, while feature based drafters exploit target hidden states. Retrieved continuations are covered separately under [context reuse](#context-reuse-and-draft-adaptation).

- **`arXiv 2023`** Draft & Verify: Lossless Large Language Model Acceleration via Self-Speculative Decoding. [[paper](https://arxiv.org/abs/2309.08168)]
- **`arXiv 2023`** DistillSpec: Improving Speculative Decoding via Knowledge Distillation. [[paper](https://arxiv.org/abs/2310.08461)]
- **`arXiv 2024`** EAGLE: Speculative Sampling Requires Rethinking Feature Uncertainty. [[paper](https://arxiv.org/abs/2401.15077)]
- **`arXiv 2024`** LayerSkip: Enabling Early Exit Inference and Self-Speculative Decoding. [[paper](https://arxiv.org/abs/2404.16710)]
- **`arXiv 2024`** Learning Harmonized Representations for Speculative Sampling. [[paper](https://arxiv.org/abs/2408.15766)]
- **`arXiv 2025`** EAGLE-3: Scaling up Inference Acceleration of Large Language Models via Training-Time Test. [[paper](https://arxiv.org/abs/2503.01840)]

### Parallel and Recurrent Drafting

Parallel prediction reduces serial draft latency but weakens within block dependencies. Recurrent, semi autoregressive, and diffusion based drafters make different tradeoffs between proposal diversity, conditional structure, and draft cost. Their output guarantees still depend on the downstream verification rule.

- **`arXiv 2024`** Medusa: Simple LLM Inference Acceleration Framework with Multiple Decoding Heads. [[paper](https://arxiv.org/abs/2401.10774)]
- **`arXiv 2024`** Recurrent Drafter for Fast Speculative Decoding in Large Language Models. [[paper](https://arxiv.org/abs/2403.09919)]
- **`arXiv 2024`** Better & Faster Large Language Models via Multi-token Prediction. [[paper](https://arxiv.org/abs/2404.19737)]
- **`arXiv 2024`** ParallelSpec: Parallel Drafter for Efficient Speculative Decoding. [[paper](https://arxiv.org/abs/2410.05589)]
- **`arXiv 2026`** P-EAGLE: Parallel-Drafting EAGLE with Scalable Training. [[paper](https://arxiv.org/abs/2602.01469)]
- **`arXiv 2026`** DFlash: Block Diffusion for Flash Speculative Decoding. [[paper](https://arxiv.org/abs/2602.06036)]
- **`arXiv 2026`** DSpark: Confidence-Scheduled Speculative Decoding with Semi-Autoregressive Generation. [[paper](https://arxiv.org/abs/2607.05147)]
- **`arXiv 2026`** From Chains to Trees: Parent-Conditioned Drafting for Semi-Autoregressive Speculative Decoding. [[paper](https://arxiv.org/abs/2608.02123)]
- **`arXiv 2026`** DARTree: Speculative Diffusion Decoding with Autoregressive Draft Trees. [[paper](https://arxiv.org/abs/2608.13524)]
- **`arXiv 2026`** DForkTree: Accelerating Speculative Decoding with Fork-Aware Block Diffusion Draft Trees. [[paper](https://arxiv.org/abs/2611.12356)]

### Candidate Trees and Verification

A deeper tree offers a longer possible continuation. A wider tree protects against early ambiguity. Both consume target computation, and usually only one committed continuation survives. Useful tree design therefore balances expected progress against verification and memory costs.

- **`arXiv 2023`** SpecInfer: Accelerating Generative Large Language Model Serving with Tree-based Speculative Inference and Verification. [[paper](https://arxiv.org/abs/2305.09781)]
- **`arXiv 2024`** Sequoia: Scalable, Robust, and Hardware-aware Speculative Decoding. [[paper](https://arxiv.org/abs/2402.12374)]
- **`arXiv 2024`** DeFT: Decoding with Flash Tree-attention for Efficient Tree-structured LLM Inference. [[paper](https://arxiv.org/abs/2404.00242)]
- **`arXiv 2024`** EAGLE-2: Faster Inference of Language Models with Dynamic Draft Trees. [[paper](https://arxiv.org/abs/2406.16858)]
- **`arXiv 2024`** OPT-Tree: Speculative Decoding with Adaptive Draft Tree Structure. [[paper](https://arxiv.org/abs/2406.17276)]
- **`arXiv 2025`** Traversal Verification for Speculative Tree Decoding. [[paper](https://arxiv.org/abs/2505.12398)]
- **`arXiv 2026`** Bastion: Budget-Aware Speculative Decoding with Tree-structured Block Diffusion Drafting. [[paper](https://arxiv.org/abs/2605.29727)]
- **`arXiv 2026`** TAPS: Target-Aware Prefix Tree Selection for Diffusion-Drafted Speculative Decoding. [[paper](https://arxiv.org/abs/2606.00487)]

### Training for Verification

Draft training increasingly targets the opportunities created by verification. These methods move beyond local token fit toward prefix survival, candidate coverage, tree level utility, and progress across successive decoding rounds.

- **`arXiv 2025`** Bridging Draft Policy Misalignment: Group Tree Optimization for Speculative Decoding. [[paper](https://arxiv.org/abs/2509.22134)]
- **`arXiv 2026`** Acceptance-Aware Draft Model Training for Speculative Decoding. [[paper](https://arxiv.org/abs/2609.24150)]
- **`arXiv 2026`** Training Parallel Speculative Draft Models by Directly Minimizing Expected Decoding Rounds. [[paper](https://arxiv.org/abs/2610.10411)]
- **`arXiv 2026`** TreeSpark: Learning to Draft the Trees You Verify for Speculative Decoding. [[paper](https://arxiv.org/abs/2610.20175)]
- **`arXiv 2026`** Factorization-Guided Training and Inference for Diffusion-Drafted Speculative Decoding. [[paper](https://arxiv.org/abs/2610.23935)]

## Long Context Speculative Decoding

Long contexts make access to predictive information a major part of decoding cost. A small draft can repeatedly read a large history, duplicating work already performed by the target. Restricting that access saves computation but can hide the distant fact needed for the next continuation. Long context speculation is therefore a problem of information allocation as well as model compression.

<div align=center>
<img src=assets/long_context.png width=96% alt=Context_design_and_verification_for_long_context_speculation>
<br/>
<sub><b>Context design.</b> Sparse histories, shared caches, and bounded target derived views reduce different costs, while verification determines what can be committed.</sub>
</div>

### Sparse Draft Histories

- **`arXiv 2024`** TriForce: Lossless Acceleration of Long Sequence Generation with Hierarchical Speculative Decoding. [[paper](https://arxiv.org/abs/2404.11912)]
- **`arXiv 2024`** MagicDec: Breaking the Latency-Throughput Tradeoff for Long Context Generation with Speculative Decoding. [[paper](https://arxiv.org/abs/2408.11049)]
- **`arXiv 2025`** LongSpec: Long-Context Lossless Speculative Decoding with Efficient Drafting and Verification. [[paper](https://arxiv.org/abs/2502.17421)]
- **`arXiv 2025`** SpecExtend: A Drop-in Enhancement for Speculative Decoding of Long Sequences. [[paper](https://arxiv.org/abs/2505.20776)]
- **`arXiv 2026`** Vegas: Self-Speculative Decoding with Verification-Guided Sparse Attention. [[paper](https://arxiv.org/abs/2602.07223)]
- **`arXiv 2026`** BudgetDraft: Acceptance-Aware Multi-View Training for Sparse-KV Speculative Decoding. [[paper](https://arxiv.org/abs/2606.00144)]
- **`arXiv 2026`** RIFT: KV Cache Compression for Draft Models with Complementary Compressibility Training. [[paper](https://arxiv.org/abs/2610.31098)]

### Shared and Bounded Draft State

Bounding draft storage does not necessarily bound the cost of reading target history. These methods explore that distinction through shared caches, fixed size target derived views, local windows, positional treatment, and recurrent state.

- **`arXiv 2025`** OWL: Overcoming Window Length-Dependence in Speculative Decoding for Long-Context Inputs. [[paper](https://arxiv.org/abs/2510.07535)]
- **`arXiv 2026`** DeLS-Spec: Decoupled Long-Short Contexts for Parallel Speculative Drafting. [[paper](https://arxiv.org/abs/2607.07409)]
- **`arXiv 2026`** Windowed-MTP: Removing the Full-Context Draft-KV Tax at Million-Token Context. [[paper](https://arxiv.org/abs/2607.21535)]
- **`arXiv 2026`** LongSpark: Efficient speculative decoding with a fixed-cost parallel drafter. [[paper](https://arxiv.org/abs/2609.37029)]
- **`arXiv 2026`** SharpDraft: Accelerating Long-Context Speculative Decoding with Cardinality-Aware Query Scaling. [[paper](https://arxiv.org/abs/2610.05106)]
- **`arXiv 2026`** FOLD: Position-Free Far Memory with Local Order for Long-Context Speculative Decoding. [[paper](https://arxiv.org/abs/2610.20392)]
- **`arXiv 2026`** Long-Context Drafting Does Not Require Long-Context Drafters. [[paper](https://arxiv.org/abs/2610.20865)]
- **`arXiv 2026`** DLite: History-free Speculative Decoding for Agentic AI. [[paper](https://arxiv.org/abs/2610.23787)]

### Approximate Verification and Cache Compression

Quantizing only draft state can preserve the target distribution. Approximating target verification or changing the target context creates a different reference computation and requires explicit quality evaluation. This distinction is essential when comparing claims of lossless acceleration.

- **`arXiv 2025`** QuantSpec: Self-Speculative Decoding with Hierarchical Quantized KV Cache. [[paper](https://arxiv.org/abs/2502.10424)]
- **`arXiv 2025`** SpecAttn: Speculating Sparse Attention. [[paper](https://arxiv.org/abs/2510.27641)]
- **`arXiv 2025`** SpecPV: Improving Self-Speculative Decoding for Long-Context Generation via Partial Verification. [[paper](https://arxiv.org/abs/2512.02337)]
- **`arXiv 2025`** Accelerate Speculative Decoding with Sparse Computation in Verification. [[paper](https://arxiv.org/abs/2512.21911)]
- **`arXiv 2026`** Dustin: Draft-Augmented Sparse Verification for Efficient Long-Context Generation with Speculative Decoding. [[paper](https://arxiv.org/abs/2606.24957)]
- **`arXiv 2026`** Memory-Efficient Speculative Decoding with Quantized Draft KV Cache. [[paper](https://arxiv.org/abs/2611.20565)]

### Long Inputs, Long Outputs, and Context Diagnostics

Long input, long output, interaction horizon, and speculative depth are separate workload variables. A long document followed by a short answer emphasizes initialization and historical attention. A long answer repeatedly grows decoding state. Diagnostics must therefore report where and under which workload a method helps.

- **`arXiv 2025`** Speculative Prefill: Turbocharging TTFT with Lightweight and Training-Free Token Importance Estimation. [[paper](https://arxiv.org/abs/2502.02789)]
- **`arXiv 2025`** TokenSwift: Lossless Acceleration of Ultra Long Sequence Generation. [[paper](https://arxiv.org/abs/2502.18890)]
- **`arXiv 2025`** RAPID: Long-Context Inference with Retrieval-Augmented Speculative Decoding. [[paper](https://arxiv.org/abs/2502.20330)]
- **`arXiv 2026`** When Hidden States Drift: Can KV Caches Rescue Long-Range Speculative Decoding? [[paper](https://arxiv.org/abs/2604.26412)]
- **`arXiv 2026`** Speculative Acceptance Does Not Decay with Position: A Full-Length Diagnostic on Long Chain-of-Thought with Independent Drafts. [[paper](https://arxiv.org/abs/2610.30644)]

## Context Reuse and Draft Adaptation

A long interaction accumulates information that can make later predictions cheaper. Previously generated code, retrieved passages, and recurring tool arguments supply reusable continuations. At the same time, each observation or edit can change what the agent needs to produce next. Reuse exploits accumulated structure, while adaptation learns the changing distribution of decisions.

<div align=center>
<img src=assets/reuse_adaptation.png width=96% alt=Routes_from_interaction_history_to_draft_reuse_and_adaptation>
<br/>
<sub><b>Reuse and adaptation.</b> Retrieval supplies existing continuations, while trajectory aware learning changes the model that proposes them.</sub>
</div>

The validity of reuse depends on the role of stored information. Retrieved text is only a proposal, so the target can reject an outdated fragment. Cached target keys and values affect verification probabilities and require prefix consistency. Tool results depend on environment state even when the visible conversation remains unchanged.

### Retrieval Based Drafting and Workspace Reuse

- **`arXiv 2023`** REST: Retrieval-Based Speculative Decoding. [[paper](https://arxiv.org/abs/2311.08252)]
- **`arXiv 2024`** Turning Trash into Treasure: Accelerating Inference of Large Language Models with Token Recycling. [[paper](https://arxiv.org/abs/2408.08696)]
- **`arXiv 2024`** SuffixDecoding: Extreme Speculative Decoding for Emerging AI Applications. [[paper](https://arxiv.org/abs/2411.04975)]
- **`arXiv 2024`** SAM Decoding: Speculative Decoding via Suffix Automaton. [[paper](https://arxiv.org/abs/2411.10666)]
- **`arXiv 2025`** DReSD: Dense Retrieval for Speculative Decoding. [[paper](https://arxiv.org/abs/2502.15572)]
- **`arXiv 2025`** RASD: Retrieval-Augmented Speculative Decoding. [[paper](https://arxiv.org/abs/2503.03434)]
- **`arXiv 2025`** EfficientEdit: Accelerating Code Editing via Edit-Oriented Speculative Decoding. [[paper](https://arxiv.org/abs/2506.02780)]
- **`arXiv 2025`** Cacheback: Speculative Decoding With Nothing But Cache. [[paper](https://arxiv.org/abs/2511.21699)]
- **`arXiv 2026`** AdaPLD: Adaptive Retrieval and Reuse for Efficient Model-Free Speculative Decoding. [[paper](https://arxiv.org/abs/2606.05742)]
- **`arXiv 2026`** AgSpec: Pushing the Limits of Retrieval-Based Speculative Decoding in Coding Agent Pipelines. [[paper](https://arxiv.org/abs/2610.01108)]

### Trajectory Aware and Online Adaptation

Reuse operates at several timescales. Candidate fragments persist across nearby rounds, retrieval memories span turns or tasks, and draft parameters adapt across deployment experience. Longer persistence creates more opportunities to recover acquisition cost, but also increases the need for refresh as workloads change.

- **`arXiv 2023`** Online Speculative Decoding. [[paper](https://arxiv.org/abs/2310.07177)]
- **`arXiv 2026`** When Drafts Evolve: Speculative Decoding Meets Online Learning. [[paper](https://arxiv.org/abs/2603.12617)]
- **`arXiv 2026`** AgentSpec: Speculative Decoding for Batch Inference of LLM Agents. [[paper](https://arxiv.org/abs/2608.24004)]
- **`arXiv 2026`** TraceSpec: Understanding and Improving Speculative Decoding for LLM Agents. [[paper](https://arxiv.org/abs/2610.41177)]

## Speculative Execution for Agents

An agent repeatedly waits for information that determines its next action. Across a long task, model calls, tool execution, and observation processing form a sequence of delays. Speculation can shorten that sequence by predicting call syntax, starting a likely action early, or preparing a continuation for an expected observation.

<div align=center>
<img src=assets/agent_execution.png width=96% alt=Speculation_within_the_agent_execution_loop>
<br/>
<sub><b>Agent execution loop.</b> Call generation, early actions, observation prediction, and trajectory review require different confirmation criteria and recovery mechanisms.</sub>
</div>

The critical distinction is between predicting text and executing an action. A schema valid call can still select the wrong tool or arguments. Even an identical action can produce an unusable result if a relevant file, service, or hidden dependency changes before commitment. Speculative execution therefore requires dependency tracking, effect containment, and ordered recovery in addition to prediction accuracy.

### Structured Tool Call Generation

- **`arXiv 2026`** ToolSpec: Accelerating Tool Calling via Schema-Aware and Retrieval-Augmented Speculative Decoding. [[paper](https://arxiv.org/abs/2604.13519)]
- **`arXiv 2026`** Agent-X: Full Pipeline Acceleration of On-device AI Agents. [[paper](https://arxiv.org/abs/2605.10380)]
- **`arXiv 2026`** OoO-Spec: Out-of-Order Semantic Speculation for Fast Tool Calling. [[paper](https://arxiv.org/abs/2608.00814)]
- **`arXiv 2026`** SchemaFill: Efficient LLM Tool Calling via Slot-Parallel Speculative Decoding. [[paper](https://arxiv.org/abs/2610.07086)]

### Speculative Actions

- **`arXiv 2025`** Speculative Actions: A Lossless Framework for Faster Agentic Systems. [[paper](https://arxiv.org/abs/2510.04371)]
- **`arXiv 2025`** Optimizing Agentic Language Model Inference via Speculative Tool Calls. [[paper](https://arxiv.org/abs/2512.15834)]
- **`arXiv 2026`** Parallelizing Tool Execution and LLM Generation for Low-Latency Agent Serving. [[paper](https://arxiv.org/abs/2603.18897)]
- **`arXiv 2026`** Skim: Speculative Execution for Fast and Efficient Web Agents. [[paper](https://arxiv.org/abs/2605.16565)]
- **`arXiv 2026`** SPORK: Self-Speculative Forking to Accelerate Agentic LLM Inference. [[paper](https://arxiv.org/abs/2607.03333)]
- **`arXiv 2026`** Speculate with Memory: Lossless Acceleration for LLM Agents. [[paper](https://arxiv.org/abs/2607.12236)]
- **`arXiv 2026`** LEAP: Learning Efficient Action Proposals For LLM Agents. [[paper](https://arxiv.org/abs/2610.02670)]

### Observation Prediction and Execution Isolation

- **`arXiv 2025`** Sherlock: Reliable and Efficient Agentic Workflow Execution. [[paper](https://arxiv.org/abs/2511.00330)]
- **`arXiv 2026`** Speculative Interaction Agents: Building Real-Time Agents with Asynchronous I/O and Speculative Tool Calling. [[paper](https://arxiv.org/abs/2605.13360)]
- **`arXiv 2026`** IdleSpec: Exploiting Idle Time via Speculative Planning for LLM Agents. [[paper](https://arxiv.org/abs/2605.22154)]
- **`arXiv 2026`** AOSpec: Action and Observation Co-Speculation for Low-Latency Agent Serving. [[paper](https://arxiv.org/abs/2608.00881)]
- **`arXiv 2026`** TomasuLLM: Out-of-Order Speculative Execution for LLM Agents. [[paper](https://arxiv.org/abs/2609.38201)]

### Trajectory Verification and Certified Execution

Segment level verification can amortize expensive model calls over several decisions. Semantic acceptance gives the proposed trajectory more freedom, but places greater responsibility on the verifier. Certified execution instead relies on a domain specific solver or certificate where such a check is available.

- **`arXiv 2026`** DualSpec: Accelerating Deep Research Agents via Dual-Process Action Speculation. [[paper](https://arxiv.org/abs/2603.07416)]
- **`arXiv 2026`** B-PASTE: Beam-Aware Pattern-Guided Speculative Execution for Resource-Constrained LLM Agents. [[paper](https://arxiv.org/abs/2604.16469)]
- **`arXiv 2026`** Certified Speculative Execution for Untrusted AI Agents. [[paper](https://arxiv.org/abs/2606.31023)]
- **`arXiv 2026`** Speculative Macro Commit for Faster Tool-Using Agents. [[paper](https://arxiv.org/abs/2609.03236)]
- **`arXiv 2026`** Trajectory-Level Speculation with Semantic Verification for Tool-Using Agents. [[paper](https://arxiv.org/abs/2610.21463)]

## Scheduling and Resource Allocation

Scheduling determines whether local speculative gains survive in a shared service. Candidate generation and verification compete with other requests for compute, memory, and execution slots. A budget that accelerates an isolated sequence can delay a loaded server.

<div align=center>
<img src=assets/scheduling.png width=96% alt=Scheduling_speculative_work_under_shared_resource_constraints>
<br/>
<sub><b>Resource aware scheduling.</b> Budget allocation and placement determine what can overlap, while runtime feedback guides subsequent adjustments.</sub>
</div>

Reducing draft cost can move the bottleneck to verification. Faster model generation can expose tool latency. Composition is therefore an optimization problem in its own right, and the ability to shorten or disable speculation is as important as the ability to expand it.

### Budget Allocation Across Requests

- **`arXiv 2024`** BASS: Batched Attention-optimized Speculative Sampling. [[paper](https://arxiv.org/abs/2404.15778)]
- **`arXiv 2024`** TurboSpec: Closed-loop Speculation Control System for Optimizing LLM Serving Goodput. [[paper](https://arxiv.org/abs/2406.14066)]
- **`arXiv 2025`** AdaServe: Accelerating Multi-SLO LLM Serving with SLO-Customized Speculative Decoding. [[paper](https://arxiv.org/abs/2501.12162)]
- **`arXiv 2025`** TETRIS: Optimal Draft Token Selection for Batch Speculative Decoding. [[paper](https://arxiv.org/abs/2502.15197)]
- **`arXiv 2026`** ForeSpark: Joint Draft and Verification Budget Scheduling for Parallel Speculative Decoding. [[paper](https://arxiv.org/abs/2611.13442)]
- **`arXiv 2026`** SpecSearch: Towards Hardware-Aware Efficient Speculative Decoding. [[paper](https://arxiv.org/abs/2611.22296)]

### Overlap, Placement, and Throughput

- **`arXiv 2024`** Distributed Speculative Inference (DSI): Speculation Parallelism for Provably Faster Lossless Language Model Inference. [[paper](https://arxiv.org/abs/2405.14105)]
- **`arXiv 2024`** SpecExec: Massively Parallel Speculative Decoding for Interactive LLM Inference on Consumer Devices. [[paper](https://arxiv.org/abs/2406.02532)]
- **`arXiv 2025`** SPIRe: Boosting LLM Inference Throughput with Speculative Decoding. [[paper](https://arxiv.org/abs/2504.06419)]
- **`arXiv 2025`** SwiftSpec: Ultra-Low Latency LLM Decoding by Scaling Asynchronous Speculative Decoding. [[paper](https://arxiv.org/abs/2506.11309)]

## Performance, Benchmarks, and Evaluation

Accepted length measures usable candidate coverage. Round timing captures its computational price. Request and task measurements show the benefit under actual scheduling and interaction. These levels answer different questions and should be reported together when possible.

<div align=center>
<img src=assets/evaluation.png width=96% alt=Evaluation_from_token_execution_to_completed_tasks>
<br/>
<sub><b>Controlled evaluation.</b> Aligned target, workload, runtime, hardware, and timing conditions connect implementation fidelity to completed task outcomes.</sub>
</div>

A credible comparison needs more than a shared dataset. Target artifacts, numerical precision, cache policies, attention kernels, active batch, sampling configuration, and timing scope all affect the result. For agents, fixed prefix replay, dynamic execution, and real tool execution answer complementary questions and should not be treated as interchangeable.

### Benchmarks and Runtime Controlled Studies

- **`arXiv 2024`** Decoding Speculative Decoding. [[paper](https://arxiv.org/abs/2402.01528)]
- **`arXiv 2025`** Speculative Decoding: Performance or Illusion? [[paper](https://arxiv.org/abs/2601.11580)]
- **`arXiv 2026`** SPEED-Bench: A Unified and Diverse Benchmark for Speculative Decoding. [[paper](https://arxiv.org/abs/2604.09557)]
- **`Electronics 2026`** Runtime-Controlled Evaluation of Speculative Decoding Methods. [[paper](https://doi.org/10.3390/electronics15143179)]

### Correctness, Cost, and Negative Results

- **`arXiv 2025`** The Disparate Impacts of Speculative Decoding. [[paper](https://arxiv.org/abs/2510.02128)]
- **`arXiv 2025`** Correctness Forensics for Batch Speculative Decoding: Diagnosing the Ragged Tensor Problem. [[paper](https://arxiv.org/abs/2510.22876)]
- **`arXiv 2026`** Benchmarking the Energy Savings with Speculative Decoding Strategies. [[paper](https://arxiv.org/abs/2602.09113)]
- **`arXiv 2026`** Accepted Prefixes Are Not All You Need: A Negative Result on PEFT-Based Block-Diffusion Drafting. [[paper](https://arxiv.org/abs/2607.12422)]
- **`arXiv 2026`** Lossless but Not Free: An Empirical Anatomy of Speculative Decoding on Consumer Hardware. [[paper](https://arxiv.org/abs/2607.17283)]
- **`arXiv 2026`** When Does Better Acceptance Pay? A Controlled Study of Speculative Decoding. [[paper](https://arxiv.org/abs/2610.24993)]
- **`arXiv 2026`** Target Regeneration Shrinks the Gains of Draft-Training Objectives in Speculative Decoding. [[paper](https://arxiv.org/abs/2610.29013)]

### Application and Test Time Scaling Studies

- **`arXiv 2025`** Scaling Up, Speeding Up: A Benchmark of Speculative Decoding for Efficient LLM Test-Time Scaling. [[paper](https://arxiv.org/abs/2509.04474)]
- **`arXiv 2026`** An Empirical Study of Speculative Decoding on Software Engineering Tasks. [[paper](https://arxiv.org/abs/2604.26469)]
- **`arXiv 2026`** Accelerating PayPal's Commerce Agent with Speculative Decoding: An Empirical Study on EAGLE3 with Fine-Tuned Nemotron Models. [[paper](https://arxiv.org/abs/2604.19767)]

### Evaluation Checklist

A useful empirical report should make the following conditions visible.

1. **Reference contract:** State whether the method preserves a target distribution, matches greedy execution, changes target computation, or uses semantic acceptance.
2. **Workload composition:** Report input length, output length, interaction horizon, task mix, trajectory phase, and sampling settings where relevant.
3. **Runtime alignment:** Match model artifacts, hardware, precision, attention kernels, cache policy, scheduler, and active batch against the baseline.
4. **Complete cost:** Measure drafting, verification, state maintenance, synchronization, data transfer, tool execution, cancellation, and recovery without double counting.
5. **Absolute and relative performance:** Report delivered throughput and latency alongside speedup against a matched baseline, including tail behavior under load.
6. **Task outcomes:** For agents, measure success, elapsed time, and total cost across both successful and unsuccessful attempts.
7. **Fixed budget comparisons:** Test both equal work and equal time or cost. The latter reveals what saved resources enable through additional checking or exploration.

## Research Directions

Long contexts and extended agent interactions create a distinctive opportunity for speculative inference. Repeated history provides reusable information, verification supplies feedback, and tool waits create room for useful computation. The challenge is to sustain reductions in task latency and cost as histories, decisions, and resource availability change.

<div align=center>
<img src=assets/research_directions.png width=96% alt=Research_directions_for_long_context_and_long_horizon_speculation>
<br/>
<sub><b>Research framework.</b> Six directions connect information allocation and learning to adaptation, state validity, scheduling, and task quality.</sub>
</div>

### 1. Joint Context and Candidate Selection

Context and candidate budgets compete for the same resources but compensate for different errors. A wider tree helps when several continuations are plausible. Reading a relevant distant passage helps when the draft lacks the information needed to propose any of them. A promising controller would select the historical view together with tree depth, width, and verification budget, using feedback to distinguish missing context from insufficient candidate coverage.

### 2. Learning from Deployment Feedback

The current tree policy determines which branches receive target evaluation, while the current agent policy determines which histories are encountered. Training only on frequently selected cases can reinforce a narrow proposal strategy. Selective verification of alternative branches and replay of underrepresented states offer a way to measure this bias. Supervision should reflect reachable prefix coverage and the cost of rounds that a prediction can save.

### 3. Adaptation over the Deployment Lifetime

An update pays for itself only when future savings exceed training, transfer, and state rebuilding costs. A short unfamiliar task may favor retrieval or a generic draft, while a recurring workflow may justify specialization. Research should measure recovery time after workload shifts, training contention, peak memory, and retained performance when earlier tasks return.

### 4. Dependency Aware Reuse

Text fragments can remain useful proposals after an edit, while a target cache or tool result may become invalid immediately. Selective invalidation could discard only the work affected by a change and retain independent branches. The key tradeoff is between fine grained provenance tracking, which preserves more work, and its own time and memory cost.

### 5. Scheduling along the Task Critical Path

The most likely prediction is not always the most valuable one. Correctly anticipating a short tool call may save little time, while a less frequent but predictable test run can dominate task latency. A joint scheduler should estimate hidden latency, verification cost, discarded work, interference, and recovery across both model and tool execution. Knowing when to pause speculation is part of the solution.

### 6. Task Quality under Fixed Budgets

Time saved during generation can fund another solution candidate, a test, or a repair attempt before a deadline. This connects acceleration to the quality of completed work. The central question is how to allocate saved resources between familiar candidates that are easy to draft and novel alternatives that may be more useful for resolving a failure. Equal time and equal cost experiments can show when speculation merely shortens a workflow and when it enables a more capable one.

## Contributing

Contributions are welcome. Please open a pull request to add a missing paper, correct metadata, repair a link, or improve the taxonomy. For a new section or a broad reorganization, open an issue first so the change can be discussed in context.

Use the following entry format and place each work under its primary contribution.

```markdown
- **`arXiv 2026`** Paper Title. [[paper](https://arxiv.org/abs/XXXX.XXXXX)] [[code](https://github.com/OWNER/REPO)]
```

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for scope, metadata conventions, and the review checklist.

## Citation

If this survey or collection is useful to your work, please cite:

```bibtex
@article{zhao2026racing,
	doi = {10.20944/preprints202611.2438.v1},
	url = {https://doi.org/10.20944/preprints202611.2438.v1},
	year = 2026,
	month = {November},
	publisher = {Preprints},
	author = {Jihao Zhao and Zhe Sun and Shuaishuai Zu and Jiabao Kang and Lingfeng Feng and Biao Qin},
	title = {Racing Against Time: Speculative Inference in the Era of Long Contexts and Long-Horizon Agents},
	journal = {Preprints}
}
```

## License

This repository is licensed under the [Apache License 2.0](LICENSE).
