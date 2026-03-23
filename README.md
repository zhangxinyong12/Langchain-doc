# AI 全栈开发学习计划

> 从前端工程师转型 AI 全栈 / AI Agent 应用开发的学习之旅

## 项目背景

- **学员背景**: 10+年前端开发经验，精通 Next.js、NestJS
- **学习目标**: 2-3个月后求职 AI 全栈 / AI Agent 应用开发
- **技术基础**: 前端精通，Node.js 有项目经验，Python 基础

## 学习路线 (12周计划)

### 第一阶段: 基础夯实 (第1-3周)

| 模块 | 内容 | 实战项目 |
|------|------|----------|
| Python 进阶 | asyncio、Type Hints、pydantic | Python API 服务 |
| LLM 基础 | 大模型原理、OpenAI API、Prompt Engineering | 智能对话机器人 |
| LangChain 核心 | Chain、Prompt、Model、Output Parser | 文档问答系统 |

### 第二阶段: 核心技能 (第4-7周)

| 模块 | 内容 | 实战项目 |
|------|------|----------|
| RAG | 向量数据库、Embedding、文档分块、检索优化 | 企业知识库问答 |
| Agent | 智能体架构、Tools、Memory、Multi-Agent | 自动化办公助手 |

### 第三阶段: 工程化 (第8-10周)

| 模块 | 内容 | 实战项目 |
|------|------|----------|
| 后端工程 | FastAPI、流式响应、数据库设计 | AI 应用后端 API |
| 前端集成 | Next.js + AI、流式 UI | AI 全栈应用前端 |

### 第四阶段: 综合项目 (第11-12周)

- 完整的 AI 全栈应用开发
- 简历项目打磨

## 核心技能清单

### 必备技能 (80%+ 招聘要求)

- Python / LangChain / LLM 原理 / Prompt Engineering / RAG

### 重要技能 (50-80%)

- Agent 设计 / 向量数据库 / Fine-tuning / FastAPI / Docker

### 加分技能 (30-50%)

- LlamaIndex / Dify / 多模态 / 云平台

## 项目结构

```
Langchain/
├── 学习计划.md       # 详细学习计划
├── 学习进度.json     # 学习进度跟踪
├── requirements.txt  # Python 依赖
├── .env.example      # 环境变量示例
└── README.md         # 项目说明
```

## 环境配置

### 1. 创建虚拟环境

```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS/Linux
source .venv/bin/activate
```

### 2. 安装依赖

```bash
pip install -r requirements.txt
```

### 3. 配置环境变量

```bash
cp .env.example .env
# 编辑 .env 填入你的 API Key
```

## 学习资源

- [LangChain 官方文档](https://python.langchain.com/)
- [OpenAI API 文档](https://platform.openai.com/docs)
- [FastAPI 官方文档](https://fastapi.tiangolo.com/)

## 进度跟踪

学习进度记录在 `学习进度.json` 文件中，持续更新中...

---

> 学习是一场马拉松，不是短跑。保持节奏，稳步前行。
