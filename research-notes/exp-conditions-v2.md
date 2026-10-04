# 实验分组 v2：六条件 + 真 MCP

> 建立于 2026-09-22。起因：v1 的五条件里 `M` 被当成 MCP 组，但它既不是 MCP 协议，
> 也不是现实部署的 MCP——它是"假如工具表按状态动态裁剪"的假想上界。本文件定义
> 修正后的分组、要改的代码、以及跑数顺序。
>
> 本文件是计划。论文第 8 章 `sec:eval:method:conditions` 里的条件定义（H / J / J+）
> **已作废**，以本文件为准，第 8 章后续按本文件重写。

---

## 0. 唯一判据

> **每一对相邻条件之间，只能差一个属性。**

任何"更真实"但会让某一步同时动两个变量的设计都不做；任何不影响某一步单变量性的
成本都可以省。v1 的 `J2 → M` 违反了这条（同时去掉了前置条件散文、又加上了状态过
滤），v2 修掉。

---

## 1. 六个条件

| 代号 | 名称 | 模型看到的动作信息 | 选择方式 |
|---|---|---|---|
| **P** | perception | 无动作列表，给原始 HTML | 自己构造 HTTP 请求 |
| **J** | static catalogue | 全部 21 个操作：名 + 参数名 + 一行描述 | 文本列表，回 `{"index": n}` |
| **J+** | catalogue + preconditions | J + 前置条件散文（`CatalogueEntry.note`） | 同上 |
| **X** | MCP, static | 真 MCP `tools/list`，21 个 tool，JSON Schema，description 含前置条件 | native tool call |
| **X+** | MCP, dynamic | 同一个 MCP server，`tools/list` 按当前状态裁剪 | native tool call |
| **H** | hypermedia | 服务端渲染出的控件（`discover(html)`） | 文本列表，回 `{"index": n}` |

### 1.0 ⚠️ 阶梯是错的，两族才对（2026-09-22 由数据修正）

下面 §1.1 画的线性阶梯 `P → J → J+ → X → X+ → H` **把 P 放错了位置**。第一轮数据
出来后重读，正确的结构是两族：

```
读表示：   P（可以不听） ──────→ H（不能不听）
读契约：   J → J+ → X → X+
```

**P 和 H 读的是同一份字节。** 原始 HTML 里就包含服务端渲染的控件，所以 P 不是在
"推断"，是在读 hypermedia——只是没有解析器，也不受约束。证据：

- P 自己构造的请求 **86.8%（33/38）命中服务端当时渲染的合法动作集**
- G1/G2 两个门控陷阱 P 全部答对（`pay_deposit` 而非 `confirm_booking`，
  `request_cancellation` 而非 `cancel_booking`）——因为页面上只有那一个控件
- 它的 5 个 invalid 全是 off-catalogue，且全是同一类：**路径从页面读对了、方法猜错**
  （`POST /availability` 实为 GET；`GET /preorder` 实为 POST）

所以 `H vs P` 隔离的是**约束本身**，信息完全相同。这一格比 `X+ → H` 更直接地
证明主张，应当提到主结果位置。

**这不是对论文不利，这是论文的第一个发现**：表示本身就是充分且随状态正确的
动作空间。如果 P 表现差，"不需要额外契约"这半边主张才站不住。

### 1.1 四个属性与阶梯

|  | 动作集合被声明 | 按当前状态过滤 | 值绑定在控件里 | 真工具协议 |
|---|---|---|---|---|
| P | ✗ | — | — | ✗ |
| J | ✓ | ✗ | ✗ | ✗ |
| J+ | ✓ | ✗ | ✗ | ✗ |
| X | ✓ | ✗ | ✗ | ✓ |
| X+ | ✓ | ✓ | ✗ | ✓ |
| H | ✓ | ✓ | ✓ | ✗ |

每一步隔离的东西：

| 比较 | 隔离出的变量 | 回答的问题 |
|---|---|---|
| **P → J** | 动作空间被声明 vs 被推断 | 把整页 HTML 丢给模型让它自己想，比给它一张操作表差多少？ |
| **J → J+** | 前置条件是否写进文档 | 把"什么时候能用"写成散文，能不能补偿列表不会自己收窄？ |
| **J+ → X** | **只差协议** | 同样的信息，换成真 MCP 的 typed tool + native tool call，差距还在吗？ |
| **X → X+** | **只差状态过滤** | 让工具表随服务端状态收窄，非法动作是否消失？ |
| **X+ → H** | 值是否随控件一起送达 | 动作对了、值错了的那些失败，绑定值能不能消掉？ |
| **H vs P** | 声明 vs 推断（同一份字节） | 两者读同一份 HTML；H 只能用里面声明的控件，P 可以造任何请求。 |

