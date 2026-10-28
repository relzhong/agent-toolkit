# Implementation Planning: Issue-owned Review Loop

> Status: Implementation complete — 源码与静态验证已完成；真实多级委派验证按用户要求延期。
> 用户已批准直接修改、commit 和 push；不更新消费项目。实施中用户要求由当前执行者自行完成、不再委派，已停止继续使用审查代理。
> 源码基线：`068fc209c6741932c9becea8173abf2bf02e84b1`。下文保留规划历史，当前结果及验证限制见文末 Delivery Record；因运行验证未完成，暂不归档为全部验收完成。
> 实现入口：[review-loop](../../.rulesync/skills/review-loop/SKILL.md)、[Paseo adapter](../../.rulesync/skills/review-loop-paseo/SKILL.md)、[parallel-review](../../.rulesync/skills/parallel-review/SKILL.md)、[当前职责说明](../../README.md)。

## Objective

精简实现/审查/修复流程，并明确三层边界：

- **`delivery-protocol`：通用交付契约。** 规定要求、证据、完成和整体验收，不规定由哪个 agent/profile 执行、谁派发或如何收通知。
- **`review-loop`：通用 subagent 执行模式。** 当前执行者负责实现、自测、调用独立 reviewer、处理 findings 和修复；执行者可以是当前会话，也可以是受委派的 subagent。不强制额外建立 Lead，不绑定 Paseo 或 named profiles。
- **`review-loop-paseo`：Paseo 编排适配。** 在通用闭环之上映射薄 Lead、Implementation、Basic Review、按需 Architecture Review，以及任务派发、通知和恢复。

减少角色交接和重复阅读，而不削弱验收、权限和真实验证。本次不开发调度器，不引入新的框架、模型、配置层或固定审批部门。不承诺仅凭文字精简就获得确定的 token/费用收益。

本次澄清 supersedes 初稿中“通用 review-loop 也采用固定 Lead 层级”和“将 Implementation/Lead 职责下沉到 delivery protocol”的提案；以下 Lead 专属行为只属于 Paseo 适配层。

## User Journeys

**通用模式：** 用户请求实现并审查一个已明确的任务；当前执行者完成实现和自测，调用干净上下文 subagent 审查，再处理反馈、验证并交付。无需额外创建 Lead；没有 parent spec 的直接任务不制造规格树。

**Paseo 模式：**

1. 用户授权一个明确的 issue 或一批既有 issues；Lead 沿用批准计划、依赖、权限和范围，不重新 grill。
2. Lead 选择依赖已满足的 issue，派发路径、必要约束指针、工作区/变更边界、交付授权。
3. Implementation 连续完成理解、实现、自测、范围内修复；在形成可审查状态后调用干净上下文 Reviewer。
4. Reviewer 独立检查需求、diff 和证据，返回 findings；Implementation 判断并修复成立的范围内问题，必要时请求定向复审。
5. 有具体技术难题时，Implementation 咨询只读 Architect；涉及需求、权限或不可安全继续的决策时，向 Lead 汇报，由其汇总需要人的决定。
6. Implementation 返回完成证据或真实阻塞；Lead 检查依赖释放条件，不重做技术审查。已获批的批次自动推进，无须用户逐项启动；整体完成后执行原有 whole-spec acceptance。

## Confirmed Requirements

