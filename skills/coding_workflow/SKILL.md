---
name: coding_workflow
version: v1.0
descriptionm: 确保 Codex 在执行功能迭代时，需要严格遵循 PROJECT.md + TODOS.md + TASK.md 的流程推进，避免偏航、遗漏验收、状态不一致。
---

# 0. 术语与真相源（Source of Truth）
- docs/PROJECT.md：全局工程约束与统一标准（长期稳定），是**工程规范真相源**。
- docs/TODOS.md：项目路线图与功能点（F-xxx）状态，是**功能完成状态真相源**。
- docs/tasks/TASK-F-xxx-*.md：单任务执行计划与步骤（Sx）状态，是**任务执行真相源**。

**一致性规则：**
- 只有当某功能点关联的所有 TASK 均完成且验收通过，才能在 TODOS.md 勾选该功能点完成。
- 任何勾选必须同时写更新日志（不得只打勾不写日志）。

---

# 1. 核心行为约束（Hard Rules / Gates）

## Gate A：范围控制（Scope Control）
- 必须只做“TASK”里列出的任务（最多 1 个任务或该任务的 1-3 个步骤）。
- 未在 TASK 的内容：不得实现、不得顺手优化、不得重构扩展。
- 如发现依赖缺失或阻塞：记录到 TASK.md 的“阻塞/风险”，并停止扩展。

## Gate B：先读后写（Read Before Write）
每次开始必须先读取以下信息并在输出中简要回显：
1) PROJECT.md 中与本次任务相关的规范（测试、代码风格、目录、禁止事项）
2) TODOS.md 中本次目标的功能点 F-xxx 以及依赖的历史功能点
3) 对应 TASK.md 中的当前状态、未完成步骤

## Gate C：必须验收（Verification Required）
- 每完成一个步骤（或一次增量），必须执行/给出可执行的验收命令与预期结果。
- 若无法执行命令（环境限制），必须给出“用户可复制运行”的命令清单与验证步骤，并标注为“未在本环境运行，仅给出可复现步骤”。

## Gate D：文档同步（Documentation Sync）
- 完成实现后必须更新：
  1) TASK.md：勾选完成的步骤 + 更新日志（含变更文件与验收结果）
  2) 若任务完成导致功能完成：更新 TODOS.md（勾选功能点 + 功能级更新日志）
- 未完成文档更新：视为任务未完成。

## Gate E：变更可追踪（Traceability）
- 每次输出必须包含：
  - 本次目标（T-xxx / Sx）
  - 变更文件列表（新增/修改）
  - 验收命令与结果
  - 文档更新位置（哪些文件哪些章节被更新）

---

# 2. 标准工作流（每次迭代必须按顺序执行）

## Step 1：定位任务
- 从 TODOS.md 中获取当前第一天未完成的功能点 F-xxx。
- 打开对应 docs/tasks/TASK-F-xxx-*.md，定位下一未完成步骤 Sx。

## Step 2：制定最小计划（Plan）
- 只计划本次要完成的 1-3 个步骤。
- 若需要新增小步骤（例如补充迁移/测试），必须写入 TASK.md 的实现路径里再执行。

## Step 3：实现（Implement）
- 严格遵循 PROJECT.md 的代码规范与目录约束。
- 禁止“顺手重构”“顺手加功能”“顺手优化性能”。

## Step 4：验收（Verify）
- 运行/给出验收命令：
  - 测试：`<test command>`
  - 静态检查：`<lint/typecheck command>`
  - 手动验证：`curl ...` / UI 操作步骤
- 若失败：记录失败原因与修复动作；不得将失败步骤勾选完成。

## Step 5：更新文档（Update Docs）
- 更新 TASK.md：
  - 勾选完成步骤
  - 写日志（日期 + 变更摘要 + 文件清单 + 验收结果）
- 若TASK-F-xxx下某一任务完成：将 TASK.md 对应任务状态置为 ✅ Done，并总结日志
- 若该功能点所有任务均完成：更新 TODOS.md 对应功能点为 ✅ Done，并追加功能级日志

## Step 6：输出总结（Report）
必须输出以下结构（固定格式）：
- 本次完成：T-xxx / Sx
- 变更文件：
  - Added: ...
  - Modified: ...
- 验收：
  - 命令：...
  - 结果：通过/失败（附关键输出）
- 文档更新：
  - 更新了：TASK.md 的哪些段落
  - 更新了：TODOS.md 的哪些段落（如有）
- 下一步建议（仅引用 TODOS.md 的 Next Actions）

---

# 3. 任务拆分与粒度标准（Task Sizing）
- 单个 TASK（T-xxx）应在 1-3 次迭代内完成。
- 超过该规模必须拆分为多个 TASK：
  - 例如：数据模型与 API、UI、测试与 CI 应拆为不同任务。
- 每个步骤（Sx）必须具备：
  - 输出物（artifact）
  - 验收方式（command/steps）
  - 可追踪的文件变更

---

# 4. 日志格式（Logging Standard）
所有日志必须遵循：
- 日期：YYYY-MM-DD
- 内容包含：做了什么、改了哪些文件、怎么验收、验收结果

## TASK.md 日志示例
```markdown
---
- 2025-10-20：完成此任务
- [ ] 代码实现完成，变更文件列表已记录（必须列出）：
  - Added:
    - 
  - Modified:
    - 
  - Deleted（如有）:
    - 

- [ ] 依赖变更（如有）已记录：
  - 新增依赖：
  - 移除依赖：
  - 变更原因（简述）：

- [ ] 配置/环境变量变更（如有）已记录：
  - 新增/修改 env：
  - 是否已同步更新 env.example（是/否）：
```

## TODOS.md 日志示例（功能级）
```markdown
---
- 2026-01-25：完成 Todo CRUD（创建/列表/更新/删除），通过 API 冒烟测试与集成测试。
change summary...
```
---

# 5. 异常与阻塞处理（Exception Handling）
当遇到以下情况必须停止扩展并记录：
- 需要产品决策（交互、字段含义、权限策略）
- 需要选择依赖/框架但未被 PROJECT.md 规定
- 发现超出范围的需求

记录位置：
- 写入对应 TASK.md 的 “阻塞/风险”；

---

# 6. 安全与合规（Baseline Safety）
- 不引入未知来源的二进制与脚本。
- 新增依赖必须说明理由与替代方案，并记录到 TASK.md 的日志中。
- 不输出或记录敏感信息（密钥、token）；只使用 env.example 占位符。

---

# 7. 开始执行前的检查清单（Preflight Checklist）
在第一次编码前，必须确保：
- [ ] docs/PROJECT.md 存在并包含：代码/测试/文档规范
- [ ] docs/TODOS.md 存在并包含相应任务点
- [ ] 对应 TASK.md 存在并包含：实现路径与验收标准
