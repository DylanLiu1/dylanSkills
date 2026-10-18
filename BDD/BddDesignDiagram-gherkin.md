
### Gherkin 标准解析底座：gherkin-official（Cucumber 官方出品）

- **官方仓库**：[https://github.com/cucumber/gherkin]
- **Python 包**：`gherkin-official`
- **定位**：Cucumber 团队官方维护的 Gherkin 语言解析编译器，纯解析库，不带执行逻辑
- **核心可复用价值**
  1. 最标准的 Gherkin 语法解析，输出标准 AST 抽象语法树，100% 兼容官方规范，不会出现自定义解析的语法边界问题
  2. 支持命令行直接转 JSON：`gherkin test.feature` 直接输出结构化 ndjson，不用自己写语法解析
  3. 轻量无额外依赖，解析性能极高，专门做「Gherkin → 结构化数据」这一件事
  4. 官方持续维护，支持最新 Gherkin 语法特性
- **适配你的自定义 DSL**
这是**Gherkin 转自定义 JSON DSL 的最佳底座**：用它把 Gherkin 解析成标准 AST 节点，你只需要写一个几十行的转换函数，把 Feature/Scenario/Step 节点映射到你的自定义 DSL 字段即可，完全不用自己写词法、语法解析。
- **适合场景**：你想自己掌控执行引擎，只需要一个标准、稳定的 Gherkin 解析组件