`J+ → X` 是新增的关键一格。它先证明"差距不在协议上"，`X → X+` 才能被读作
"差距在状态依赖上"。没有这一格，"你的 MCP baseline 不是 MCP" 这个质疑无法回答。

### 1.2 对 v1 的三处改动及理由

1. **删掉 `M`（状态过滤的文本目录）。** X+ 用真协议做了同一件事。保留 M 只为凑
   "协议 × 信息" 的 2×2，而 `J+ → X` 已经单独给出了协议这一维。省下的是付费模型
   的一整格机时。

2. **X+ 保留 `note`（前置条件散文），X 也有。** v1 的 M 刻意丢掉了 note，理由是
   "一个会过滤的契约不需要前置条件散文"。这个理由成立，但它让 `J2 → M` 同时动了
   两个变量。v2 里 `X → X+` 是论文的核心一步，不能有第二个变量，所以两边都带
   note，差别严格只有过滤。

3. **`J1/J2` 改名 `J/J+`，`P` 命名确定为 perception。** 只是命名。

### 1.3 页面：所有条件一律给，不是操纵变量

`describe(html)` 在全部六个条件下逐字相同。P 额外拿到原始 HTML（这正是"把页面丢给
模型"的条件本身）。

对 X / X+ 这意味着它们比真实 MCP client 更强——真实的 MCP client 没有页面，状态
观测全部来自 tool result。**这是刻意的 steelman**：不给页面会把"动作集合是否封闭"
和"能不能看到状态"两件事混在一起，而论文主张的是前者。写进 threats to validity。

### 1.4 为什么 X 和 X+ 必须同时存在

MCP 规范里确实有 `notifications/tools/list_changed`：server 在
`capabilities.tools.listChanged` 中声明支持，客户端收到通知后重新调 `tools/list`。
所以"工具表随状态变化"在协议上是**被允许**的，X+ 不是假想协议。

但 listChanged 的设计意图是**会话级、粗粒度**的变更——用户授权了新账户、加载了
插件、配置变了——而不是"每一步按应用当前状态重算可用动作"。现实中的 MCP server
多数是对某个 API 的包装，没有单一的"当前状态"可供过滤；工具表在连接时组装一次，
客户端往往整个会话缓存它。

于是：

- **X = MCP 实际被部署成的样子**（静态工具表）
- **X+ = MCP 在规范允许范围内能做到的最好**（每步重算）

两格都要，理由有三层，第二层是决定性的：

**(1) 只留一个，两边都会挨打。**
只跑 X → "MCP 有 listChanged，你无视了，稻草人。"
只跑 X+ → "没人这么部署 MCP，你给 baseline 加了它现实中没有的能力，结论描述的
不是现实。"两个都跑，这两句话同时失效。

**(2) `X → X+` 就是论文要测的那个量本身。**
论文主张"**动作集合随状态变化**是关键"。`X → X+` 是这个变量在真协议里被单独隔离
出来的唯一一格。删掉 X，就只剩 `J+ → X+`，而那一步同时动了协议和过滤两个变量
——核心命题将没有对应的测量。这不是省一格机时，是拆承重墙。

**(3) `X+ → H` 的残差才是真正的贡献。**
若 X+ ≈ H，诚实的结论是"MCP 用上 listChanged 就够了，hypermedia 没有额外价值"
——这个结果必须允许被测出来。若 X+ < H，差距就精确落在"值随控件一起送达"与
"与人类界面同一份产物"上，而不是落在"MCP 没做状态过滤"这种可以靠工程修掉的事
情上。

这也给第 2 章那句 *separateness is exactly what the property excludes* 提供了实证
形态：X+ 是 out-of-band 契约的上限，它仍然要在第二条通道里**重建**一遍状态相关的
动作集合；H 不重建——它**就是**那个集合。

---

## 2. X / X+ 的架构

