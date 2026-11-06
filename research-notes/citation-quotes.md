# 可引用原句摘录

> 2026-11-03。逐篇读原文（arXiv HTML 或本地 PDF），摘出能直接引用的原句和位置。引号里是原文；没读到或只看到二手摘要的都标出来了。key 以 Zotero 导出后 `MsC.bib` 里的为准，这里写的是预期的 key。
> 「用在哪」指向 `citation-gaps.md` 第 2 节的缺口。

---

## ⚠️ 会影响正文的发现（先看这里）

1. **MCP elicitation 不能用来要敏感信息。** 规范原文：「Servers **MUST NOT** use elicitation to request sensitive information.」（elicitation 页，User Interaction Model）。Discussion §fabricated 现在写的是 MCP elicitation「might remove the problem」，而被编造的字段正是 name、phone、card。至少 card 属于敏感信息，按规范不能用 elicitation 要。所以那句要改：elicitation 能解决一部分（比如 reason），但解决不了 card。这反过来是 H 的一个论点：H 的表单直接交给人填，不经过 agent。
2. **MCP 规范没有把工具和应用状态挂钩。** tools 页只说「When the list of available tools changes, servers that declared the `listChanged` capability SHOULD send a notification」，从来没说工具应该随应用状态出现或消失。这正好可以支撑「X（固定列表）代表规范的默认用法，X+ 是规范允许、但没有要求的做法」。
3. **Steiner et al. 的 HTML 条件读的是 accessibility tree，不是原始 HTML。** 原文：「The agent observes the accessibility tree (AXTree) of each page.」之前记的「把整页 HTML 塞给模型」不准确。它仍然是读页面、自己推断动作，所以和 P 条件同类，但第 2 章描述它时要写对。
4. **温度 0 下的确定性：本地和托管不一样。** Atil et al. 发现托管 API 在温度 0 下也不确定，但「we ran Llama3-8b on our local GPUs without any optimizations, yielding deterministic results」。本文的 2b/4b/9b 跑在本地 Ollama 上，27b 走 OpenRouter 托管。所以第 4 章「每格跑一次」的理由要分开写：本地模型大概是确定的（最好实际核对一次），托管的 27b 不能保证。
5. **arXiv 2507.16044 有好几个版本，内容不同。** 下面摘的是 v1（标题 *Making REST APIs Agent-Ready*，50 个 API）。最新的 v4 改名为 *From REST to MCP: An Empirical Study of API Wrapping and Automated Server Generation for LLM Agents*，研究内容也变了（116 个官方 server、80 份 OpenAPI 契约）。Zotero 多半导入的是最新版。引用 v1 的数字时，bib 里要写明 v1，或者改用 v4 的数字（v4 还没读）。

---

## MCP 规范（`mcpSpec2025`，`MISC.bib`）

用在哪：02 §frameworks；04 §variants；09 §fabricated。来源：modelcontextprotocol.io，revision 2025-06-18。

- tools 页，Capabilities：「`listChanged` indicates whether the server will emit notifications when the list of available tools changes.」
- tools 页，List Changed Notification：「When the list of available tools changes, servers that declared the `listChanged` capability **SHOULD** send a notification」，方法名是 `notifications/tools/list_changed`。
- tools 页，Data Types：「`inputSchema`: JSON Schema defining expected parameters」。
- tools 页里**没有**任何关于工具随应用状态变化的说法。
- elicitation 页开头：「The Model Context Protocol (MCP) provides a standardized way for servers to request additional information from users through the client during interactions.」
- elicitation 页，Request Schema：「elicitation schemas are limited to flat objects with primitive properties only」。
- elicitation 页：「Servers **MUST NOT** use elicitation to request sensitive information.」（User Interaction Model）；Security Considerations 里又说了一次。
- elicitation 页顶部注释：「Elicitation is newly introduced in this version of the MCP specification and its design may evolve in future protocol versions.」
- changelog（Major changes 第 6 条）：「Add support for elicitation, enabling servers to request additional information from users during interactions.」

