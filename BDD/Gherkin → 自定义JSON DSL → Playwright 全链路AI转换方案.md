# Gherkin → 自定义JSON DSL → Playwright 全链路AI转换方案

# 背景与目标

本方案面向自动化测试平台建设场景，解决「Gherkin 业务用例 → 可执行的测试脚本」之间的工程化转换问题。核心目标是在保留 Gherkin 业务语义的基础上，通过 AI 将其转换为平台自定义的 JSON DSL，再由 JSON DSL 映射到 Playwright 脚本执行，形成从需求到执行的完整链路。

## 要解决的问题

1. **语义鸿沟**：Gherkin 是业务视角的自然语言，Playwright 是技术视角的 API 调用，两者之间缺少可被平台存储、解析、编辑的中间结构。

2. **定位器不可靠**：自然语言中的「登录按钮」无法直接映射到页面元素，AI 直接生成定位器容易出现编造、过期、重名冲突等问题。

3. **转换不可控**：直接让 AI 输出代码，语法错误率高、步骤粒度不统一、难以校验和追溯。

## 核心设计原则

|原则|说明|
|---|---|
|**AI 只做语义转换**|AI 负责需求理解、步骤拆解、动作类型判断与结构生成，不直接产出定位器。|
|**定位器只来自元素库**|所有元素定位信息由元素知识库经工具调用返回，从根源杜绝 AI 编造定位器。|
|**Schema 强约束**|JSON DSL 以 JSON Schema 定义语法边界，结构错误在入库前即被拦截。|
|**多层校验兜底**|结构校验、元素一致性校验、语义一致性校验、定位器预校验四道门禁。|
|**闭环持续优化**|执行结果与人工修正回流知识库，持续提升转换准确率。|

# 整体方案架构

整个链路分为六层：Gherkin 输入 → 预处理解析 → AI 转换引擎 → 多层校验 → 自定义 JSON DSL → Playwright 执行映射，并通过闭环优化机制持续迭代。核心技术选型：AI 引擎采用 AWS Bedrock Claude 3\.5 Sonnet（工具调用能力强、逻辑严谨、遵循指令）；元素检索采用语义向量匹配 \+ 精确页面过滤；规范约束采用 JSON Schema 强校验 \+ 工具调用强制机制；执行底座采用 Playwright 原生 API 映射。

# 第一步：自定义 JSON DSL 语法规范

JSON DSL 是整个体系的核心标准。设计原则：业务语义与执行信息分离、结构化可解析、字段可扩展、直接映射 Playwright。

## DSL 完整结构定义

```json
{
  "dsl_version": "1.0",
  "feature": {
    "id": "string",
    "name": "string",
    "description": "string",
    "module": "string",
    "tags": ["string"]
  },
  "scenarios": [
    {
      "id": "string",
      "name": "string",
      "description": "string",
      "tags": ["string"],
      "steps": [
        {
          "id": "string",
          "type": "Given|When|Then|And|But",
          "page": "string",
          "element": "string | null",
          "action": "string",
          "params": "object",
          "assertion": "object | null",
          "description": "string"
        }
      ]
    }
  ]
}
```

## 核心字段说明与枚举约束

|字段|说明|约束|
|---|---|---|
|**type**|步骤类型，对应 Gherkin 关键字|枚举：Given / When / Then / And / But|
|**page**|步骤所属页面，必须与元素库中的页面名称一致|必填，字符串|
|**element**|操作元素的业务名称，与元素库 business\_name 对应|导航类步骤可为 null|
|**action**|执行动作类型，统一枚举，直接映射 Playwright API|必填，从动作枚举中选择|
|**params**|动作参数，如输入值、等待时长、URL 等|JSON 对象，不同动作对应不同参数|
|**assertion**|断言配置，仅 Then 类型步骤有值|包含 expected\_value、match\_type|

## 标准动作枚举（与 Playwright 一一映射）

