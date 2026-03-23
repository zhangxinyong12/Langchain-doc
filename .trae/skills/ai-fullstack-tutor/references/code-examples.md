# 代码示例模板

本文档包含各模块的实践代码模板，用于辅导过程中的练习环节。

## Python 异步编程

### 基础: async/await

```python
import asyncio

async def say_hello(name: str, delay: int) -> str:
    """延迟后返回问候语"""
    await asyncio.sleep(delay)
    return f"Hello, {name}!"

async def main():
    result = await say_hello("World", 1)
    print(result)

asyncio.run(main())
```

### 进阶: 并发执行

```python
import asyncio

async def fetch_url(url: str) -> str:
    """模拟 HTTP 请求"""
    await asyncio.sleep(1)  # 模拟网络延迟
    return f"Response from {url}"

async def fetch_all(urls: list[str]) -> list[str]:
    """并发请求多个 URL"""
    tasks = [fetch_url(url) for url in urls]
    results = await asyncio.gather(*tasks)
    return results

async def main():
    urls = [
        "https://api.example.com/1",
        "https://api.example.com/2",
        "https://api.example.com/3",
    ]
    results = await fetch_all(urls)
    for r in results:
        print(r)

asyncio.run(main())
```

### 实践任务

```python
# 任务: 实现一个异步计时器，每秒打印一次时间，共打印 5 次
import asyncio

async def timer(seconds: int) -> None:
    # TODO: 实现这个函数
    pass

asyncio.run(timer(5))
```

---

## OpenAI API

### 基础: 对话补全

```python
from openai import OpenAI

client = OpenAI()

def chat(user_message: str) -> str:
    """发送消息并获取回复"""
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "user", "content": user_message}
        ]
    )
    return response.choices[0].message.content

print(chat("你好，介绍一下自己"))
```

### 进阶: 流式响应

```python
from openai import OpenAI

client = OpenAI()

def stream_chat(user_message: str) -> None:
    """流式输出回复"""
    stream = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": user_message}],
        stream=True
    )
    
    for chunk in stream:
        if chunk.choices[0].delta.content:
            print(chunk.choices[0].delta.content, end="", flush=True)
    print()

stream_chat("讲一个短笑话")
```

### 进阶: Function Calling

```python
from openai import OpenAI
import json

client = OpenAI()

def get_weather(city: str) -> dict:
    """模拟天气 API"""
    weather_data = {
        "北京": {"temp": 25, "weather": "晴"},
        "上海": {"temp": 28, "weather": "多云"},
    }
    return weather_data.get(city, {"temp": 20, "weather": "未知"})

tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "获取城市天气",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "城市名称"}
                },
                "required": ["city"]
            }
        }
    }
]

def chat_with_tools(user_message: str) -> str:
    messages = [{"role": "user", "content": user_message}]
    
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages,
        tools=tools
    )
    
    message = response.choices[0].message
    
    if message.tool_calls:
        tool_call = message.tool_calls[0]
        args = json.loads(tool_call.function.arguments)
        result = get_weather(args["city"])
        
        messages.append(message)
        messages.append({
            "role": "tool",
            "tool_call_id": tool_call.id,
            "content": json.dumps(result, ensure_ascii=False)
        })
        
        final_response = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=messages
        )
        return final_response.choices[0].message.content
    
    return message.content

print(chat_with_tools("北京今天天气怎么样？"))
```

---

## LangChain 核心

### 基础: LCEL Chain

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_openai import ChatOpenAI

# 创建组件
prompt = ChatPromptTemplate.from_template("给我讲一个关于{topic}的笑话")
model = ChatOpenAI(model="gpt-4o-mini")
parser = StrOutputParser()

# 组合成链
chain = prompt | model | parser

