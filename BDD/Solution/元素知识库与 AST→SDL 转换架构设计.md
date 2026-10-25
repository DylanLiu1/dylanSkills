# 元素知识库与 AST→SDL 转换架构设计

# 执行摘要

**结论：**当前应推进的方向是——以「确定性优先、AI 兜底」为原则，搭建以 **PostgreSQL \+ pgvector** 为载体的元素知识库，作为 AST→SDL 转换的事实底座。Gherkin→AST 保持确定性解析，AI 只介入 AST→SDL 中最难的一环（业务对象到真实 UI 元素的映射），且被约束为「选择题」而非「翻译题」。

**依据：**

- Gherkin→AST 由 @cucumber/gherkin 官方解析器确定性完成，全链路唯一需要 AI 的环节是 AST→SDL 的步骤语义映射。

- 元素知识库以「业务别名 \+ 多策略定位器 \+ 行为 \+ 执行反馈」四层数据覆盖映射需求，解决 AI 幻觉（编造 selector）这一核心风险。

- PostgreSQL \+ pgvector 关系与向量一体，同一张表同时支撑精确匹配（别名、模板）与语义检索（Top\-K 召回），避免双库数据一致性问题。

- 三级决策（L1 规则匹配 → L2 语义检索 → L3 LLM 兜底）把 AI 限制在最内层，常见步骤零成本直出，schema 校验 \+ 回退重试保证输出可控。

**约束：**知识库质量依赖 verified 纪律（AI 产物默认 pending，验证后才允许直出 SDL）与执行反馈闭环；冷启动阶段需投入人工审核，无法完全自动化。

**行动：**按四阶段落地——Phase 0 定 JSON Schema 与表结构，Phase 1 冷启动注入（从现有测试/录制抽取元素），Phase 2 三级决策接入转换管线并灰度放开，Phase 3 反馈闭环（失败回写、定位器自愈）。

# 问题界定：元素知识库解决什么

## 转换链路与唯一 AI 环节

现有链路为：**Gherkin → AST → 自定义 JSON SDL → Playwright 代码**。Gherkin→AST 是确定性解析，SDL→Playwright 已有实现。真正需要 AI 的只有 AST→SDL 这一步，而它的难点不是结构翻译，而是：**把 step 文本中的业务对象（「登录按钮」）映射到真实 UI 元素，并确定动作与参数**。知识库的职责就是让这个映射有事实依据、可检索、可验证、可自我修复。

## 知识库承载的四类数据

|实体|回答的问题|示例|
|---|---|---|
|**page**|元素属于哪个页面/模块|登录页、结算页|
|**element**|业务对象对应的真实元素是什么|「登录按钮」→ login\-submit|
|**step\_template**|常见 step 句式对应什么动作|「点击X」→ click；「输入X到Y」→ fill|
|**execution\_feedback**|执行失败后如何修正|定位器失效、别名缺失|

来源：系统设计约束｜口径：实体职责定义

## 边界：不要做成什么

- **纯向量库**：丢关系查询，别名/定位器/反馈天然是关系型数据。

- **只存 selector 的扁平字典**：没有语义层，AI 依然靠猜。

- **整页 DOM 入库**：噪音大于信号，检索质量差。

# 数据库选型

## 候选方案对比

|方案|定位|优点|缺点|结论|
|---|---|---|---|---|
|**PostgreSQL \+ pgvector**|关系 \+ 向量一体|事务、关系查询、向量检索同库；别名/定位器/反馈天然关系型，语义检索靠向量列|需要运维 PG|推荐主选|
|SQLite \+ sqlite\-vec|单机轻量|零运维、可嵌入|并发弱、向量规模受限|MVP 起步可选|
|Qdrant / Milvus|纯向量|海量向量高并发 ANN|关系查询弱，需维护第二套库，一致性要自己做|元素 \>100 万再考虑|
|Elasticsearch|全文 \+ 向量|全文检索强|重、关系弱|不推荐为主库|

来源：业界方案调研｜口径：按数据结构特征与运维成本评估

## 结论：PostgreSQL \+ pgvector

元素解析的典型请求是「step 文本 → 候选元素」，同时需要**精确匹配**（别名完全一致、step 模板正则命中——这部分不该走向量）和**语义召回**（「提交表单」匹配到名为「确认提交」的按钮——走 embedding）。PG 一张表两列即可同时覆盖，并省去双库一致性问题。给 aliases 与 step 文本建 embedding 列，用余弦相似度做 Top\-K 检索。

# 数据格式设计

## 元素实体 JSON Schema

知识库元素记录与 SDL 同源采用 JSON 结构，便于 AI 生成后对齐：

```json
{
  "id": "login_submit_btn",
  "page_id": "login_page",
  "type": "button",
  "name": "登录按钮",
  "aliases": [
    { "text": "登录", "lang": "zh", "source": "manual" },
    { "text": "登录按钮", "lang": "zh", "source": "ai_extract" },
    { "text": "sign in", "lang": "en", "source": "ai_extract" }
  ],
  "locators": [
    { "strategy": "data-testid", "value": "login-submit",  "priority": 1, "confidence": 0.95 },
    { "strategy": "role",        "value": "button", "name": "登录", "priority": 2, "confidence": 0.85 },
    { "strategy": "css",         "value": "#login-form .btn-primary", "priority": 3, "confidence": 0.70 }
  ],
  "behaviors": { "actions": ["click", "wait_visible"], "assertions": ["visible", "enabled"] },
  "gherkin_templates": [
    { "pattern": "点击{登录按钮}", "action": "click", "param_slots": [] }
  ],
  "state": { "status": "verified", "source": "ai_extract", "version": 4, "updated_at": "..." }
}
```