|动作分类|枚举值|对应 Playwright 能力|
|---|---|---|
|页面导航|navigate / refresh / go\_back / go\_forward|page\.goto\(\)、page\.reload\(\) 等|
|元素交互|click / fill / clear / hover / select / check|locator\.click\(\)、locator\.fill\(\) 等|
|等待操作|wait\_for\_load / wait\_for\_element / sleep|page\.waitForLoadState\(\) 等|
|文本断言|assert\_text / assert\_text\_contains|expect\(locator\)\.toHaveText\(\)|
|状态断言|assert\_visible / assert\_hidden / assert\_enabled|expect\(locator\)\.toBeVisible\(\)|
|页面断言|assert\_url / assert\_title|expect\(page\)\.toHaveURL\(\)|

## 官方 JSON Schema 校验规范

用于自动化结构校验，确保生成的 DSL 完全符合规范。关键约束：dsl\_version 固定为 1\.0；type 只能取五个 Gherkin 关键字；assertion\.match\_type 只能取 equal / contains / regex。

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["dsl_version", "feature", "scenarios"],
  "properties": {
    "dsl_version": {"type": "string", "const": "1.0"},
    "feature": {
      "type": "object",
      "required": ["id", "name", "module"],
      "properties": {
        "id": {"type": "string"},
        "name": {"type": "string"},
        "description": {"type": "string"},
        "module": {"type": "string"},
        "tags": {"type": "array", "items": {"type": "string"}}
      }
    },
    "scenarios": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["id", "name", "steps"],
        "properties": {
          "id": {"type": "string"},
          "name": {"type": "string"},
          "tags": {"type": "array", "items": {"type": "string"}},
          "steps": {
            "type": "array",
            "items": {
              "type": "object",
              "required": ["id", "type", "page", "action", "description"],
              "properties": {
                "id": {"type": "string"},
                "type": {"enum": ["Given", "When", "Then", "And", "But"]},
                "page": {"type": "string"},
                "element": {"type": ["string", "null"]},
                "action": {"type": "string"},
                "params": {"type": "object"},
                "assertion": {
                  "type": ["object", "null"],
                  "properties": {
                    "expected_value": {"type": ["string", "number", "boolean"]},
                    "match_type": {"enum": ["equal", "contains", "regex"]}
                  }
                },
                "description": {"type": "string"}
              }
            }
          }
        }
      }
    }
  }
}
```

# 第二步：构建页面元素知识库

元素知识库是保证 DSL 可执行的核心基础。所有元素定位器只能来自元素库，绝对不允许 AI 生成。

## 元素库数据结构

按页面维度组织，每个元素包含业务语义、别名、结构化定位器、属性信息：

```json
{
  "pages": [
    {
      "page_id": "page_login",
      "page_name": "登录页面",
      "page_url": "/login",
      "aliases": ["登录页", "系统登录页", "用户登录页"],
      "elements": [
        {
          "element_id": "el_login_phone",
          "business_name": "手机号输入框",
          "aliases": ["账号输入框", "手机号码输入框", "手机号框"],
          "locator": {
            "method": "getByRole",
            "args": ["textbox", {"name": "手机号"}]
          },
          "element_type": "input",
          "precondition": null,
          "is_visible": true
        },
        {
          "element_id": "el_login_submit",
          "business_name": "登录按钮",
          "aliases": ["提交按钮", "登陆按钮", "登录键"],
          "locator": {
            "method": "getByRole",
            "args": ["button", {"name": "登录"}]
          },
          "element_type": "button",
          "precondition": null,
          "is_visible": true
        },
        {
          "element_id": "el_welcome_text",
          "business_name": "欢迎信息",
          "aliases": ["欢迎提示", "欢迎回来"],
          "locator": {
            "method": "getByTestId",
            "args": ["welcome-message"]
          },
          "element_type": "text",
          "precondition": "登录成功后可见",
          "is_visible": true
        }
      ]
    }
  ]
}
```

## 元素库构建方式

### 初期快速构建

1. **从现有 POM 代码抽取**：写脚本批量解析项目中的页面对象文件，提取元素名称和定位器。

2. **Playwright 页面扫描生成**：通过 Playwright 遍历页面，自动提取所有可交互元素的 role、text、testid，批量生成初始元素库。

3. **人工校准**：对自动生成的元素补充业务名称、别名，调整命名规范。

### 日常维护

- 新增页面 / 元素时人工录入。

- 执行失败触发定位器自动更新。

- 人工修正 DSL 时，新别名自动回流到元素库。

## 元素检索机制（语义匹配）

采用「页面精确过滤 \+ 语义相似度匹配」的双层检索机制，准确率可达 98% 以上：

1. **页面过滤**：先根据步骤中的 page 字段，锁定对应页面的元素列表。

2. **语义匹配**：将输入的元素名称与元素库中的 business\_name \+ aliases 做语义向量相似度计算。

3. **阈值判断**：相似度 ≥ 0\.85 返回对应元素信息；\< 0\.85 返回「未找到匹配元素」，触发人工介入。

向量模型选型：使用 AWS Bedrock Titan Embeddings v2，与 AWS 技术栈统一，无需额外部署。

# 第三步：AI 转换引擎核心实现

## 核心技术方案：强制工具调用模式

绝对不允许 AI 直接生成定位器，而是通过 Bedrock Claude 的原生工具调用（Tool Use）能力，让 AI 主动调用「元素查询工具」，从元素库获取准确的定位器信息，再生成 DSL。这是整个方案准确率的核心保障：

- AI 只负责：语义理解、步骤拆解、动作类型判断、结构生成。

- 定位器只来自：元素知识库，由工具返回。

- 彻底杜绝 AI 编造选择器、瞎编定位器的问题。

## 工具定义

给 Claude 定义唯一的工具 query\_page\_element：

```json
{
  "tools": [
    {
      "toolSpec": {
        "name": "query_page_element",
        "description": "根据页面名称和元素的业务名称，查询元素的详细信息，包括元素ID、结构化定位器、元素类型。生成DSL时所有操作元素的步骤必须调用此工具获取定位信息，禁止自行编造定位器。",
        "inputSchema": {
          "json": {
            "type": "object",
            "required": ["page_name", "element_name"],
            "properties": {
              "page_name": {
                "type": "string",
                "description": "元素所属的页面名称，比如'登录页面'"
              },
              "element_name": {
                "type": "string",
                "description": "元素的业务名称，比如'手机号输入框'"
              }
            }
          }
        }
      }
    }
  ]
}
```

## 系统提示词设计（核心 Prompt）

```text
你是专业的测试DSL转换工程师，负责将Gherkin格式的测试用例转换为标准的自定义JSON DSL。

