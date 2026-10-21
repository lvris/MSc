# Results 

B1 site, 24 tasks (T1 10 / T2 8 / T3 6), temp 0, 1 run per task. Task set v3 (2026-10-21).

| Cond | Action set |
| --- | --- |
| P | none, infer requests from HTML |
| J | static catalogue (21 ops) |
| J+ | J + preconditions |
| X | MCP, static tool list |
| X+ | MCP, tools filtered by state |
| H | controls in current page |

## Invalid action rate

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 91.2 | 89.8 | 89.0 | 0 | 0 |
| qwen3.5:2b | 48.9 | 44.4 | 33.3 | 71.1 | 3.5 | 0 |
| qwen3.5:4b | 0 | 72.4 | 72.1 | 52.9 | 0 | 0 |
| qwen3.5:9b | 15.6 | 82.1 | 72.0 | 65.9 | 0 | 0 |
| qwen3.8-27b | 0 | 53.2 | 31.8 | 31.6 | 0 | 0 |

## Success rate

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 16.7 | 25.0 | 12.5 | 33.3 | 37.5 |
| qwen3.5:2b | 62.5 | 29.2 | 12.5 | 66.7 | 62.5 | 37.5 |
| qwen3.5:4b | 75.0 | 58.3 | 66.7 | 70.8 | 58.3 | 75.0 |
| qwen3.5:9b | 70.8 | 54.2 | 54.2 | 58.3 | 70.8 | 79.2 |
| qwen3.8-27b | 66.7 | 70.8 | 70.8 | 66.7 | 70.8 | 79.2 |

n = 24 per cell.

## T3: stopped clean / stopped after changing state / did not stop

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 4/2/0 | 5/1/0 | 2/0/4 | 3/0/3 | 1/2/3 | 5/1/0 |
| qwen3.5:4b | 4/2/0 | 1/0/5 | 3/1/2 | 2/1/3 | 1/3/2 | 3/2/1 |
| qwen3.5:9b | 4/1/1 | 1/0/5 | 1/0/5 | 2/1/3 | 4/1/1 | 4/1/1 |
| qwen3.8-27b | 4/2/0 | 2/1/3 | 3/2/1 | 3/3/0 | 4/1/1 | 3/1/2 |

Only "stopped clean" counts as success.

## Tokens (k)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 188 | 30 | 14 | 282 | 115 | 12 |
| qwen3.5:4b | 23 | 145 | 240 | 242 | 47 | 31 |
| qwen3.5:9b | 31 | 170 | 258 | 201 | 27 | 27 |
| qwen3.8-27b | 18 | 115 | 78 | 117 | 21 | 31 |

## Interface

| | H | Catalogue |
| --- | ---: | ---: |
| Dead controls | 0 / 2865 | 18396 / 20937 (87.9%) |
| Controls per state | 2.87 | 21 |
| Values carried by control | 58.4% | 0% |
| Fields to ask per action | 0.24 | 0.57 |

# UI view of the same logs

## Dead suggested buttons (%)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 88.5 | 87.2 | 86.7 | 0 | 0 |
| qwen3.5:2b | 41.5 | 37.5 | 33.3 | 47.8 | 3.5 | 0 |
| qwen3.5:4b | 0 | 46.2 | 47.3 | 31.8 | 0 | 0 |
| qwen3.5:9b | 10.0 | 53.2 | 41.1 | 46.2 | 0 | 0 |
| qwen3.8-27b | 0 | 39.0 | 25.0 | 29.7 | 0 | 0 |

## Tasks where the user sees at least one dead button (%)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| random | - | 100 | 100 | 100 | 0 | 0 |
| qwen3.5:2b | 45.8 | 12.5 | 8.3 | 54.2 | 12.5 | 0 |
| qwen3.5:4b | 0 | 45.8 | 50.0 | 29.2 | 0 | 0 |
| qwen3.5:9b | 12.5 | 66.7 | 58.3 | 45.8 | 0 | 0 |
| qwen3.8-27b | 0 | 41.7 | 33.3 | 29.2 | 0 | 0 |

