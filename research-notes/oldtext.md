# 旧稿（oldtext）

> 从各章 `oldtext` 环境里移出来的旧稿和中文原稿，按章、按所在小节排列。
> 论文正文里已经不再保留这些内容；需要时从这里找。

## chapters/01_introduction.tex

### Motivation（原为注释）

```latex
% agent 已经改变了人们的生活, 除了传统的搜索和问答, 还可以在网页上执行操作, 例如订餐、预定机票、购买商品等.
% 然而, 想象一下在一个餐厅中, 你想预定一张大桌子, 但是餐厅要求你先支付 80 欧元的定金, 然后才能确认预订.
% agent 在这种情况下可能会遇到问题: 它可能会尝试直接确认预订, 但是由于没有支付定金而被拒绝.
% 而网站自己的页面只提供「付定金 / 修改 / 放弃」选项. 这说明了纯对话式界面在处理复杂、状态依赖的网页任务时存在认知负荷和效率低下的问题.
```

## chapters/02_literature_review.tex

### From Conversational Agents to Generative UI

```latex
The rest of this chapter is organized around that problem: a generated surface can present controls that are invalid, stale, or outright hallucinated relative to what the application can actually do at this moment \cite{ji2023survey}.
```

### REST and the hypermedia constraint

```latex
Read against \autoref{sec:bg:frameworks}, this identifies the pattern that MCP repeats.
WSDL and OpenAPI were the out-of-band contracts of their eras \cite{wsdl2001, openapi2021}; a tool catalog obtained at connection time is the same client-held description, separate from the representation the application actually serves.
```

### Consequences

```latex
A separate contract such as MCP cannot offer this property, however well specified, because separateness is exactly what the property excludes.
```

### How Existing Systems Decide What Can Be Done

```latex
这一节比较现有的系统如何指引 agent 选择动作，回答 RQ1。
我按照动作集的来源进行分类和比较, 关心的问题是: 动作集由谁产出、是否随当前状态变、要额外维护什么、和人用的界面是不是同一份。
RQ1(引用一下) 的回答整体可以用一张表总结，见 \autoref{tab:bg:comparison}。
```

### Research Gap

```latex
Web agents can operate human-facing sites, but they infer their actions from artifacts made for human perception, and the inference is brittle.
Generative-UI frameworks can render dynamic surfaces, but they ground the actions those surfaces expose weakly: inferred from the page, fixed at build time, or delegated to a machine-only contract that runs parallel to the human interface and can drift from it.
No existing framework binds a human-facing generated surface to a state-dependent, server-authoritative action set.
```

## chapters/03_methods.tex

### （章开头）

```latex
为了解决我们在之前提出的问题, 我在这篇文章中主要构建了一个原型, 并通过受控实验来验证其有效性。
在这个章节中, 我将介绍我使用的方法, 并将在下一章中详细描述工作原理和实验细节。
```

### Research Approach

```latex
整体上我们按照 design science 的步骤进行研究, 步骤参考了 \citet{peffers2007design} 的 DSRM.
这是因为我们的 RQ 主要关注的是如何做到, 而 design science 的研究方法就是通过构建一个 artifact 来回答这个问题.

在上一章(ref)中, 我们通过文献比较分析了现状.
对于解析 affordance 的问题, 我分别构建了一个框架(ref)和一个原型(ref)来验证其有效性.
最后, 我使用受控实验来验证我的方法的有效性, 并分析了实验结果(ref).

在表格(ref)中, 我们总结了 DSRM 的每一步在本文中对应的部分和问题.
```

### Evaluation

```latex
我希望能够满足:
- 约束能够被清楚地定义, 并且在满足约束的情况下, 系统能够保证正确性.
- 原型能够满足约束, 并且有检查脚本来验证.
- 通过实验来验证约束的有效性.

(这稍微论证一下, 前两条看框架和原型就知道, 然后我们需要第三条实验来验证约束的作用)
```

### Experimental design

