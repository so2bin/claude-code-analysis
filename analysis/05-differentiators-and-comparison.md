# 第五章：程序架构及亮点

[返回总目录](../README.md)

## 1. 本章导读

这一章只讨论本项目自身的程序架构及其亮点，不再承担同类产品对比。对比内容已经移到：

- [`analysis/08-competitive-comparison.md`](./08-competitive-comparison.md)

## 2. 本项目的架构亮点

### 2.1 统一执行内核

这个项目最突出的亮点，是把多种运行模式统一在同一套执行内核之上：

- REPL
- headless / SDK
- subagent
- background agent
- bridge / remote

核心实现集中在：

- [`src/query.ts`](../src/query.ts)
- [`src/QueryEngine.ts`](../src/QueryEngine.ts)

很多同类产品也支持多种入口，但往往不是同一套内核复用得这么深。

### 2.2 Memory 不是黑盒

本项目把 memory 做成：

- markdown 文件
- 索引文件
- 可编辑目录
- 可检索、可同步、可 snapshot

相关实现：

- [`src/memdir`](../src/memdir)
- [`src/services/SessionMemory`](../src/services/SessionMemory)
- [`src/tools/AgentTool/agentMemory.ts`](../src/tools/AgentTool/agentMemory.ts)

这比隐藏式 profile 或纯服务端向量存储更透明、更可审计。

### 2.3 权限系统是主干，不是补丁

相关实现：

- [`src/Tool.ts`](../src/Tool.ts)
- [`src/utils/permissions/permissionSetup.ts`](../src/utils/permissions/permissionSetup.ts)
- [`src/interactiveHelpers.tsx`](../src/interactiveHelpers.tsx)

表现为：

- permission mode 是一等公民
- trust dialog 与工具权限分层
- auto mode 下会主动剥离危险 permission rules
- subagent 可以独立收窄权限

这说明安全边界从一开始就被当成系统设计的一部分。

### 2.4 多 Agent 协作成熟度高

相关实现：

- [`src/tools/AgentTool/runAgent.ts`](../src/tools/AgentTool/runAgent.ts)
- [`src/utils/swarm/spawnInProcess.ts`](../src/utils/swarm/spawnInProcess.ts)
- [`src/utils/swarm/backends/registry.ts`](../src/utils/swarm/backends/registry.ts)

亮点不在“能开子任务”，而在它支持：

- background agent
- in-process teammate
- tmux / iTerm2 pane teammate
- agent transcript
- agent memory
- team/swarm 模式

这已经接近完整的本地多 agent runtime。

### 2.5 长会话治理做成体系

相关实现：

- [`src/services/compact/compact.ts`](../src/services/compact/compact.ts)
- [`src/services/compact/sessionMemoryCompact.ts`](../src/services/compact/sessionMemoryCompact.ts)

项目不是简单在上下文过长时裁掉历史，而是：

- auto compact
- micro compact
- session memory compact
- post compact cleanup

这比很多产品只做“截断 + 简单摘要”更稳定。

## 3. 架构亮点的总体判断

我认为本项目真正有辨识度的，不是“功能多”，而是以下三点同时成立：

1. 统一的 query / agent / tool / permission 内核
2. 文件化、可审计、分层的 memory 系统
3. local-first，但能平滑扩展到 remote / bridge / swarm

很多产品能做到其中一两点，但很少三点同时成立。

## 4. 本章小结

如果只看本项目自身的程序架构，它最有特色的不是“命令多”或“工具多”，而是它把长期协作、权限治理、多 agent 运行时和 memory 体系放进了同一套本地 agent 平台内核。
