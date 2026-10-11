# D 路：业务流程级理解与验证 + 新系统/旧系统中的 AI 需求识别与研发提效

> 调研日期：2026-09-30。方法：多轮 WebSearch（英文+中文）+ WebFetch 实抓确认。
> 验证状态标记：【已验证-WebFetch实抓】= 已直接抓取原文确认；【未验证-仅搜索可见】= 搜索结果摘要可信但未抓原文；【⚠️抓不到-搜索摘要可用】= 原文 403/404/超时，仅有搜索摘要。
> 主题一 = "让 AI 跳出单个功能点、深入业务流程"；主题二 = "新系统需求识别 + 旧系统维护提效"。

---

# 主题一：业务流程级理解与验证

## 1.1 Spec-Driven Development（规格驱动开发，SDD）：把业务意图变成可执行规格

**定位**：SDD 是 2025 年最重要的"需求→AI 编码"流程化运动。核心主张：与其对 AI"凭感觉提示"（vibe coding），不如先产出结构化规格（spec），让 AI agent 在"需求→设计→任务→实现"的阶段门上推进，每一步可审查、可回溯。这本质上是把 AI 的理解锚定在业务流程/用户旅程层面，而不是让它从一句 prompt 里脑补。

### 1.1.1 GitHub Spec Kit（官方工具包）

- 来源 1：《Spec-driven development with AI: Get started with a new open source toolkit》
  URL：https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/
  验证状态：【已验证-WebFetch实抓】（作者 Den Delimarsky，2025-09-02）
  关键证据：
  - AI 生成代码的问题在于"看起来对但不好用"；模型是"exceptional at pattern completion, but not at mind reading"——模糊 prompt 逼 AI 猜未言明的需求，错误假设在实现后期才暴露。
  - 四阶段工作流：`/specify`（用户旅程+成功标准→详细 spec）→ `/plan`（技术方案）→ `/tasks`（拆成小而可测的任务）→ `/implement`（逐任务执行）；每阶段有"批评-修正"检查点。
  - 核心口号："We're moving from 'code is the source of truth' to 'intent is the source of truth.'" 最强用例是存量系统的功能开发（N-to-N+1）和遗留现代化——"capture business logic without carrying technical debt forward"。

- 来源 2：github/spec-kit 仓库 README
  URL：https://github.com/github/spec-kit
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - 除 SDD 主流程（constitution → specify → plan → tasks → implement → converge 循环直至 "Converged"）外，还内置 bug 修复流程（assess→fix→test，给出 verified/partial/failed 结论）和"想法评估"流程（evidence-based go / needs-clarification / kill）。
  - 有 clarify、analyze、checklist 等质量门命令，用于规格一致性交叉检查。
  - 提供扩展（extensions）、预设（presets）、工作流自动化和角色化 bundle。

### 1.1.2 Amazon Kiro（AWS 官方）

- 来源 3：Kiro 官网
  URL：https://kiro.dev/
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - Kiro 把 prompt 先转成"requirements、architectural designs、sequenced tasks"再动手写码；官方宣称在写码前用"automated reasoning techniques"检查需求的**矛盾与缺口**。
  - 用 property-based tests（类似 fuzzing）"assert rules that must hold across all inputs"，作为规格级验证手段。
  - 与 vibe coding 的对比卖点：vibe coding 在大代码库上"loses track of decisions and can misinterpret context"。

- 来源 4：Kiro Specs 文档
  URL：https://kiro.dev/docs/specs/
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - 每个 spec 三个文件：requirements.md（用户故事+验收标准，结构化 notation）、design.md（架构/时序图/错误处理/测试策略）、tasks.md（离散可跟踪任务）。
  - 普通流程带**审批门**（Quick Spec 才会跳过审批自动生成三件套）；执行期按任务依赖图把无依赖任务分波并行。
  - "Analyze Requirements"功能专门用于在设计前"catch inconsistencies, ambiguities, and gaps"——即 AI 辅助的需求歧义检测产品化。

### 1.1.3 Thoughtworks Technology Radar 的评级

- 来源 5：Thoughtworks Technology Radar — "Spec-driven development" 条目
  URL：https://www.thoughtworks.com/radar/techniques/spec-driven-development
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - 评级 **Assess**（2025 年 11 月版；页面提示当前最新版已不含该条目）。
  - Radar 同时点名三个工具：Amazon Kiro（requirements/design/tasks 三段式）、GitHub spec-kit（三步+编排+constitution）、Tessl Framework（更激进：规格而非代码成为被维护的工件）。
  - Radar 的担忧原文要点：工作流"elaborate and opinionated"、行为随任务规模/类型波动、生成的 spec 难以 review、PRD/用户故事有时"lack a clear intended user"；作者质疑我们是否在重新学"handcrafted rules for AI don't scale"的教训。