- **C1：Paseo 的薄 Lead。** 负责派单、依赖、跨 issue 状态、阻塞汇总和最终整体验收，不逐文件/命令批准，也不重复技术 review；通用 review-loop 不要求这个独立角色。
- **C2：执行者负责工作单元内闭环。** 当前会话或受委派执行者承担实施、验证、review 调用、findings 处理和范围内修复。Paseo 中映射到 Implementation，不在每个内部阶段等待 Lead 放行。
- **C3：独立 review。** 在适当的可审查边界调用 clean-context reviewer 检查当前 issue，而不是固定逐步审查或复制作者全部历史。
- **C4：按需升级。** Architect 解决技术判断；人工决定需求、范围、权限和其他必须由用户决定的问题。二者不是每项必经阶段。
- **C5：保留已有投资与边界。** 沿用既有 PRD/issues、验收映射、证据、单写入 owner 和已授权操作；安全停止不自动产生新产品要求。
- **C6：先给修改计划。** 本轮仅规划。批准实施后包含计划内必要验证，不设置逐步骤人工批准；发布和消费项目同步不隐式授权，不干预现有 agents。
- **C7：协议与编排解耦。** `review-loop` 保持通用 subagent 模式；Lead/profile/通知等归 `review-loop-paseo`；delivery protocol 不绑定两者的角色分工。
- **C8：自动收尾默认并行审查，手动执行由人选择。** `review-loop` 执行最终整体验收时默认自动调用 `parallel-review`，Paseo 复用该策略；普通 issue 仍默认单 reviewer。人工逐项执行时，由用户判断是否手动调用 parallel-review。此策略只属于执行工作流，不写死到 `/to-issues` 生成的 final-review issue 或 delivery protocol。

## Architecture and Implementation Decisions

### D1：通用执行者闭环，Paseo 再映射层级

- Status: Proposed（实现 C1–C3、C7）
- `review-loop` 描述执行者和只读 reviewer 的协作，不设固定 Lead，不要求将当前执行者再次包装成一个 Implementation 子代理。
- 执行者在当前工作单元内有界调用一个独立 reviewer，必要时咨询只读技术顾问；反馈回到调用者。不会因此获得执行其他任务或递归建 agent 树的权限。
- Reviewer/顾问不写产品代码、不再委派、不扩大验收范围。共享 worktree 同时只有一个产品代码写入者。
- Paseo 将上述执行者映射到 Implementation。Lead 派发完整 issue，Implementation 自主运行通用闭环，向 Lead 返回完成结果或真正需要升级的阻塞。
- 不增加新的角色 skill、固定 Investigation 阶段或 profile 名称。

### D2：保留独立判断，但取消重复判读

- Status: Proposed（实现 C2–C3）
- Reviewer 输入是 issue、映射的父级要求/共享约束、正确的 issue diff base、相关验证证据；不继承实施聊天，不以作者 PASS 宣告代替检查。
- 一个 issue 的完整变更形成后做一次默认独立审查。低风险机械修复以有效验证闭环；实质风险或验收断言变化才做定向复审。
- Implementation 对 findings 进行有证据的接受、拒绝或升级；不能通过直接忽略已证实缺陷获得完成。
- 当前 issue 的 reviewer 可用于该 issue 的窄复审，不复用到无关 issue。模型/harness 的 fork、resume 与真正干净会话要区分。
- 通用模式由当前执行者依据验收条件完成交付；Paseo 中 Lead 额外检查完成回执及必要证据是否齐全，不再次逐行审 diff 或逐条重判所有 findings，发现缺项才要求补齐。

### D3：保留收敛和升级条件，不使用无限自动修复

- Status: Proposed（实现 C2、C4、C5）
- 首轮修复及必要的定向复审由 Implementation 自主完成，不新增审批。
- 继续修复必须有具体残留缺陷、可界定修复和可验证预期；重复无进展、成本/风险明显增长、存在重大分歧或需要改变批准设计时暂停受影响部分并升级。
- Architect 接收具体问题、已有证据和备选方案，只提供技术意见；它不能替用户授权新需求、生产操作或放宽验收。
- 范围内普通技术细节由 Implementation 自行解决；不要把咨询本身变成新的强制阶段。
- 通用模式沿用当前任务的授权和沟通路径请求人工决定；Paseo 中由 Lead 汇总，避免多个 agent 重复询问同一问题。安全失败不是自动扩展恢复能力的需求。

### D4：Paseo wrapper 只承担工具适配，不再重复业务流程

