# 引用缺口与检索准备

> 2026-11-03 建立。按论文现在的论证结构，逐章列出缺少引用支撑的论断，以及要去找什么样的来源。
> 「候选」一栏只是检索起点：标 ✅ 的已在 bib 里；标 ☐ 的是我确定存在、但还没读过或没核对页码的文献；标 ? 的是还不知道有没有现成文献、需要检索。所有 ☐ 和 ? 都要读过原文再引。

## 0. 为什么别人的论文在 literature review 之外也有很多引用

文献综述之外，其他章节的引用主要有四种用途：

| 用途 | 例子 | 本论文现状 |
| --- | --- | --- |
| A. 方法有出处 | 「我用 design science」「invalid action rate 按 AgentBoard 的定义」「用符号检验」 | Methods 0 条；AgentBoard 只写了名字，没进 bib |
| B. 借来的概念、工具和规范 | REST、affordance、MCP、JSON Schema、htmx、Qwen、Ollama | 只有 htmx 有引用 |
| C. 关于外部世界的论断 | 「很多网站是单页应用」「MCP 的常见用法是固定工具列表」 | 基本没有引用 |
| D. Discussion 里把自己的结果放回文献 | 「这和 X 的发现一致」「和 Y 不同，因为……」 | 只有 Magentic-UI 一处 |

引用自己的章节（`\autoref`）本身没有问题，Results 章几乎不引外部文献也正常，因为它报告的是自己的数据。

**真正的缺口在 C 和 D。** Discussion 现在只在自己的六个条件之间比较，没有和别人的结果对话；第 4 章和 Methods 里有不少「众所周知」式的说法，但没有出处。所以读起来像是「只引自己」。这不是写作方法错了，而是这两类引用还没补。

## 1. 现状统计

| 章 | `\cite` | `\todo{cite...}` |
| --- | --: | --: |
| 01 Introduction | 0（正文；另有 3 条在注释里） | 0 |
| 02 Background | 14 | 8 |
| 03 Methods | 0 | 0 |
| 04 Framework and Implementation | 2（都是 htmx） | 1 |
| 08 Results | 0 | 0 |
| 09 Discussion | 1 | 2 |
| 10 Conclusion | 0 | 0 |

bib 里共 19 条。`amershi2019guidelines`、`gao2024taxonomy`、`oulasvirta2020combinatorial`、`stige2023artificial` 在 bib 里，但正文没有引用。

## 2. 逐章缺口

优先级：**高** = 论证依赖它，审稿人一定会问；**中** = 应该有；**低** = 有更好。

### 01 Introduction

| 位置 | 论断 | 需要什么 | 候选 | 优先级 |
| --- | --- | --- | --- | --- |
| 开头（还没写） | web 从给人看到也给机器操作；LLM 带来的变化 | 综述性来源 | ✅ yang2025agentic、ning2025survey | 中 |
| Motivation 第 1 点 | 纯对话界面在复杂、有状态的任务里负担重、效率低 | HCI 实证研究 | ? 对话界面认知负荷、chatbot 可用性研究；✅ zhang2025exploring 待核对是否覆盖 | 高 |
| Motivation 第 2 点 | 现有 agentic / generative UI 要从页面或弱结构 API 推断动作 | 系统文献 | 同 02 §frameworks | 中 |

### 02 Background（已有 8 个 cite todo，按原文顺序）

