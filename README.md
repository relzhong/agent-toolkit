# Agent Toolkit

跨项目复用的 Agent 工作流、交付协议和 Rulesync 根级规则。源文件位于 `.rulesync/`；项目自己的业务规则、tracker 配置和实际交付文档留在项目内。

## 安装到项目

在项目现有 `rulesync.jsonc` 中合并以下 sources，保留已有 targets 和其他配置。不要重复添加已有上游源。

```jsonc
{
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

已用 Rulesync 16.4.0 验证。`install` 将远程内容放入项目的 `.rulesync/{rules,skills}/.curated/`；`generate` 生成各工具的指令和技能。提交消费项目的 `rulesync.lock`，使后续安装使用锁定的提交；需要升级时运行 `rulesync install --update`，该命令会重新解析配置中的所有来源。CI 使用 `rulesync install --frozen`。

工具箱来源使用 Git transport，通过已有 Git 凭据访问仓库，不依赖 GitHub Contents API 的逐文件下载配额。

本包没有传递安装机制：消费项目需要明确声明上游 sources。这里列出完整的 engineering/productivity 集合，以保留上游技能之间的依赖。Paseo 流程还需要目标环境已安装 `paseo` skill、可用的 Paseo 工具，以及工作流使用的 profiles；本仓库不分发用户的 daemon、凭据或 profile 配置。

## 内容

| 用途 | Skills |
| --- | --- |
| 从想法或调查形成计划 | `sketch-to-planning`、`conversation-to-planning`、`review-to-planning` |
| 独立审查 | `parallel-review` |
| 保存交付知识与归档 | `document-architecture-decision`、`mark-planning-implemented` |
| 从证据到交付 | `evidence-pack`、`to-prd`、`to-issues`、`review-loop` |
| Paseo 编排 | `review-loop-paseo` |
| 共享参考协议 | `delivery-protocol` |

`delivery-protocol/references/` 随 skill 一起分发，供各阶段按需读取；安装它不会自动启动发布、实施或委派。

五条共享规则均为 `root: true`，与项目自己的 root 规则共同合并到根级指令正文，不只是生成外部引用。保持共享规则名称唯一：本地同名规则或 skill 会覆盖 curated 版本。规则来源不决定指令优先级，`root: true` 也不授予高于用户要求的权限。检查最终生成结果，尤其是多个 targets 共用 `AGENTS.md` 时。

`engineering-workflow` 是本工具箱的工作流选择。只想使用基础约束的项目可以不选它；只安装个别 skills 时，应同时安装其实际依赖。增强交付技能需要 `delivery-protocol` 和 `task-artifacts` 规则；规划技能的后续步骤需要相应的 toolkit 或上游技能。

## 项目约定

项目保留自己的 `overview.md`（`root: true`），用于业务约束、项目入口和以下绑定：

- `docs/agents/issue-tracker.md`：tracker、spec/ticket/evidence 的位置及发布方式。
- `docs/agents/domain.md`：领域文档位置；默认示例使用 `CONTEXT.md`、`docs/planning/`、`docs/adr/`、`docs/concepts/`。
- `docs/agents/triage-labels.md`：采用 triage 时的标签映射。

已有配置直接沿用。新项目可以调用上游 `setup-matt-pocock-skills` 建立约定；在 Rulesync 项目中，将它提出的根级指令修改写入 `.rulesync/rules/overview.md`，然后生成，不直接修改生成文件。

从旧项目迁移时：

1. 先比较并保留本地修改；将已迁走的本地同名 skill 源目录移出 Rulesync 输入树，避免遮住 curated 版本。项目专属 skills 保留，安装完成前保留现有生成副本。
2. 添加来源并安装，确认 lockfile 和磁盘均包含 12 个共享 skills、五条规则和协议 references。
3. 将共享内容从本地 overview 移出，保留项目业务和 tracker/domain 绑定。
4. 旧 `docs/agents/delivery-*.md` 与 `final-acceptance-review.md` 保留为入口；保留原有章节锚点，指向共享协议对应章节，历史 tickets 无需重写。
5. 生成并检查根文件正文、技能依赖和历史链接。消费项目若将 curated 输入纳入 Git，应一并提交新的规则和技能副本以及 lockfile，使新 checkout 中的协议入口可用。

Rulesync 16.4.0 在本地同名覆盖存在时，可能只将未覆盖的技能记录到新来源的 lock 条目；移除覆盖后，普通安装仍可能报告已是最新。如果已经按这个顺序安装过，需要重新解析该来源并检查完整清单。`rulesync install --update` 可以重新解析，但会涉及所有来源；本次迁移只重建了 toolkit 的 lock 条目以保留上游锁定状态。

历史交付记录描述当时锁定的协议与实现状态，升级工具箱不应重写既有验收结论。

## 维护

共享变更在本仓库 `.rulesync/` 中修改；项目专属例外留在项目内。本仓库根级 `AGENTS.md`、`CLAUDE.md` 为生成输出，其他工具目录不纳入 Git。

```bash
rulesync install --frozen
rulesync generate
rulesync generate --check
```

发布前在临时消费仓库安装，验证多个 root 规则的合并、协议资源、相对链接以及本地同名覆盖行为。临时截图、日志和测试产物留在系统临时目录。

## 来源与边界

初始 11 个技能、交付协议和五条规则提取自 SCHH 的 `.rulesync/` 与 `docs/agents/`，基线提交 `59deee7c628daf5dd27e1fdffaf1521cc27fa1ce`。迁移保留验收标准、证据分层、人工与自动执行边界，并移除 SCHH 专属验证脚本路径。目录约定允许消费项目沿用现有位置。

Matt Pocock 原版技能继续从原仓库安装，不在此复制维护。医院集成、kiosk 主题、AppIcon、数据库诊断 wrapper、Windows VM 和项目技术栈规则不属于本包。