```
  b1/server.ts  (HTTP, :3000)            ← 已有，不改
        ▲                    ▲
        │ HTTP 执行          │ GET /__session（X+ 用来裁剪工具表）
        │                    │
  b1/mcp.ts  (MCP server, stdio)         ← 新建，属于「站点侧」
        ▲
        │ JSON-RPC / stdio：tools/list、tools/call
        │
  exp/mcp-client.ts                      ← 新建，属于「harness 侧」
        │
        ├─→ surface.ts：tools/list → Affordance[]（供 judge / provenance / random）
        └─→ choose.ts ：tool schema → chat completion 的 tools 参数
```

### 2.1 谁允许知道什么

| 模块 | 可以 | 绝不可以 |
|---|---|---|
| `b1/mcp.ts` | 读 `GET /__session`；import `b1/catalogue.ts` | — |
| `exp/mcp-client.ts` | 调 `tools/list` / `tools/call` | — |
| `a/`（agent + chooser） | 只看 tool schema | import 任何 `b1/*`；碰 `/__session` |

`b1/mcp.ts` 读 `/__session` **不是作弊**，性质与 `render.ts` 调用
`validAffordances()` 完全相同：服务端知道自己的状态是前提，不是结论。要守住的是
**agent 侧**不知道——现有的 A4 静态检查（"`a/` 目录下不出现 b1 字样"）已经覆盖，
再加一条断言 `a/` 与 `exp/mcp-client.ts` 都不出现 `__session`。

### 2.2 tool 定义从哪来

从 `b1/catalogue.ts` 生成，不手写：

```
CatalogueEntry              →  MCP Tool
  id            "hold_slot" →    name        "hold_slot"
  description + note        →    description
  params ["slot"]           →    inputSchema {type:"object",
                                   properties:{slot:{type:"string"}},
                                   required:["slot"]}
```

同一个源头保证 J+ 与 X 的信息量严格相同——这是 `J+ → X` 只差协议的实现保障，
不是口头承诺。

### 2.3 X 与 X+ 的差别

一个 server，一个开关（env `B1_MCP_DYNAMIC=1` 或 CLI flag）：

- **X**：`tools/list` 返回 CATALOGUE 全部 21 条。
- **X+**：`tools/list` 先 `GET /__session`，只返回 `valid` 里那几条。每步重新调
  `tools/list`（真实 MCP client 收到 `listChanged` 通知后就是这么做的；我们主动
  每步拉一次，等价且更简单）。

跑 X 和 X+ 时各启一个 server 进程，整轮复用，不是每 episode 重启。

### 2.4 执行走 HTTP，不走 tools/call —— 以及为什么

**六个条件全部经由 `episode.ts` 的同一个 `exec()` 发 HTTP。** X/X+ 的 tool call
被解析成 `(method, url, values)` 之后，和其他条件走同一条执行路径。

理由是公平性：judge、provenance、状态码、history 行的格式必须逐字相同，否则
`X+ → H` 的差异里会混进"错误信息长得不一样"。

但 `tools/call` **必须实现且必须被测**，否则"这是一个真的 MCP server"就站不住。
新增检查 **M1**：对抽样状态，同一个操作分别经 `tools/call` 和直接 HTTP 执行，断言
产生相同的状态转移与状态码。论文里据此写："server 是完整的 MCP server；为可比性
执行被统一路由，等价性由 M1 保证。"

### 2.5 random backend 在 X/X+ 下怎么办

`tools/list` 的结果**同时**转成 `Affordance[]`。真实模型走 native tool call；
random backend 走 index 选择（它不调用模型，对它而言没有协议可言，拿到的是同一个
集合）。

这让 `random × X+` 这一格可用——它是 Q3（约束 vs 能力）最干净的一块证据：一个零
理解能力的 backend 在 X+ 下能达到什么水平，完全归功于约束。

---

## 3. 代码改动清单 —— ✅ 全部完成（2026-09-22）

### 3.1 新建

| 文件 | 内容 | |
|---|---|---|
| `b1/mcp.ts` | MCP server（stdio），tools/list + tools/call，从 CATALOGUE 生成 schema，`B1_MCP_DYNAMIC` 开关，dynamic 模式真发 `sendToolListChanged()` | ✅ |
| `exp/mcp-client.ts` | MCP client 封装：`McpSession.start/surface/call/close`，tools/list 同时给出 `tools[]` 与 `affordances[]` | ✅ |
| `exp/check-mcp.ts` | M1 / M1b / M2 / M3 | ✅ |