| 位置 | 论断 | 需要什么 | 候选 | 优先级 |
| --- | --- | --- | --- | --- |
| §internet | 从只读的爬虫，到会操作的 agent | web 历史、非人类 web 客户端 | ? 爬虫/web 自动化史；✅ yang2025agentic | 中 |
| §internet | Semantic Web 和结构化标注 | 原始文献 | ☐ Berners-Lee, Hendler & Lassila 2001 *The Semantic Web*（Scientific American）；☐ Guha, Brickley & Macbeth 2016 *Schema.org: evolution of structured data on the web*（CACM）；☐ W3C 的 RDFa / JSON-LD 规范 | 高 |
| §internet | 渲染不需要的标注最先过时 | 关于结构化数据质量或衰减的实证 | ? 「structured data quality / staleness」「schema.org adoption errors」；Web Data Commons 的统计也许可用 | 高（这是 drift 论证的根） |
| §genui | 主流助手越来越多地生成小组件和表单 | 产品文档或发布说明 | ? Gemini、ChatGPT 的 generative UI 官方页面；✅ leviathan2025generative | 中 |
| §genui | 生成界面会出现失效、过时或幻觉的控件 | LLM 幻觉文献 | ☐ Ji et al. 2023 *Survey of Hallucination in Natural Language Generation*（ACM Computing Surveys）；☐ Huang et al. 2023 幻觉综述（arXiv 2311.05232） | 高 |
| §hypermedia | REST、HATEOAS | 原始出处 | ☐ Fielding 2000 博士论文 *Architectural Styles and the Design of Network-based Software Architectures*；✅ hypermediaBook | 高 |
| §hypermedia | HAL、Siren 这类只给机器的超媒体格式 | 规范 | ☐ HAL（IETF draft, Kelly）；☐ Siren 规范（GitHub） | 低 |
| §hypermedia | WSDL、OpenAPI 是带外契约 | 规范 | ☐ W3C WSDL 1.1 Note（2001）；☐ OpenAPI Specification | 中 |
| §hypermedia | affordance 这个词的来源 | 原始概念 | ☐ Gibson 1979；☐ Norman *The Design of Everyday Things*；✅ gidey2025affordance | 中（词来自 HCI，至少要交代一句） |
| §frameworks | DOM / 截图推断式 agent | 系统与 benchmark | ✅ zhou2024webarena、song2025browsing、gur2023realworld；☐ Mind2Web（Deng et al. 2023, NeurIPS） | 中 |
| §frameworks | MCP 及其能力（服务端拥有、类型化 schema、运行时更新工具列表） | 规范 | ☐ MCP 规范（modelcontextprotocol.io，要写明版本日期） | **高（全文的主要对照对象，目前没有任何引用）** |
| §frameworks | 第四档 agent 专用接口 | 论文 | ☐ ASIL（arXiv 2608.26991，`papers/` 里有）；☐ Wang et al. 2026（arXiv 2510.04607）；☐ arXiv 2602.17245 typed actions；? AXIS、CLI-Anything、OpenCLI、DynaSaur、UFO2、OS-Copilot | 高 |
| §frameworks | 最接近的重叠工作 | 论文 | ☐ MCP vs RAG vs NLWeb vs HTML（arXiv 2511.23281） | **高（必须正面回应）** |
| §rendering | CopilotKit | 文档 | ? CopilotKit 官方文档 | 低 |

### 03 Methods（还是要点，写正式英文时一起补）

| 位置 | 论断 | 需要什么 | 候选 | 优先级 |
| --- | --- | --- | --- | --- |
| Design Science | 方法本身 | 方法论文献 | ☐ Hevner et al. 2004（MIS Quarterly）；☐ Peffers et al. 2007（JMIS） | 高 |
| Literature Comparison | 定向检索、顺着引用找（snowballing） | 方法论文献 | ☐ Wohlin 2014 snowballing 指南（EASE） | 中 |
| 测什么 | invalid action rate 的定义 | 原始定义 | ☐ AgentBoard（Ma et al. 2024, NeurIPS Datasets & Benchmarks） | 高 |
| 为什么用合成站点 | 现有 benchmark 只检查终态 | 对照 | ✅ zhou2024webarena；☐ ASIL（它也只查终态 checkpoint） | 中 |
| 独立裁判 | 和 ASIL 的独立校验类似 | 论文 | ☐ ASIL | 中 |

### 04 Framework and Implementation