## Made-up personal data on the form

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 4/11 | - | - | 7/13 | 21/35 | 0/5 |
| qwen3.5:4b | 0/5 | 0/7 | 0/15 | 4/16 | 6/15 | 0/15 |
| qwen3.5:9b | 0/4 | 0/6 | 0/10 | 7/9 | 5/7 | 0/7 |
| qwen3.8-27b | - | 0/15 | 0/13 | 1/11 | 0/4 | 0/32 |

Values copied from the goal or from the page are not counted as made up. 

(Same instruction in every condition: leave personal data empty, the user will be asked)

## Fields asked of the user that the site already determines (date, partySize, dish)

| Model | P | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| qwen3.5:2b | 5 | 2 | 4 | 0 | 0 | 0 |
| qwen3.5:4b | 4 | 12 | 5 | 0 | 0 | 0 |
| qwen3.5:9b | 6 | 4 | 6 | 0 | 0 | 0 |
| qwen3.8-27b | 0 | 5 | 0 | 0 | 0 | 0 |

X / X+ near 0 because the model fills the values itself, read together with the table above.

## Offered list drawn as the screen, states actually visited

| | J | J+ | X | X+ | H |
| --- | ---: | ---: | ---: | ---: | ---: |
| Dead controls, all models | 86–89% | 86–88% | 85–87% | 0 | 0 |



# Results 章核对记录（2026-10-21，从 08_results.tex 的 todo 移出）

写 Results 正文时对着日志核过的数字和取舍，正文不需要，留作答辩和改稿时查。

## \subsubsection{Fixed action lists}

(1) 2b 以 agent\_stop 结束：J 22/24（92\%），J+ 15/24（63\%）；其中不超过 1 步就停的 20 / 14。J+$\rightarrow$X：4b 72.1$\rightarrow$52.9，27b 31.8$\rightarrow$31.6，2b 33.3$\rightarrow$71.1。
(2) 606 次越界（J / J+ / X，不含 random）：add\_dish 209、open\_preorder 41、remove\_dish 20、submit\_preorder 1，合计 271（44.7\%，G3）；confirm\_booking 18 + cancel\_booking 14 = 32（5.3\%，G1 / G2）。最多的任务是 T2-04（118 次）。
(3) P 的 20 次编造：2b 15、9b 5。X 的列表不变，所以没有 stale tool，表里记 --。

## \subsubsection{Choosing from the offered list}

(1) X+ 的 4 次越界（都是 2b）：T3-06 两次 edit\_details（holding、confirmed）、T2-08 release\_hold（browsing）、T3-05 start\_over（deposit\_pending）。
(2) AgentBoard 的另一类越界（既没调用工具也没停，X、X+ 各 1 次）放到 Discussion 的 endings 表；三类越界的定义在 Methods 指标一节。

## \subsubsection{Paired comparison}

(1) 2b 在 H 下：agent\_stop 20/24（83\%），其中不超过 1 步就停的 19。
(2) 去掉 2b 后 H 对 J 是 13 对 1，对 J+ 13 对 3；去掉 2b 的 sign test（双侧）：对 X 14--5 $p = 0.064$，对 X+ 11--3 $p = 0.057$，对 P 7--2 $p = 0.18$，对 J 13--1 $p = 0.002$，对 J+ 13--3 $p = 0.021$。

## \subsubsection{Multi-step tasks}

random 成功几乎全在 T1：H 下 T1 7/10、T2 2/8；J 下 T1 4/10、T2 0。

## \subsubsection{Unreachable goals}

T3：J / J+ 下没停的 25 次里 16 次 stuck、5 次换桌（T3-03）、4 次 chooser\_error。T3 成功判定（停下且没执行 GET 以外的动作）在 Methods 写。

## \subsubsection{Values that come with the control}

