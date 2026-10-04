# B1 v2 改造计划 + 测试文档

> 建立于 2026-08-12。目的：把 B1 从"线性且可猜的状态机"改造成能支撑 Q1 实验的环境，并定义配套的测试与任务集。
> 本文件是计划，不含代码。确认后再改 `prototype/b1/model.ts`。

---

## 0. 唯一判据

> **当前状态下合法的操作集合，必须不能是「操作名 + 常识」的函数，必须是「服务端所持状态」的函数。**

满足即停手。任何不服务于这一条的"更真实"都不做。

### 为什么需要改

现状 `model.ts`：6 个状态、11 个操作，路径是 `browsing → holding → details_entered → confirmed` 一条直线。操作名本身泄露了正确顺序（看到 `enter_details` 和 `confirm_booking` 就知道先后），所以 J+ 条件下模型靠常识就能走对，H 与 J+ 的差距会趋近于零 → **null result**。

现有设计里唯一真正不可猜的是 `conflict`（20:00 被他人抢占，只有服务端知道）。改造就是把这一处的性质变成主体。

### 不改的东西

内存状态、无登录、无数据库、无样式、单用户、单餐厅。这些不影响任何指标。

`render.ts` 与 agent `a/` **一行都不用改**——渲染由 `validAffordances()` 生成，A 从 HTML 发现控件。这是架构红利，本次兑现。

---

## 1. 目标状态机

### 1.1 会话状态

会话由两个正交部分 + 若干服务端事实构成。正交是关键：它用很少的代码制造组合状态，而组合状态正是静态目录最没辙的地方。

**预订状态 `booking.status`**

| 状态 | 含义 |
|---|---|
| `browsing` | 尚未持有时段 |
| `holding` | 已暂扣某时段 |
| `details_entered` | 已填客人信息，未确认 |
| `deposit_pending` | 需付定金（大桌）才能确认 |
| `confirmed` | 已确认 |
| `conflict` | 确认时发现时段被抢占 |
| `waitlisted` | 满场，已进候补队列 |
| `cancelled` | 已取消 |
| `cancellation_requested` | 24h 内申请取消，待餐厅处理 |

**预点菜状态 `preorder.status`**

| 状态 | 含义 |
|---|---|
| `none` | 未开启 |
| `open` | 可增删菜品 |
| `submitted` | 已提交，锁定 |

**服务端持有的事实**（客户端无法推断，门控的来源）

| 事实 | 说明 |
|---|---|
| `slotAvailability` | 各时段是否可订，含被抢占的时段 |
| `partySize` | 与阈值 `LARGE_PARTY = 6` 比较，决定是否需要定金 |
| `hoursUntilSeating` | 与阈值 `CANCEL_WINDOW = 24h` 比较，决定取消形式 |
| `waitlistPosition` | 候补位次 |
| `depositAmount` | 定金金额 |

### 1.2 操作清单

| # | 操作 id | 合法状态 | 门控依据 | 可猜？ |
|---|---|---|---|---|
| 1 | `check_availability` | browsing | — | 是 |
| 2 | `hold_slot` | browsing, conflict, waitlisted(有空位时) | `slotAvailability` | **否** |
| 3 | `join_waitlist` | browsing/conflict 且全满 | `slotAvailability` | **否** |
| 4 | `leave_waitlist` | waitlisted | — | 是 |
| 5 | `check_position` | waitlisted | — | 是 |
| 6 | `enter_details` | holding | — | 是 |
| 7 | `change_slot` | holding | — | 是 |
| 8 | `release_hold` | holding | — | 是 |
| 9 | `confirm_booking` | details_entered 且 **不需定金** | `partySize > LARGE_PARTY` | **否** |
| 10 | `pay_deposit` | deposit_pending | `partySize > LARGE_PARTY` | **否** |
| 11 | `edit_details` | details_entered, deposit_pending | — | 是 |
| 12 | `discard_draft` | details_entered, deposit_pending | — | 部分 |
| 13 | `open_preorder` | details_entered 且 preorder=none | — | 否 |
| 14 | `add_dish` | preorder=open | preorder 子状态 | **否** |
| 15 | `remove_dish` | preorder=open | preorder 子状态 | **否** |
| 16 | `submit_preorder` | preorder=open | preorder 子状态 | **否** |
| 17 | `view_confirmation` | confirmed | — | 是 |
| 18 | `cancel_booking` | confirmed 且 `hoursUntilSeating > 24` | 时间窗 | **否** |
| 19 | `request_cancellation` | confirmed 且 `hoursUntilSeating ≤ 24` | 时间窗 | **否** |
| 20 | `view_cancellation` | cancellation_requested | — | 是 |
| 21 | `start_over` | cancelled, cancellation_requested | — | 是 |

合计约 **21 个操作、12–14 个可达状态**，每状态合法集合 1–4 个。目录/合法比约 5:1 到 20:1。

### 1.3 五条不可猜门控（改造的实质）