依赖：`@modelcontextprotocol/sdk@1.30.0`。

### 3.2 修改

| 文件 | 改动 | |
|---|---|---|
| `exp/surface.ts` | `Condition = "P"\|"J"\|"J+"\|"X"\|"X+"\|"H"`；删 `filteredSurface`；新增 `TOOLCALL` / `SERVER_FILTERED`；`surfaceFor` 换成 `Surfacer` 类（持有 MCP 会话，按需惰性启动） | ✅ |
| `a/choose.ts` | 新增 `callTool()`：传 `tools[]`、读 `tool_calls`；`TOOL_PROMPT` 与 `SYSTEM_PROMPT` 同构，停止约定为裸 `STOP`；`ToolDef` 结构化声明使 `a/` 不依赖 MCP SDK；`callModel` 返回 usage | ✅ |
| `exp/episode.ts` | 三路分派；P 的 fields 合成修复；`no_tool_call` 停止原因；StepRecord 加 `toolName` / `tokens` | ✅ |
| `exp/judge.ts` | **无需改动** —— MCP 来的 affordance 携带 catalogue 的 `(method,url)`，`actionIdOf` 直接解析；幻觉 tool 被造成 `/toolname` 打不中路由，自然落到 `null` → off-catalogue | ✅ |
| `exp/report.ts` | 条件顺序随 `CONDITIONS`；结构保证脚注改为"H 与 X+"；新增 token 段与 NTC 列；off-catalogue 图例改写 | ✅ |
| `exp/run.ts` | 整轮开一个 `Surfacer` 并在结束时关闭；random 的 skip 仍只含 P（X/X+ 通过 index 回退保留） | ✅ |
| `exp/figures.ts` | 中间面板从 `filteredSurface` 改为**真实 X+** 的 `tools/list` | ✅ |
| `a/check.ts` | 新增 **A5**：`a/` 与 `exp/mcp-client.ts` 都不得读 `/__session`（先剥注释；排除 checker 自身） | ✅ |

### 3.3 回归与负控制

全绿：`typecheck` / `b1:check` 52748 / `a:check` 6747 / `check-judges` 1239 格
100% / `check-mcp` 1966 / `elicitation` 162 字段一致。

三个负控制都抓到了（否则 100% 通过没有意义）：

| 注入的错误 | 应当触发 | 实际 |
|---|---|---|
| `toolFor` 丢掉 `note` | M3 | 42 条失败（21 工具 × 两个方向） |
| X+ 的 `tools/list` 漏放 `confirm_booking` | M2 | 7 条失败（8 个状态里 7 个它非法） |
| `mcp-client.ts` 里 fetch `/__session` | A5 | 1 条失败 |

### 3.4 端到端已验证

- `random` × 5 条件（跳过 P）：X+/H 的 invalid 结构性为 0，J/J+/X 在 85–90%。
- `qwen3.5:9b` × X/X+ 真 tool calling：`toolName`、`proposedId`、`tokens`、
  `provenance` 全部正确落盘。
- **X 的 prompt token 是 X+ 的约 3 倍**（1819 vs 590，单步），纯粹来自工具表大小。
  这是 §5 成本模型的第一个真实数据点，也是可直接报告的结果。
- `figures.ts` 三面板：`browsing` 下 H 提供 5 个控件问 0 个问题，X+ 提供 2 个操作
  却要问 3 个 —— 值绑定的论证在一张图里。

### 3.3 P 的取值不公平 —— 必修

`fill()` 对缺失的必填字段用 `SAMPLE_VALUES` 兜底，但构造模式下 `chosen.fields = []`，
所以 **P 拿不到任何兜底**。现有的 3 条 P episode 里已经中招：

```
T1-01  POST /deposit  valid=True  http=400
       POST /deposit  valid=True  http=400     ← card 没给
```

同一情形在 H 下是：`card` 是声明过的必填字段 → harness 填上 → 记为 `asked-open`
（elicitation demand，算**界面成本**）。在 P 下变成 400 → 记为 wrong-value
（算**模型失败**）。同一件事记到两个不同指标上。