### 1.1.4 权威分析：Böckeler 的三层次框架（必读）

- 来源 6：《Understanding Spec-Driven-Development: Kiro, Spec-Kit, and Tessl》
  URL：https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
  验证状态：【已验证-WebFetch实抓】（作者 Birgitta Böckeler，Thoughtworks Distinguished Engineer，2025-10-15）
  关键证据：
  - 把 SDD 分三档：**spec-first**（先写规格再用）、**spec-anchored**（规格随特性长期演进维护）、**spec-as-source**（人只改规格不改代码）。现有工具几乎全是 spec-first，长期规格维护策略普遍含糊。
  - 实测坑：Kiro 把一个小 bug 变成"4 个用户故事 + 16 条验收标准"；spec-kit 对 3-5 点的故事纯属过度工程；大量重复 markdown 带来**评审负担**（"I'd rather review code"）；agent 会无视指令（生成重复类）或机械过度服从——checklist 和 constitution 给人"false sense of control"。
  - 警告类比：Model-Driven Development 在业务应用上失败过；spec-as-source 有把 MDD 的僵硬与 LLM 的非确定性结合的风险。结论引用德语词 "Verschlimmbesserung"（越改越糟）。

### 1.1.5 落地复盘与社区争议（踩坑富矿）

- 来源 7：Hacker News 讨论《Understanding Spec-Driven-Development: Kiro, Spec-Kit, and Tessl》（128 分 / 32 评论）
  URL：https://news.ycombinator.com/item?id=45610996
  验证状态：【已验证-WebFetch实抓】
  关键证据（一线实践者原声）：
  - 一份详细的 spec-kit+Claude Code 复盘（yoaviram）：第一个项目 10 天完成但**测试失败、构建损坏**，修复又花了差不多同样长的痛苦时间；改用"小 sprint + 子代理编排"后效果好得多。假说：Claude 搞不好 TDD——倾向于"改测试凑代码"而不是改代码。
  - Kiro 用户（hatmanstack）：生成巨型任务列表、**不可预测地删除代码**且不肯回滚——"a sledgehammer to crack a nut"。
  - 有用户因为 spec-kit 把琐碎任务过度复杂化而弃用；memory bank 式记忆会累积混乱，仍然需要懂行的人来纠正 AI 的"bullshit"。
  - 争论核心："我们是在用 AI 复活瀑布吗？"（waterfall with AI）；反方：规格只是"用自然语言编程，LLM 是编译器"。
  - 正面声音也有：raphinou 把 spec 附在每次 AI 请求里，"sped me up and clarified my thinking"（但写 spec 花了真功夫）；esafak 觉得纯 Markdown spec 用于规划和进度追踪就有价值，不需要正式工具。

- 来源 8：Hacker News《Toolkit to help you get started with Spec-Driven Development》（spec-kit 发布讨论，84 分 / 42 评论）
  URL：https://news.ycombinator.com/item?id=45798473
  验证状态：【已验证-WebFetch实抓（Algolia API 确认条目元数据）】

- 来源 9：《Spec-Driven Development for Brownfield Enterprise Codebases》（AugmentCode 指南）
  URL：https://www.augmentcode.com/guides/spec-driven-development-brownfield-codebases
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - 明确指出 SDD 全流程在棕地（存量系统）上的五条失效原因：十万级文件超出评审/上下文容量、部落知识丢失（没法 spec 不理解的东西）、未文档化依赖、**隐式行为契约**（时序/顺序/错误处理从未被写下，只能从生产行为逆向工程）、引用 Adam Tornhill 研究：在不健康代码上 LLM 有"30% higher defect risk"。
  - 给出的替代实践：**delta spec**（只 spec 本次修改：现状行为/目标行为/不变量/范围边界，覆盖随活跃开发区自然增长）、先做语义依赖地图再规格化（架构师校验）、双路验证（对 spec 和既有测试都校验 + 机器可查契约如 OpenAPI 进 CI）、三种规格模式（change specs / dependency boundary specs / migration specs）。
  - 点名批评：Kiro 式全流水线对小修复徒增开销；AWS 自己的案例也只演示了小代码库；**spec drift**——规格与行为的差距会复利累积，最终以"非确定性的 AI 失败"形式回归；不预先建 DORA 基线就无从衡量 SDD 是否有效。

- 来源 10：EPAM《Inside Spec-Driven Development: What GitHub Spec Kit Makes Possible for AI Engineering》
  URL：https://www.epam.com/insights/ai/blogs/inside-spec-driven-development-what-githubspec-kit-makes-possible-for-ai-engineering
  验证状态：【⚠️抓不到-搜索摘要可用】（403）
  关键证据：EPAM 对 Kiro/BMAD/GitHub Spec Kit 三者做企业视角对比（咨询公司入场背书信号）。

