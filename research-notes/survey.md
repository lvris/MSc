# 相关工作调研笔记

> 建立于 2026-08-11。起因：导师建议确认是否已有人做过类似的研究或实验。
> 这里只记录检索到的条目和它们与本论文的关系，不是章节草稿。

---

## 1. MCP vs RAG vs NLWeb vs HTML

**出处**：arXiv 2511.23281，ACM DL 2026-04-28。Mannheim 大学 WBSG 组。
- 论文：https://arxiv.org/abs/2511.23281
- 项目页 / testbed：https://wbsg-uni-mannheim.github.io/WebMall-Interfaces/

**做了什么**：在单一受控环境里比较 agent 访问 web 的四种接口形式——HTML（传统浏览式）、RAG（对预爬取内容做检索）、MCP（Web API）、NLWeb（自然语言查询）。testbed 是四个模拟电商站，每个站同时提供 HTML / MCP / NLWeb 三种接口。任务从简单商品搜索、比价，到互补品/替代品查询和结账流程。

**指标与结论**：F1、token 用量、每任务耗时、完成率。HTML 全面落后：F1 0.67 vs 其余 0.75–0.77；token 241k vs 47k–140k；耗时 291s vs 50–62s。RAG + GPT-5 最好（F1 0.87，完成率 0.79）。

**与本论文的关系**：这是目前检索到的**最直接的重叠工作**，必须在第 2 章引用并正面回应。但重叠是表面的：

- 它比较的轴是 **effectiveness / efficiency**，不是 **validity**。没有 invalid action rate，不检查被提出的操作在当前状态下是否合法，也没有 state-dependence 这个维度。这正是本论文 Q1 的全部内容。
- 它的 "HTML 条件" 约等于本论文计划中的 **P 条件（推断式）**，不是 **H 条件**。它把整页 HTML 交给模型去推断（token 才会到 241k）；H 是从 representation 里提取服务端声明的控件集合再从中选择。这两件事的区别恰恰是本论文的论点，所以它的数据反而支持"把原始 HTML 当推断素材确实很差"。
- 任务集只覆盖正常电商流程，没有 T2（合法但未暴露）/ T3（当前状态非法、应当拒绝）这两类。

**可选动作**：testbed 是公开的，B2 可以模仿其中一个场景，使数字可与之并排阅读。非必须。

---

## 2. Web Agents Should Use Typed Actions Instead of Click-Based Browsing

**出处**：arXiv 2602.17245。
- https://arxiv.org/pdf/2602.17245

**做了什么**：主张 web agent 应使用从 HTML/DOM 提取的语义化 typed actions（如 `fill_form`、`click_button`），而非坐标点击。提出数据集 MSR-Web-Verbs，把 web 交互映射到语义动作类型。**用到了 state-dependent valid action set**——只有适用于当前页面状态的动作可选。

**与本论文的关系**：机制上距离最近的一篇。需要注意区分：它似乎是从 **agent 性能**角度出发的工程改进（typed action 比点击更好用），而本论文是从**架构与契约**角度（服务端权威、人机同一 artifact、行动空间的确定性保证）。

> ⚠️ 待办：这一条是基于 PDF 摘要判断的，**需要通读原文确认**它是否触及"服务端权威"和"与人类界面同一份 artifact"这两点。如果它也主张了，本论文的 gap 需要重新措辞。

---

## 3. Adaptive Planning on the Web: Using LLMs and Affordances for Web Agents

**出处**：Springer，https://link.springer.com/chapter/10.1007/978-3-031-81221-7_7

**做了什么**：把 affordance 概念用于 web agent 的规划。

**与本论文的关系**：affordance 术语在 web agent 语境下的既有用法，第 2 章 `sec:bg:hypermedia` 讨论 affordance 定义时可引，用于说明本论文取的是 hypermedia 社群的含义而非规划文献的含义。不构成威胁。

---

## 4. Revisiting Hypermedia, The Forgotten Web

**出处**：TechRxiv，2026-02-11。https://www.techrxiv.org/doi/pdf/10.36227/techrxiv.177078147.70240970