【严格遵守规则】
1. 输出必须是严格符合规范的JSON格式，不能有任何多余的解释、开场白、结束语
2. 所有涉及页面元素操作的步骤，必须先调用query_page_element工具查询元素信息，禁止自行编造任何定位器
3. 步骤的action必须从指定枚举中选择：navigate、click、fill、clear、hover、select、assert_text、assert_visible、assert_url
4. 步骤类型必须严格对应Gherkin的Given/When/Then/And/But，不能混用
5. 每个步骤的description字段保留原始Gherkin的业务描述
6. Then类型的步骤必须填充assertion字段，明确预期值和匹配类型
7. 导航类动作（如打开页面）element字段为null，不需要调用元素查询工具
8. 步骤ID按step_001、step_002顺序递增，场景ID、功能ID按语义自动生成

【DSL版本】
dsl_version固定为"1.0"

【转换流程】
1. 先完整解析Gherkin的所有场景和步骤
2. 对每个涉及元素的步骤，调用query_page_element工具查询元素信息
3. 拿到元素信息后，填入对应步骤的字段
4. 最终输出完整的JSON DSL
```

## 转换执行流程

1. **输入 Gherkin 文本**：支持单 Feature、多 Scenario。

2. **调用 Bedrock Converse API**：传入系统提示词、Gherkin 内容、工具定义。

3. **工具调用拦截**：检测到 Claude 调用 query\_page\_element 时，执行元素检索逻辑，返回结果给 Claude。

4. **多轮工具调用**：Claude 根据需要多次调用工具，查询所有元素。

5. **生成最终 DSL**：所有元素查询完成后，Claude 输出完整的 JSON DSL。

6. **结果返回**：解析返回的 JSON，输出标准化的自定义 DSL。

# 第四步：多层质量校验体系

AI 生成的结果必须经过多道校验才能进入执行环节，确保 100% 可执行。

|校验层|校验内容|
|---|---|
|**结构语法校验**|使用 JSON Schema 校验字段完整性、枚举值合法性、格式正确性；不通过的直接返回错误，触发 AI 重写。|
|**元素一致性校验**|遍历所有步骤的 element 和 page 字段，验证每个元素都能在对应页面的元素库中找到，定位器结构完整、方法合法；防止 AI 跳过工具调用、直接填写元素名称。|
|**语义一致性校验**|调用大模型反向核对「原始 Gherkin」和「生成的 JSON DSL」语义是否 100% 一致，检查遗漏、多余步骤与语义偏差；相似度低于 90 分自动打回重生成。|
|**定位器预校验**|启动 Playwright 无头浏览器打开对应页面，逐个验证 DSL 中的定位器能否正常找到元素；定位失败触发自动修复流程，更新元素库。|

# 第五步：JSON DSL → Playwright 执行映射

实现一个执行引擎，将 JSON DSL 的步骤直接映射为 Playwright API 调用，无需人工写代码。

```python
class PlaywrightExecutor:
    def __init__(self, page):
        self.page = page
        # 动作映射表
        self.action_map = {
            "navigate": self._action_navigate,
            "click": self._action_click,
            "fill": self._action_fill,
            "assert_text": self._assert_text,
            "assert_visible": self._assert_visible,
            "assert_url": self._assert_url
        }

    def _get_locator(self, locator_config):
        """根据结构化定位器配置生成Playwright Locator"""
        method = locator_config["method"]
        args = locator_config["args"]
        return getattr(self.page, method)(*args)

    def execute_step(self, step, element_lib):
        """执行单个DSL步骤"""
        action = step["action"]
        handler = self.action_map.get(action)
        if not handler:
            raise ValueError(f"不支持的动作类型: {action}")
        return handler(step, element_lib)

    def _action_fill(self, step, element_lib):
        element = element_lib.get_element(step["page"], step["element"])
        locator = self._get_locator(element["locator"])
        locator.fill(step["params"]["value"])

    def _assert_text(self, step, element_lib):
        element = element_lib.get_element(step["page"], step["element"])
        locator = self._get_locator(element["locator"])
        expected = step["assertion"]["expected_value"]
        match_type = step["assertion"]["match_type"]
        if match_type == "contains":
            expect(locator).to_contain_text(expected)
        else:
            expect(locator).to_have_text(expected)
