# review-code

一个面向 Codex 的证据驱动代码审查 Skill。用于审查 Git diff、提交、Pull Request、指定文件或整个仓库，优先发现能够被实际执行路径和具体影响证明的缺陷，而不是输出大量风格建议或推测性问题。

## 核心能力

- 审查正确性、边界条件和异常路径
- 识别权限绕过、注入和敏感数据泄露
- 检查数据丢失、部分写入、事务和迁移风险
- 分析竞态条件、锁、重试、取消和幂等性
- 检查 API、配置、Schema 和序列化格式的兼容性
- 发现资源泄漏和显著性能回退
- 判断测试是否真正覆盖了变更行为
- 控制误报，不把格式、个人偏好或无法验证的猜测当成缺陷

## 审查原则

每个 finding 必须尽可能建立以下证据链：

1. 明确的触发条件
2. 能到达问题的执行路径
3. 可观察的实际影响
4. 现有类型、校验、配置或测试为什么无法阻止问题
5. 问题是否由本次变更引入

证据不足时，Skill 会省略该 finding，或者将其列为尚未验证的残余风险，而不会把猜测包装成确定缺陷。

## 审查流程

使用 Skill 时，Codex 会按以下顺序工作：

1. 确定审查范围，例如未提交变更、提交区间、PR 或指定文件。
2. 读取适用的 `AGENTS.md`、贡献规范、架构说明和测试指令。
3. 理解变更意图，并追踪调用方、被调用方、数据流和错误路径。
4. 按正确性、安全、数据、并发、兼容性、性能和测试风险进行检查。
5. 通过代码上下文、历史记录、测试或安全的针对性探针验证问题。
6. 按严重程度输出精确、可执行的 findings。

仅要求“审查”时，Skill 不会修改代码。只有用户明确要求修复时，才会进入代码修改流程。

## 严重程度

| 等级 | 含义 |
| --- | --- |
| `P0` | 需要紧急处理的广泛灾难性问题，例如可靠的未授权远程代码执行、不可逆的大规模数据丢失或全面生产中断。 |
| `P1` | 很可能造成严重生产影响的问题，例如权限绕过、敏感数据泄露、重要数据损坏或关键功能失效。 |
| `P2` | 已确认但影响范围有限，或由较少见但现实条件触发的功能缺陷。 |
| `P3` | 影响较低但仍值得修复的真实问题，例如窄触发条件下的资源泄漏或误导性诊断。 |

严重程度以已经证明的影响为依据，不因假设性的部署场景而夸大。

## 输出示例

```markdown
### [P1] Validate refunds before mutating stored state

`src/orders/service.py:42`

An excessive refund updates the stored amount before validation raises an
error. Because the store retains the same object, a rejected request leaves
the order in an invalid state. Validate the complete operation before applying
the mutation.

Verification:

- `python -m pytest tests/orders` — passed
- A focused reproduction confirmed the failed request persists the mutation
```

如果没有发现能够确认的缺陷，审查结果会明确写出：

```text
No confirmed defects found.
```

这不代表代码被宣称为完全安全或绝对正确；未运行的检查和剩余风险仍会单独说明。

## 安装

将仓库克隆到 Codex Skills 目录：

```powershell
git clone https://github.com/Feitong-lag/code-review.git "$env:USERPROFILE\.codex\skills\review-code"
```

安装完成后，新建一个 Codex 任务即可使用。如果已存在同名目录，请先确认其中是否包含需要保留的个人修改，不要直接覆盖。

## 使用方式

审查当前未提交的代码：

```text
使用 $review-code 审查当前 Git diff，只报告本次变更引入且能够被证据确认的缺陷。
```

审查指定提交：

```text
使用 $review-code 审查 HEAD~1..HEAD，重点检查并发、数据一致性和兼容性。
```

审查指定文件：

```text
使用 $review-code 审查 src/api/orders.ts，只报告能够确认的功能、安全和兼容性缺陷。
```

审查并修复：

```text
使用 $review-code 审查当前变更，先报告 findings，再修复确认的问题并运行相关测试。
```

## 目录结构

```text
.
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── review-checklist.md
    └── severity.md
```

- `SKILL.md`：审查工作流、证据要求和输出规则
- `agents/openai.yaml`：Codex 中的显示名称、简短说明和默认提示词
- `references/review-checklist.md`：按风险领域组织的审查检查表
- `references/severity.md`：`P0` 至 `P3` 的严重程度标准

## 不作为 finding 的内容

除非用户明确要求，否则以下内容不会作为缺陷报告：

- 个人代码风格偏好
- 纯格式问题
- 没有实际失败路径的笼统可维护性意见
- 无法证明可达的假设性风险
- 与本次变更无关的既有缺陷
- 只说明“缺少测试”但没有关联具体行为风险的意见
- 已经能够被项目现有工具可靠发现的琐碎问题

## 许可

本仓库当前未声明开源许可证。在复用或分发前，请由仓库所有者补充适合的许可证文件。