**修法**：构造出的请求若命中目录路由，就用该 `CatalogueEntry.params` 合成
`fields`，让 fill / provenance / 兜底走与其他条件完全相同的路径。命不中路由的仍然
`fields = []`（本来就无从得知参数，off-catalogue 的代价该由 P 承担）。

### 3.5 历史数据 —— 已全部删除

不归档，直接删（2026-09-22，用户决定）。删掉的是 9 个文件：五条件那批被中断的
85 个 episode、三条件时代的 5 个 jsonl、以及 `report.txt` / `report-latex.txt`。

理由是条件定义已经换了一代：`M` 不复存在、`J1/J2` 改名、`X/X+` 是新的，留着旧
日志只会让 `exp/report.ts`（默认扫 `logs/` 下全部 jsonl，且会把不认识的条件名静默
追加到表尾）把两代数据混成一张表。**`exp/logs/` 现在是空的，§5 的采集从零开始。**

`exp/figures/` 的 html 不是实验数据而是状态快照，保留并已用新的 X+ 面板重建。

---

## 4. 新增与保留的检查

| ID | 断言 | 状态 |
|---|---|---|
| I1–I6 | 站点不变量 | 已有，全绿 |
| A1–A4 | agent 发现 | 已有，A4 需扩充 |
| 判据一致性 | 声明式 vs 行为式两个 judge 同意（`check-judges.ts`） | 已有 |
| **M1** | `tools/call` 与直接 HTTP 产生相同转移与状态码 | 新增 |
| **M2** | X 的 `tools/list` 集合 == CATALOGUE 全集；X+ 的 == `validAffordances(s)` 的 id 集合，对抽样状态双向包含 | 新增 |
| **M3** | X 的 tool description 集合 == J+ 的 label 集合（信息量对齐的可执行形式） | 新增 |

M3 是 `J+ → X` 单变量性的可执行证明，值得单独列一行——论文里那句"唯一的差别是
协议"由它兑现，而不是由我们的措辞兑现。

---

## 5. 跑数顺序与成本

付费模型是花费的主要来源，所以先用本地模型把 token 分布摸出来，再外推。

| 步 | 内容 | 目的 |
|---|---|---|
| 1 | `typecheck` + `b1:check` + `a:check` + `check-judges` + `check-mcp` 全绿 | 地基 |
| 2 | `random`，5 条件（跳过 P），1 rep | 基线；`random × X+` 是 Q3 的关键格 |
| 3 | `qwen3.5:9b @t0`，6 条件，**1 rep** | 主结果。t0 是贪心解码，多 rep 只会复制同一条轨迹（`run.ts` 自己有 warning），v1 的 t0 那批 44 个 episode 实际只有 1 rep 的信息量 |
| 4 | `qwen3.5:9b @t0.7`，6 条件，3 rep | 采样方差 |
| 5 | 读第 3 步的 token 统计 → 估算付费模型成本 | **花费闸门** |
| 6 | 付费模型，**分层跑**（见 5.1） | 能力维度 |

单轮规模：6 条件 × 22 任务 = **132 episodes**。步数分布极不均匀——按 v1 数据，
过滤过的条件（H、旧 M）约 2 步/episode，没过滤的（J、J+）经常打满 20 步上限，
P 介于中间。所以 **X 恰恰是贵的那一格，X+ 和 H 一样便宜**，而 §1.4(2) 说明了 X
不能删。token 成本大头在 J / J+ / X / P，这本身就是可报告的结果（与 WebMall 那篇
的 token 对比正好并排读）。

**因此 `tokens` 字段要在第 3 步之前就加上**，否则第 5 步没有依据。

### 5.1 付费模型的分层跑法

该省的不是条件格，是**在哪个模型上跑哪些格**：

| 模型 | 条件 | 规模 | 用途 |
|---|---|---|---|
| qwen3.5:9b（免费） | 全 6 格 | 132 ep | 确立 `J+ ≈ X`（协议不重要）与 `X → X+`（过滤重要） |
| 付费模型 | **P / J+ / X+ / H** 四格 | 88 ep | 核心阶梯 |
| 付费模型 | `J+` vs `X`，**只跑 T1 的 10 条** | 20 ep | 抽查协议不变性 |

省掉约三分之一的付费 episode，论断一条不少。