```

# 完整可运行代码示例（Python \+ AWS Bedrock）

## 依赖安装

```bash
pip install boto3 jsonschema sentence-transformers playwright
playwright install chromium
```

## 核心实现代码

以下代码包含元素知识库类、Bedrock 转换引擎、DSL 校验器三部分，可直接作为平台 AI 转换模块的基础骨架。

```python
import json
import boto3
import jsonschema
from typing import List, Dict

# ========== 1. 配置 ==========
BEDROCK_REGION = "us-east-1"
MODEL_ID = "anthropic.claude-3-5-sonnet-20240620-v1:0"

# ========== 2. 元素知识库类 ==========
class ElementLibrary:
    def __init__(self, lib_path: str):
        with open(lib_path, "r", encoding="utf-8") as f:
            self.data = json.load(f)
        self.page_map = {p["page_name"]: p for p in self.data["pages"]}
        # 别名映射
        for page in self.data["pages"]:
            for alias in page.get("aliases", []):
                self.page_map[alias] = page

    def get_page(self, page_name: str) -&gt; Dict:
        return self.page_map.get(page_name)

    def get_element(self, page_name: str, element_name: str) -&gt; Dict:
        """简化版元素查询，生产环境替换成语义向量匹配"""
        page = self.get_page(page_name)
        if not page:
            return None
        # 精确匹配业务名称
        for el in page["elements"]:
            if el["business_name"] == element_name:
                return el
        # 别名匹配
        for el in page["elements"]:
            if element_name in el.get("aliases", []):
                return el
        return None

