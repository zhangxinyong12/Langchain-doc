# 知识点详解

本文档包含 AI 全栈学习各模块的详细知识点，用于导师参考和设计问题。

## 模块 1: Python 进阶

### 1.1 异步编程

**核心概念**:
- 同步 vs 异步: 阻塞与非阻塞执行
- 事件循环 (Event Loop): 任务调度核心
- 协程 (Coroutine): async def 定义的函数
- await: 等待协程完成
- asyncio.gather: 并发执行多个协程
- asyncio.create_task: 创建后台任务

**常见误区**:
- "await 会创建新线程" → 实际是单线程协作式多任务
- "async 函数自动并发" → 需要 await 或 create_task 才会执行
- "异步一定比同步快" → 取决于 IO 密集还是 CPU 密集

**诊断问题**:
1. "同步和异步代码执行有什么区别？"
2. "event loop 是什么？为什么需要它？"
3. "什么时候应该用 async/await？"

**实践任务**:
- 写一个并发请求多个 URL 的程序
- 实现一个简单的异步计时器

### 1.2 类型系统

**核心概念**:
- 基本类型注解: `int`, `str`, `List[int]`, `Dict[str, Any]`
- Optional: 可能为 None 的类型
- Union: 多种类型之一
- Generic: 泛型 `T`
- TypeVar: 类型变量
- Protocol: 结构化子类型

**常见误区**:
- "类型注解会影响运行时性能" → 只是静态检查
- "Any 和 object 一样" → Any 逃逸检查，object 需要类型转换

### 1.3 Pydantic

**核心概念**:
- BaseModel: 数据模型基类
- Field: 字段验证和描述
- validator: 自定义验证器
- model_dump / model_json: 序列化
- model_validate: 反序列化验证

---

## 模块 2: LLM 基础

### 2.1 大语言模型原理

**核心概念**:
- Transformer 架构: 自注意力机制
- Token: 文本最小单位
- 上下文窗口: 模型能处理的最大 token 数
- 参数量: 模型大小 (7B, 70B 等)
- 训练数据截止日期

**常见误区**:
- "模型理解我说的话" → 实际是概率预测
- "上下文窗口越大越好" → 成本和延迟也会增加

### 2.2 OpenAI API

**核心概念**:
- Chat Completion API: 对话补全
- Messages 格式: system, user, assistant
- Temperature: 随机性控制 (0-2)
- Max Tokens: 最大生成长度
- Stream: 流式响应
- Function Calling: 函数调用

**代码模板**:
```python
from openai import OpenAI

client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "你是一个有帮助的助手"},
        {"role": "user", "content": "你好"}
    ],
    temperature=0.7,
    stream=True
)

for chunk in response:
    print(chunk.choices[0].delta.content, end="")
```

### 2.3 Prompt Engineering

**核心技巧**:
- 角色设定: "你是一个..."
- 任务分解: 分步骤指令
- Few-shot: 给出示例
- Chain of Thought: 要求展示推理过程
- 输出格式控制: JSON, Markdown 等

---

## 模块 3: LangChain 核心

### 3.1 LCEL (LangChain Expression Language)

**核心概念**:
- Runnable 接口: invoke, stream, batch
- 管道操作符 `|`: 链式组合
- RunnablePassthrough: 传递输入
- RunnableParallel: 并行执行
- itemgetter: 提取字典字段

**代码模板**:
```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_openai import ChatOpenAI

prompt = ChatPromptTemplate.from_template("讲一个关于{topic}的笑话")
model = ChatOpenAI()
parser = StrOutputParser()

chain = prompt | model | parser

result = chain.invoke({"topic": "程序员"})
```

### 3.2 Prompt Template

**核心概念**:
- from_template: 从字符串创建
- from_messages: 多角色消息
- Partial: 部分填充变量

### 3.3 Output Parser

**核心概念**:
- StrOutputParser: 字符串输出
- JsonOutputParser: JSON 解析
- PydanticOutputParser: 结构化输出
- 自动修复: 修复格式错误

---

## 模块 4: RAG

### 4.1 向量数据库

**核心概念**:
- 向量: 文本的数值表示
- 相似度搜索: 余弦相似度、欧氏距离
- 索引: HNSW, IVF
- 常见选择: Chroma, Pinecone, Milvus, Weaviate