那 20 条抽查不能省：协议不变性可能是**模型相关**的——前沿模型在 native tool call
上被大量对齐过，可能天然比读文本列表强，`J+ ≈ X` 在 qwen 上成立不代表在它上面成立。
只在免费模型上测一次然后外推是个真实的漏洞，抽查 T1 基本补上。

→ `run.ts` 需要支持 `--tasks T1 --conditions J+,X` 这种组合（现有的 `--tasks`
前缀过滤已经够用，`--conditions` 也已存在，确认 `J+` 带加号在 CLI 分词下无碍）。

---

## 5.2 实施验证（2026-09-22，已完成）

### qwen3.5:9b 的 native tool calling —— 可用，六项全过

经 Ollama（v0.30.11）OpenAI 兼容端点 `/v1/chat/completions`：

| 探测 | 结果 |
|---|---|
| `tools` 参数 + `tool_calls` 响应 | ✅ `finish_reason=tool_calls`，带 `id` |
| G1 陷阱（大桌下说"确认订位"） | ✅ 选 `pay_deposit` 而非 `confirm_booking` |
| 过滤后的 2 工具表 | ✅ 同样正确 |
| STOP 约定（三种情形） | ✅ 3/3，`finish_reason=stop`，content 恰为 `"STOP"` |
| 多轮 `role:"tool"` + `tool_call_id` 回灌 | ✅ 第二步正确调 `hold_slot slot=19:00` |
| `usage` token 计数 | ✅ 有，成本闸门可用 |

→ §6.1 的风险解除，`J+ → X` 不必降级措辞。

**第一发就撞出了论文预测的失败模式**：`pay_deposit args={"card":"+358 40 123 4567"}`
—— 模型把**电话号码填进了 card 字段**。动作对、值错，正是 `X+ → H` 要测的东西。
H 下 `card` 是控件声明的必填字段，记为 `asked-open`（界面该问用户的），而不是坏值。
`wrongValue` 那一列不会是空的。

> ⚠️ 探测里 X（静态 21 工具）也选对了 `pay_deposit`，但那是因为我手写的页面文案
> 直白写了"需要 80 欧定金"。**真实 `describe(html)` 在 `deposit_pending` 下到底有
> 多直白，决定了 X 会不会太强**。跑主轮前要先看一眼实际渲染文字。

### MCP server —— 已实现并端到端验证

`@modelcontextprotocol/sdk@1.30.0`。新增两个文件，`typecheck` / `b1:check`
(52748) / `a:check` (6733) 全部照过。

| 文件 | 内容 |
|---|---|
| `b1/mcp.ts` | stdio MCP server，21 个 tool 从 `catalogue.ts` 生成，`B1_MCP_DYNAMIC` 开关 |
| `exp/mcp-client.ts` | harness 侧 client：`McpSession.start/surface/call/close`，`tools/list` 同时给出 `tools[]` 与 `affordances[]` |
| `exp/mcp-smoke.ts` | 临时冒烟脚本，`check-mcp.ts` 落地后删除 |

**验证结果**：

```
STATIC  tools/list → 21 tools
DYNAMIC browsing          → check_availability                                  MATCH ✓
DYNAMIC deposit_pending   → discard_draft, edit_details, pay_deposit            MATCH ✓
DYNAMIC confirmed (near)  → request_cancellation, view_confirmation             MATCH ✓
CALL check_availability  browsing        → browsing          now: check_availability, hold_slot
CALL hold_slot           browsing        → holding           now: enter_details, change_slot, release_hold
CALL enter_details       holding         → details_entered   now: confirm_booking, edit_details, discard_draft, open_preorder
CALL confirm_booking     details_entered → confirmed         now: view_confirmation, cancel_booking
CALL confirm_booking (此处非法)    → isError=true  HTTP 409
CALL obliterate_restaurant (不存在) → isError=true  "No such tool"
```

即 **X+ 的 `tools/list` 与 `validAffordances(s)` 在测试状态上逐一相等**（M2 的雏形），
且工具表随每次调用实时收窄。

### 三个落地时的决定