# ========== 3. Bedrock转换引擎 ==========
class GherkinToDslConverter:
    def __init__(self, element_lib: ElementLibrary):
        self.bedrock = boto3.client("bedrock-runtime", region_name=BEDROCK_REGION)
        self.element_lib = element_lib
        self.system_prompt = """
你是专业的测试DSL转换工程师，负责将Gherkin格式的测试用例转换为标准的自定义JSON DSL。

【严格遵守规则】
1. 输出必须是严格符合规范的JSON格式，不能有任何多余的解释、开场白、结束语
2. 所有涉及页面元素操作的步骤，必须先调用query_page_element工具查询元素信息，禁止自行编造任何定位器
3. 步骤的action必须从指定枚举中选择：navigate、click、fill、clear、hover、select、assert_text、assert_visible、assert_url
4. 步骤类型必须严格对应Gherkin的Given/When/Then/And/But，不能混用
5. 每个步骤的description字段保留原始Gherkin的业务描述
6. Then类型的步骤必须填充assertion字段，明确预期值和匹配类型
7. 导航类动作element字段为null，不需要调用元素查询工具
8. dsl_version固定为"1.0"
"""
        self.tool_def = [
            {
                "toolSpec": {
                    "name": "query_page_element",
                    "description": "根据页面名称和元素业务名称，查询元素详细信息",
                    "inputSchema": {
                        "json": {
                            "type": "object",
                            "required": ["page_name", "element_name"],
                            "properties": {
                                "page_name": {"type": "string"},
                                "element_name": {"type": "string"}
                            }
                        }
                    }
                }
            }
        ]

    def _handle_tool_call(self, tool_use):
        """处理工具调用，执行元素查询"""
        input_data = tool_use["input"]
        page_name = input_data["page_name"]
        element_name = input_data["element_name"]
        element = self.element_lib.get_element(page_name, element_name)

        result = {
            "page_name": page_name,
            "element_name": element_name,
            "found": element is not None,
            "element_info": element
        }
        return {
            "toolResult": {
                "toolUseId": tool_use["toolUseId"],
                "content": [{"json": result}]
            }
        }

    def convert(self, gherkin_text: str) -&gt; Dict:
        messages = [
            {
                "role": "user",
                "content": [{"text": f"请将以下Gherkin转换为自定义JSON DSL：\n{gherkin_text}"}]
            }
        ]

        while True:
            response = self.bedrock.converse(
                modelId=MODEL_ID,
                system=[{"text": self.system_prompt}],
                messages=messages,
                toolConfig={"tools": self.tool_def}
            )

            output = response["output"]["message"]
            messages.append(output)

            # 判断是否有工具调用
            if "stopReason" in response and response["stopReason"] == "tool_use":
                # 处理所有工具调用
                tool_results = []
                for content in output["content"]:
                    if "toolUse" in content:
                        tool_result = self._handle_tool_call(content["toolUse"])
                        tool_results.append(tool_result)

                messages.append({
                    "role": "user",
                    "content": tool_results
                })
            else:
                # 没有工具调用，返回最终结果
                text_result = output["content"][0]["text"]
                # 提取JSON（处理可能的markdown包裹）
                if "```json" in text_result:
                    text_result = text_result.split("```json")[1].split("```")[0].strip()
                return json.loads(text_result)

# ========== 4. DSL校验器 ==========
class DslValidator:
    def __init__(self, schema_path: str, element_lib: ElementLibrary):
        with open(schema_path, "r", encoding="utf-8") as f:
            self.schema = json.load(f)
        self.element_lib = element_lib

    def validate(self, dsl: Dict) -&gt; Dict:
        # 1. 结构校验
        try:
            jsonschema.validate(instance=dsl, schema=self.schema)
        except jsonschema.ValidationError as e:
            return {"valid": False, "error": f"结构校验失败: {e.message}"}

        # 2. 元素校验
        for scenario in dsl["scenarios"]:
            for step in scenario["steps"]:
                if step["element"]:
                    element = self.element_lib.get_element(step["page"], step["element"])
                    if not element:
                        return {
                            "valid": False,
                            "error": f"元素校验失败：页面[{step['page']}]中未找到元素[{step['element']}]"
                        }

        return {"valid": True}

