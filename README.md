# dsh-codex-preset

> **Codex 模式** —— 把 DSH 会话调到接近 OpenAI Codex CLI 的工程手感。
> An OpenAI-Codex-style engineering workflow preset for [DeepSeek Harness](https://github.com/deepseek-ai/dsh).

不是换模型，是换一套**工作顺序**和**证据纪律**。

---

## 它解决什么问题

默认的 agent 很会说话，但你会遇到这些：

- 它说「已修复」，你没见它跑过任何命令
- 它一口气改 5 个文件，你 diff 都看不完
- 它把「我猜是这样」说得跟「我验证过」一样肯定
- 长会话跑到一半上下文爆了，前面的决定全忘

Codex 模式把这些变成**硬约束**，写进 persona：

| 阶段 | 要求 |
|---|---|
| **① EXPLORE** | 动手前先探明地形：`glob` 找文件、`grep` 找符号、只读不写 |
| **② PLAN** | 先列 `todo_write`；需求含糊就 `ask_user_question`，不许猜 |
| **③ IMPLEMENT** | 改前必读；小步走；不顺手重构无关代码 |
| **④ VERIFY** | **与「会聊天的模型」的分界线**：没跑过的结论不许说成事实，每个非零 exit code 都要处理 |
| **⑤ INTEGRATE** | 讲清改了什么/跑了什么/结果如何；落库走 git 工具 |

还有一条**证据纪律**：关于代码库的每个断言，都必须能追溯到「我真的读过」或「我真的跑过」。

---

## 安装

```sh
dsh plugin --profile web add github:qianai05233/dsh-codex-preset
```

重启 `dsh web` 后，在 **Agent 预设** 里选择「Codex 模式」。

### 前置要求

- DSH `>= 0.1.7-rc.1`
- 它声明的 17 个插件都来自 DSH 本体，无需额外安装

---

## 与内置 `standard` 预设的差异

**有意为之，不是遗漏：**

- **persona 写死五阶段工作流** —— 这是本 preset 的主要价值
- **保留 shell / jobs / subagent / workflow** —— 编码要能跑命令、看长任务、并行分工
- **挂 plan-mode** —— Codex 的「先探明再动手」在 DSH 里由 plan mode 强制落地
- **挂 compaction 剪枝** —— 长会话不爆上下文

---

## 声明了什么

`cordis.patch.yml` 里的这一行就是全部：

```yaml
- id: preset-codex
  name: '@deepseek-ai/dsh-agent-preset'
  config:
    id: codex
    name: Codex 模式
    order: 5
    plugins: [ ... 17 个 ... ]
```

> **背景**：DSH 早期用 `$DSH_HOME/.agent-presets/<id>/` 目录存预设，那个机制**已废弃** —— 官方 skill `editing-cordis-compositions` 原文：*"Nothing reads that directory any more."*
> 现役写法是由一个 bundle 的 patch 声明 `@deepseek-ai/dsh-agent-preset` 行。本仓库是这种写法的完整示例。

---

## 自定义

想改工作流？直接编辑 `cordis.patch.yml` 里的 `persona.prefix`。改完重装：

```sh
dsh plugin --profile web add github:qianai05233/dsh-codex-preset
```

---

## 配套

- **[dsh-codex-panel](https://github.com/qianai05233/dsh-codex-panel)** —— 悬浮控制面板，一键同意 git/GitHub 写操作
- **[dsh-freeapi-panel](https://github.com/qianai05233/dsh-freeapi-panel)** —— 29 家正规免费 LLM provider 聚合

## License

MIT