## 设计要点

- **aliases 是灵魂**：业务方/测试人员/AI 对同一元素的不同叫法收敛到这里，这是让 AI 做「选择题」的关键；初始由 AI 从现有测试与页面生成，后续人工补充。

- **locators 多策略 \+ 优先级**：遵循 Playwright 官方最佳实践（role/label/text/testid 优先于 css/xpath），失效时按优先级降级，是自愈的基础。

- **state\.status 三态**：verified（人工确认）/ pending（AI 生成未验证）/ deprecated（失效）。只有 verified 元素允许 L1 精确匹配直出 SDL，pending 只能作为 LLM 参考上下文。

- **枚举约束**：定位器 strategy、动作 action 的枚举在 SDL schema 同步维护，LLM 输出必须过 schema 校验。

# 整体转换架构

## 端到端架构总览

核心是右侧的元素知识库成为转换管线的「共享事实源」，底部执行反馈闭环回写。AI 只出现在 Element Resolver 的最内层：

## Resolver 三级决策

三级递进保证确定性优先：常见步骤零成本直出，AI 仅在歧义场景兜底，且输出被 schema 与检索上下文双重约束：

## 架构要点

- **L1 规则匹配（零成本）**：规范化后的 step 文本与 aliases / step\_template\.pattern 做精确或正则匹配。命中 verified 元素 → 直接产出 SDL，不走 LLM。

- **L2 语义检索**：L1 未命中 → step 文本向量化，从 pgvector 检索 Top\-K 候选元素与模板，附带置信度。高置信且动作可确定 → 直接采用。

- **L3 LLM 决策（兜底）**：置信不足或歧义 → 把检索结果 \+ step 上下文 \+ SDL Schema 交给 LLM，让它「从候选里选并解释」而非凭空创造，输出受 schema 约束的 JSON；校验失败回退重试一次，再失败转人工并标 pending。

- **知识库定位**：是 Resolver 的读源（L2 检索、L3 上下文），也是执行层的写目标（反馈闭环）。每次测试失败都意味着知识库有元素过期或缺失，自动修正后知识库越用越准。

- **为什么 AST 不需要 AI**：@cucumber/gherkin 是官方确定性解析器，Feature/Scenario/Step/Examples 结构化输出是现成的，不在这层引入 LLM。

# 知识库构建：冷启动与持续学习

## 构建管线

## 四路来源与验证门

- **现有资产注入（最高价值，先做）**：解析已有测试代码、Playwright 录制文件、Page Object 文件，把已有 selector 直接抽取入库，立即获得一批真实可信元素。

- **页面扫描**：Playwright 打开目标页面，按可访问性树语义化抽取（role、label、text、testid）生成元素候选。

- **AI 标注**：把元素候选 \+ 页面上下文交给 LLM，生成业务别名、意图标签、行为推导。**所有 AI 产物默认 pending**。

- **验证门纪律**：pending 元素经人工抽查或自动化验证（locator 真实可解析、别名可对应）后转 verified。**只有 verified 才能进 L1 直出路径**，这条纪律保证低风险。

## 持续学习闭环

执行反馈自动修正：定位器按优先级降级尝试 → 成功后回写知识库（定位器修正、失败 step 文本补为别名候选、embedding 更新、version \+1）。知识库随使用不断自我增强，无需周期性人工重建。

# 落地路线与参考

## 四阶段落地

|阶段|内容|验收点|
|---|---|---|
|**Phase 0**|定 JSON Schema \+ PG 表结构（含 pgvector 列），写 schema 校验器|Schema 与校验器可运行|
|**Phase 1**|冷启动注入：从现有测试/录制抽元素，AI 标注，验证入库|核心页面元素 verified 覆盖率达标|
|**Phase 2**|Resolver 三级决策接入转换管线，先只开 L1，逐步放开 L2/L3，新步骤走 AI、老步骤保持确定性|常见步骤直出率与 SDL 校验通过率|
|**Phase 3**|反馈闭环：失败回写、定位器自愈、embedding 更新|同类失败复发率下降|

来源：实施方案规划｜口径：按依赖顺序编排

## 开源参考

- @cucumber/gherkin：Gherkin 官方确定性解析器。

- vitalets/playwright\-bdd：Gherkin→Playwright 的成熟 BDD 实践，step 组织方式可借鉴。

- pw\-element\-repository / smart\-locators\-playwright：元素仓库 JSON 单源模式与多定位器 fallback。

- pgvector/pgvector：PostgreSQL 向量检索扩展。

**两个待拍板事项：**① 现有 JSON SDL 是否已定义「动作枚举」（click/fill/assert 等）与「元素引用方式」（内嵌 locator 还是引用元素 ID）——决定知识库与 SDL 的耦合方式；② 团队能否接受 PostgreSQL 运维，不能则先用 SQLite 起步（schema 不变，后续平滑迁移）。

> （注：部分内容由豆包工作 AI 生成）