# ========== 使用示例 ==========
if __name__ == "__main__":
    # 初始化元素库
    element_lib = ElementLibrary("element_library.json")

    # 初始化转换器
    converter = GherkinToDslConverter(element_lib)

    # 输入Gherkin
    gherkin = """
Feature: 用户手机号密码登录
  Scenario: 正确手机号密码登录成功
    Given 用户打开系统登录页面
    When 用户输入手机号 "13800138000"
    And 用户点击登录按钮
    Then 页面显示欢迎信息 "欢迎回来"
"""

    # 转换
    dsl_result = converter.convert(gherkin)

    # 校验
    validator = DslValidator("dsl_schema.json", element_lib)
    check_result = validator.validate(dsl_result)

    if check_result["valid"]:
        print("转换成功，DSL内容：")
        print(json.dumps(dsl_result, indent=2, ensure_ascii=False))
    else:
        print("校验失败：", check_result["error"])
```

# 闭环优化机制

1. **执行结果回流**：执行成功的 DSL 自动加入高质量样本库，作为后续 RAG 参考。

2. **元素别名回流**：人工修正的元素名称，自动将原名称加入对应元素的别名库，提升后续检索准确率。

3. **定位器自动更新**：定位失败时，调用多模态模型结合截图 \+ DOM 重新识别定位器，自动更新元素库。

4. **Prompt 迭代优化**：定期统计转换失败类型，优化系统提示词和工具定义。

# 落地路线图

|阶段|周期|交付内容|
|---|---|---|
|**第一阶段**|1 周|定义 JSON DSL 规范和 JSON Schema；搭建初始元素库（核心页面）；实现基础转换引擎和结构校验；跑通单一场景的端到端转换。|
|**第二阶段**|2 周|接入语义向量检索，优化元素匹配准确率；完善多层校验体系；实现 Playwright 执行映射；准确率达到 90%\+。|
|**第三阶段**|1 个月|元素库可视化管理后台；失败自动修复与闭环优化；批量转换、任务调度；与现有低代码平台集成。|

# 验收标准与未决事项

## 验收标准

1. Gherkin 输入可自动转换为符合 Schema 的 JSON DSL，结构校验通过率 100%。

2. 转换结果中所有元素均可追溯到元素知识库，无 AI 编造的定位器。

3. 生成的 JSON DSL 可直接被 Playwright 执行引擎映射执行，无需人工改写。

4. 第二阶段完成后，单场景转换一次通过率达到 90% 以上。

## 未决事项

- 语义向量检索的阈值（0\.85）需在真实数据集上校准，可能按页面类型分档。

- 多模态定位器预校验在无头环境的稳定性需进一步验证。

- 与现有低代码拖拉拽平台的节点渲染映射规则，需在集成阶段细化。

# 附录：转换效果示例

## 输入 Gherkin

```gherkin
Feature: 用户手机号密码登录
  Scenario: 正确手机号密码登录成功
    Given 用户打开系统登录页面
    When 用户输入手机号 "13800138000"
    And 用户点击登录按钮
    Then 页面显示欢迎信息 "欢迎回来"

```

## 输出 JSON DSL

```json
{
  "dsl_version": "1.0",
  "feature": {
    "id": "feature_login",
    "name": "用户手机号密码登录",
    "module": "用户中心-账号体系",
    "tags": ["登录", "用户中心"]
  },
  "scenarios": [
    {
      "id": "scenario_login_success",
      "name": "正确手机号密码登录成功",
      "steps": [
        {
          "id": "step_001",
          "type": "Given",
          "page": "登录页面",
          "element": null,
          "action": "navigate",
          "params": {"url": "/login"},
          "assertion": null,
          "description": "用户打开系统登录页面"
        },
        {
          "id": "step_002",
          "type": "When",
          "page": "登录页面",
          "element": "手机号输入框",
          "action": "fill",
          "params": {"value": "13800138000"},
          "assertion": null,
          "description": "用户输入手机号"
        },
        {
          "id": "step_003",
          "type": "And",
          "page": "登录页面",
          "element": "登录按钮",
          "action": "click",
          "params": {},
          "assertion": null,
          "description": "用户点击登录按钮"
        },
        {
          "id": "step_004",
          "type": "Then",
          "page": "登录页面",
          "element": "欢迎信息",
          "action": "assert_text",
          "params": {},
          "assertion": {
            "expected_value": "欢迎回来",
            "match_type": "equal"
          },
          "description": "页面显示欢迎信息"
        }
      ]
    }
  ]
}
```

\</doc\>

> （注：部分内容由豆包工作 AI 生成）