```latex
小标题: 改什么?
变量是模型怎么知道现在能做什么, 我们在第 4 章逐个定义了几种做法, 从``自己读页面''、``一份固定的操作列表''、``按状态过滤过的列表''，到``从页面的控件里选''..
这些方法之间的差别正是约束的作用

小标题: 固定什么?
固定不变的：同一个站点、同一个客户端、同样的页面文字、同样的任务和目标、同一条执行路径。

此外, 我们做了两个实验, 对应 RQ3 的两半:
一个让 agent 自己执行, 看它会不会去做现在做不了的事;
另外一个让模型生成给人看的界面, 看界面上会不会出现用不了的控件。
```

### What is measured, and what is not

```latex
实验最重要的就是测试, 我们设计的实验中主要测试的是合法性, 具体的指标见第四章.

小标题: 我们测什么?
我们主要关心的问题是: 做了或摆出了现在做不了的操作，占多少。
次要的是成功率, 可以知道约束有没有让任务变的难以解决.
之所以不测成功率为主, 是因为一个任务可以最后成功, 但中途被拒好几次, 只看成功率就看不到.

小标题: 我们不关心的
而有关 UI 的好用, 好看, 速度等问题.
这些需要一个用户研究来调查, 本论文不主张这些.
合法性可以机械地检查, 所以不需要人参与.
```

### Validity of the Measurement

```latex
在确定了实验设计和指标之后, 还有一个问题是: 测量的结果是否可信? 下面三小节分别回答三个顾虑: 标准答案从哪来、判对错的是不是自己、结果归功于约束还是模型。
```

### Why a synthetic site

```latex
标准答案是指, 在每个状态下, 哪些操作是可行的, 哪些操作是不可行的. 我们需要知道状态下合法的操作来计算``做了做不了的事''的比例.

真实的网站没有这个答案, 现有的 benchmark（如 WebArena）一般只检查最后的状态, 也没有这个答案.
因此我们选择一个受控的站点, 用状态机明确定义每个状态下能做什么. 这是测量的前提.
```

### An independent judge

```latex
但是这引发了另外一个问题, 如果这个状态机就是定义站点的那个状态机, 那么我们测的结果就是自己判自己, 这显然不可信.
因此我们另外设置一个裁判, 这个裁判不能和状态机相互沟通, 作为一个相对独立的第三方来检查.
具体的检查方法是: 一个操作合法, 当且仅当存在一组参数让服务器不拒绝.
第四章(具体多少节?)中有裁判和状态机的对照结果, ASIL 也使用了类似的独立校验.
```

### Separating the constraint from the model

```latex
此外, 我还有一个顾虑: 如果实验结果好，是因为约束，还是因为模型够聪明？为此，我设计了两种办法来分离约束和模型的作用。

- 用多种大小不同的模型，看看结论是否随模型能力变化。
- 一个随机选择器，它完全不理解任务，所以它在约束下做到的任何事，都只能归功于约束。

这部分的结果可以参考(多少多少节)
```

### Ethics and Reproducibility

```latex
本任务中没有人类参与者，也不收集个人数据。任务里的姓名、电话都是虚构的样例值。

使用的模型均为开源的, 代码和全部日志公开（仓库链接见第 4 章技术栈一节）；提示词全文在 \autoref{app:prompts}，任务全表在 \autoref{app:tasks}，各条件看到的内容在 \autoref{app:surfaces}。
```

## chapters/04_implementation.tex

### （章开头）

```latex
接上 Background 的 gap：没有系统把给人看的生成界面绑到一个随状态变、由服务器决定的动作集上. (我感觉这章还是就直接叫 implementation 合适一点)
最后的时候signpost 一下
```

### The Framework

```latex
一个非常符合直觉的想法是: 页面上的控件必须和当前可行的操作一一对应.
建立在这个基础上, 我们自然可以把页面上的控件抽象成一个集合, 这个集合就是当前状态下的 ``affordance set''.

everything downstream is confined to that set: the agent may only execute actions drawn from it, and the interface presented to the user may only offer controls drawn from it.
由于这个集合是从服务器选择的表示中导出的, 并且一个行为良好的服务器会渲染当前状态下有效的控件, 因此集合之外的操作对 agent 和界面都是不可用的.

接下来我们将这个想法形式化, 并说明它的保证和限制, 以及谁来使用这个集合.
```

### The affordance set

