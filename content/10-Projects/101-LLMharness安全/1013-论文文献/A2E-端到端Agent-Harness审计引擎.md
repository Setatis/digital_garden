---
publish: true
title: A²E：端到端 Agent Harness 审计引擎
created: 2026-08-13
modified: 2026-10-27T16:46:17.479Z
tags:
  - 主题/研究
  - 主题/研究/Harness工程
  - 主题/研究/评测与对齐
---

# A²E：端到端 Agent Harness 审计引擎

> **论文信息**
>
> - 标题：$A^2E$ : An End-to-End Agent Auditing Engine
> - 作者：Haoning Wang, Mingxun Zhang, Chenyue Yu, Yingjun Shang, Xia Hu, Guanchu Wang, Na Zou（佐治亚理工 datamllab 系）
> - 时间：2026-08-07（v1）/ 2026-08-10（v2）
> - 分类：cs.AI
> - 链接：[arXiv:2608.07346](https://arxiv.org/abs/2608.07346) | 代码：github.com/datamllab/A2E

## 一句话核心贡献

提出 **A²E（Agent Auditing Engine）**——面向 agent harness 的端到端评估引擎：用新的 **Agent Task Protocol (ATP)** 快速把评测任务接入不同 harness，经自动插桩的 **Monitor** 捕获标准化执行轨迹，再用多维指标刻画 harness 能力差异，实证"模型—harness 组合存在显著性能差异，无单一组合全面胜出"。

## 速读解读

论文的起点很明确：harness 已成为部署 agent 的基础设施，但评估体系没跟上。传统做法只比"正确率"，无法回答"这个 harness 到底强在哪、弱在哪"。A²E 的三个组件对应三个痛点：(1) **ATP 协议**解决"评测任务 × harness 组合爆炸"的集成成本——任务只需写一次即可跨 harness 运行；(2) **自动插桩 Monitor** 解决轨迹数据不标准化的问题——实验过程生成可对比的执行痕迹；(3) **多维指标集**把"执行效率、工具使用、任务规划、错误恢复"从单一正确率里拆出来，做细粒度刻画。

实验结论对研究者很直接：同一模型配不同 harness、或同一 harness 配不同模型，在不同类型任务上表现差异显著，**没有"万能组合"**。这既证明系统化评估的必要性，也指向"模型与 harness 需要协同演化（co-evolving）"——评估引擎正是这个演化闭环的测量端。

**对研究方向的启发**：这是 harness 工程主题下少见的"评估基础设施"工作，与上周 Model-or-Harness 的失败归因互补：那篇回答"失败该修谁"，这篇回答"harness 能力怎么公平地量"。A²E 的多维指标观也呼应了 Agent-Harness Survey 中"harness 评估不能只看任务正确率"的判断。

## 与库内笔记的关联

- [[Agent-Harness-Engineering-A-Survey-全文翻译]]：综述里"harness 层能力需系统评估"论断的落地实现，ATP 协议即"评测任务跨 harness 可移植"的工程答案
- [[Model-or-Harness-交互式失败定位分类法]]：互补关系——那篇归因"失败修谁"，本篇测量"harness 能力差距在哪"
- [[SKIMIX-多智能体Harness时缩放与技能混合]]：SKIMIX 评估多智能体 harness 配置的价值，A²E 提供跨 harness 的标准化测量手段
- [[Harnesses-for-Inference-Time-Alignment-通俗解读]]：两篇都把 harness 当独立于模型的工程对象来研究与优化

## 一句话存档

> 没有万能的模型—harness 组合——先量清楚，再谈协同演化。