(1) 9b 的 H 有 3 个界面因 4096 上下文截断没读出来（T2-02、T2-05、T2-06）。T2-02 已在 \texttt{rerun-9b-H.jsonl} 重跑成功但没并进主日志；T2-05、T2-06 的第二次重跑（\texttt{\_a-9b-rerun2.txt}）全部超时，没有结果。表里 9b H 一行是 audit-report 对主日志的输出（unread = 3），补完再更新。
(2) 4b / 2b 不做（截断，见 research-notes/genui-audit.md），在 Methods 模型一节说明。
(3) 死控件明细（J / J+）：9b 中 add\_dish 7 次（T1-06、T2-03、T2-07、T3-06）、cancel\_booking 2（T1-02）、discard\_draft 3、confirm\_booking 2（T2-02）等；27b 中 add\_dish 2、change\_slot 2（T2-04，等定金时改时间）、cancel\_booking 1、discard\_draft 1、check\_availability 1、view\_cancellation 1。
(4) Asked again 全是 date / partySize（P 下 9b 有 1 次只问 date）。
(5) P：27b 死控件 0，9b 3.7\% 外加 1 个不存在的操作；正文没写，P 在界面侧不是重点。
(6) 全状态扫描（不用模型），research-notes/results.md：死控件 18\,396 / 20\,937（87.9\%），控件自带值 58.4\%。23\% / 35\% 的拆分和 1\,443 个字段来自原 todo，results.md 里没有，要用 \texttt{exp/provenance.ts} 再核一次。
(7) T3 acts 一列在 Discussion（Section~\ref{sec:disc:possible}）讨论。
(8) 图：同一个渲染器吃 H 和吃 J 的截图（\texttt{exp/figures/*-surfaces.html}），把 87.9\% 变成看得见的东西。
(9) 每屏控件数 1.0--1.7：prompt 在试跑后改过一次（见 Section~\ref{sec:disc:threats}）。

## \subsection{Cost}

(1) 每步 prompt token（四个模型）：H 314--370、X+ 580--631、P 597--1004、J 857--1135、J+ 1088--1312、X 1906--2068。X 约是 X+ 的 3 倍。
(2) 和表对过：2b J+ 0.2 步（表里 0.3）、9b X+ 1.2（表里 1.3）、4b X 4.2（表里 4.3），差在四舍五入或日志版本，出终稿前用 report.ts 重出一次。
(3) 27b 在 H 下（31k）比 X+（21k）贵：H 下步数多（2.1 对 0.9），因为它在 T2 上走完了更多任务（7 对 3）。
(4) 2b：P 下 47 步、每步 completion 约 3\,000；X 下 121 步，4 个 stuck、1 个 step cap。原 todo 说 P 也是卡住，不对。
(5) 审计实验的 tokens/UI 没放进来：9b 的 H 反而最贵（4\,442，思考长），27b 的 H 最便宜（1\,285）。不放进正文，要放的话得单独解释。

## \subsection{Summary}

(1) T3 干净停下（表 \ref{tab:eval:t3}）：X+ 1+1+4+4 = 10；J 9、J+ 9、X 10；H 15；P 16。
(2) Tokens 一行：固定列表对 H 是 2.5--9.6 倍（27b 2.5--3.8，4b 4.7--7.8，9b 6.3--9.6）；X+ 对 H：9b 27 对 27、27b 21 对 31、4b 47 对 31，2b 115 对 12（2b 在 H 下大多直接停，不可比）。
(3) P 没放进总表：它和 H 读同一份页面，结果随模型变（越界 0--48.9\%），见 Section~\ref{sec:results:index}。

## 全状态扫描的字段拆分（2026-10-21 重跑核对）

`pnpm exp:elicitation -- --states 997`（默认只扫 200 个状态，要加 `--states 997`）：
2541 个 (state, action) 对，两边都声明 1443 个字段。H：fixed 332（23.0%）、prefilled 510（35.3%）、undeclared 601（41.6%），自带值合计 58.4%。J：全部 1443 个 undeclared。死控件 J 18396 / 20937（87.9%），H 0 / 2865。
注意：这个对比是 H 对 J（静态目录），不是 H 对 X+。

# Discussion 章核对记录（2026-10-21）

## 编造个人信息（tab:eval:fabricated）

`pnpm exp:ui-view` 的 B 节重跑，和表一致，四个模型都有数据（main.md §4 写的「只有 9b」已过时）。
- 编造合计：X / X+ 51 次（2b 28、4b 10、9b 12、27b 1）；P 4 次（只有 2b）；J、J+、H 都是 0。
- 「来自目标或页面」不算编造：27b 在 H 下有 20 个字段属于这一类。
- 把提示语写进字段的例子，日志里有：9b X `name="[Please provide your name]"`、`phone="[Please provide your phone number]"`；4b X+ `card="please provide your preferred card for the €80 deposit"`；另有 `card="[Card not provided - please specify which card to use]"`。

## 7.1 失败分类（tab:eval:endings）

对着 logs/*.jsonl 重算（576 个 episode，不含 random，无重复），表中每格一致。
- 「Goal reached」含 T3 的 goal_satisfied（T3-03 换桌）：P 1、J 2、J+ 3、X 3、X+ 4、H 2；「Agent stopped」含 T3 的干净停下（成功）：P 16、J 9、J+ 9、X 10、X+ 10、H 15。已在表注说明。
- H 下失败 31 个：agent_stop 27（2b 15，其中 11 个 0 步就停）、T3 goal_satisfied 2（9b、27b T3-03）、绕路 1（4b T3-03）、步数上限 1（27b T3-06 enter / edit 来回）。H 下停前改过状态的：T3-02 cancel_booking（4b、9b、27b）、4b T3-01、2b T3-06，以及 T2-08 release_hold（2b、4b、9b）。
- 卡住例子：9b J T3-01，8 步全是 add_dish，起始状态 preorder none。
- 绕路例子：9b X+ T1-09，release_hold → hold_slot → change_slot；第 1 步时 change_slot 已在 validIds 里。
- 电话进卡号：2b X pay_deposit card="+1-555-0123"。
- 读不了：2b P 4（T1-09、T1-06、T2-03、T2-08）、2b J+ 8。no_tool_call：9b X T3-01、2b X+ T1-10。

## 7.2.1 合法但不对（从 09 的 todo 移出）

- H 下「改过状态再停」：2b T3-06 enter_details；4b T3-01（enter / edit / discard_draft）、T3-02 cancel_booking；9b、27b 都是 T3-02 cancel_booking。H 下「没停」：9b、27b T3-03（hold_slot → pay_deposit），4b T3-03（中间丢过一次草稿，记为绕路），27b T3-06 在 enter_details / edit_details 之间来回直到步数上限。
- T3-03 以 goal_satisfied 结束（换桌再付定金）：P 1、J 2、J+ 3、X 3、X+ 4、H 2 + 4b 绕路 1，共 16 / 24（不含 random）。按失败算：用户没同意换时间。
- HTTP 方法（b1/catalogue.ts）：GET 5、POST 11、DELETE 5。cancel_booking 是 DELETE；hold_slot、pay_deposit 是 POST。
- Magentic-UI action guard（arXiv 2507.22358 §6.4）：开发者给每个动作设 irreversibility 启发式（always / never / maybe），always 要用户批准，maybe 交给 LLM judge 判断。

## 7.3 Limitations（核对，2026-10-21）

- 审计 prompt 修订：genui-audit.md 第 14 行，27b 试跑 3 题 × 5 条件后改 system prompt（原写法每屏只给 1 个控件），各条件同一改动，试跑日志在 exp/audit-logs/smoke/，不计入结果，预测未改。
- 9b H 的 3 个审计界面（T2-02、T2-05、T2-06）：主日志 audit-logs/qwen3.5_9b.jsonl 里 ui = null（completion 约 7500，两次尝试被截断）。rerun-9b-H.jsonl 的 T2-02 成功，但没并进主日志；rerun-9b-H-2.jsonl 的 T2-05、T2-06 都超时（2026-10-21 15:21 第二次重跑，每个重试 12 次后放弃）。旧 todo 写「用 16384 重跑补上」与日志不符。
- episode 截断：旧 todo 写「1413 个步骤里 3 个碰到上限」。1413 对得上，但 3 个核不了：episode 日志里的 token 是多次重试的合计（a/choose.ts），超过 4096 的步骤有 85 个，不能当作截断。正文没写这个数字。
- 2b 的 12 次 chooser_error 都是「找不到带 method / index 字段的 JSON」，是否因截断，日志里看不出来。