- Status: Proposed（实现 C1–C4）
- 共享执行闭环和 findings 处理归 `review-loop`；验收与证据的通用语义归 delivery protocol。后者不承载角色层级、委派权限、grounding 派发模式或 profile 路由。
- `review-loop-paseo` 保留 saved profiles、派发参数、通知、会话、异常恢复等 Paseo 专属规则；移除逐阶段回 Lead 授权和禁止 Implementation 调用 reviewer 的冲突约束。
- 沿用现有 `Implementation`、`Basic Review`、`Architecture Review` profile；Architect 是职责描述，不擅自重命名实际 profile。显式指定的 provider/model/effort 仍有约束力。
- 谁派发谁负责其直接子任务的结果对账：Lead 对 Implementation，Implementation 对自己的 Reviewer/Architect。使用现有记录，不新建状态数据库。
- 保留异步通知、处理不明确派发结果、避免重复执行和保留原写入者的要求。运行中操作不得因为丢通知或换会话而盲目重放。

### D5：连续交付不要求永远使用一个长会话

- Status: Proposed（实现 C1–C3、C5）
- 当前 issue 内允许正常使用原生 compaction；不新增 40%/80K 强制清空规则。
- 完整工作单元边界可更新会话；新任务传当前状态和指针，不搬运完整聊天历史。
- Paseo 的 Lead 只保留依赖、owner、完成/阻塞和证据索引。必要时在安全批次边界使用已有支持的 controller handoff；这不是通用 review-loop 的额外角色要求，也不宣称仅靠技能文字已实现自动会话轮换。
- 保持批次授权边界：单 issue 授权不能扩展为执行整个 backlog。无关任务可在既有并发授权下继续，本次不提高并发默认值。

### D6：完成、提交和整体验收明确分开

- Status: Proposed（实现 C1、C2、C5）
- Implementation 的完成回执包含 issue/assignment、状态、验证证据位置、有效的 review 结论及处置、交付状态、剩余阻塞；不复制全部过程。
- 只有 local criteria、必要检查和成立的 blockers 全部满足，才能报告 issue 完成。Reviewer 失败、无可用结果或必需环境缺失不算 PASS。
- 维持既有 commit 默认与用户覆盖；已授权的提交由 Implementation 在 issue 闭环内完成，运行必要 hooks，避免单独等待 Lead 再放行。未授权 push/deployment 不获自动权限。
- 通用契约只规定完成证据与依赖条件；Paseo 中由 Lead 在条件与要求的交付动作满足后释放依赖，不因 agent turn 结束或一次成功通知就释放。
- 最终整体验收检查当前所有父级 AC、共享约束及集成行为；由谁执行/组织由所选工作流决定。Paseo 中由 Lead 组织。Architecture Review 是按风险使用的专家，不是最终验收的同义词。
- **已确认的执行策略：** 自动 `review-loop` 到达 final-review/等价的整体验收阶段时，默认调用 `parallel-review`；`review-loop-paseo` 复用，不另设第二轮收尾。先复用或补齐必要的确定性检查，再围绕整个功能基线、最终集成状态和父级 AC 安排独立视角；普通交付 issue 不因此升级为多 reviewer。
- 手动执行时，最终验收要求不变，但是否调用 parallel-review 由用户决定，不自动启动多 agent。`to-issues` 仍只生成通用 final-review issue，协议只保留覆盖与证据门槛。
- 并行 findings 不能代替逐项 AC 与集成证据判定；需要架构视角时纳入相关审查，不固定在 parallel-review 后再叠加一次 Architecture Review。修复后只更新受影响验证和必要复审，不默认重跑整个并行组。显式用户覆盖和工具能力限制需如实处理，不暗中降低审查方式或声称完成。
- 集成发现的代码修复由已授权的写入者处理，遵守单写入者边界。手动执行 ticket 的授权范围不被自动循环规则改变。

### D7：精简以减少动作和重复为主，而不是追逐固定字数

- Status: Proposed（实现 C1–C5）
- 主入口只保留角色、正常闭环、何时升级和完成条件。
- 复用现有协议的范围、证据和验收定义，不在每个 wrapper 重述。
- `delivery-context.md` 和 `final-acceptance-review.md` 不改为 Implementation/Lead 责任手册。现有内容已出现 `Lead approval of grounding`、`return ... to the Lead`、`Lead ... routes fixes` 和 named review/profile 规则；只对这些耦合做最小去耦，改为通用授权/验收表述或指向所选执行工作流。不改变 AC、证据格式和完成门槛。
- Paseo 错误恢复等分支若确有必要，可按触发条件放到同 skill 的 reference；不预设必须拆新文件，也不让普通执行读全部异常指南。
- 保留硬权限、安全和交付要求。字节数/读取量只是静态指标，不能代替行为验证或实际费用测量。