# 调用
result = chain.invoke({"topic": "程序员"})
print(result)
```

### 进阶: 多步骤 Chain

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model="gpt-4o-mini")

# 步骤1: 生成主题
topic_prompt = ChatPromptTemplate.from_template(
    "生成一个有趣的主题，类别是{category}"
)
topic_chain = topic_prompt | model | StrOutputParser()

# 步骤2: 基于主题写故事
story_prompt = ChatPromptTemplate.from_template(
    "用100字以内写一个关于{topic}的故事"
)
story_chain = story_prompt | model | StrOutputParser()

# 组合
full_chain = {"topic": topic_chain} | story_chain

result = full_chain.invoke({"category": "科幻"})
print(result)
```

### 进阶: 结构化输出

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from pydantic import BaseModel, Field

class Person(BaseModel):
    """人物信息"""
    name: str = Field(description="姓名")
    age: int = Field(description="年龄")
    occupation: str = Field(description="职业")

model = ChatOpenAI(model="gpt-4o-mini")
structured_model = model.with_structured_output(Person)

prompt = ChatPromptTemplate.from_template(
    "从以下文本中提取人物信息: {text}"
)

chain = prompt | structured_model

result = chain.invoke({
    "text": "张三今年28岁，是一名软件工程师"
})
print(result)
# name='张三' age=28 occupation='软件工程师'
```

---

## RAG

### 完整 RAG 示例

```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import TextLoader
from langchain.chains import create_retrieval_chain
from langchain.chains.combine_documents import create_stuff_documents_chain
from langchain_core.prompts import ChatPromptTemplate

# 1. 加载文档
loader = TextLoader("knowledge.txt", encoding="utf-8")
documents = loader.load()

# 2. 分割文档
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=100
)
chunks = text_splitter.split_documents(documents)

# 3. 创建向量存储
embeddings = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(chunks, embeddings)
retriever = vectorstore.as_retriever(search_kwargs={"k": 3})

# 4. 创建 RAG Chain
model = ChatOpenAI(model="gpt-4o-mini")

prompt = ChatPromptTemplate.from_template("""
基于以下上下文回答问题。如果上下文中没有相关信息，请说明不知道。

上下文:
{context}

问题: {input}
""")

document_chain = create_stuff_documents_chain(model, prompt)
retrieval_chain = create_retrieval_chain(retriever, document_chain)

# 5. 提问
response = retrieval_chain.invoke({
    "input": "什么是 RAG？"
})
print(response["answer"])
```

### 自定义检索器

```python
from langchain_core.retrievers import BaseRetriever
from langchain_core.documents import Document
from typing import List

class SimpleRetriever(BaseRetriever):
    """简单的关键词检索器"""
    documents: List[Document]
    
    def _get_relevant_documents(self, query: str) -> List[Document]:
        keywords = query.lower().split()
        results = []
        for doc in self.documents:
            content = doc.page_content.lower()
            if any(kw in content for kw in keywords):
                results.append(doc)
        return results[:3]
```

---

## Agent

### 基础: 自定义 Tool

```python
from langchain_core.tools import tool
from langchain_openai import ChatOpenAI
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_core.prompts import ChatPromptTemplate

@tool
def add(a: int, b: int) -> int:
    """两个数字相加
    
    Args:
        a: 第一个数字
        b: 第二个数字
    
    Returns:
        两数之和
    """
    return a + b

@tool
def multiply(a: int, b: int) -> int:
    """两个数字相乘"""
    return a * b

tools = [add, multiply]
model = ChatOpenAI(model="gpt-4o-mini")

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个有用的助手，可以使用工具进行计算"),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}")
])