| 位置 | 论断 | 需要什么 | 候选 | 优先级 |
| --- | --- | --- | --- | --- |
| §contract S1 | 很多应用本来就让页面反映当前能做什么；很多不是 | 例子或统计 | ? 服务端渲染与 SPA 的比例；见 09 的 JS 依赖那条 | 中 |
| §stack | Node、TypeScript、Express、linkedom、Ollama | 软件引用 | 官网或仓库 URL 即可（`@misc`） | 低 |
| §execution | 回放时走 Post/Redirect/Get | 模式出处 | ? 一般只给一句解释，可以不引 | 低 |
| §variants | MCP `listChanged`、`tools/call` | 规范 | ☐ MCP 规范 | 高 |
| §variants | X 代表 MCP 的常见部署方式（工具列表固定） | **外部世界论断** | ? MCP server 生态调查、官方示例 server；`main.md` 待做里已有这一项 | 高 |
| §models | Qwen 模型 | 技术报告或模型卡 | ☐ Qwen3 technical report（2025）；qwen3.5 / 3.8 用官方模型卡 | 中 |
| §models | 温度 0、贪心解码重跑结果相同 | 需要核实 | ? 有研究指出温度 0 下仍有非确定性，搜「LLM non-determinism temperature 0」；本地 Ollama 的实际情况最好自己核对一次 | 中（措辞要谨慎） |
| §metrics | 符号检验 | 统计教材 | ☐ 非参数统计教材（如 Conover *Practical Nonparametric Statistics*） | 低 |

### 08 Results

自己的数据不需要外部引用。只有一处：

| 位置 | 论断 | 需要什么 | 候选 | 优先级 |
| --- | --- | --- | --- | --- |
| §values | JSON Schema 可以表达固定值 | 规范 | ☐ JSON Schema 2020-12（`const`、`default`），09 里也用到 | 低 |

### 09 Discussion（最需要补的一章）

| 位置 | 论断 | 需要什么 | 候选 | 优先级 |
| --- | --- | --- | --- | --- |
| §guarantee | 单页应用里，服务器返回的 HTML 几乎只有脚本 | 统计或文献 | ☐ HTTP Archive *Web Almanac* 的 JavaScript 章节；? SPA 与 SEO / 无障碍的研究 | 高（已有 todo） |
| §guarantee | P 对大模型也行，和别人的发现是否一致 | **D 类对话** | ☐ arXiv 2511.23281（他们的 HTML 条件最差，和我们 P 对大模型为 0 的结果要一起解释）；✅ song2025browsing | 高 |
| §restate | X+ 能做到几乎一样，代价是维护第二份描述 | drift 的外部证据 | ☐ ASIL Table 6（自认四项需要手工维护）；? API 文档与实现不一致的实证研究（「API documentation drift / inconsistency」） | 高（目前写着「论证而非测量」，有外部证据会稳得多） |
| §restate | JSON Schema `const` / `default` | 规范 | ☐ JSON Schema 2020-12 | 中 |
| §endings | 被拒后重复同一请求、卡住 | **D 类对话** | ☐ AgentBoard（它报告了 invalid action 和重复）；? web agent 失败模式分析 | 中 |
| §possible | 合法但错误的动作；先问再做 | 人机协作文献 | ✅ mozannar2025magenticui（已有）；✅ amershi2019guidelines（在 bib 但没引，正好用于「何时向用户确认」） | 中 |
| §fabricated | 工具调用替用户编值 | **D 类对话** | ☐ Gorilla（Patil et al. 2023，API 调用幻觉）；? 「tool hallucination」「argument hallucination」相关 benchmark | 高（现在只是观察，有文献对照才能说「已知现象」或「新现象」） |
| §fabricated | MCP elicitation | 规范 | ☐ MCP 规范 2025-06-18 版（引入 elicitation） | 中（已有 todo） |
| §threats | 合成站点、样本小 | 方法文献 | 可选：☐ Wohlin 等的 validity threats 分类 | 低 |

### 10 Conclusion

Conclusion 按惯例不引新文献，只回指本文各章，所以不需要补。

## 3. 按主题汇总的检索清单（去重）

先找标「高」的，大约 15 篇：