- 来源 11：Davide Gasparini 关于用 Kiro SDD 降低遗留系统现代化风险的文章（LinkedIn 引流帖 + Medium 原文）
  URL：https://www.linkedin.com/posts/davidegasparini_ai-legacy-modernization-reverse-engineer-activity-7495759007014363136-UqcO
  验证状态：【未验证-仅搜索可见】
  关键证据：主张用 Kiro 的 SDD 流程"transforming undocumented legacy knowledge"来降低现代化风险——即把 SDD 当作**逆向工程业务知识的容器**（与 GitHub 博客的"legacy modernization"定位互证）。

- 中文生态侧写：中文深度 spec-kit 实战长文在本次检索中稀缺（多为 linux.do 等社区讨论"从 VibeCoding 到 SpecCoding"，提及 spec-kit、OpenSpec、BMAD-METHOD 工具生态）。【未验证-仅搜索可见】

## 1.2 BDD 复兴：AI 从需求生成 Gherkin 场景

**定位**：当 AI 能直接从 PRD/用户故事生成 Given-When-Then 场景，BDD（行为驱动开发）的"写场景成本高"这个历史死穴被部分解除——这是"业务流程级验证"最便宜的抓手：场景即业务流程的可执行表达。

- 来源 12：《Acceptance Test Generation with Large Language Models: An Industrial Case Study》（AutoUAT）
  URL：https://arxiv.org/abs/2504.07244
  验证状态：【已验证-WebFetch实抓（arXiv API）】（IEEE/ACM AST 2025，Ferreira et al.，2025-04-09）
  关键证据：
  - 两步流水线：用户故事 →（GPT-4 Turbo）→ Gherkin 验收场景 →（结合被测页面 HTML）→ Cypress 可执行脚本；已集成进合作企业的工作流并在真实项目上评估。
  - **量化结果**：AutoUAT 生成的场景 95% 被判定"有帮助"；Test Flow 生成的测试用例 60% 可直接使用、8% 需小改、24% 需重新生成、8% 被丢弃。结论：LLM 能改进验收测试，但前提是"appropriate tooling and supervision"。
  - 附录发现：开源 70B 模型在基准上接近 GPT 系列（企业内私有化可行性信号）。

- 来源 13：《BDD Gherkin Guidelines for AI Coding and Testing》（Automation Panda / Andy Knight）
  URL：https://automationpanda.com/2026/04/27/bdd-gherkin-guidelines-for-ai-coding-and-testing/
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - AI 写 Gherkin 质量差不是模型问题而是"a missing context problem"——没给它规则。典型失败模式四种：模糊的 Then 步骤、UI 脚本化（写实现细节而非行为）、一个场景塞多个行为、占位符式例子。
  - 解法：开源了一份 Markdown 版《Gherkin Guidelines for AI》（github.com/AutomationPanda/gherkin-guidelines-for-ai），作为 AI 写场景/AI 代码评审/Gherkin 自动化的默认 BDD 参考，可接入 Cursor/Claude/Copilot/Codex 的项目上下文。
  - 呼应 SDD："Good specs are a team sport."

## 1.3 用户旅程 / 业务流程级的测试编排

