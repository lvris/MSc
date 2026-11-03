# Zotero 导入清单

## 待校对（2026-11-03 核对 MsC.bib 后留下的）

导入和自动导出已经完成，论文读 `MsC.bib` 和 `MISC.bib`，引用格式是仓库里的 `unsrtnat-doi.bst`。下面这些等校对时在 Zotero 里改：

- [ ] `ma2024agentboard`、`patil2023gorilla`：类型改成 Conference Paper，NeurIPS 2024
- [ ] `deng2023mind2web`：类型改成 Conference Paper，NeurIPS 2023 Datasets and Benchmarks Track
- [ ] `leviathan2025generative`、`schultze2025building`：没有 DOI 也没有链接，在参考文献里没法查到原文；补上 arXiv 编号或 DOI
- [ ] Fielding 2000 博士论文：还没导入（字段见下面第 2 步）
- [ ] `hevner2004design`：标题现在是 `Information Systems {{Research}}`，花括号会让 Research 保持大写。在 Zotero 里把它改回普通的 Research
- [ ] 可选：`steiner2025mcp` 带的是 ACM 正式版 DOI（10.1145/3774904.3792893），但类型还是 technical report。想引正式版就改成 Conference Paper
- [ ] `mcpSpec2025`（在 `MISC.bib`）：核对原型用的 MCP SDK 对应哪个规范版本，再定写哪一版

## 第二批（2026-11-03）

魔术棒整块粘贴：

```
10.18653/v1/2025.acl-long.381
arXiv:2504.14603
arXiv:2402.07456
arXiv:2411.01747
10.1109/MS.2014.80
arXiv:2302.12173
arXiv:2505.09388
10.1016/j.chb.2021.107093
10.1145/2109205.2109208
arXiv:2301.10597
arXiv:2507.16044
arXiv:2605.14312
```