1. **规范类（一个下午能搞定）：** MCP 规范（tools、listChanged、elicitation，写版本日期）、Fielding 2000、JSON Schema 2020-12、WSDL、OpenAPI。
2. **方法类：** Hevner 2004、Peffers 2007、AgentBoard、Wohlin 2014。
3. **最近的相关工作：** ASIL、arXiv 2511.23281、arXiv 2510.04607、arXiv 2602.17245，以及第四档的其余系统。
4. **外部世界论断（要检索）：**
   - SPA / JavaScript 依赖的统计（Web Almanac）
   - 结构化标注会过时
   - API 描述与实现不一致（drift）
   - MCP server 实际是否大多用固定工具列表
5. **D 类对话（要检索）：**
   - 工具调用参数幻觉（Gorilla 等）
   - LLM 幻觉综述
   - web agent 的失败模式
   - 对话界面的认知负荷

## 4. 第一批检索结果（2026-11-03）

**已写进 bib（元数据核对过；还没在正文里引用）：**

| key | 文件 | 支撑什么 | 备注 |
| --- | --- | --- | --- |
| `fielding2000rest` | WEB | REST、HATEOAS | UCI 博士论文，ICS 页面有全文 |
| `mcpSpec2025` | MISC | MCP 的 tools、`listChanged`、elicitation | elicitation 是 2025-06-18 版新加的；`listChanged` 从 2024-11-05 版就有。**要核对原型用的 SDK 对应哪一版**，引用时写那一版 |
| `jsonSchemaValidation2020` | MISC | `const`、`default` | `const` 在 §6.1.3；`default` 是 annotation（§9.2），不做校验 |
| `hevner2004design`、`peffers2007design` | MISC | design science | Peffers 的六步：问题、目标、设计开发、演示、评估、交流。Methods 可以直接对照这六步写总流程 |
| `wohlin2014guidelines` | MISC | 顺着引用找文献 | 结论是 snowballing 可以替代数据库检索作为首选策略，正好支撑「定向检索 + 顺着引用找」 |
| `ma2024agentboard` | HAI | 指标出处 | **见下方警告** |
| `xie2026asil` | HAI | 第四档；独立裁判 | `papers/` 里有 PDF |
| `wang2025declarative` | HAI | 第四档 | **标题和之前记的不一样**：实际是 *A Case for Declarative LLM-friendly Interfaces for Improved Efficiency of Computer-Use Agents*。它报告 80.9\% 的失败是 policy（理解错）而不是 mechanism（操作界面），和本文「约束挡不住合法但错误的动作」对得上 |
| `jiang2026typed` | WEB | 第四档 | ICML 2026 的 position paper：主张「Web Verbs」，即带前置条件的类型化函数。和本文最近，但动作集来自另一份接口，不是页面本身 |
| `steiner2025mcpvshtml` | WEB | 最接近的重叠工作 | Steiner、Peeters、Bizer（Mannheim）。之前记的数字（F1 0.67 对 0.75–0.77 等）来自二手摘要，引用前要对原文核对 |
| `deng2023mind2web` | WEB | 推断式 web agent | 发现先用小模型过滤原始 HTML 能明显提高效果，可以和 P 条件对照 |
| `patil2024gorilla` | HAI | 工具调用幻觉 | 测的是 API 名字和结构的幻觉，**不是**替用户编参数值；只能作背景 |
| `ross2025when2call` | HAI | 替用户编值 | **最对口**：测模型该调工具、该追问还是该说做不到；发现模型常常为缺失的必填参数编值，而不是追问。正好对应 Discussion 的 §fabricated |
| `ji2023survey` | HAI | 幻觉综述 | ACM CSUR 55(12) |
| `berners-lee2001semantic` | WEB | Semantic Web | 各处给的页码不一致（34–43 / 28–37），所以没写页码 |
| `guha2016schemaorg` | WEB | schema.org | 文中有采用率：31.3\% 的页面带 schema.org 标注 |

