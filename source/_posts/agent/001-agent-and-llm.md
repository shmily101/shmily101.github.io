---
title: 了解 Agent：图解 Agent & LLM
date: 2024-10-14 14:41:52
tags:
  - agent
  - llm
  - ai
categories:
  - 大模型基础
---

{% note info no-icon %}
本文最重要的总结：Agent 组成框架
{% asset_img agent1.png %}
{% endnote %}

## LLM（Large language model 大型语言模型）

### 传统 LLM

​	传统 LLM 采用的是 next-token 预测的方式，根据学习的语料库，预测当前上下文中最可能的 token 是什么。也许会有多个可能的 token，LLM 会选择可能性最大的那个 token。

{% asset_img agent2.png %}

### 增强型 LLM

​	单纯使用 next-token 预测的方式，会出现很多问题，比如对于数学计算，使用 token 预测就很有可能会出现错误幻觉。并且在对话中，如果不依赖对话系统将整个对话历史作为上下文传入模型，那么模型并不会记得对话的内容。

{% asset_img agent3.png %}

​	对此，我们可以将 LLM 结合一些外部工具，通过外部系统增强 LLM 的能力，

即 增强型大模型（Augmented LLM）。比如加入外部工具和记忆系统，当遇到数学问题的时候，就采用“计算器”这个工具去完成任务。同时记忆系统可以保持上下文记忆。

{% asset_img agent4.png %}

## Agent

### Agent的定义

> *An* **agent** *is anything that can be viewed as perceiving its environment through* **sensors** *and acting upon that environment through* **actuators**.
>
> **一个 agent 是任何可以被视为通过传感器感知环境，并通过执行器作用于该环境的实体。**
>
> *—— Russell & Norvig，《人工智能：一种现代方法》(2016)*

​	增强型 LLM 和 Agent 有些相似，但又不完全是 Agent。从 Agent 的定义可以看到，一个 Agent 必须要需要有**传感器、执行器**来和环境交互的。Agent 的组成可以概括如图：

{% asset_img agent5.png %}

- **环境** —  Agent 交互的世界
- **传感器** — 用于观察环境
- **执行器** — 用于与环境交互的工具
- **效应器** — 决定如何从观察转化为行动的"大脑"或规则

​	增强型 LLM 实际上是效应器的一部分。我们把效应器拆开来看：

{% asset_img agent6.png %}

​	使用"增强型"LLM，Agent 可以通过文本输入观察环境（因为LLM通常是文本模型），并通过使用工具（如网络搜索）执行特定操作。

​	为了选择采取哪些行动，LLM Agent 拥有一个至关重要的组件：**规划能力**。

​	而拥有规划能力，则意味着 LLM 需要能够通过**思维链**（CoT, Chain-of-Thought）等方法进行"推理"与"思考"。

{% asset_img agent7.png %}

​	我们可以把这种拥有推理、思考能力的 LLM 称之为**推理型 LLM**。

​	利用这种推理行为，LLM Agent 将规划出必要的行动步骤。

{% asset_img agent8.png %}

​	这种规划行为使 Agent 能够理解情况（LLM）、规划下一步（Planning）、采取行动（Tools）并跟踪已采取的行动（Memory）。

{% asset_img agent9.png %}

{% note success flat %}
- Chain-of-Thoughts：思维链，推理型 LLM，把问题规划成小步骤连续链式执行；

- Reflection：evaluation + feedback，将模型输出结果进行评估，判断是否采纳，如不采纳，模型进行自我调优；

- Subgoal decompositon：子任务分解。
{% endnote %}

​	这些用来增强 LLM 的外部系统，决定了 LLM Agent 的自主程度（autonomy）。外部系统步骤越灵活、越丰富，LLM 越能控制整个 Agent 系统的表现。

​	总结一下，在 LLM Model 固定的情况下（即标准模型固定，比如都使用 gpt4.1 等等），**能影响一个 Agent 表现的智能程度的就是“外部系统”——Planning、Tools、Memory**。如果我们想拥有一个好的 Agent，就要在这三个主要组件中下功夫。