**做了什么**：观点性文章。核心论点包括：OpenAI 的 function calling description 实质上就是一种 hypermedia affordance，供 LLM 做选择；LLM 的 structured output、tool calling、human-in-the-loop 需求已经在重新发明 hypermedia 模式。

**与本论文的关系**：可用于支撑第 2 章 `sec:bg:hypermedia` 里"MCP / 工具目录重复了 WSDL、OpenAPI 那一代的 out-of-band 契约"这条论述。是立场支持，不是实证。

---

## 5. Generative UI 侧的两篇

- **Generative Interfaces for Language Models** — arXiv 2508.19227，https://arxiv.org/pdf/2508.19227
- **Generative UI: LLMs are Effective UI Generators** — arXiv 2604.09577，https://arxiv.org/html/2604.09577v1（Leviathan et al.，已在 `_main` 中引用）

**与本论文的关系**：两者都用 **bounded generation**——意图用自然语言表达，但编译成 schema 校验过的组合，在一份固定的、经过审核的组件清单上操作，以此抑制幻觉。这正是第 2 章 `sec:bg:frameworks` 中"build-time registry 只 ground 了词汇、没有 ground 状态"那一行的实证依据。属于友军 + 对照，不是竞争。

---

## 6. Invalid Action Rate 作为既有指标

**出处**：AgentBoard（NeurIPS 2024 D&B，https://proceedings.neurips.cc/paper_files/paper/2024/file/877b40688e330a0e2a3fc24084208dfa-Paper-Datasets_and_Benchmarks_Track.pdf）等 agent benchmark 文献。相关：LASER（arXiv 2309.08172，web 导航的状态空间探索）。

**内容**：Invalid Action Rate (IAR) = 被提出的动作中未通过 tool-call 解析、角色约束或 parser 检查的比例。常见的二分类：
- **No-Tool-Call (NTC)**：模型完全没有产出可执行的 tool call。
- **Out-of-Space (OOS)**：产出的 tool call 落在合法动作空间之外。

同时这类文献里已有 "constrain-then-select" 的说法：先为每个状态定义许可动作集，再让 LLM 在其中选择，以此保证动作总是合法的。ALFWorld 里动作空间随任务/位置变化，是 state-dependent action space 的经典例子。

**与本论文的关系**：**指标定义直接沿用，不要自己发明**，这样 `sec:eval:method:metrics` 里的 invalid action rate 有出处。同时要说明差异：这些工作在 RL / benchmark 语境下约束动作空间，约束由**研究者为评测而构造**；本论文的约束来自**被评测应用自身的服务端渲染结果**，并且同一份产物就是人类界面。

---

## 调研结论

没有检索到与本论文完全重叠的工作。已有的是两条各自独立的线：

1. **比较 agent 接口形式**（第 1 条）——但比的是快慢与成功率，不涉及合法性与状态依赖。
2. **约束动作空间**（第 6 条）——但在 RL / benchmark 语境里，不涉及 web 架构，也不涉及人机共享同一份 artifact。

本论文的组合仍是空白：**服务端权威 + 随状态变化 + 与人类界面同一份 artifact + 测量合法性而非成功率**。

**对实验设计的影响**：第 1 条已经把"浏览式 HTML"钉死为最差选项，因此评估章节增加 **P 条件**（截图 / 渲染后 DOM 推断）变得更有必要——需要用它证明本论文的 H 不是那个东西。

## 待办

- [ ] 通读 arXiv 2602.17245 全文，确认第 2 条的定位是否准确
- [ ] **MCP 的 `tools.listChanged` 在现实中用得如何？** 抽样一批公开 MCP server，
      统计有几个声明了该 capability、又有几个真的按应用状态动态裁剪工具表。这决定
      `exp-conditions-v2.md` 里 X（静态工具表）能否被称作"MCP 实际被部署成的样子"
- [ ] 把以上条目加入 `references/` 下对应的 bib 文件（WEB.bib / UI.bib / MISC.bib）
- [ ] 在 `02_literature_review.tex` 的 `sec:bg:gap` 前后加入对第 1 条的正面回应
- [ ] `sec:eval:method:metrics` 的 IAR 定义对齐 AgentBoard 的 NTC / OOS 分类