### D8：精简 parallel-review，保持可复用的审查能力

- Status: Proposed（用户提出进一步优化，具体默认值待随计划批准）
- 定位为“选取审查视角 → 干净上下文审查 → 综合 findings”，而不是第二套执行/修复调度器。调用方可以是当前主会话、受委派执行者或 Paseo 的 Implementation，不绑定顶层 Lead。
- 复用到普通 issue 闭环时默认一名 reviewer；直接请求 parallel-review 或自动最终验收调用时建议默认两名，存在第三个实质独立风险时再加第三名。用户指定人数/视角优先，不把三人当质量保证，不为凑人数创造泛化的审查角度。此提案替代初稿“直接调用默认行为不变”的限制。
- 对代码/issue 审查，确保至少一个视角覆盖需求正确性与回归，其余覆盖实际独立风险；避免所有 reviewer 各自全量重复审查，也避免只看专项而遗漏需求。对计划或文档审查，按目标选择相应覆盖面，不强行要求代码 diff 或可执行测试。
- 输入包括明确目标/稳定版本、原始要求、相关约束与证据指针；干净上下文不继承作者的实施历史。Reviewers 始终只读，不因为收到 autofix 请求而切换成 writer，也不再委派。目标发生相关变化时，更新受影响结论。
- Findings 要有可定位证据、违反的要求/具体失效路径、影响与最小修复方向；不强制发现问题，不写泛泛最佳实践。调用方做去重、成立性与范围判断，不按票数判正确，不额外再做一遍全面 review。
- 保留复用有效验证、按需补查、避免多个 reviewer 重复运行完整测试的规则。失败后只补未覆盖的重要检查；失败、截断和缺失证据如实保留，不能当 clean。
- 保留显式 autofix 用法，但它只是修复授权入口：由已有获授权执行者处理已接受的范围内 findings；没有 writer 时才指定一个。内部不再创建另一轮 review-loop，不自动做无限复审，不询问已经授权的常规修复。单纯提到或引用单词 autofix 不构成授权。
- 未授权修复时只返回综合报告；需要修复授权或真正的需求/权限决定时才询问，不默认推动用户接受可选优化。

## Constraints and Invariants

- 从 `.rulesync/` 源文件修改，再生成配置的四个工具目标；不直接修 generated/curated 副本。
- 不升级 Matt 上游、不改 `rulesync.jsonc` 的来源与 targets，不新增依赖或平台服务。
- 不修改用户已确认的 PRD/issue 语义、AC IDs/revisions 和历史证据身份。
- 不将测试成功、commit、进程退出或 reviewer 自报完成直接等同真实验收。
- 保持当前命名 profile 选择与 unavailable-role 的显式处理，不暗中换模型或降审查级别。
- Worktree 不是权限沙盒；保留环境与现场操作边界。
- 原始验证日志/运行捕获只写任务专属 `/tmp`，持久材料保留简短命令、原始退出码、关键结果和限制。

## Data and Interface Changes

不改变产品代码/API 或 ticket schema。调整的是技能角色与派发契约。

