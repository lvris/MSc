# 这篇论文要说明什么

> 工作文档，和 Claude 一起改。正文写作以这里为准。
> 标了「TODO(我)」的地方需要我用自己的话写。

## 1. 一句话

AI 帮人操作网站时，应该直接用网站页面上现有的按钮和表单，来决定它能做什么、给人看什么，而不是另外维护一份接口。

## 2. 一个例子

客人订大桌，想「确认订位」。但大桌要先付 80 欧定金，现在还不能确认。

- 拿着完整工具列表的 agent：列表里有「确认订位」，直接调用，被网站拒绝。
- 如果它把这个列表画成界面给人看：人会看到一个「确认」按钮，点了没用。
- 网站自己的页面：此时只显示「付定金」「修改信息」「放弃」三个按钮。

论文就是想说明：第三种情况里的那份页面，可以直接给 agent 和界面用。

TODO(我)：这个例子是否够直白？要不要换一个。

## 3. 论证顺序

| # | 要说明的事 | 依据 | 状态 |
| --- | --- | --- | --- |
| P1 | 现在 agent 操作网站主要有三种方式：读页面猜、用另一份机器接口（工具列表、MCP）、给每个应用写适配层 | 文献：WebMall、ASIL、MCP 等 | 第 2 章已有一部分 |
| P2 | 一个操作能不能做，取决于当前状态 | 文献：REST；网站本身：每个状态 21 个操作里平均只有 2.87 个能用 | 有 |
| P3 | 网站页面上的按钮，就是当前能做的操作；这份页面同时也是人用的界面 | 文献：REST、HTML 规范；检查：独立判定和网站一致 | 有 |
| C1 | 给 agent 一份不随状态变的工具列表，它会经常选到当前做不了的操作 | 实验：J / J+ / X 出错 34–81% | 有 |
| C2 | 按状态过滤能消掉大部分错；但只有让 agent 从列表里挑，才能保证不出错 | 实验：X → X+ 66% → 0；小模型在 X+ 下仍出错 6.1%；H 全部 0 | 有 |
| C3 | 让模型生成给人看的界面时，没过滤的方式会把做不了的按钮摆给人 | 实验：界面审计（待做） | 待做，组会讨论 |
| C4 | 直接用页面，不需要另外维护一份接口，成本也最低 | 实验：H 33k token，J+ 298k；渲染界面不需要模型 | 有 |

重点在 C3。C1、C2 讲的是 agent 会不会选错，C3 讲的是人看到的界面对不对。题目里的 Generative UI 主要靠 C3 支撑。

TODO(我)：P 和 C 之间的过渡用自己的话写一句。

## 4. 不主张/限制等等

- H 比直接读页面（P）更准。大模型（27b）在 P 下也是 0 错。
- 控件自带值是优势。MCP 加上可选值列表以后就追平了。
- 成功率能说明问题。每格只有 24 个任务，波动大。
- 界面好用。没有做用户研究。

另外有一个观察，不作为结论：缺少用户的个人信息时，工具调用（X / X+）倾向于自己填一个值，其他方式则会留给用户填。只有 9b 一个模型的数据；MCP 自带的 elicitation 没测。

## 5. 待做

实验：
- [ ] 界面审计实验（C3）：先定输出格式，跑之前写下预测
- [ ] 修 T2-02、T2-04、T3 的判定问题，重新出报告
- [ ] 核对 4b 在 X+ 下的出错次数（提交记录写 3 次，表里是 0）

文献和出处：
- [ ] MCP `listChanged` 的原文，说明 X 代表常见做法
- [ ] agent 接口相关文献补全（Beyond Browsing、2510.04607、2602.17245）





# 论文骨架（claim → section → evidence）

> 中英混写的草稿层。每节先在这里定「讲什么、用什么证据」，再去 .tex 里落 `\todo`。

### 1 RQ ↔ 章节

新 RQ（草稿，措辞再改）：

- **RQ1**: How do existing systems decide which actions an agent may take and which controls a generated interface shows, and what is missing?
  （现有系统怎么决定 agent 能做什么、界面上放什么按钮，缺了什么。框架对比留在这里）
- **RQ2**: 不变。How can HTML hypermedia affordances, such as links, forms, actions, and element states, be extracted and represented as a trusted constraint layer for state-aware agentic UI generation?
- **RQ3**: In a simulated booking site, when actions are limited to the controls on the current page, how often (a) does the agent try an action it cannot take, and (b) does the generated interface show a control the user cannot use, compared with other ways of giving the model its actions?
  （(a) 对应 C1 / C2，(b) 对应 C3；成本作为 (a) 的附带结果）

