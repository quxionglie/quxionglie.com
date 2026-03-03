---
title: 消失的程序员
date: 2026-02-28 22:28:10
tags:
categories: ai
comments: true
---

从去年开始，各种 AI 大模型得到了长足发展，由此导致 AI Code 发生了翻天覆地的变化。

AI 辅助编码带来开发效率的显著提升——即便不说翻十倍，至少也是 3 到 5 倍的提升。若工作量不变，效率提升就意味着不再需要那么多人，接下来便是裁员。

未来是机遇还是挑战？

<!-- more -->

# Vibe Coding 行业狂欢 与 职业危机

2025 年初 Vibe coding 概念正式提出。Vibe coding 是一种以 AI 辅助为核心的编程范式：程序员用自然语言描述要处理的问题，交给面向软件开发的大型语言模型；应用源代码由模型生成，程序员的工作从手写代码转变为指导 AI 生成代码、测试与优化代码。

除了在工作中使用外，我自己也用 Cursor，在 GitHub 上写了两个项目：gozero-ruoyi-vue-plus 和 wj-form ，用于验证 AI 辅助编程能力。

- [gozero-ruoyi-vue-plus](https://github.com/quxionglie/gozero-ruoyi-vue-plus)：用 go-zero 实现 [RuoYi-Vue-Plus](https://github.com/dromara/RuoYi-Vue-Plus)，未使用 GORM，直接基于 go-zero 自带能力。代码迁移基本都通过 Cursor AI 完成。
- [wj-form](https://github.com/quxionglie/wj-form)：动态问卷表单项目，主要用于测试 AI 编程工具与多语言实现能力。

只要用过 AI 辅助编程，你多半会认同 Redis 之父的说法：**AI 已经永久改变了编程。除非为了纯粹的乐趣，亲手写代码已经不再有意义**。

软件交付的设计原本是围绕人机协作的。传统开发过程包含需求分析、设计、编码、测试、上线等阶段，因而存在产品经理、架构师、程序员、测试员、运维等角色。

但 AI 出现后，随着效率提升，上述工作几乎一个人就能完成。如今一个人几天内上线一个 App 已不是梦。

未来 IT 行业这个「池塘」是会扩大并吸纳更多人，还是保持现状而人员紧缩？面对未来的不可预知，我深感焦虑。

# AI Code 工具
Claude Code、Antigravity、Cursor、OpenAI Codex 等都是常见的 AI 编程工具，大多能满足日常需求。

**Claude Code** 于 2025 年 5 月与 Claude 4 一同正式发布。截至 2026 年 1 月，Claude Code 与 Opus 4.5 搭配使用时，被广泛认为是最佳 AI 编码助手。

**Cursor** 曾被视为业界领先者，但因缺乏自研模型，市场份额正逐渐被蚕食。

# 编码程序员 → 聊天程序员 → Agent 程序员 → 失业程序员？

日前 Cursor CEO 提出，AI 软件开发的「第三时代」已经到来。

**AI 软件开发的三个时代：**
- **第一时代：Tab 自动补全**。我订阅过 GitHub Copilot Pro 两年，当时的体验大抵如此：通过注释生成代码，有用但有限。这一阶段里，开发者仍是写代码的主角。
- **第二时代：Coding Agent——「对话式编程」**。开发者通过同步的「提示—响应」循环指挥 Agent，当前正处于这一时代，开发者可以不写一行代码就完成开发。
- **第三时代：云端 Agents——「软件工厂」雏形**。特征是 Agent 能在更长时间尺度上、更独立地完成更大任务，所需人为干预更少。代码只是中间产物，真正的「生产单元」是由一支支 Agent 组成的团队。类似 OpenClaw，你只需在聊天框里说出想要的功能，远程软件工厂就能自动实现。

# 总结

AI 编程已成为现实，据我判断未来两年内也会继续长足发展。它既放大个体能力，也加剧社会变革。面对这股不可逆的浪潮，个人该如何重新定位、抓住机遇而非被淘汰，是每一位从业者(不限于 IT，因为 IT 的变革终将辐射到各行各业)都需要深思的问题。

# 参考资料
- [Background agents work across the entire SDLC.](https://background-agents.com/)
- [Cursor：AI 软件开发的第三个时代](https://cursor.com/cn/blog/third-era)
- [池建强：昔日王者 Cursor 慌了，宣布 AI 软件开发的第三个时代即将到来](https://note.mowen.cn/detail/xuv_IbtLXu7xtB-H__B8z)