| 门控 | 规则 | 攻击的假设 |
|---|---|---|
| **G1 定金阈值** | `partySize > 6` 时 `confirm_booking` 不存在，只有 `pay_deposit` | 目录式 agent 会直接调 confirm；阈值只有服务端知道 |
| **G2 取消时间窗** | `≤24h` 时 `cancel_booking` 消失，变为 `request_cancellation`（需 `reason` 字段） | 同一意图在不同时刻对应不同操作 |
| **G3 预点菜窗口** | 仅在 `details_entered` 之后、`confirmed` 之前可开启；`submitted` 后锁定 | 子资源的可用性由主资源状态决定，组合状态 |
| **G4 候补非单调** | 满场时 `hold_slot` 消失、`join_waitlist` 出现；有人取消后 `hold_slot` 回来 | 破坏"流程是单向漏斗"的常识假设 |
| **G5 cancel 同名不同义** | `details_entered` 下是 `discard_draft`（丢弃草稿、无字段）；`confirmed` 下是 `cancel_booking`/`request_cancellation`（真取消、可能需理由与费用） | 破坏"一个名字 = 一个工具"的目录假设 |

> **G1、G2、G5 是三条最重要的**：它们让同一句用户目标（"确认订位"/"取消订位"）在不同服务端状态下映射到不同操作，而模型无从判断自己处在哪一侧。

---

## 2. 测试文档

三层，各自回答不同的问题。**第 1 层是论文的承重结构**——它断言的正是论文主张的性质。

### 2.1 第 1 层：站点不变量（扩充 `b1/check.ts`）

对**每一个可达状态**穷举断言。这些不是普通单元测试，是论文命题的可执行形式。

| ID | 不变量 | 断言 | 对应论文位置 |
|---|---|---|---|
| I1 | **渲染 = 合法** | 页面中被解析出的控件集合与 `validAffordances(s)` 完全相等（双向包含，不只是子集） | `sec:design:soundness` |
| I2 | **守卫拒绝** | 对每个状态，构造所有 21 个操作的 HTTP 请求；不在合法集合中的必须被拒（409），且不改变状态 | 「非法请求不可绕过」 |
| I3 | **提供即可执行** | 每个被渲染的控件，按其声明的 method/url/fields 发出请求，必须成功且产生状态转移，无 5xx | 「每个提供的控件在展示当时都可执行」 |
| I4 | **可达性** | 从 `freshSession()` 出发，每个状态都存在到达路径；给出该路径 | 任务集推导的前提 |
| I5 | **字段完备** | 每个控件声明的 `fields[]` 足以构造成功请求，无隐含的必填参数 | 「required 字段恰好定义了该问什么」 |
| I6 | **门控实证** | 对 G1–G5，构造门控两侧的状态对，断言合法集合确实不同 | 第 1.3 节的五条门控 |

> I1 的双向包含是重点。现在的 `check.ts` 只验单向，但论文主张的是"服务端渲染的恰好是合法集合"，少一个方向命题就不完整。

### 2.2 第 2 层：agent 发现（扩充 `a/check.ts`）

| ID | 断言 |
|---|---|
| A1 | A 在每个状态发现的规范化控件集合 == `validAffordances(s)`（按 `(method,url,fields)` 键比对） |
| A2 | 被门控排除的操作**不出现在**发现集合中——是缺席，不是被拒绝 |
| A3 | 去重键包含字段值（已修复过的 bug，加回归断言：5 个 hold_slot 表单不得塌成 1 个） |
| A4 | A 不含任何 B1 特定代码——以「A 目录下不出现 b1 字样」的静态检查表达 |

### 2.3 第 3 层：实验采集（新增 `exp/`）

这一层不是"测试"而是**测量**，但同属本次要建的东西。

- **判定器**：每一步记录 agent 提出的操作，并调用 `validAffordances()` 判定其是否合法。ground truth 免费且不可争议。
- **日志**：JSONL，一行一步。字段至少：`run_id, condition, model, task_id, step, state_snapshot, proposed_action, in_valid_set, executed, outcome`。
- **注意**：H 条件下 `in_valid_set` 恒为 true。这**不是测出来的，是结构上不可能为 false**。论文必须如实标注为「保证，非测量」——`sec:eval:aim` 那张 precision/recall 框架表已埋此伏笔，此处兑现。否则会被指为循环论证。

---

## 3. 任务集推导

从状态机自动推导，不手挑，以免被质疑为「挑了对自己有利的任务」。

| 类别 | 推导规则 | 预计条数 |
|---|---|---|
| **T1 合法且已暴露** | 对每个状态，为其合法集合中的每个操作生成一条目标 | 8–10 |
| **T2 合法但未暴露** | 取门控两侧：目标在**另一侧**状态下合法，但当前状态未提供。H 必须失败（正确地拒绝一件事实上可能的事），无约束条件可能猜对 | 4–5 |
| **T3 当前状态非法** | 每条门控 G1–G5 各产出至少一条：大桌直接确认、24h 内直接取消、confirmed 后改预点菜、满场时直接 hold、confirmed 后 discard_draft | 5–6 |