### 4.2 Embedding

**核心概念**:
- 文本转向量
- OpenAI Embeddings: text-embedding-3-small/large
- 开源选择: sentence-transformers, BGE

### 4.3 文档处理

**核心概念**:
- 文档加载器: PDF, Markdown, Web
- 文本分割: RecursiveCharacterTextSplitter
- 分块策略: 固定大小、语义分割、递归分割
- 重叠: 保持上下文连贯

**代码模板**:
```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    length_function=len
)

chunks = splitter.split_text(long_text)
```

### 4.4 RAG 流程

```
查询 → Embedding → 向量搜索 → 检索相关文档 → 构建上下文 → LLM 生成回答
```

**代码模板**:
```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain.chains import RetrievalQA

embeddings = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(documents, embeddings)
retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

llm = ChatOpenAI()

qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    return_source_documents=True
)

result = qa_chain.invoke({"query": "什么是 RAG?"})
```

---

## 模块 5: Agent

### 5.1 Agent 架构

**核心概念**:
- ReAct: Reasoning + Acting 循环
- Observation: 工具返回结果
- Thought: 推理过程
- Action: 决定调用哪个工具

### 5.2 Tools

**核心概念**:
- @tool 装饰器: 定义工具
- 工具描述: 告诉 LLM 如何使用
- 参数 Schema: JSON Schema 定义

**代码模板**:
```python
from langchain_core.tools import tool

@tool
def get_weather(city: str) -> str:
    """获取指定城市的天气信息
    
    Args:
        city: 城市名称，如"北京"
    
    Returns:
        天气描述字符串
    """
    # 实际实现调用天气 API
    return f"{city}今天晴天，温度25度"

# 使用工具
tools = [get_weather]
llm_with_tools = llm.bind_tools(tools)
```

### 5.3 Function Calling

**核心概念**:
- tools 参数: 定义可用函数
- tool_choice: 控制调用行为
- 自动解析: 从响应中提取函数调用

### 5.4 Multi-Agent

**核心概念**:
- Agent 通信: 消息传递
- 角色分工: 每个 Agent 负责特定任务
- 协调机制: 中央协调或对等协作
- LangGraph: 状态图编排

---

## 模块 6: 后端工程

### 6.1 FastAPI

**核心概念**:
- 路由装饰器: @app.get, @app.post
- 依赖注入: Depends
- 请求模型: Pydantic BaseModel
- 响应模型: response_model

### 6.2 流式响应

**核心概念**:
- SSE (Server-Sent Events): 单向推送
- WebSocket: 双向通信
- StreamingResponse: FastAPI 流式响应

**代码模板**:
```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
import asyncio

app = FastAPI()

async def generate_stream():
    for i in range(10):
        yield f"data: 消息 {i}\n\n"
        await asyncio.sleep(0.5)

@app.get("/stream")
async def stream():
    return StreamingResponse(
        generate_stream(),
        media_type="text/event-stream"
    )
```

### 6.3 数据库设计

**核心概念**:
- PostgreSQL: 关系型数据库
- pgvector: 向量扩展
- SQLAlchemy ORM
- 异步数据库: asyncpg, databases

---

## 模块 7: 前端集成

### 7.1 Next.js + AI

**核心概念**:
- Vercel AI SDK: useChat, useCompletion
- Route Handler: API 路由
- 流式渲染: 实时显示生成内容

**代码模板**:
```tsx
// app/api/chat/route.ts
import { OpenAI } from 'openai'
import { OpenAIStream, StreamingTextResponse } from 'ai'

const openai = new OpenAI()

export async function POST(req: Request) {
  const { messages } = await req.json()
  
  const response = await openai.chat.completions.create({
    model: 'gpt-4',
    messages,
    stream: true,
  })
  
  const stream = OpenAIStream(response)
  return new StreamingTextResponse(stream)
}
```

```tsx
// app/page.tsx
import { useChat } from 'ai/react'

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat()
  
  return (
    <div>
      {messages.map(m => (
        <div key={m.id}>{m.role}: {m.content}</div>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} />
      </form>
    </div>
  )
}
```

### 7.2 流式 UI

**核心概念**:
- 渐进式渲染
- 打字机效果
- 加载状态管理
- 错误处理