| 标识符 | 文献 | 导入后要改的 |
| --- | --- | --- |
| 10.18653/v1/2025.acl-long.381 | Lu et al. 2025, AXIS（ACL 2025） | 如果 DOI 解析失败，在 ACL Anthology 搜 2025.acl-long.381 |
| arXiv:2504.14603 | Zhang et al. 2025, UFO2: The Desktop AgentOS | — |
| arXiv:2402.07456 | Wu et al. 2024, OS-Copilot | 可选：改成 Conference Paper（ICLR 2024 LLM Agents Workshop） |
| arXiv:2411.01747 | Nguyen et al., DynaSaur | 可选：改成 Conference Paper（COLM 2025） |
| 10.1109/MS.2014.80 | Uddin & Robillard 2015, How API Documentation Fails | — |
| arXiv:2302.12173 | Greshake et al. 2023, indirect prompt injection | 改成 Conference Paper：Proceedings of the 16th ACM Workshop on Artificial Intelligence and Security (AISec '23)，pp. 79–90 |
| arXiv:2505.09388 | Qwen3 Technical Report | — |
| 10.1016/j.chb.2021.107093 | Nguyen, Sidorova & Torres 2022, chatbot vs menu-based interfaces | — |
| 10.1145/2109205.2109208 | Mesbah et al. 2012, Crawling Ajax-based web applications | — |
| arXiv:2301.10597 | Fouquet et al. 2023, Breaking Bad: web elements' dependence on JavaScript | — |
| arXiv:2507.16044 | Making REST APIs Agent-Ready: From OpenAPI to MCP Servers | — |
| arXiv:2605.14312 | Making OpenAPI Documentation Agent-Ready | — |

网页和规范已直接写进 `MISC.bib`：`wsdl2001`、`openapi2021`、`copilotkitDocs`、`ollama`、`cliAnything`。

已经改好：`gidey2025affordance`、`gur2023realworld` 改成了 Preprint；Hevner 标题末尾的 “1” 删掉了；Wohlin 的地点改成了 London, UK。

> 2026-11-03。目标：Zotero 作为唯一来源，Better BibTeX 把 Thesis collection 自动导出为 `references/references.bib`。导完告诉我，我来切换 `_toc.tex`、核对 key、删掉旧的四个 bib。

## 第 1 步：按标识符添加

在 Zotero 里先选中 Thesis collection，点工具栏的魔术棒「Add Item(s) by Identifier」，把下面整块粘进去（一行一个，可以一次全粘）：

```
10.2307/25148625
10.2753/MIS0742-1222240302
10.1145/2601248.2601268
arXiv:2401.13178
arXiv:2608.26991
arXiv:2510.04607
arXiv:2305.15334
10.18653/v1/2025.naacl-long.174
10.1145/3571730
arXiv:2306.06070
arXiv:2511.23281
arXiv:2602.17245
10.1145/2844544
10.1038/scientificamerican0501-34
```

对照表（导入后检查用）：

| 标识符 | 文献 | 导入后要改的 |
| --- | --- | --- |
| 10.2307/25148625 | Hevner et al. 2004, Design Science in IS Research | — |
| 10.2753/MIS0742-1222240302 | Peffers et al. 2007, DSRM | — |
| 10.1145/2601248.2601268 | Wohlin 2014, snowballing | — |
| arXiv:2401.13178 | AgentBoard | 类型改成 Conference Paper，Proceedings Title 填 Advances in Neural Information Processing Systems 37 (NeurIPS 2024) |
| arXiv:2608.26991 | ASIL | — |
| arXiv:2510.04607 | Wang et al. 2025, Declarative LLM-friendly Interfaces | — |
| arXiv:2305.15334 | Gorilla | 类型改成 Conference Paper，NeurIPS 2024；DOI 填 10.52202/079017-4020 |
| 10.18653/v1/2025.naacl-long.174 | When2Call | — |
| 10.1145/3571730 | Ji et al. 2023, hallucination survey | — |
| arXiv:2306.06070 | Mind2Web | 类型改成 Conference Paper，NeurIPS 2023 Datasets and Benchmarks Track |
| arXiv:2511.23281 | Steiner et al. 2025, MCP vs RAG vs NLWeb vs HTML | — |
| arXiv:2602.17245 | Jiang et al. 2026, Typed Actions | Extra 里可以加一行 ICML 2026 |
| 10.1145/2844544 | Guha et al. 2016, Schema.org | — |
| 10.1038/scientificamerican0501-34 | Berners-Lee et al. 2001, The Semantic Web | 如果这个 DOI 解析失败，就手动加：Magazine Article，Scientific American，284(5)，2001 |

**可选（还没读，导进来方便在 Zotero 里看 PDF；确认要引用前不会出现在论文里）：**

```
arXiv:2412.04141
arXiv:2408.04667
arXiv:2308.02828
arXiv:2509.25292
arXiv:2602.18914
```

依次是：Reducing Tool Hallucination via Reliability Alignment、Non-Determinism of "Deterministic" LLM Settings、LLM is Like a Box of Chocolates、A Measurement Study of MCP Ecosystem、From Docs to Descriptions。可以放进一个「待读」子 collection。如果它们在 Thesis collection 里，只要正文没有 `\cite`，就不会出现在参考文献里。

## 第 2 步：手动添加（没有 DOI 的）

| 类型 | 字段 |
| --- | --- |
| **Thesis** | Title: Architectural Styles and the Design of Network-based Software Architectures；Author: Roy Thomas Fielding；Type: Doctoral dissertation；University: University of California, Irvine；Date: 2000；URL: https://ics.uci.edu/~fielding/pubs/dissertation/top.htm |
| **Web Page** | Title: Model Context Protocol Specification (Revision 2025-06-18)；Website Title: Model Context Protocol；URL: https://modelcontextprotocol.io/specification/2025-06-18；Accessed: 2026-11-03。版本要和原型用的 SDK 对上，确认后再定 |
| **Report** | Title: JSON Schema Validation: A Vocabulary for Structural Validation of JSON；Authors: Austin Wright, Henry Andrews, Ben Hutton；Report Type: Internet-Draft；Report Number: draft-bhutton-json-schema-validation-01；Institution: IETF；Date: 2022-06；URL: https://json-schema.org/draft/2020-12/json-schema-validation |

## 第 3 步：原来 `MISC.bib` 里的四条

这四条不像是从 Zotero 来的。如果 Zotero 里还没有，就新建；如果已经有，就检查一下。每条都在 **Extra** 字段加一行 `Citation Key: ...`，把 key 固定成现在正文用的那个，这样正文就不用改。

| 类型 | 字段 | Extra |
| --- | --- | --- |
| Book | Title: Hypermedia Systems；Authors: Carson Gross, Adam Stepinski, Deniz Akşimşek；Date: 2023；URL: https://hypermedia.systems/ | `Citation Key: hypermediaBook` |
| Web Page | Title: htmx Documentation；Website: htmx.org；URL: https://htmx.org/docs/ | `Citation Key: htmxlDocs` |
| Web Page | Title: Generative User Interfaces；Website: AI SDK (Vercel)；URL: https://ai-sdk.dev/docs/ai-sdk-ui/generative-user-interfaces | `Citation Key: aisdkGUI` |
| Web Page | Title: AG-UI Overview；Website: Agent User Interaction Protocol；URL: https://docs.ag-ui.com/introduction | `Citation Key: aguiOverview` |

## 第 4 步：自动导出

1. 右键 Thesis collection → Export Collection…
2. Format 选 **Better BibTeX**（不是 Better BibLaTeX，论文用的是 BibTeX + natbib）。
3. 勾选 **Keep updated**，Export Notes 和 Export Files 不勾。
4. 保存为 `D:\Code\LaTex\MSC\references\references.bib`。

导完后我来做：
- `_toc.tex` 改成只读 `references.bib`；
- 核对正文里现有的每个 `\cite` key 在新文件里都存在，不存在的列给你；
- 编译检查；
- 删掉 `HAI.bib`、`WEB.bib`、`UI.bib`、`MISC.bib`，并更新 `CLAUDE.md`。

以后新加文献：我只给你标识符清单，不再直接改 bib。