| RQ | 回答在 | 结论在 |
| --- | --- | --- |
| RQ1 | 02 §frameworks、§gap | 10 |
| RQ2 | 05 Design、06 Implementation | 10 |
| RQ3 (a) | 08 Results C1、C2、C4 | 10 |
| RQ3 (b) | 08 Results C3 | 10 |

CUI 的局限只作为 01 引言里的动机，不再是 RQ。没有第二个站点：通用性在 05 用 site contract 论证，写进 threats。

### 2 每节的 claim 和证据

| 章 / 节 | 讲什么 | claim | 证据（表 / 图） | 状态 |
| --- | --- | --- | --- | --- |
| 01 Intro | 问题 + 例子（大桌定金）+ RQ + contributions | — | 例子用 §2 | RQ3 措辞可能要提到 interface，不只 agent |
| 02 §internet, §genui | web 从给人看 → 也要给机器操作；CUI → GenUI | P1 | 文献 | 有初稿 |
| 02 §frameworks | 现有三档 + **缺第四档 agent-native interface（ASIL 等）** | P1 | five-dimension comparison table | 缺 ASIL 档 |
| 02 §hypermedia | REST、hypermedia constraint、state-dependent controls | P2, P3 | 文献 | 有初稿 |
| 02 §gap | 没人把 GenUI 绑到 server-authoritative、随状态变的动作集上 | gap | — | ⚠️ 第一句「infer from artifacts made for human perception」已被 ASIL 否掉，要改 |
| 05 Design | $A(s)$ 定义、soundness、三层（action / elicitation / presentation）、两个 consumer | P3 | figure: $s \to r(s) \to A(s)$；field type → widget table | 框架有，检查「值绑定」旧说法 |
| 06 Implementation | B1 状态机、parser、replay、renderer（R1 无模型）；MCP server 挪到 07 | P3, C4 | state diagram；两张 consumers 截图 | 删 B2 一节；Presentation 节过时（renderer 已做完） |
| 07 Method | ground truth + 独立裁判、六条件、任务推导（R1–R4）、模型、指标、审计做法 | — | conditions table；task table；metric table | todo 过时：Q1–Q3、frontier pending |
| 08 Results §trace | 一条完整 run + 一条 T3 拒绝 | — | listing | 待选 run |
| 08 Results §C1 | static list → 常选到做不了的操作 | C1 | invalid rate table（J / J+ / X） | 有数据 |
| 08 Results §C2 | X → X+ 消掉大部分；只有 H（选下标）是结构性的 0；random×H 说明是约束在起作用 | C2 | 同一张表的 X+ / H 列；random 行；stale tool | 有数据 |
| 08 Results §C3 ★ | 生成界面时，没过滤的方式把死控件摆给人；X+ 死控件 0 但会多问 / 编字段 | C3 | 审计表；surfaces 对比图（H vs J 同一渲染器） | 27b 完成，其余在跑 |
| 08 Results §C4 | 成本：token；渲染路径无模型 | C4 | token table；R1 检查 | 有数据 |
| 08 Results §T3 | 约束挡不住破坏性的合法动作（取消已确认订位） | 限制 | T3 clean / dirty / no-stop table | 有数据 |
| 09 Limitations（原 08 Threats） | 合成站点（因变量的前提）、n=24、单站点、prompt 在试跑后改过、无用户研究、T3 局限 | — | — | 已挪到 09，素材在 §4 |
| 09 Discussion | 每节一句陈述：same-artifact 的价值在哪；X+ 够不够；P 对大模型也行；T3 局限；编造个人信息（观察） | — | 回引 08 | 待列 |
| 10 Conclusion | 逐条回答 RQ1–3 + open questions | — | — | 最后写 |

### 3 05 / 06 / 07 怎么分

| 部分 | 判断：这句话…… | 写 | 不写 |
| --- | --- | --- | --- |
| 05 Design | 换个网站还成立 → 放这里 | $A(s)$ 定义、soundness、site / client contract、三层、两个 consumer | 订餐、B1、代码 |
| 06 Implementation | 只对我们做的东西成立 | B1 状态机、parser、replay、renderer（R1）、技术栈 | 实验条件、基线 |
| 07 Method | 在说怎么测 | 裁判、六条件（**MCP server、目录都放这里**）、任务推导、模型、指标、审计做法 | 系统怎么工作 |

B1 状态机：06 描述，07 只引用。06 写短（6–8 页）。

### 4 08 Results 结构调整（已定）

- 旧：Q1 magnitude / Q2 cost / Q3 constraint vs capability / Consistency / Cross-site
- 新：trace → C1 → C2 → **C3** → C4 → T3 limitation
- Q3（random chooser）并进 C2；Q2（cost）由 T2 / T3 结果承担，并进 C2 或 T3 节；Cross-site 删掉（没做 B2）；Consistency（独立裁判 100%）移到 Method §ground truth