| 源文件 | 计划修改 |
| --- | --- |
| `.rulesync/skills/review-loop/SKILL.md` | 通用执行者 + clean-context subagent reviewer 闭环；移除固定 Lead 和 Paseo 角色要求；最终整体验收默认自动 parallel-review，普通 issue 单 reviewer；保留范围、完成及升级条件 |
| `.rulesync/skills/review-loop-paseo/SKILL.md` | 适配两层任务归属和直接子任务通知；沿用 profiles；删除逐阶段 Lead 批准和重复共有流程 |
| `.rulesync/skills/parallel-review/SKILL.md` | 调用方中立；普通闭环一名 reviewer，直接并行建议默认两名并按风险扩展；稳定目标、证据化 findings、去重；autofix 复用已授权 writer，不建立嵌套循环 |
| `.rulesync/skills/delivery-protocol/references/delivery-context.md` | 不下沉新角色职责；仅移出现有 Lead/grounding 编排绑定；保持要求来源、稳定 AC、按需读取、证据和完成语义 |
| `.rulesync/skills/delivery-protocol/references/final-acceptance-review.md` | 不指定由 Lead 或 Implementation 验收；仅移出现有 Lead 路由和 profile 分工；保留全规格覆盖、证据有效性和手动/自动授权边界 |
| `.rulesync/rules/engineering-workflow.md` | 仅同步 Execution and Review 的职责描述，不展开改造其他规划/文档流程 |
| `README.md` | 说明新的职责图、调用方式、按需升级、能力限制与兼容性 |

其他 `.rulesync/` 文件只有在发现直接引用冲突时才作最小同步，并在实施结果中说明；不以此为由扩展为全工具箱重构。根级 `AGENTS.md`/`CLAUDE.md` 的变化来自生成，不手工编辑。

## Rejected Alternatives

- **R1：只删文字，保留所有逐阶段审批。** 不能减少真正的交接成本。
- **R2：取消 reviewer 或让作者自称通过即完成。** 丢失独立审查价值。
- **R3：每项固定再经过 Architect/Investigation。** 把按需帮助变成新的部门链。
- **R4：所有子代理自由递归委派。** 所有权、成本和通知路径失控。
- **R5：40%/80K 强制终止、禁用 compaction。** 不符合已确认上下文策略，也可能打断仍有效的诊断。
- **R6：为这次精简立即开发调度器、升级 Paseo 或改用其他平台。** 超出当前范围，先利用已有能力并诚实报告缺口。
- **R7：将 Paseo 的 Lead/profile 分工写入通用 review-loop 或 delivery protocol。** 让普通 subagent、手动执行和其他运行器被迫采用同一编排，违反层次边界。
- **R8：固定三名 reviewer 重复全量审查，再由调用方重审一次。** 不以人数或多数票代替证据，也不在 parallel-review 内嵌另一套修复编排。

## Open Questions

以下是实施时核验的工具能力，不需要重新讨论已确认的职责设计：

1. **Paseo 实际运行能力：** 已安装 Paseo skill 描述了 agent-scoped 子代理创建和回调语义，但指定 Implementation 的 provider、MCP 配置、子代理层级与通知是否支持 Implementation → Reviewer，需要在隔离任务中验证，不能由文档推断当前环境已可用。
2. **其他 harness 的委派限制：** 若当前执行者不能创建真正干净的 reviewer，默认报告明确能力缺口；由已有父调用方代派/结果转发可作为显式说明的兼容路径，不为此新建 Lead 角色。Paseo 的父调用方是 Lead。技术 findings 仍回当前执行者处理，不得悄悄变成作者自审或另选 provider；无法提供独立上下文时，该环节保持阻塞。
3. **长期 Lead 轮换：** 本次减少其上下文负担，沿用已有 handoff 能力；没有承诺新的自动跨会话调度实现。若运行环境不支持，记录限制，而不把要求退回用户逐 issue 手动启动。

## Vertical Slices

- [x] **S1：不依赖 Lead 的通用执行闭环与角色中立协议**
  - User outcome：当前会话或受委派执行者可以完成实现、独立审查和修复，不强制引入额外控制层。
  - Implementation scope：`review-loop`、`parallel-review`；delivery context/final acceptance 仅去耦现有编排措辞；工程规则/README 的必要对应部分。
  - Acceptance criteria：通用入口没有固定 Lead/profile/通知依赖；协议不指定 agent 所有权；有效 findings 有处置；范围外要求升级；整体与局部验收仍分开；parallel-review 可独立调用和被单 reviewer 闭环复用，不触发嵌套修复循环。
  - Verification：分别走查手动 ticket、当前会话实现 + 原生 subagent review、Paseo 消费相同协议三条路径；覆盖正常完成、一次修复、实质分歧、无效 findings、真实验证缺失、最终集成缺陷。另检查并行默认人数、用户指定三人、计划/文档目标、reviewer 失败、无 findings、显式 autofix、仅引用 autofix 和已授权修复的区别。静态情景走查不冒充运行实测。
  - Verification（收尾策略）：自动 review-loop 到 final-review 时触发并行组；普通 issue 不触发；人工执行 final-review 不自动派发；并行失败不误报验收通过；修复后不无条件重启全组；协议和生成的 final-review issue 不绑定执行方式。
  - Preserves：C1–C5、C7–C8，D1–D3、D5–D8。

