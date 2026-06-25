# Hello LLM - Anthropic SDK 学习项目

这个项目从0到1构建一个AI coding agent，展示Anthropic Claude SDK的核心用法。

## 项目结构

```
hello-llm/
├── s01_agent_loop.py    # 基础Agent循环（核心模式）
├── s02_tool_use.py      # 多工具支持 + 分发映射
├── s03_permission.py    # 权限系统（三道门）
├── s04_hooks.py         # Hook系统（事件驱动）
└── s05_todo_write.py    # Todo规划工具
```

## Anthropic SDK 核心返回值解析

### 1. `client.messages.create()` 返回的 `response` 对象

```python
response = client.messages.create(
    model=MODEL,
    system=SYSTEM,
    messages=messages,
    tools=TOOLS,
    max_tokens=8000,
)
```

**response 包含以下关键属性：**

#### `response.content` (list)
- **类型**: `list[ContentBlock]`
- **说明**: 模型返回的内容块列表
- **每个 block 可能是：**
  - `TextBlock`: 文本响应
    ```python
    block.type == "text"
    block.text  # 文本内容
    ```
  - `ToolUseBlock`: 工具调用请求
    ```python
    block.type == "tool_use"
    block.id     # 工具调用ID（用于匹配结果）
    block.name   # 工具名称（如 "bash", "read_file"）
    block.input  # 工具参数（dict）
    ```

#### `response.stop_reason` (str)
- **类型**: `str`
- **可能的值**:
  - `"end_turn"`: 模型正常结束（没有更多要说的）
  - `"tool_use"`: 模型请求调用工具
  - `"max_tokens"`: 达到最大token限制
  - `"stop_sequence"`: 遇到停止序列

#### `response.model` (str)
- **类型**: `str`
- **说明**: 实际使用的模型名称

#### `response.usage` (Usage)
- **类型**: `Usage` 对象
- **属性**:
  - `input_tokens`: 输入token数
  - `output_tokens`: 输出token数

### 2. 工具定义格式 (TOOLS)

```python
TOOLS = [{
    "name": "bash",                    # 工具名称
    "description": "Run a shell command.",  # 工具描述（帮助模型理解何时使用）
    "input_schema": {                  # JSON Schema 定义参数
        "type": "object",
        "properties": {
            "command": {
                "type": "string",
                "description": "要执行的shell命令"
            }
        },
        "required": ["command"]        # 必需参数
    }
}]
```

### 3. 工具结果返回格式

```python
{
    "type": "tool_result",
    "tool_use_id": block.id,   # 必须与请求的 block.id 匹配
    "content": output           # 工具执行结果（字符串）
}
```

### 4. 完整的Agent循环流程

```python
while True:
    # 1. 调用LLM
    response = client.messages.create(...)

    # 2. 保存assistant的响应到历史
    messages.append({"role": "assistant", "content": response.content})

    # 3. 检查是否需要继续
    if response.stop_reason != "tool_use":
        return  # 模型决定停止

    # 4. 执行所有工具调用
    results = []
    for block in response.content:
        if block.type == "tool_use":
            output = execute_tool(block.name, block.input)
            results.append({
                "type": "tool_result",
                "tool_use_id": block.id,
                "content": output
            })

    # 5. 将工具结果反馈给模型（循环继续）
    messages.append({"role": "user", "content": results})
```

## 运行方式

### 1. 安装依赖
```bash
pip install anthropic python-dotenv
```

### 2. 配置环境变量
创建 `.env` 文件：
```env
ANTHROPIC_API_KEY=your-api-key-here
MODEL_ID=claude-sonnet-4-20250514
# 可选：自定义base URL
# ANTHROPIC_BASE_URL=https://api.anthropic.com
```

### 3. 运行
```bash
# 基础版
python s01_agent_loop.py

# 多工具版
python s02_tool_use.py

# 带权限检查版
python s03_permission.py

# 带Hook系统版
python s04_hooks.py

# 带Todo规划版
python s05_todo_write.py
```

## 关键概念总结

1. **Agent Loop = While循环 + 工具调用**
   - 核心模式：`while stop_reason == "tool_use"`
   - 每次循环：调用LLM → 执行工具 → 反馈结果

2. **Tool Use = LLM的能力扩展**
   - 通过 `tools` 参数告诉LLM有哪些工具可用
   - LLM返回 `tool_use` block 来请求调用工具
   - 你执行工具后，通过 `tool_result` 反馈结果

3. **Messages = 对话历史**
   - `user`: 用户输入或工具结果
   - `assistant`: LLM的响应
   - 保持完整的上下文

4. **Stop Reason = 控制流信号**
   - `"tool_use"`: 继续循环
   - `"end_turn"`: 退出循环

## 学习路径

- **s01**: 理解最核心的Agent循环模式
- **s02**: 学习如何扩展工具集和分发机制
- **s03**: 理解权限系统如何保护安全
- **s04**: 学习Hook系统实现可扩展性
- **s05**: 理解规划工具如何提升Agent能力

每个文件都是独立可运行的，逐步增加复杂度，帮助你从0理解AI Agent的构建原理。
