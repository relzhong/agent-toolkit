# Agent Toolkit

可跨项目复用的 AI Agent 工作流与工程规则。从想法整理、方案设计到实现审查和交付归档，让不同项目使用一致的方法，同时保留各自的技术栈、业务约束和文档约定。

通过 [Rulesync](https://github.com/dyoshikawa/rulesync) 分发，当前配置覆盖 Codex CLI、Claude Code、Pi 和 OpenCode。

## 包含什么

**12 个 skills**：11 个工作流技能，以及供它们共享的交付协议。

| 用途 | Skills |
| --- | --- |
| 将想法、讨论或审查发现整理成计划 | `sketch-to-planning`、`conversation-to-planning`、`review-to-planning` |
| 保存决策与调查依据 | `evidence-pack` |
| 编写规格并拆分交付任务 | `to-prd`、`to-issues` |
| 实现、审查与修复 | `review-loop`、`parallel-review` |
| 使用 Paseo 编排工作 | `review-loop-paseo` |
| 记录实现知识并归档计划 | `document-architecture-decision`、`mark-planning-implemented` |
| 共享验收与证据规则 | `delivery-protocol` |

**5 条根级规则**：作为项目指令的一部分加载。

| 规则 | 作用 |
| --- | --- |
| `implementation-scope` | 保持实现范围，区分已批准要求与新增建议 |
| `tool-output` | 控制工具输出量，保留退出码和必要验证证据 |
| `task-artifacts` | 区分持久化文字记录与临时截图、日志等产物 |
| `engineering-workflow` | 定义各阶段应使用的技能及完成条件 |
| `rulesync-authoring` | 明确源文件、远程安装副本和生成文件的维护方式 |

## 快速开始

以下配置已使用 Rulesync 33.0.0 验证。安装本工具箱需要 Rulesync 和 Git；私有仓库访问使用现有 Git 凭据。

在项目的 `rulesync.jsonc` 中加入以下配置。已有配置时，合并 `sources`，保留需要的 targets 和其他功能，不重复添加上游来源。示例按 target 分配规则和技能；使用对象形式的 `targets` 时，删除顶层 `features`，将项目已用的其他功能保留在相应 target 中。

```jsonc
{
  "$schema": "https://github.com/dyoshikawa/rulesync/releases/latest/download/config-schema.json",
  "targets": {
    "codexcli": ["skills"],
    "claudecode": ["rules", "skills"],
    "pi": ["skills"],
    "opencode": ["rules", "skills"],
    "agentsmd": ["rules"]
  },
  "sources": [
    {
      "source": "https://github.com/relzhong/agent-toolkit.git",
      "transport": "git",
      "ref": "main",
      "path": ".rulesync/skills",
      "skills": ["*"],
      "rulesPath": ".rulesync/rules",
      "rules": [
        "implementation-scope",
        "tool-output",
        "task-artifacts",
        "engineering-workflow",
        "rulesync-authoring"
      ]
    },
    { "source": "mattpocock/skills@v1.2.3:skills/engineering" },
    { "source": "mattpocock/skills@v1.2.3:skills/productivity" }
  ]
}
```

```bash
rulesync install
rulesync generate
rulesync generate --check
```

`install` 下载到项目的 `.rulesync/{rules,skills}/.curated/`；`generate` 输出各工具使用的指令和技能。将 `rulesync.lock` 纳入版本管理，使团队和 CI 使用相同的来源提交。

配置中的 [Matt Pocock skills](https://github.com/mattpocock/skills) 提供 `to-spec`、`to-tickets`、`grill-with-docs` 等依赖。消费项目必须显式声明这些来源，安装工具箱不会自动安装其上游依赖。

## 如何使用

根据任务所处阶段选择技能，无需为每个小改动走完整流程。

```text
想法或已有讨论
  → sketch-to-planning / conversation-to-planning
  → grill-with-docs
  → evidence-pack
  → to-prd
  → to-issues
  → 手动执行任务 / review-loop
  → 最终验收
  → document-architecture-decision
  → mark-planning-implemented
```

- 已有架构审查发现时，从 `review-to-planning` 开始。
- 需要独立审查视角时，使用 `parallel-review`。
- 明确选择 Paseo 编排时，使用 `review-loop-paseo`。目标环境还需安装 `paseo` skill，连接可用工具，并配置对应 profiles；这些环境配置由使用者维护。
- `delivery-protocol` 提供共享参考，不会自行启动发布、实施或委派。

技能调用方式以所用 Agent 工具为准，例如让 Agent “使用 review-loop 实现并审查这项任务”。

实现循环按可验收功能连续推进：方向明确时先核对上下文再直接实现，跨模块任务尽早验证最薄的完整链路，完成后集中独立审查。普通相邻文件修改和已授权步骤不逐项回报等批准；真正的范围、权限或不确定副作用仍需停下处理。新工作单元使用新审查会话，Paseo 派发异常先对账再等待，避免把流程交接当成交付进展。

## 与项目约定配合

项目自己的 `overview.md` 可以继续使用 `root: true`，保存业务约束、技术栈和项目入口。工具箱的五条规则也都使用 `root: true`，正文会与项目规则共同合并到根级指令文件；不需要把共享正文手工复制进 overview。

项目级绑定保留在项目内：

| 文件 | 用途 |
| --- | --- |
| `docs/agents/issue-tracker.md` | 指定 tracker，以及规格、任务和证据的存放与发布方式 |
| `docs/agents/domain.md` | 指定领域文档布局 |
| `docs/agents/triage-labels.md` | 使用 triage 时的标签映射 |

已有约定直接沿用。新项目可使用上游 `setup-matt-pocock-skills` 建立配置；在 Rulesync 项目中，将它提出的根级指令修改写入 `.rulesync/rules/overview.md`，然后重新生成。

文档路径示例默认使用 `CONTEXT.md`、`docs/planning/`、`docs/adr/` 和 `docs/concepts/`。项目已有其他布局时，沿用既定路径。

只需要基础约束时，可在来源的 `rules` 列表中省略 `engineering-workflow`。按需选择 skills 时，须保留其依赖；增强交付流程需要 `delivery-protocol` 和 `task-artifacts` 规则。

## 更新与验证

```bash
# 使用锁定版本安装，适用于团队协作和 CI
rulesync install --frozen
rulesync generate --check

# 更新来源后重新生成；会重新解析配置中的所有来源
rulesync install --update
rulesync generate
rulesync generate --check
```

更新后检查 lockfile 和生成差异，再提交。项目可以版本化 curated 输入，也可以在使用前执行冻结安装；无论选择哪种方式，生成和运行技能前都需要完整的安装资源。

本地同名规则或 skill 会覆盖 curated 版本。从已有本地技能迁移时，先保存并比较本地修改，移出已迁走的同名源，再安装并检查清单。Rulesync 16.4.0 在覆盖存在时安装来源，可能只锁定未覆盖项；移除覆盖后如仍缺少技能，需要重新解析来源，不能仅凭“已是最新”的提示判断安装完整。

旧版 lock 缺少选择记录时，Rulesync 33 的普通 `install` 会在锁定提交重新获取来源并补充 `skillSelection`；检查并提交这些元数据即可。输入已完整安装时，`install --frozen` 可以复用旧 lock。上游声明的 `disable-model-invocation: true` 会生成 Codex 调用控制文件，这些技能保留显式调用方式。

## 多工具规则入口

示例让 `agentsmd` 保持在 targets 的最后，负责最终公共 `AGENTS.md` 和 `.agents/memories/` 规则索引。Codex CLI 和 Pi 读取公共入口，不再各自生成随后被覆盖的完整规则正文；它们的技能继续分别生成。Claude Code 保留自己的规则输出。OpenCode 仍生成原生 memories 和配置，其同名根文件由最后的 `agentsmd` 输出替换。执行完整的 `rulesync generate` 来刷新公共入口；单独生成其他 target 的 rules 可能改变最终根文件。

根文件保留共享规则和项目关键约束，详细规则通过索引读取。在项目自己的 overview 中明确要求：修改或审查文件前，读取所有匹配路径的规则正文；任务范围扩大时重新检查；没有路径过滤的规则使用明确的任务触发条件；生成副本缺失时回到 `.rulesync/rules/` 源文件，必要规则不可用时说明缺失并暂停受影响的工作。索引不是规则正文，也不代表每个工具都会自动按 glob 加载。

生成后确认共享正文、项目约束、索引目标和各工具技能均完整，并检查根文件大小。生成检查验证文件一致性，运行时是否遵循加载要求还需按实际工具验证。`root: true` 控制生成位置，不会赋予高于用户要求的指令权限。

## 仓库维护

```text
.rulesync/
├── rules/                  # 共享根级规则
└── skills/                 # 工作流技能
    └── delivery-protocol/
        └── references/     # 交付协议、产物格式与最终验收
```

共享变更在本仓库的 `.rulesync/` 源文件中维护，执行 `rulesync generate` 后检查输出。根目录的 `AGENTS.md`、`CLAUDE.md` 是生成文件；其他工具目录不纳入本仓库的版本管理。

发布前在临时消费项目中检查安装、根规则合并、技能资源、相对链接和本地覆盖行为。任务日志与临时产物放在系统临时目录。

上游技能继续由原仓库维护；本仓库负责共享工作流扩展、交付协议和工程规则。