总计 **17–21 条**，控制在 20 上下。数量不是重点，**覆盖状态机的边与非边**才是。

每条任务记录：`id, class, goal_text, start_state, target_state, expected_outcome_under_H`。直接对应 `sec:eval:method:tasks` 里那张待填的表。

> T3 全部由门控产出，这正是改造的回报：改造前只能勉强凑出「编辑已确认的订位」一条。

---

## 4. 工作顺序与完成标准

| 步骤 | 内容 | 估时 |
|---|---|---|
| 1 | `model.ts` 改造：新状态、21 个操作、G1–G5 | 0.5–1 天 |
| 2 | `check.ts` 扩充：I1–I6 全部通过 | 0.5 天 |
| 3 | `a/check.ts` 扩充：A1–A4 通过 | 0.25 天 |
| 4 | 任务集推导脚本 + 产出 17–21 条 | 0.25 天 |
| 5 | J / J+ 两个 surface + 日志 + 采集器 | 1 天 |
| 6 | 跑 Q1（先 random + qwen3.5:9b） | 0.5 天 |

**完成标准**：I1–I6、A1–A4 全绿；任务集生成；Q1 那张表（H/J/J+ × invalid action rate）有数字。

**明确不做**：P 条件（截图/DOM 推断）、WebMall B2 探针、任何 UI 美化、任何持久化。前两项在 Q1 出数之后再评估。

---

## 5. 待确认 → 已落地的决定（2026-08-12）

- [x] **五条门控全部保留**。G3 的代码量没有预想的大（约 40 行），且它确实是唯一制造组合状态的一条——`deposit_pending × preorder=open` 这类组合直接产出了两条 T3 任务。
- [x] **`LARGE_PARTY = 6`（严格大于才要定金）、`CANCEL_WINDOW_HOURS = 24`**，另加 `DEPOSIT_PER_GUEST = 10`。
- [x] **`hoursUntilSeating` 用预烘焙的日期表**，不引入真实时钟：`tomorrow` 20h（窗口内）、`next-week` 168h（窗口外）、`saturday` 54h 且**全满**（G4 的入口）。同一张表顺带承担了各日期的已订时段，所以「窗口两侧」与「满/不满」都可复现。

### 实施中发现、并改动了计划的两点

1. **T3「不可达」必须限定为"在不放弃当前预订的前提下"**。按全图算，`start_over` 让状态图强连通，任何目标都可达，T3 会**恒为空**（第一版跑出来正是 0 条）。现在把 `start_over / discard_draft / release_hold / change_slot / leave_waitlist` 标为 abandoning 边，可达性在剩余子图上算。

2. **任务必须按「用户意图」而非「操作 id」判定**。`confirm_booking` 与 `pay_deposit` 是同一个意图（G1），`cancel_booking` 与 `request_cancellation` 也是（G2）——这正是两条门控的要害。若按操作 id 打分，`deposit_pending` 下说「确认订位」会被误判为不可能，而它其实完全可行、只是入口不同。见 `b1/goals.ts` 的 `INTENTS`。

   这一改动还让 **T1 自身就有判别力**：T1-01「确认订位」起始于大桌的 `deposit_pending`，T1-02「取消订位」起始于 24h 内的 `confirmed`——两条都是「目标合法、且控件就在页面上，但目录式 agent 会调用错误的那个同义操作」。

---

## 6. 实施结果（2026-08-12）

步骤 1–4 完成，`render.ts` 之外 **agent `a/` 一行未改**，A 侧检查全绿——第 0 节说的架构红利兑现。

| 步骤 | 状态 | 产出 |
|---|---|---|
| 1 model.ts 改造 | ✅ | 9 个 booking 状态 × preorder 子状态，21 个操作，G1–G5 |
| 2 check.ts I1–I6 | ✅ | **997 个可达状态、3950 条转移、52748 条断言全过**（~35s）。走查自行收敛，未触上限 |
| 3 a/check.ts A1–A4 | ✅ | 4230 条断言全过；A1 双向包含在 301 个状态上成立 |
| 4 任务集推导 | ✅ | `exp/tasks.json`，22 条（T1 10 / T2 6 / T3 6） |
| 5 J / J+ + 采集器 | ⬜ | 下一步 |
| 6 跑 Q1 | ⬜ | |

**目录/合法比：21 个目录操作 vs 每状态平均 2.87 个合法 → 7.3:1。** 这个数字本身就是论文里可以直接引用的一句话。

新增文件：`b1/catalogue.ts`（静态目录，J/J+ 共用）、`b1/goals.ts`（意图）、`b1/explore.ts`（状态空间走查，check 与 tasks 共用）、`b1/tasks.ts`。`server.ts` 新增 `/__session` 测试钩子——它不被渲染、不可从 HTML 发现，A 无从触及。