## AgentBoard（`ma2024agentboard`）

用在哪：04 §metrics。详见 `citation-gaps.md` §4。

- §4.3：「Table 4 reports grounding accuracy, the percentage of valid actions.」
- §2.1：环境反馈包括「(3) issue errors when the agent performs an action outside of the action space」。
- 没有 invalid action rate，没有子分类，也没有讨论重复同一个无效动作。

## Steiner, Peeters & Bizer（`steiner2025mcp`）

用在哪：02 §frameworks（最接近的重叠工作）；09 §guarantee。发表在 *Proceedings of the ACM Web Conference 2026 (WWW '26)*，pp. 8493–8496，DOI 10.1145/3774904.3792893（arXiv 的 journal-ref 字段，论文正文里没写）。Zotero 里可以改成 Conference Paper。

- §2 HTML Architecture：「The agent observes the accessibility tree (AXTree) of each page.」「The agents interact with these HTML pages by clicking hyperlinks and performing form-filling actions.」
- 摘要：「Averaged over all tasks, F1 rises from 0.67 for HTML to between 0.75 and 0.77 for the other agents. Token usage falls from about 241k for HTML to between 47k and 140k per task. The runtime per task drops from 291 seconds to between 50 and 62 seconds.」（来自 Table 3）
- Table 3 完成率：HTML 0.57、RAG 0.68、MCP 0.62、NLWeb 0.64。
- §4.3：「The HTML agent performs on average 23 steps per task」。
- **没有**测无效、被拒或失败的动作，也没有状态依赖这个维度；§5 的错误分析还把 HTML 条件排除在外。这正是本文和它的区别，可以在第 2 章直接这样写。
- 注意：Table 9 用另一种平均方式，数字不同（HTML 225k token、281 秒）。引用时注明用的是 Table 3。

## When2Call（`ross2025when2call`）

用在哪：09 §fabricated（替用户编值）。

- §1：「the appropriate tool may be available, but the user may not provide enough information to fill its particular required parameters. In this case, we expect the LM to ask a follow-up question, not hallucinate the missing parameters.」
- §5：「the Qwen, xLAM, and fine-tuned Mistral-NeMo-Minitron models are able to correctly ask follow-up questions over half the time, though they still often hallucinate a tool call with the missing parameters instead.」
- §1，作者解释的原因：「most publicly available training datasets contain many examples of calling tools when tools are provided in the system prompt but a considerably lesser amount of examples of not calling tools when tools are provided.」
- 和本文的关系：本文观察到 X 和 X+（原生工具调用）下模型替用户编名字、电话和卡号，正是 When2Call 说的 parameter hallucination。本文的观察是在多步 agent 里、而且有明确指示「留给用户」的情况下发生的，这一点是它没测的。
- 注意：Table 3 的「Tool Hall%」是没给工具时还去调工具的比例，不是缺参数时编值的比例，不要混用。

## Wang, Li & Chen（`wang2026imperative`）

用在哪：02 第四档；09 §possible。下面引自 arXiv v1；EuroSys 2026 正式版的措辞没核对。

- §1：「with GOI, over 80.9% of failures are policy-related (e.g., LLM semantic misunderstandings), rather than mechanism related.」
- §1 的定义：「orchestrating application functionality according to task semantics, which we term policy. The second is the process of using the functional controls, which involves navigation and interaction, an aspect we refer to as mechanism.」
- 摘要：「Our key idea is policy-mechanism separation: LLMs focus on high-level semantic planning (policy) while GOI handles low-level navigation and interaction (mechanism).」
- 和本文的关系：本文的约束解决的也是 mechanism 一侧（只给当前合法的动作），剩下的失败是 policy 一侧（合法但不是用户要的），和它的发现一致。它**没有**只暴露当前可用动作；它提供的是禁用控件时的错误反馈（§3.4）。

## ASIL（`xie2026asil`，本地 PDF `papers/ASIL screenshot2json.pdf`）

用在哪：02 第四档（必须点名回应）；03 Methods 的独立裁判。

- p. 1 摘要：「screenshots are state-incomplete, and GUI actions are brittle, semantically weak, and poorly matched to long-horizon planning.」
- p. 1 §1：「The central question is therefore not how to make models better at looking at screens, but what observation and action interfaces a software-operating agent should natively use.」
- p. 14 附录 B：「validate_action(action) checks whether an action is valid for the application.」交互元素带有「constraints, available actions」。可选钩子里有「GUI-to-canonical-state synchronization」。
- p. 15 Table 6，标为非自动（manual）的四项：「Access-path discovery, profile authoring/review」「Application-specific bridges」「GUI synchronization and rendering」「Task and evaluator design」。
- p. 7：「A fail-closed raw validator that does not call the ASIL observation builder agrees with the official evaluator on 60/60 balanced final states.」
- 和本文的划界（之前已定）：(1) 动作集由 adapter 产出，接入要手工做（Table 6）；(2) 不是人用的界面，要可视化还得另做 GUI 同步（附录 B 的钩子）；(3) 只测任务成功率，没有合法性指标。

## *Making REST APIs Agent-Ready*（arXiv 2507.16044 **v1**）

用在哪：09 §restate（第二份描述和实现不一致）。作者：Mastouri、Ksontini、Kessentini。注意上面的版本问题。

- 摘要：「Manual failure analysis revealed five recurring issues, all attributable to inconsistencies or omissions in the OpenAPI contracts.」
- 摘要：「We evaluate it on 50 real-world APIs spanning 5,066 endpoints and more than 10 domains. From a stratified sample of 1,023 tool calls, 76.5% succeeded out-of-the-box.」
- §4.3：「Every fault lay in the OpenAPI contract; none originated in AutoMCP or the MCP runtime.」
- §4.3：「In some cases, parameter types declared in the OpenAPI specification did not align with the expectations of the live API.」
- §4.3：「the OpenRoute API specified an API key parameter on only 5 out of its 24 endpoints, despite its documentation stating that all endpoints require this key.」
- 和本文的关系：真实 API 的机器描述（OpenAPI）和实际行为不一致，从这些描述生成的 MCP server 会照搬这些错误。这是「第二份描述会走样」的实证。
- 注意：v1 里失败数前后不一致（240 / 228 / 174），引用时只用摘要里的说法。它**没有**直接说维护第二份描述的成本。

## Fouquet, Laperdrix & Rouvoy, *Breaking Bad*（ACM TOIT，DOI 10.1145/3579846）

用在哪：09 §guarantee（真实网站依赖 JavaScript）。**Zotero 导入请用 DOI `10.1145/3579846`，不要用 arXiv 编号**，这样导进来就是期刊论文。

- 摘要：「We discover that 43% of web pages are not strictly dependent on JavaScript and that more than 67% of pages are likely to be usable as long as the visitor only requires the content from the main section of the page」
- §5.4.3：「for around 25 % of pages, at least one element from an interactive feature found in the main section is broken」（禁用 JavaScript 时）。
- §5.4.1：交互功能包括「Lone Controls, Forms, Empty Anchor Buttons, Mislinked Fragment Anchors, Disclosure Buttons」。
- §7.4.1：「Bootstrap explicitly documents that its components do not fall back gracefully without JavaScript」。
- 样本：6384 个页面、3774 个域名（Hispar 列表），2021 年 3 月抓取。
- 和本文的关系：57% 的页面至少在某处依赖 JavaScript，约四分之一的页面主体里有交互控件离开 JavaScript 就坏掉。所以 H 和 P 读到的服务器 HTML 对很多真实网站是不完整的，这正是 site contract S1、S2 要排除的情况。

## Uddin & Robillard 2015, *How API Documentation Fails*（IEEE Software 32(4)，DOI 10.1109/MS.2014.80）

用在哪：09 §restate（drift）。页码按作者自存 PDF。

- p. 4 Table 2，content 类的六个问题：Incompleteness、Ambiguity、Unexplained examples、Obsoleteness、Inconsistency、Incorrectness。
- p. 4 Obsoleteness 的定义：「The documentation on a topic referred to a previous version of the API.」
- p. 5：「When APIs go through rapid development, their documentation can quickly become outdated, so the current documentation often doesn't reflect recent changes.」
- p. 6：「the input parameters and output type of an API method as implemented in the source code can differ from those in the documentation.」
- p. 1：「The three severest problems were ambiguity, incompleteness, and incorrectness of content.」被列为「blocker」的六个问题里包括 obsoleteness、incorrectness 和 inconsistency（p. 6–7）。
- 和本文的关系：和代码分开维护的描述会过时、会和实现不符，这是有实证的老问题。

## Atil et al., *Non-Determinism of "Deterministic" LLM Settings*（arXiv 2408.04667）

用在哪：04 §models（温度 0 是否等于确定）。还没导入 Zotero，可选。

- 摘要：「We see accuracy variations up to 15% across naturally occurring runs」；「none of the LLMs consistently delivers repeatable accuracy across all tasks, much less identical output strings.」
- §5：「We set the temperature at 0, top-p at 1, and fix the seed.」
- §8.1：「engineering optimizations to run LLMs faster, such as continuous batching, chunk prefilling, or prefix caching, might lead to non-deterministic behavior.」
- §8.1：「we ran Llama3-8b on our local GPUs without any optimizations, yielding deterministic results.」

## Nguyen, Sidorova & Torres 2022（CHB 128, 107093）

用在哪：01 Motivation。**原文没读到**：ScienceDirect 返回 403。下面这句只来自检索摘要，不能直接引用：「chatbot systems lead to a lower level of perceived autonomy and higher cognitive load, compared with menu-based interface systems, resulting in a lower degree of user satisfaction」。请用 Aalto 的权限在浏览器里打开 DOI，核对摘要原文。

---

## 还没读原文的（第二批其余条目）

AXIS、UFO2、OS-Copilot、DynaSaur、Greshake、Qwen3、Mesbah、*Making OpenAPI Documentation Agent-Ready*、Mind2Web、Gorilla、Jiang（Typed Actions）、Hevner、Peffers、Wohlin。它们在正文里的用法都比较简单（列举系统、交代方法出处），写到对应句子时再核对即可。

## Web Verbs（`jiang2026web`，arXiv 2602.17245，2026-11-06 读 HTML 版 §1–§6；§7 以后只看了摘要）

用在哪：02 §frameworks 第三档、对比表。
- §3.1：「Websites should expose high-value web operations as verbs with typed inputs, structured outputs, and clearly documented behavior.」
- §3.1：「agents interact only with the typed verb interface」
- §5：维护集中在「the site developer, a third-party maintainer, or a community tool builder」
- 摘要：「Verb calls can carry preconditions, postconditions, policy tags, and logging hooks.」
- 没有说动词集合按状态过滤；没有提 hypermedia、REST、HATEOAS。

## AXIS（`lu2025axis`，arXiv 2409.17140v2）

用在哪：02 第四档、对比表。会议（ACL 2025）在读到的页面里没核实。
- 附录 B.1：初始技能「generated by restructuring the fundamental APIs from the application provider」，其余靠自动探索。
- §1：「Regular UI interactions are only called when the related APIs are unavailable.」
- 技能库是一份固定的库（附录 B.6），没有按 UI 状态过滤 → 表里 agent-native 一行「随状态变」写成 ASIL 是、AXIS 否。
- 没有讨论 API 的维护成本。