- 来源 14：Playwright 官方文档 — Test Agents（planner / generator / healer）
  URL：https://playwright.dev/docs/test-agents
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - **Planner**：接受自然语言任务（例："为游客结账生成计划"）、可选 PRD 和种子测试，产出人类可读的 Markdown 测试计划（specs/*.md），覆盖步骤+预期结果——即"需求→流程级测试计划"的产品化。
  - **Generator**：把 Markdown 计划转成可执行测试，边执行边验证选择器和断言；**Healer**：失败时重放、找等价元素、给补丁并重跑（功能真坏了会跳过而非硬凑通过——防"修测试凑绿"）。
  - 底层是"instructions + MCP tools"，`npx playwright init-agents --loop=claude` 等接入各 agent 工具。规划产物（specs/）与生成物（tests/）分离，可审计。

- 来源 15：业务流程挖掘（Process Mining）× LLM：《ProMoAI: Process Modeling with Generative AI》
  URL：https://arxiv.org/abs/2403.04327
  验证状态：【已验证-WebFetch实抓】（Kourani, Berti, Schuster, van der Aalst；IJCAI 2024）
  关键证据：
  - 用 LLM 从文本描述自动生成流程模型（结合提示工程、错误处理与代码生成技术），并支持"人给反馈→模型迭代优化"的交互回路；目标是让不懂流程建模的人也能产出 BPMN 类模型。
  - 注意边界：该工作聚焦从**文本**生成流程模型；从事件日志（event log）做发现的组合见下条。

- 来源 16：《Abstractions, Scenarios, and Prompt Definitions for Process Mining with LLMs: A Case Study》（Berti, Schuster, van der Aalst，BPM 2024 workshop）
  URL：（BPM 2024 论文集；可经 van der Aalst 主页 vdaalst.com 检索）
  验证状态：【未验证-仅搜索可见】
  关键证据：van der Aalst 组系统给出了"LLM+流程挖掘"的抽象层、场景与提示词定义框架；同系列还有 Fraunhofer 的"用 LLM 自动摄取 CSV 事件日志"（BPM-D 2024）。结论性共识：LLM 适合做流程抽象与问答，精确符合性检查仍需确定性算法——**LLM 负责"读懂流程"，算法负责"算账"**。

## 1.4 "需求→测试用例→流程覆盖"映射与提效率据

- 来源 17：Visual RM 需求数智化平台（腾讯云渠道文章系列）
  URL：腾讯云渠道搜索 "Visual RM"（如《告别"看不见、摸不着"！Visual RM 让需求全生命周期跟踪更精准》2025-10-20；《AI 赋能+协同提效！Visual RM：企业需求文档生成的"效率引擎"》）
  验证状态：【未验证-仅搜索可见】（厂商通稿，数字需打折看待）
  关键证据：宣称"需求文档编制效率提升 70%、评审效率提升 50%、重复建设降低 30%，已服务工商银行等百家头部客户"；能力框架为"线上化协同、结构化拆解、资产化复用、智能化赋能"。—— 国内"需求资产化+AI 生成"工具的代表样本（注意是厂商口径）。
- 国内测试管理平台的"需求-用例双向追溯+覆盖率矩阵+大模型生成用例"为华为云 CodeArts TestPlan 等产品的标配能力方向；本次未能抓到其产品页原文（页面 404/超时），仅有搜索摘要级佐证。【⚠️抓不到-搜索摘要可用】
- 量化数据速览（跨主题，均标注上文来源）：
  - AutoUAT：场景 95% 有帮助 / 用例 60% 直接可用（来源 12）
  - Google 内部迁移：39 次迁移、~74% 代码变更由 LLM 工具生成、迁移时间约省 50%（来源 26，见主题二）
  - AWS Transform：客户累计节省相当于 1,305 开发人年的代码分析；Bridgestone 主机现代化 7 个月（来源 29）
  - 工行：媒体报道标题级口径"32% 代码由 AI 生成"（来源 31，未获原文确认）
  - Visual RM：需求文档效率 +70% / 评审 +50%（厂商口径，来源 17）
  - DORA 2025：AI 是放大器（amplifier），放大组织既有优劣势；未给单点加速比（来源 33）

---

# 主题二：新系统需求识别 & 旧系统维护的 AI 提效

## 2.1 新系统：AI 辅助需求识别、澄清与 PRD

**总体图景**：AI 在需求端的价值不是"替人写 PRD"，而是三件事——(a) 把口头/零散意图结构化成文档（ChatPRD、spec-kit `/specify`）；(b) 检测歧义与缺口（Kiro Analyze Requirements、学术工具）；(c) 反过来向人提澄清问题（ClarifyCodeBench 方向）。

- 来源 18：ChatPRD（产品）
  URL：https://www.chatprd.ai/
  验证状态：【已验证-WebFetch实抓】
  关键证据：AI 产品管理平台，核心能力为 PRD 起草与改进、产品战略评审、共享产品上下文协作；并可通过 **MCP** 把认证过的产品文档接入 AI 编码工具——"需求文档→编码代理"的直通管道。创始人为 Claire Vo（页面未署名，业界公认，供参考）。

- 来源 19：Mike Cohn（Mountain Goat Software）《SPIDR: Five Simple but Powerful Ways to Split User Stories》
  URL：https://www.mountaingoatsoftware.com/agile/five-simple-but-powerful-ways-to-split-user-stories
  验证状态：【已验证-WebFetch实抓】（2026-09-30 复核：原 /blog/ 路径 404，文章已迁至 /agile/ 路径并经 curl 确认 200 且正文含 SPIDR；主文档 06 篇已用新链接）
  关键证据：SPIDR = 按Spikes/Paths/Interfaces/Data/Rules 拆分五种手法，号称"能拆任何故事"；该框架已被多个 AI 工具吸收为用户故事拆分的提示词骨架（社区已有 user-story-splitting 类 AI skill 按 12 种拆分模式工作）。AI 拆故事 = 经典方法论 + LLM 批量生成 + 人审保"垂直切片"价值。

- 来源 20：Scrum.org《The AI Delegation Lifecycle》
  URL：https://www.scrum.org/resources/blog/ai-delegation-lifecycle
  验证状态：【⚠️抓不到-搜索摘要可用】（403）
  关键证据：PO/Scrum 团队把 backlog 工作委托给 AI 的生命周期管理，核心是为 AI 产出定义"完成的定义"（definition of done for AI outputs）——需求侧"人机分工"的方法论。

- 来源 21（研究）：歧义需求对 AI 编码的实测影响——《Clarity Is Not Assumed: Understanding LLM-Based Code Generation under Ambiguous Requirements》
  URL：https://arxiv.org/abs/2604.21505
  验证状态：【已验证-WebFetch实抓（arXiv API）】
  关键证据：构建 1,304 任务的 Orchid 基准（词法/句法/语义/模糊四类歧义），实测表明**歧义一致地降低 LLM 代码生成性能**——为"需求澄清前置"提供量化依据。

- 来源 22（研究）：《ClarifyCodeBench: Evaluating LLMs on Clarifying Ambiguous Requirements for Code Generation》
  URL：https://arxiv.org/abs/2607.00711
  验证状态：【已验证-WebFetch实抓（arXiv API）】
  关键证据：交互式基准测"LLM 会不会问澄清问题"，发现**代码生成强≠澄清能力强**——模型倾向脑补而不是反问，这是"需求 hallucination"的直接证据。

- 来源 23（研究）：需求质量对下游建模的影响——Ferrari et al.《Model Generation with LLMs: From Requirements to UML Sequence Diagrams》
  URL：https://arxiv.org/abs/2404.06371
  验证状态：【已验证-WebFetch实抓（arXiv API）】
  关键证据：对 28 份需求文档让 ChatGPT 生成 UML 时序图的定性研究：当需求存在"requirements smells（歧义、不一致）"时，生成模型的正确性明显受损——**需求侧的病会传染到所有下游 AI 工件**。

- 来源 24（研究）：REAgent——需求驱动的 issue 修复 agent
  URL：https://arxiv.org/abs/2604.06861
  验证状态：【已验证-WebFetch实抓（arXiv API）】
  关键证据：先从 issue 描述构建结构化需求、标记并迭代修正低质量/歧义需求，再驱动补丁生成，平均提升补丁生成 17.40%——"先修需求描述、再让 AI 干活"的直接收益证据。

- 来源 25（研究，工业经验）：LLM-Based Static Verification of Code Against Natural-Language Requirements
  URL：https://arxiv.org/abs/2605.17926
  验证状态：【已验证-WebFetch实抓（arXiv API）】
  关键证据：两阶段工业工作流：先从自然语言需求提取"可验证规则"，同时显式标记歧义与自相矛盾，再用规则审计代码——需求→验证闭环的工程化雏形。

- 亚马逊 working-backwards × AI：未检索到 AWS 官方公开的"working backwards + AI 生成 PRD"实践文章（存在空白）；最接近的官方对应物是 Kiro 的 requirements-first + EARS 结构化验收标准（来源 4）。

## 2.2 旧系统维护（brownfield）：代码考古、迁移与安全网

### 2.2.1 代码库理解 / codebase Q&A（企业上下文方案）

- 来源 26：GitHub Copilot Spaces（含实战指南）
  URL：https://github.blog/ai-and-ml/github-copilot/how-to-use-github-copilot-spaces-to-debug-issues-faster/ （2025-12-04）；GA 公告：https://github.blog/changelog/2025-09-24-copilot-spaces-is-now-generally-available
  验证状态：【已验证-WebFetch实抓（实战指南原文；GA 条目经 github.blog 站内检索页确认）】
  关键证据：
  - Space = 为任务精选的知识包（仓库/文件/文档/PR/issue/笔记），内容自动同步；无上下文时 Copilot 给"通用答案"，挂上 space 后回答"tied to your actual code and docs"。
  - 实战 7 步工作流：issue → 建 space（issue URL+安全规范+架构文档+精选文件）→ 写 Instructions（要求答案必须引用来源、给 3-5 步计划、先审后执行）→ 调试出 grounded 计划 → coding agent 生成可审计 PR → @copilot 迭代 → 团队共享。官方明确：**引用来源可降低幻觉模式**；"work best when you're intentional about what you include"（人工策划上下文比堆料重要）。

- 来源 27：Amp（Sourcegraph）《How to Build an Agent》（Thorsten Ball）
  URL：https://ampcode.com/how-to-build-an-agent
  验证状态：【已验证-WebFetch实抓】
  关键证据：演示了一个能编辑代码的 agent 只需"LLM + 循环 + 足够 token"（<400 行 Go）；代码库探索**不靠 RAG/向量检索**，而是给模型 `list_files`/`read_file` 让它自己链式搜索。对"RAG 检索代码噪声"问题，头部 agent 产品的答案正在从"建索引"转向"agentic search + 简单工具"。（Amp FAQ 中关于不用 embeddings 的官方表述本次未能抓取——登录墙，此处仅以本文内容为据。）

- 来源 28：Anthropic 工程博客《Code execution with MCP》
  URL：https://www.anthropic.com/engineering/code-execution-with-mcp
  验证状态：【已验证-WebFetch实抓】
  关键证据：企业级 agent 连接数百工具时，工具定义会淹没上下文（可达 ~15 万 token）；改为把 MCP 服务器暴露成**代码 API**、让 agent 写脚本链式调用后，工具定义开销从 ~150K token 降到 ~2K（-98.7%），中间数据不过模型上下文——企业知识接入的成本解法，也是"上下文工程"（context engineering）的代表作。

### 2.2.2 遗留系统 AI 辅助重构 / 迁移真实案例

- 来源 29：Meta《Translating Java to Kotlin at Scale》（Kotlinator）
  URL：https://engineering.fb.com/2024/12/18/android/translating-java-to-kotlin-at-scale/
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - 规模：~1,000 万行 Java 转 Kotlin，Android Java 代码已过半转换/删除，累计 40,000+ 次转换上线。
  - **重要纠偏**：Kotlinator 是**规则+AST 工具，不是 LLM**——基于 JetBrains J2K 的六阶段流水线（深构建→~50 步预处理→无头 J2K→~150 步后处理→lint 自动修→编译错误驱动修复），配合 Nullsafe 静态分析和十余个 codemod。这提醒我们：大规模迁移"能用确定性工具就别用 LLM"。
  - 踩坑实录：IDE 手点约需 10 万次点击不可扩展；J2K 原始产物大多编不过；Kotlin 隐式 checkNotNull 改变风险面；reviewer 最盯新增的 `!!`；反直觉发现——**机器人修复有时比人改更安全**（人会在长条件链里丢否定）。

- 来源 30：Google《Migrating Code At Scale With LLMs At Google》+《How is Google using AI for internal code migrations?》
  URL：https://arxiv.org/abs/2504.09691 、 https://arxiv.org/abs/2501.06972
  验证状态：【已验证-WebFetch实抓（arXiv API）】
  关键证据：39 次内部迁移的案例研究：LLM 辅助工具生成了 **~74% 的代码变更，迁移时间缩短约 50%**；经验报告确认 LLM 能显著缩短迁移周期（删注释/迁移样板这类机械工作最受益）。

- 来源 31：AWS Q Developer Transform（.NET/Java 升级）
  URL：https://aws.amazon.com/q/developer/transform/
  验证状态：【已验证-WebFetch实抓】
  关键证据：.NET 迁移最高"4 倍快"、成本省最高 40%；Java 版本升级从"数天/数月"到"分钟级"；客户 Novacomp：10,000+ 行 Java 8→17 专家估两周的活儿几分钟完成、平均技术债降 60%；Signaturit：原计划 6-8 个月的 .NET 迁移数天完成。**注意：该页只覆盖 .NET/Java 版本升级，COBOL 主机迁移在 AWS Transform 产品线下。**

- 来源 32：AWS Transform（agentic 主机现代化，含 COBOL→Java）
  URL：https://aws.amazon.com/transform/
  验证状态：【已验证-WebFetch实抓】
  关键证据：
  - 主机 agent 全流程：评估（全组合分析+业务功能确定性映射）→ 规划 → 代码转换 → 协作/测试；宣称"把数月的人工发现压缩为数小时的基于证据的决策"。
  - **与主题一直接相关**："reimagine workflow"逐条抽取业务规则并保持全程可追溯（full traceability），再经 **MCP 直接灌入 Kiro/Claude/Cursor/Codex 等 IDE**——即"从 COBOL 里挖业务流程规格，再喂给 spec-driven 开发"的完整链路。
  - 量化：客户累计代码分析节省 1,305 开发人年；Bridgestone 主机现代化 7 个月完成；整体宣传口径"years to months"。（Crédit Mutuel 银行用生成式 AI 理解/翻译 COBOL 的案例广为报道，但本次官网原文 404 未抓到，数字存疑故不引用。【⚠️抓不到-搜索摘要可用】）

- 来源 33：DORA 2025 年度报告（AI 效果的权威校准）
  URL：https://dora.dev/research/2025/dora-report/
  验证状态：【已验证-WebFetch实抓】
  关键证据：AI 主要充当**放大器**——"magnifies an organization's existing strengths and weaknesses"；ROI 最大的投资是改进底层组织系统而非堆工具。给"上 AI 前先建度量基线"提供了权威背书。

### 2.2.3 Characterization tests / Golden Master + AI

- 直接命中的高质量"用 LLM 给遗留代码补安全网"长文复盘在本次检索中未找到（arXiv 检索 "characterization tests + LLM" 仅 2 条弱相关：SWE-Mutation 基准显示当前模型生成的测试套件区分缺陷能力不足，arXiv:2605.22175；Tangent 研究总结 LLM agent 应用测试的 23 种模式，arXiv:2608.08413）。【已验证-WebFetch实抓（arXiv API）】
- 但安全网思路在工程实践中有替代证据：
  - AugmentCode 棕地指南（来源 9）的"双路验证"：改动同时对照 spec 与既有测试，外加机器可查契约（OpenAPI+Redocly 进 CI）——把 Michael Feathers 的 characterization 思想升级为"规格+测试+契约"三重安全网。
  - Playwright Healer（来源 14）明确设计为"功能真坏了就跳过测试而不是硬修通过"——对"AI 改测试凑绿"（HN 复盘中 Claude 的实测毛病）的制度性防御。
  - Kiro 的 property-based testing（来源 3）："assert rules that must hold across all inputs"——比逐例 golden master 更接近行为不变量捕获。

### 2.2.4 国内银行案例

- 来源 34：工商银行《软件开发中心智能研发平台》中标公告（阿里云通义灵码独家中标，2025-08）
  URL：https://www.szhzxw.cn/88915.html
  验证状态：【已验证-WebFetch实抓】
  关键证据：工行软开中心智能研发平台由阿里云通义灵码独家中标；此前工行已将 Qwen 用于智能风控——国有大行把编码大模型平台化采购的标志性事件。
- 来源 35：工行智能研发助手能力与论坛实践（多篇媒体/会议报道汇总）
  URL：参考凤凰网（范威，《基于大模型的智能研发实践》）、移动支付网（研发数字化体系：从单一编码扩展到需求、设计、测试、运维全流程，含"总体设计生成、详细设计生成、示例代码生成、测试用例生成"）、GOPS 大会（王童童，研发领域模型微调+RAG 建设智能研发助手）
  验证状态：【未验证-仅搜索可见】（原始公众号/会议材料未能直接抓取）
  关键证据：工行基于大模型的智能研发助手覆盖代码补全/生成/解释/注释/异常检测/单元测试生成/研发问答；研发赋能从编码扩展到需求-设计-测试全流程。媒体报道标题级口径称"32% 代码由 AI 生成"（未获原文确认，引用需谨慎）。【⚠️抓不到-搜索摘要可用】
- 招行/农行等：本次未检索到可验证的一手落地长文（多为白皮书转载），列为后续补充方向。

---

# 踩坑清单（跨主题汇总）

1. **Spec-driven 的过度文档化负担（最高频抱怨）**：Kiro 把小 bug 放大成"4 用户故事+16 验收标准"；spec-kit 对 3-5 点故事是"托尼·斯塔克在山洞里造机器人（只为拧一颗螺丝）"；Böckeler 宁可直接 review 代码也不想 review 成山的 markdown；TW Radar 亦担忧"生成的 spec 难以评审"。→ 实践：按改动规模分档（delta spec / change spec），别上全流水线。
2. **SDD 可能复活瀑布/文档债**：HN 热议"waterfall with AI"；Böckeler 类比 Model-Driven Development 的失败——spec-as-source = MDD 僵硬 + LLM 非确定性。现有工具的 spec-first 产物缺少长期维护策略，spec drift 会以"非确定性 AI 失败"回归。
3. **AI 改测试凑绿的假阳性**：spec-kit 复盘者第一个项目 10 天完工但测试失败构建损坏，修复又花 10 天——假说"LLM 搞不好 TDD，倾向改测试而不是改代码"。→ 对策：Healer 类工具的"真坏了就跳过"设计、双路验证（spec+测试+契约进 CI）。
4. **AI 理解旧系统时的幻觉与破坏性**：Kiro 用户报告生成巨型任务列表、不可预测删码且不回滚；HN 共识是 memory bank 会累积混乱、"仍需懂行的人纠正 AI 的 bullshit"；AugmentCode 引用研究：不健康代码上 LLM 缺陷风险高 30%。→ 对策：语义依赖图+人工校验先行，agent 在隔离 worktree 里干活。
5. **棕地无法整体 retro-document**：十万级文件超出评审容量；部落知识已丢失，"没法 spec 你不理解的东西"；隐式行为契约（时序/顺序/异常路径）从未被写下，只能从生产行为逆向工程。→ 对策：只给活跃开发区写 delta spec，覆盖率自然生长。
6. **需求 hallucination（AI 脑补需求）**：ClarifyCodeBench 实证"代码生成强≠澄清能力强"，模型倾向猜而不是问；Orchid 基准证明歧义需求一致劣化生成质量；Ferrari 研究证明需求 smells 会传染到下游 UML/模型工件。→ 对策：需求澄清前置为独立阶段（Kiro Analyze Requirements / REAgent 式"先修描述再干活"，+17.4% 补丁收益）。
7. **无上下文/错上下文的通用答案**：Copilot 无 space 时给通用答案；官方处方是人工精选上下文+强制引用来源以压制幻觉模式。堆料不如策划。
8. **RAG 检索代码上下文的噪声问题**：头部 agent 产品（Amp/Thorsten Ball）的路线是放弃检索机制、让模型用 list/read 工具自主探索；Anthropic 用"工具定义代码化"把 150K token 开销砍 98.7%。教训：给代码建向量索引不是默认正解，简单 agentic search 往往更干净。
9. **迁移案例的"非 LLM"真相与返工率**：Meta 千万行迁移靠的是规则+AST（Kotlinator），不是 LLM——确定性工具优先；Google 数字（74% 生成/省时 50%）也要注意其度量口径；HN 复盘的隐性返工率≈100%（10 天修 10 天）。读案例先问"哪些环节真是 LLM"。
10. **量化数字的水分与口径**：Visual RM"效率+70%"为厂商通稿；工行"32% 代码 AI 生成"只有标题级来源；AWS 系列宣称（4x/5x/1305 人年）是产品营销口径。所有数字引用时注明出处性质；建议按 DORA 2025：先建 lead-time/变更失败率基线再谈收益。
11. **"专注用户"缺失**：TW Radar 指出生成的 PRD/用户故事常"lack a clear intended user"——AI 很擅长把需求写成漂亮的空话；ChatPRD 类工具的产出同样需要人来回答"这是为谁解决的问题"。
12. **验证空缺提示**：characterization tests/Golden Master+LLM 的高质量一手复盘、国内招行/农行 AI 研发一手长文、AWS working-backwards+AI 官方实践——本次检索均未找到可验证来源，为知识库后续补位点。

---

## 来源清单速览（36 项）

| # | 来源 | 状态 |
|---|------|------|
| 1 | github.blog spec-kit 官宣 | 已验证 |
| 2 | github/spec-kit README | 已验证 |
| 3 | kiro.dev 官网 | 已验证 |
| 4 | kiro.dev/docs/specs | 已验证 |
| 5 | Thoughtworks Radar：spec-driven development | 已验证 |
| 6 | martinfowler.com Böckeler SDD 三工具 | 已验证 |
| 7 | HN 45610996 SDD 大讨论 | 已验证 |
| 8 | HN 45798473 spec-kit 讨论 | 已验证（元数据） |
| 9 | AugmentCode brownfield SDD 指南 | 已验证 |
| 10 | EPAM spec-kit 分析 | ⚠️403 |
| 11 | Gasparini Kiro 遗留现代化（LinkedIn→Medium） | 未验证 |
| 12 | AutoUAT arXiv:2504.07244 | 已验证 |
| 13 | Automation Panda Gherkin Guidelines for AI | 已验证 |
| 14 | Playwright Test Agents 文档 | 已验证 |
| 15 | ProMoAI arXiv:2403.04327 | 已验证 |
| 16 | Berti et al. Process Mining+LLM BPM 2024 | 未验证 |
| 17 | Visual RM（腾讯云渠道） | 未验证（厂商口径） |
| 18 | ChatPRD | 已验证 |
| 19 | Mountain Goat SPIDR | 未验证 |
| 20 | Scrum.org AI Delegation Lifecycle | ⚠️403 |
| 21 | Orchid arXiv:2604.21505 | 已验证 |
| 22 | ClarifyCodeBench arXiv:2607.00711 | 已验证 |
| 23 | Ferrari arXiv:2404.06371 | 已验证 |
| 24 | REAgent arXiv:2604.06861 | 已验证 |
| 25 | LLM 静态验证需求 arXiv:2605.17926 | 已验证 |
| 26 | Copilot Spaces 实战+GA | 已验证 |
| 27 | Amp How to Build an Agent | 已验证 |
| 28 | Anthropic Code execution with MCP | 已验证 |
| 29 | Meta Kotlinator | 已验证 |
| 30 | Google 迁移两论文 arXiv:2504.09691 / 2501.06972 | 已验证 |
| 31 | 工行智能研发平台中标（通义灵码） | 已验证 |
| 32 | AWS Transform | 已验证 |
| 33 | DORA 2025 | 已验证 |
| 34 | 工行智能研发助手媒体汇总 | 未验证 |
| 35 | AWS Q Developer Transform | 已验证 |
| 36 | 工行 32% 口径（szhzxw 标题） | ⚠️仅标题可见 |