1. **用低层 `Server` 而非 `McpServer`。** SDK 把 `Server` 标了 deprecated，但原文是
   "only use `Server` for advanced use cases" —— 我们正是：(a) 工具表必须在
   `tools/list` **到达时**从另一个进程的状态现算，`registerTool` 在注册时就定死、
   只有 `enable()/disable()` 且无法按请求驱动；(b) schema 必须是从 `catalogue.ts`
   生成的原始 JSON Schema，`registerTool` 收 zod 再转换，会让 MCP 条件携带与目录
   条件不同形状的 schema，悄悄破坏 `J+ → X`。`Server` 就是协议实现本身，
   `McpServer` 是它的糖，合规性没有损失。

2. **`listChanged` 只在 dynamic 模式声明，并且真的发。** 声明了却从不发送，等于往
   有利于 baseline 的方向虚报。X+ 在每次成功调用后 `sendToolListChanged()`；我们
   自己的 client 无论如何每步重新 list，但"声明了就要做到"是服务端的事。

3. **X 与 X+ 的 tool description 都带 `note`。** 与旧 M 的取舍相反，理由见 §1.2(2)。

### 踩坑

`import.meta.url === \`file://${process.argv[1]}\`` 这种手拼前缀在 Windows 上**永远
不成立** —— argv[1] 是 `D:\...`，而 `import.meta.url` 是 `file:///D:/...`（三斜杠）。
结果是 stdio transport 从不启动，子进程静默退出，客户端只看到
`MCP error -32000: Connection closed`。必须用 `pathToFileURL(process.argv[1]).href`。

---

## 5.3 第一轮结果（2026-09-22）

qwen3.5:9b@t0，24 任务 × 6 条件 × 1 rep。数据在 `exp/logs/`，commit `429eb2f`。

| 条件 | invalid | invalid\* | success | abstain | 步/ep | token |
|---|---|---|---|---|---|---|
| P | 13.2% | 8.3% | 75.0% | 41.7% | 1.6 | 36k |
| J | 80.9% | 56.5% | 54.2% | 12.5% | 5.9 | 216k |
| J+ | 78.1% | 51.5% | 54.2% | 16.7% | 6.1 | 298k |
| X | 66.3% | 50.7% | 45.8% | 41.7% | 4.3 | 249k |
| X+ | **0%（结构性）** | 0% | 79.2% | 41.7% | 1.9 | 44k |
| H | **0%（结构性）** | 0% | 83.3% | 33.3% | 2.1 | 33k |

`invalid*` 按不同的 (状态,请求) 对计，`invalid` 按决策计（AgentBoard 口径）。
两者差得远说明该条件的错误是「少数错误被坚持很久」而非「很多次错误」。

### 成立的

- **`X → X+`**：66.3% → 0%。状态依赖在真协议里的效果，论文核心量。
- **成本**：H 33k vs J+ 298k，**9 倍**；X+ 44k vs X 249k，**5.7 倍**。
- **T3（应当拒绝）**：X+ 100% 正确弃权，H 83.3%，J/J+ 16.7%/33.3%。

### 不成立的 —— 值绑定，如实记下

给了 enum 之后差距关闭。配对任务（T2-03 "tiramisù" / T2-08 "the pasta dish"）
上 J/J+/X/X+/H **全部**正确供出 `Tagliatelle al ragù`，`wrong value` 全线约等于零
（X+ 0/45，H 0/51）。**"H 携带值而契约只命名参数"这条论证在 steelman 后不成立**，
05、08 章不能再这么写。

### 替代它的，而且更强

两边 prompt 都写明「绝不编造目标里没有的个人数据，留空会去问用户」：

| 条件 | 个人数据字段 | 模型编造 | 变成向用户提问 |
|---|---|---|---|
| P / J / J+ / H | 5 / 8 / 8 / 8 | **0%** | **100%** |
| X / X+ | 6 / 6 | **100%** | **0%** |

```
X+  enter_details  {"name": "John Doe", "phone": "555-1234"}
X   pay_deposit    {"card": "4111-2222-3333-4444"}        ← 编造的卡号
X+  pay_deposit    {"card": "default_card"}
X   pay_deposit    {"card": "[Card not provided - please specify which card to use]"}
X+  enter_details  {"name": "[Please provide your name]"}
```

机制：**JSON Schema 的 `required` 与 HTML 的 `required` 是同一个词的相反含义。**
前者要求产出一个字符串，后者标记一格要去问用户。native tool call 必须产出
schema-valid 的完整对象，模型**没有办法表达"我不知道"**——最后两行是它在试图
提问而被协议堵住。