agent = create_tool_calling_agent(model, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

result = agent_executor.invoke({
    "input": "计算 15 加 27，然后乘以 2"
})
print(result["output"])
```

### 进阶: ReAct Agent

```python
from langchain_openai import ChatOpenAI
from langchain.agents import create_react_agent, AgentExecutor
from langchain_core.prompts import PromptTemplate
from langchain_core.tools import Tool

def search_wiki(query: str) -> str:
    """模拟 Wikipedia 搜索"""
    database = {
        "Python": "Python 是一种高级编程语言",
        "LangChain": "LangChain 是用于构建 LLM 应用的框架"
    }
    for key in database:
        if key.lower() in query.lower():
            return database[key]
    return "没有找到相关信息"

def calculator(expression: str) -> str:
    """计算数学表达式"""
    try:
        return str(eval(expression))
    except:
        return "计算错误"

tools = [
    Tool(
        name="Search",
        func=search_wiki,
        description="搜索 Wikipedia 获取信息"
    ),
    Tool(
        name="Calculator",
        func=calculator,
        description="计算数学表达式，输入如 '2 + 2'"
    )
]

prompt = PromptTemplate.from_template("""
你是一个可以使用工具的助手。

可用工具:
{tool_names}
{tools}

使用以下格式:
Question: 输入问题
Thought: 思考应该做什么
Action: 工具名称 [{tool_names}]
Action Input: 工具输入
Observation: 工具输出
... (可以重复 Thought/Action/Action Input/Observation)
Thought: 我知道最终答案了
Final Answer: 最终答案

开始!

Question: {input}
Thought: {agent_scratchpad}
""")

model = ChatOpenAI(model="gpt-4o-mini", temperature=0)
agent = create_react_agent(model, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

result = agent_executor.invoke({
    "input": "LangChain 是什么？它有几个字？"
})
```

---

## FastAPI

### 基础: REST API

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float
    is_offer: bool = False

items_db = {}

@app.get("/")
def read_root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")
def read_item(item_id: int):
    if item_id in items_db:
        return items_db[item_id]
    return {"error": "Item not found"}

@app.post("/items/{item_id}")
def create_item(item_id: int, item: Item):
    items_db[item_id] = item
    return {"item_id": item_id, "item": item}

# 运行: uvicorn main:app --reload
```

### 进阶: 流式响应

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
from openai import OpenAI
import asyncio

app = FastAPI()
client = OpenAI()

async def stream_chat(message: str):
    """流式生成 AI 回复"""
    stream = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": message}],
        stream=True
    )
    
    for chunk in stream:
        if chunk.choices[0].delta.content:
            yield f"data: {chunk.choices[0].delta.content}\n\n"
    
    yield "data: [DONE]\n\n"

@app.post("/chat/stream")
async def chat_stream(message: str):
    return StreamingResponse(
        stream_chat(message),
        media_type="text/event-stream"
    )
```

---

## Next.js + AI

### API Route

```typescript
import { OpenAI } from 'openai'
import { OpenAIStream, StreamingTextResponse } from 'ai'

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
})

export const runtime = 'edge'

export async function POST(req: Request) {
  const { messages } = await req.json()
  
  const response = await openai.chat.completions.create({
    model: 'gpt-4o-mini',
    messages,
    stream: true
  })
  
  const stream = OpenAIStream(response)
  return new StreamingTextResponse(stream)
}
```

### Chat Component

```tsx
'use client'

import { useChat } from 'ai/react'

export default function Chat() {
  const { 
    messages, 
    input, 
    handleInputChange, 
    handleSubmit,
    isLoading 
  } = useChat()
  
  return (
    <div className="flex flex-col h-screen max-w-2xl mx-auto p-4">
      <div className="flex-1 overflow-y-auto space-y-4">
        {messages.map(m => (
          <div 
            key={m.id}
            className={`p-3 rounded-lg ${
              m.role === 'user' 
                ? 'bg-blue-500 ml-auto' 
                : 'bg-gray-700'
            }`}
          >
            {m.content}
          </div>
        ))}
      </div>
      
      <form onSubmit={handleSubmit} className="flex gap-2 mt-4">
        <input
          value={input}
          onChange={handleInputChange}
          placeholder="输入消息..."
          className="flex-1 p-2 rounded border bg-gray-800"
          disabled={isLoading}
        />
        <button 
          type="submit" 
          disabled={isLoading}
          className="px-4 py-2 bg-blue-500 rounded disabled:opacity-50"
        >
          发送
        </button>
      </form>
    </div>
  )
}
```
