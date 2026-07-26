# 真实工程经验 → 沉淀规则

本文说明个人在工业 IoT、MCU 平台适配、SDK 优化和 AI Coding 实践中反复遇到的问题，如何沉淀为 [AGENTS.md](../AGENTS.md) 中可直接用于任务的工程规则。

这是一份经验索引，不是项目履历复述，也不是当前仓库已经完成构建或硬件验证的证据。内容只保留可公开、可复用的问题模式，不包含公司代码、客户信息、私有协议、商业数据或未经记录的效果数字。

## Quick Mapping

| 真实工程经验 | 反复出现的问题 | 沉淀规则 | `AGENTS.md` 对应位置 |
|---|---|---|---|
| 工业 IoT 边缘网关、DTU 和设备接入 | 多设备、多协议、网络断开、消息重试与恢复逻辑容易耦合 | Protocol/通信分层、显式 FSM、有界重试与 Recovery | [§5.3 Communication](../AGENTS.md#53-communication-and-streaming-protocols)、[§5.4 State Machines](../AGENTS.md#54-state-machines)、[§5.5 Fault Tolerance](../AGENTS.md#55-defensive-behavior-and-fault-tolerance) |
| MCU 平台适配（含 CAT1 水表） | 芯片、SDK、启动、时钟、引脚、内存和 BootLoader 条件依赖具体目标 | Hardware Truth、Resource Constraint、静态设计与初始化顺序 | [§2 Hardware Truth](../AGENTS.md#2-hardware-truth-boundary)、[§3 Resource Class](../AGENTS.md#3-resource-class-and-system-profile)、[§5.1 Memory](../AGENTS.md#51-memory)、[§5.6 Initialization](../AGENTS.md#56-initialization) |
| SDK 与开发效率优化 | 环境安装、工程创建、编译日志和 FOTA 步骤重复且容易失去证据 | Workflow、原生工具入口、Build Gate 与 Evidence | [§1 Preflight](../AGENTS.md#1-preflight)、[§7 Tool Execution](../AGENTS.md#7-tool-execution-rules)、[§8 Build Gate](../AGENTS.md#8-build-gate)、[§11 Report Template](../AGENTS.md#11-completion-report-template) |
| Codex / ChatGPT 辅助嵌入式开发 | AI 容易缺少工程、硬件和版本上下文，并把合理推断误当作运行结果 | Context、事实/假设/未知分离、Build 与 Hardware 分级验证 | [§0 Working Loop](../AGENTS.md#0-scope-and-working-loop)、[§6 AI Reasoning](../AGENTS.md#6-ai-reasoning-checklist)、[§8 Build Gate](../AGENTS.md#8-build-gate)、[§9 Hardware Validation](../AGENTS.md#9-hardware-validation-gate) |

## 1. 工业 IoT → Protocol / FSM / Recovery

工业设备接入中的主要难点不只是报文编解码。传输链路、协议状态、设备状态和业务投递可能分别失败。如果这些职责混在回调或条件分支中，断链、重连和故障定位会越来越困难。

因此形成以下规则：

- 将 Transport、Framing、Protocol、Service 和 Application 分层；
- 用显式状态、事件、超时和守卫条件描述连接与恢复；
- 对重试、退避、离线缓存和失败出口设置边界；
- 用状态迁移、错误来源和原始通信证据支持问题闭环。

## 2. MCU 平台适配 → Hardware Truth / Resource Constraint

CAT1 水表和 MCU 平台适配涉及具体芯片、SDK、启动文件、时钟、引脚、Flash/RAM、BootLoader 与升级边界。相似芯片的示例或模型记忆不能替代当前目标资料。

因此形成以下规则：

- 硬件事实必须来自精确器件资料、原理图、当前工程或测量；
- SDK 版本、链接布局和启动路径未确认时，不直接套用其他平台代码；
- 先根据真实资源和负载选择内存、执行模型和架构；
- 优先采用容量、所有权和失败行为明确的静态设计；
- Build 通过不能替代目标板启动、通信和升级验证。

## 3. SDK 优化 → Workflow / Build Gate / Evidence

SDK 优化实践中，真正消耗时间的环节包括重复配置环境、创建工程、定位首个编译错误、识别产物以及执行容易出错的 FOTA 步骤。

因此形成以下规则：

- 先记录工具链、SDK、构建目标和未修改基线；
- 优先使用工程原生的生成、构建、烧录和调试入口；
- 保存命令、版本、退出结果、警告和产物身份；
- 将 Build 与设备写入、启动和硬件行为分成不同门禁；
- 对烧录、FOTA、分区切换和其他设备变更要求目标确认、风险说明、回退路径与明确授权。

## 4. AI Coding → Context / Validation

实际使用中，ChatGPT 用于整理和打磨需求、逻辑、架构、异常路径与验收条件；Codex 用于读取真实工程、限定修改范围、执行构建和处理调试反馈。可靠性来自完整闭环，而不是一次生成。

因此形成以下规则：

- 在实现前提供 MCU、板卡、SDK、工程结构、构建入口和允许修改范围；
- 区分 Verified Fact、Assumption 与 Unknown，不用常见配置填补缺口；
- 先读工程和调用链，再生成或修改代码；
- 让编译器、链接器、串口、调试器和真实设备结果进入下一轮分析；
- 只将经过验证且仍适用于后续任务的内容沉淀回项目上下文。

更通用的 Agent 行为映射见 [mapping-to-practice.md](mapping-to-practice.md)，每类规则的形成过程见 [origin-of-rules.md](origin-of-rules.md)。