- [ ] **S2：Paseo 按相同责任模型派发与返回**
  - Delivery note：适配源码及静态检查完成；真实 Implementation → Reviewer 通知/修复闭环未运行。用户要求不再使用子代理，保留此验证限制而非宣告运行时通过。
  - User outcome：issue 内 reviewer/architect 结果回到执行 owner，Lead 只消费 issue 级结果或升级。
  - Implementation scope：`review-loop-paseo` 及相关说明；不修改用户保存的 profiles、不升级 daemon。
  - Acceptance criteria：正常路径无逐阶段 Lead release；Reviewer 无作者历史；指定 profile 不漂移；失败通知不误判完成；未知派发状态不触发重复写入。
  - Verification：先核对可用工具与权限；获准后在临时小仓库运行一个可验收任务，覆盖 reviewer 提出一个真实缺陷并由原 writer 修复；明确记录不支持的层级/通知能力。
  - Preserves：C1–C5，D4；单写入者与副作用边界。

- [x] **S3：生成输出和消费兼容性正确**
  - User outcome：同一源修改在配置的工具目标一致，既有 PRD/issues 可继续使用。
  - Implementation scope：生成输出与 README；仅临时消费项目做 smoke check。
  - Acceptance criteria：`rulesync generate`、`rulesync generate --check`、引用检查与差异检查通过；无无关上游或 lock 更新；所有 task captures 在仓库外。
  - Verification：保留命令原始退出码；检查生成目标、相对链接、遗留职责冲突和最终改动范围。静态检查不冒充多 provider 行为实测。
  - Preserves：C5–C6，现有安装与授权边界。

## Migration and Rollout

1. 用户批准本计划后，才修改技能与规则源文件。
2. 先完成共享职责和 Paseo 适配，再生成输出并做静态检查。
3. 在隔离环境做小规模真实执行验证，不使用正在开发的部署任务作试验；需要当前工具/模型权限时明确说明。
4. 汇报实际行为、验证限制和剩余能力缺口；不承诺省费百分比。
5. Commit、push、消费项目 install/update 和对运行中 agents 的迁移不隐式包含在本次计划中，按后续授权执行。既有正在运行的任务不热切换职责。

## Documentation Impact

- 本计划是当前讨论的唯一规划记录，不新增 PRD/ticket tree。
- 更新 README 和直接相关职责说明；不重写全部规划技能、证据模板或文档沉淀工作流。
- 不为该提示职责调整强行建立新的领域 glossary 或 ADR；若实施暴露了实际平台限制，在本计划/README 的对应位置记录即可。

## Deferred Scope

- 自建持久 scheduler、自动 Lead 轮换服务、心跳与遥测平台。
- 全量 skill 精简、安装列表裁剪、Matt 上游升级。
- 收窄所有文档技能触发条件、重构 planning/evidence/PRD 生产链。
- 保存新 profiles、更换模型或调整 effort、提高并行 writer 数量。
- 在真实生产/现场环境验证自动重试或恢复。

## Conversation Coverage

- Requirements captured：8（C1–C8）。
- Decisions captured：8（D1–D8；D8 包括本轮新增的 parallel-review 优化建议，具体实现细节待批准）。
- Open questions captured：3（工具能力核验，不重启产品设计）。
- Rejected alternatives captured：8（R1–R8）。
- 关键保留：通用协议/通用 subagent 闭环/Paseo 适配三层解耦、一次授权连续推进、单 issue 完整技术责任、干净审查、按需 Architect/人工、既有验收与权限、无新的框架或固定 token 红线。