**⚠️ AgentBoard 的问题（影响正文）：** AgentBoard **没有**定义 invalid action rate。它报告的是 grounding accuracy，即「the percentage of valid actions」（§4.3，Table 4），没有给出按步还是按 episode 算的公式，也没有子分类。之前记忆里的「NTC / OOS 二分」在这篇里找不到，来源不明。所以正文不能写「following AgentBoard」，可以写成：「the complement of the grounding accuracy reported by AgentBoard」，再加上本文自己的定义（按每次决策算）。涉及的位置：第 4 章 4.6 的要点和旧稿、Results 表 `tab:eval:invalid` 附近。AgentBoard 也没有讨论重复同一个无效动作，所以 09 §endings 不能拿它对照。

**找到了，但还没写进 bib（要先读原文）：**

- **工具调用参数幻觉：**
  - *Reducing Tool Hallucination via Reliability Alignment*（arXiv 2412.04141）。它的 RelyToolBench 有 Missing Parameter 子集，并把幻觉分成工具类型、格式、内容三类。
- **温度 0 也不确定：**
  - Atil et al. *Non-Determinism of "Deterministic" LLM Settings*（arXiv 2408.04667）。
  - Ouyang et al. *LLM is Like a Box of Chocolates*（arXiv 2308.02828，后来发表在 TOSEM）。

  **影响正文：** 第 4 章说「贪心解码重跑结果都一样」，要改成更谨慎的说法。最好再用本地 Ollama 实际重跑几格核对一次。
- **MCP 生态：**
  - *A Measurement Study of Model Context Protocol Ecosystem*（arXiv 2509.25292，8060 个 server）
  - *From Docs to Descriptions*（arXiv 2602.18914，工具描述的质量问题）

  它们都**没有**统计有多少 server 支持 `listChanged`。所以「X 代表常见做法」目前没有现成的统计，只能用规范和官方示例来论证，或者自己抽样检查一批 server。
- **结构化标注的质量：**
  - Meusel & Paulheim, *Heuristics for Fixing Common Errors in Deployed schema.org Microdata*（ESWC 2015）
  - Meusel, Bizer & Paulheim 2015（WIMS，研究词汇表演化）

  它们讲的是标注**有错**，不是**过时**，用来支撑「渲染不需要的标注最先过时」还差一步，要读原文看能不能用。

**没找到合适来源的：**
- **API 文档与实现不一致的同行评审实证：** 只搜到厂商博客。下一轮换关键词再搜：「OpenAPI specification conformance」「REST API documentation inconsistency」。
- **Web Almanac 里 SPA / 客户端渲染的数据：** 只确认了 2024 年 JavaScript 章存在，具体数字没拿到，要读原文。

**顺手修了：** `hypermediaBook` 的作者原来写的是 William Talcott，这是错的，已改成 Gross、Stepinski、Akşimşek（*Hypermedia Systems* 的三位作者）。`htmxlDocs` 的标题里有 HTML 转义残留，已清掉。

## 4b. 第二批检索结果（2026-11-03）

导入清单见 `zotero-import.md`「第二批」。key 以 Zotero 导出后的为准。下面「支撑什么」都是根据摘要和检索结果写的，引用前要读原文。