这条比值绑定强在：baseline 加到最强之后依然成立；100% vs 0% 是 categorical 的；
直接命中 `sec:eval:method:metrics` 的 elicitation demand；且附带安全性含义。
⚠️ 每条件仅 6–8 个字段，需要更多模型确认。

### 途中修掉的两个自己引入的偏差

1. **`describe()` 只取 `h1,h2,h3,p`**，而站点把已点的菜、被占时段放在 `<li>`。
   H 从控件读状态不受影响，被饿着的只有 J/J+/X——缺掉的内容直接进了测出来的差距，
   方向对论文有利。现取全部块级内容但排除控件内部文本。
2. **`stuck` 阈值 3 截断了自我纠正**。X 在 `add_dish` 被拒两次后第 3 步改调
   `open_preorder` 活下来，J 正好第 3 次被切。阈值卡在条件差异的位置上。改为：
   `duplicate` 标记管指标，`STUCK_LIMIT=8` 管病态（核实切中的都是重复 8–12 次）。

---

## 6. 待定 / 风险

1. ~~**模型是否支持 native tool calling。**~~ → **已验证可用，风险解除**（见 §5.2）。

2. **tool-calling 模式下"停止"与"没产出 tool call"不可区分。** 约定：模型回复裸
   `STOP` = 主动弃权（`agent_stop`）；回复其他文本且无 tool call = `no_tool_call`
   （对应 AgentBoard 的 NTC）。这与 J/J+ 的 `index: -1`、P 的 `method: "STOP"` 同构。

3. **X+ 每步一次 `tools/list` 的开销。** stdio 往返，可忽略；但要确认不会在 132 ×
   20 步的量级上拖慢整轮。

4. **仍然只有一个本地模型 + random。** 第 6 步的付费模型补上 frontier 一端；
   mid-tier 那一端目前空着，视预算决定。

5. **B2 不存在**，跨站泛化仍然没有东西支撑。本轮不做。

6. **差距大小依赖页面散文的详尽程度。** J/J+/X 在 `details_entered`（预点菜未开启）
   下看到的全部内容只有两行：`Review & confirm` / `next-week at 19:00 for 2 — Ada`。
   "预点菜还没开启、所以还不能加菜"这件事，**页面散文一个字没说，只有控件集合说了**
   （渲染了 `POST /preorder`，没渲染 `POST /preorder/dishes`）。这是 G3 门控的真实
   后果，不是 `describe()` 漏抓——但它意味着换一个散文更啰嗦的站点，H 与契约类条件
   的差距会缩小。**必须写进 threats to validity**：b1 靠"渲染了哪些区块"传达状态，
   说得偏少。

7. **P 与 H 的经验差距在本站点、本模型上是小的**（成功率 75% vs 83.3%）。论文
   **不能**写成"H 更准"，只能写成四条：(a) 表示已是充分动作源（P 证明）；
   (b) 约束把"通常对"变成"不可能错"（random 臂：H/X+ 0% invalid 且完成 37.5%，
   而 random 在 P 下**连跑都跑不起来**——没有可选集合）；(c) 同一份产物同时服务人
   与 agent（UI 侧：over-offer 0/2865 vs 目录 87.9%，P 没有界面）；(d) 独立契约要
   追平须把状态依赖重实现，且仍在 elicitation 上分叉。

8. **与 WebMall 的反向结果值得正面写。** 该文测出 HTML 全面垫底（F1 0.67、token
   241k vs 47–140k）；本实验里 **P 36k / H 33k 是最便宜的两格**，J+ 298k、X 249k。
   差别在于他们的"HTML"是整页真实电商站推断，本站是规矩的 hypermedia 应用。直接
   支撑"让站点对 agent 友好的是 controls-in-representation，不是 server-rendering"。

9. **§1.4 里"现实中的 MCP server 极少按状态动态裁剪工具表"这句缺出处。** 目前
   只是判断。论文要用它论证 X 的代表性，就得支撑：要么引规范中 listChanged 的
   措辞说明其粒度意图，要么抽样一批公开 MCP server 统计有几个声明了
   `tools.listChanged` 且真的动态裁剪。后者是一个小调研，几小时的事，但它决定
   "X 代表现实部署"这句话站不站得住。→ 记入 `survey.md` 待办。