## Delivery Record — 2026-10-27

### Delivered scope

- 重写三个执行/审查技能：通用 executor 闭环、薄 Paseo Lead、调用方中立的并行审查。
- 普通 issue 默认一名 reviewer；直接并行及自动整体验收默认两名，额外风险/明确要求可扩展。手动最终验收不自动派发。
- 两个协议引用只移除现有角色/编排绑定；AC 语义、产物模板、to-prd/to-issues 与锁定来源保持不变。
- 工程规则和 README 同步；AGENTS.md/CLAUDE.md 由 Rulesync 生成。明确只读请求不获得修复权限，profile 旧工作流备注不能与本次授权的委派职责同时冲突传递。
- 实现知识保留在 README 的三层边界说明，不新增 ADR/规格/工单树或运行服务。

### Verification and provenance

基于上述基线到本记录所在提交的源文件变更，使用 Rulesync `40.2.0`。以下退出值保留原始结果：

| 检查 | 结果与边界 |
| --- | --- |
| `rulesync generate` | exit 0；四工具技能和公共根规则生成成功 |
| `rulesync generate --check` | exit 0；生成输出一致 |
| `git diff --check` | exit 0；无空白错误 |
| Python 源/生成资源核对 | exit 0；三个技能正文及两个协议引用与四个目标匹配，相关 Markdown 相对链接有效 |
| Python 文本契约检查 | exit 0；通用 loop 无 Lead，协议无 named role/profile，普通/最终/人工审查分支、只读请求、autofix 授权与无嵌套循环等关键条款存在；这不是模型行为实测 |
| 兼容性范围检查 | exit 0；rulesync.jsonc、rulesync.lock、to-prd、to-issues 和 delivery-artifacts 未变化 |
| 临时消费目录 `rulesync generate` / `--check` | 均 exit 0；复制当前已安装输入后生成，项目自有 root sentinel 被保留、四目标资源齐全；未执行网络 fresh install，不代表已验证远程拉取 |

自行走查了：普通任务无需协议树、当前执行者调用 reviewer、普通 issue 单审查、自动 final-review 并行、人工收尾不派发、显式只读与 autofix 区别、review-ready 不作为交付完成、未知派发不重复写入、可用报告在 transport failure 后保留、必需证据缺失不判 clean、架构咨询不替代用户授权。此为静态行为约定核对，不宣称模型已按这些分支实际运行。

三个技能 UTF-8 正文文件大小分别从 14946/11685/5664 bytes 减至 9509/8769/4730 bytes；字节变化不代表 token 或费用节约比例。

### Runtime limits and user-directed change

- 原计划准备了 `/tmp` 中的故障注入小仓库，初始三项 unittest exit 0；该结果只说明夹具准备成功，不验证新技能。
- 曾启动两个只读审查代理。用户随后要求自行负责、不要子代理：需求审查代理已取消；架构审查代理在取消前已结束并报告无 actionable findings（仅静态审查）。没有启动 Implementation 烟测代理，也没有继续委派或将子代理报告作为本轮自行核查的替代。
- 因此未验证实际的 Implementation → Reviewer 创建、干净上下文隔离、子级通知和修复回路，也未验证跨 provider 行为。这些能力缺口仍需使用环境验证；源码中保留明确的能力检查、受限代派和阻塞处理。
- 已读取现有 profiles，发现旧 Lead/Implementation 备注仍描述旧式角色约束；未修改保存的 profiles、daemon 或任何既有开发 agent。新适配要求在派发提示中按用户选定工作流明确协调，工具权限仍不可绕过。
- 用户明确授权本仓库 commit/push，覆盖规划阶段的发布待授权说明。消费项目同步由用户另行安排。

### Capture cleanup

原始日志、完整差异、临时 Python 核对脚本、审查报告、消费生成目录与烟测夹具全部位于 `/tmp/agent-toolkit-issue-owned-implementation/`，属于可能过期的临时材料。仓库内仅保留源码、生成根文件、README 和本维护性规划/交付摘要；不提交原始捕获，不添加 task-specific ignore，也不清理无关项目文件。