```latex
我们令 $s$ 表示服务器持有的应用状态，$r(s)$ 表示服务器为该状态返回的表示.
如果我们使用一个解析器来分析界面中的控件, 对 HTML 来说就是连接, 表单等, 以及拓展 如htmx 这这些.
就可以得到一个解析出的 affordance 集合:

A(s) = ...

集合中的元素应该像表 2 所说的一样留下能够可以重建这个请求的信息.

如图一所示, 这个集合应该不仅仅是呈现出所有的控件, 而且应该是一个封闭的集合, 也就是说, 这个集合中不应该有任何元素是无法执行的.
而一旦选择执行, 不论是用户点击了控件还是 Agent 发送了请求(这两者应该是本质上相同的), 都会将应用状态从 s 转换到 s'.
并且服务器会返回一个新的表示 r(s') , 这个表示又会被解析成一个新的 affordance 集合 A(s'), 以此类推.

但是, 服务器真正能够接受的操作集合 V(s) 不一定满足 A(s) = V(s) 的契约, 这正是我们接下来要讨论的.
```

### The site contract

```latex
为了满足 $A(s) = V(s)$, 需要网站的配合:

S1: 页面上存在且包含现在能用的所有控件.
这要求网站诚实的反应现在能做的操作, 很多网站现在就是这样的, 因为人在使用网站的时候也需要这个条件.
但是像 SPA 这样重度依赖 JavaScript 的应用就很难实现, 我们在 discussion 里展开.

S2: 请求本身能从控件重建, 不依赖 Javascript 脚本的临时计算.
这是非常严格的要求, 现代的 Web 应用通常使用 JavaScript 来动态生成和更新页面内容，这使得从静态 HTML 中重建请求变得困难.

S3: 服务器自己会拒绝不合法的请求. 
(你自己编一点, 这个是非常合理的)

(例子你帮我补上)
```

### Guarantees and limits

```latex
如果满足了站点协议, 那么我们就可以推出一些有用的内容

(小标题)保证
照搬两句, 用列表

(小标题)不保证
用列表
- 无法访问网站没有放在页面上的操作
- 挑选操作仍然受限于模型的能力或提示等
- 模型里和用户交流的部分, 界面和对话无法保证
```

### The consumers of \texorpdfstring{$A(s)$

```latex
在我们提取到 A(s) 之后, 有两个消费者: 执行和展示.

小标题(执行), agent 可以挑选出一个它认为对用户意图最合适的控件, 并从它声明的字段里填入能从意图推出来的值, 不能推出来的就去问用户.

小标题(展示), agent 和用户交互的界面上, 可以从 A(s) 中由声明的字段作为关键要素.
想问用户的值, 也可以由``必填但没有值''的字段决定.

（旧稿里的）：内容由 agent 讲，控件由服务器授权。
```

### The Prototype

```latex
从这里开始讲的是具体实现，只对这个原型成立.
这个原型正是为了解决我们在引言中提出的问题, 一个通过 Agent 订餐的场景.
我们包含两个单独运行的部分, 一个通用的客户端和一个订餐的站点.
如图2所示, 用户通过客户端来完成各种任务, 而客户端和站点之前只有 HTTP 请求.
(signpost 一下)
```

### The site side

```latex
订餐是一类经典的场景, 即在不同的状态下能执行的操作不相同, 以及定金和取消期等特殊的规则.
我选择了这个任务作为原型的站点, 主要是因为它有足够的复杂性, 但又不需要读者有领域知识就能判断任务做对没有.

如图三所示, 这是整个网站所有的 9 种状态和转移.
在表格中展示了每个状态的详细信息，以及每个状态下页面上有哪些控件，哪些控件刻意不出现.

整体上是一个比较朴素的站点, 顾客需要浏览, 预订等.
此外, 我们还设计了五条规则(如表):
大桌子需要付定金, 24小时的取消窗口, 预点菜窗口等, 我们的问题设计很大程度上依托这些规则.
```

### The client contract

```latex
客户端是一个用户主要和 Agent 沟通的载体.
为了做到通用, 客户端需要满足的是对站点的 blindness, 也就是只通过页面上声明的东西来理解控件.
如果需要支持另外的超媒体格式, 只需要将 parser 作为一种插件来理解, 就可以产出相同形状的控件.
对于我们的原型, 客户端不带任何会话信息, 也不保存 cookie, 所以在原型里不是问题, 但真实多用户网站需要客户端像浏览器一样保存 cookie.
```