| 文献 | 支撑什么（对应第 2 节的哪一条） | 要点 |
| --- | --- | --- |
| Fouquet, Laperdrix & Rouvoy 2023, *Breaking Bad*（arXiv 2301.10597） | 09 §guarantee：很多网站离开 JavaScript 就用不了 | 禁用 JavaScript 后爬了 6384 个页面：只有 43\% 的页面不严格依赖 JavaScript。**这是目前最直接的证据**，比 Web Almanac 好用 |
| Mesbah, van Deursen & Lenselink 2012（ACM TWEB 6(1)） | 同上；也可用于 04 的 S2 | Ajax 应用的界面状态只能在浏览器里通过执行事件才能发现，爬虫要动态分析 DOM 变化。说明「控件不在服务器返回的 HTML 里」是老问题 |
| Web Almanac（2022 / 2024 / 2025 SEO 章、2024 Jamstack 章） | 同上，但**不够直接** | 只比较了渲染前后的 canonical 标签、结构化数据和字数（例如 2024 年渲染改变了 2.1\% 移动页面的 canonical），没有统计控件，也没有给出 SPA 的占比。2024 Jamstack 章说约 94.5\% 的站点属于「Dynamic」，但这是启发式分类。**建议不用**，或者只作补充 |
| Uddin & Robillard 2015, *How API Documentation Fails*（IEEE Software 32(4)） | 09 §restate：第二份描述会走样（drift） | 两次调查共 323 名开发者，分析了 179 个文档单元，归纳出 10 类文档问题。要读原文确认其中有没有「文档过时 / 与实现不一致」 |
| *Making REST APIs Agent-Ready*（arXiv 2507.16044） | 同上，**而且直接连到 MCP** | 把 50 个真实 API 的 OpenAPI 转成 MCP server，五类反复出现的失败都来自 OpenAPI 契约不一致或不完整，包括声明的参数类型和实际 API 不符。这正是「另一份机器描述会和实现走样」的实证 |
| *Making OpenAPI Documentation Agent-Ready*（arXiv 2605.14312） | 同上 | 16 个工业 API、约 600 个端点：文档缺漏和偏离 REST 约定会导致 agent 调用工具失败 |
| Nguyen, Sidorova & Torres 2022（Computers in Human Behavior 128） | 01 Motivation 第 1 点：纯对话界面负担重 | 和菜单式界面相比，chatbot 带来更低的自主感和更高的认知负荷，满意度更低。**同行评审，正好对口** |
| AXIS（Lu et al., ACL 2025） | 02 第四档 | API 优先，UI 兜底；自动探索应用来构建 API。动作集来自探索，不是应用自己按状态给出 |
| UFO2（Zhang et al. 2025, arXiv 2504.14603） | 02 第四档 | Windows 上 GUI 动作和原生 API 混用；控件检测靠 UI Automation 加视觉模型 |
| OS-Copilot（Wu et al. 2024） | 02 第四档 | 统一接口操作整个操作系统，FRIDAY 能自学不熟悉的应用 |
| DynaSaur（Nguyen et al., COLM 2025） | 02 第四档；也可用于 Discussion | 动作不是预先定义的，而是 agent 自己写 Python 函数生成。和本文正好相反：本文是把动作空间收紧到页面给的控件 |
| CLI-Anything（HKUDS, GitHub） | 02 第四档 | 为任意软件自动生成给 agent 用的命令行。只有仓库，没有论文，已写进 `MISC.bib` |
| Greshake et al. 2023（AISec '23） | 01 Scope：不处理 prompt injection | 提出「indirect prompt injection」：攻击指令可以藏在 agent 读的网页里。本文的 agent 也读页面，所以 Scope 里要点名它 |
| Qwen3 Technical Report（arXiv 2505.09388） | 04 §models | qwen3.5 / qwen3.8 没有单独的技术报告，可以引 Qwen3 报告，再加模型卡链接 |
| WSDL 1.1（W3C Note 2001）、OpenAPI 3.1.0 | 02 §hypermedia：带外契约 | 已写进 `MISC.bib` |
| CopilotKit、Ollama | 02 §rendering；04 §stack | 已写进 `MISC.bib` |

**还没找的：** OpenCLI；Gibson 1979 / Norman（affordance 的原始出处，是书，要手动加进 Zotero）；HAL 和 Siren 规范（优先级低）；MCP 生态里 `listChanged` 的实际使用情况（目前没有现成统计）。

## 5. 顺带发现的问题

- **09 有一处引用指空：** 09 §endings 说 Section `sec:disc:threats` 会讨论输出读不出来的「一个可能原因」（4096 上下文截断）。但 Limitations 里原来那条 interface audit 已经删了，现在那里没有这个内容。第 4 章 Experiment 2 的 todo 也还指向 Limitations。要么把那条加回来，要么改掉这两处引用。
- **01 的引用在注释里：** 01 的 `\cite{jarvi_algorithms_2009}` 和 `\cite{yang2025agentic}` 在注释里，是模板残留，不会出现在 PDF 里。
