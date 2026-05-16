# Open Notebook 知识图谱分析报告

> 由 Graphify 代码知识图谱工具生成
> 分析时间: 2026-05-17
> 项目路径: ~/Desktop/Project/AlterEgo_NoteBook/Source/open-notebook

---

## 项目概览

**Open Notebook** 是一个开源、隐私优先的 AI 研究助手，是 Google Notebook LM 的替代方案。支持多模态内容（PDF、视频、音频、网页）上传、智能笔记生成、语义搜索、AI 对话和专业播客生成。

### 核心特性

- 🔒 **隐私优先** - 自托管，数据完全可控
- 🤖 **多模型支持** - 支持 18+ AI 提供商（OpenAI、Anthropic、Ollama、Google 等）
- 🎙️ **专业播客生成** - 1-4 个说话者的高级多说话者播客
- 🔍 **智能搜索** - 全文 + 向量搜索
- 💬 **上下文感知对话** - 基于研究材料的 AI 对话
- 🌐 **多语言 UI** - 支持英语、葡萄牙语、中文、日语、俄语、孟加拉语

### 技术栈

| 层级 | 技术 |
|------|------|
| 前端 | Next.js 16 (React 19) + TypeScript + Tailwind CSS |
| 后端 API | FastAPI + Python 3.11+ |
| 工作流 | LangGraph 状态机 |
| 数据库 | SurrealDB (图数据库) |
| AI 提供商 | Esperanto 库 (18+ 提供商) |
| 内容处理 | content-core 库 |
| 任务队列 | Surreal-Commands |

---

## 统计概览

### 文件分布

```
总计文件数: ~450 个
├── Python 文件 (.py): ~180 个
├── TypeScript/TSX 文件: ~110 个
├── 配置文件: 25+
├── 文档文件: 50+
└── 测试文件: 10 个
```

### 目录结构

```
open-notebook/
├── api/                        # FastAPI 后端 API
│   ├── routers/               # API 路由层 (22个路由文件)
│   ├── main.py                # API 入口
│   ├── models.py              # Pydantic 模型
│   └── *_service.py           # 业务服务层
├── open_notebook/             # 核心后端模块
│   ├── ai/                    # AI 模型管理
│   ├── domain/                # 领域模型
│   ├── graphs/                # LangGraph 工作流
│   ├── database/              # 数据库访问层
│   ├── utils/                 # 工具函数
│   └── podcasts/              # 播客生成
├── frontend/                  # Next.js 前端
│   ├── src/
│   │   ├── app/              # Next.js App Router
│   │   ├── components/       # React 组件
│   │   └── lib/              # 工具库
│   └── package.json
├── docs/                      # 文档
├── tests/                     # 测试
├── prompts/                   # AI 提示词模板
└── commands/                  # 异步命令队列
```

---

## 核心概念图

### 1. 系统架构

```
┌─────────────────────────────────────────────────────────┐
│              Frontend (React/Next.js)                    │
│              @ port 3000                                 │
├─────────────────────────────────────────────────────────┤
│ - Notebooks, sources, notes, chat, podcasts, search UI  │
│ - Zustand state management, TanStack Query              │
│ - Shadcn/ui component library                           │
└────────────────────────┬────────────────────────────────┘
                         │ HTTP REST
┌────────────────────────▼────────────────────────────────┐
│              API (FastAPI)                              │
│              @ port 5055                                │
├─────────────────────────────────────────────────────────┤
│ - REST endpoints for notebooks, sources, notes, chat    │
│ - LangGraph workflow orchestration                      │
│ - Job queue for async operations (podcasts)             │
│ - Multi-provider AI provisioning via Esperanto          │
└────────────────────────┬────────────────────────────────┘
                         │ SurrealQL
┌────────────────────────▼────────────────────────────────┐
│         Database (SurrealDB)                            │
│         Graph database @ port 8000                      │
├─────────────────────────────────────────────────────────┤
│ - Records: Notebook, Source, Note, ChatSession          │
│ - Relationships: source-to-notebook, note-to-source     │
│ - Vector embeddings for semantic search                 │
└─────────────────────────────────────────────────────────┘
```

### 2. LangGraph 工作流

```
┌─────────────────────────────────────────────────────────┐
│                  LangGraph Workflows                    │
├─────────────┬─────────────┬─────────────┬──────────────┤
│   source.py │   chat.py   │   ask.py    │ transformation│
├─────────────┼─────────────┼─────────────┼──────────────┤
│ 内容摄取    │ 对话代理    │ 搜索+合成   │ 内容转换     │
│             │             │             │              │
│ extract →   │  message    │  retrieve   │  transform   │
│ embed →     │   history   │  relevant   │   content    │
│ save        │    + LLM    │  sources →  │              │
│             │             │    LLM      │              │
└─────────────┴─────────────┴─────────────┴──────────────┘
```

### 3. 数据模型关系

```
┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│   Notebook   │───────│    Source    │───────│     Note     │
│   (笔记本)    │ 1:N   │   (来源)      │ 1:N   │   (笔记)     │
├──────────────┤       ├──────────────┤       ├──────────────┤
│ - id         │       │ - id         │       │ - id         │
│ - title      │       │ - title      │       │ - title      │
│ - owner      │       │ - content    │       │ - content    │
│ - created_at │       │ - source_type│       │ - source_id  │
└──────────────┘       └──────────────┘       └──────────────┘
         │                                              │
         └──────────────────────────────────────────────┘
                            │
                    ┌──────────────┐
                    │ ChatSession  │
                    │  (聊天会话)   │
                    └──────────────┘
```

---

## 实体清单

### 后端实体 (Python)

#### 1. API 路由层 (api/routers/)

| 文件 | 实体 | 职责 |
|------|------|------|
| notebooks.py | Router | 笔记本 CRUD 操作 |
| sources.py | Router | 来源管理 (PDF、视频、网页等) |
| notes.py | Router | 笔记管理 |
| chat.py | Router | AI 对话功能 |
| podcasts.py | Router | 播客生成 |
| search.py | Router | 搜索功能 (全文+向量) |
| transformations.py | Router | 内容转换 |
| credentials.py | Router | AI 凭证管理 |
| models.py | Router | AI 模型管理 |
| commands.py | Router | 异步命令队列 |

#### 2. API 服务层 (api/)

| 文件 | 实体 | 职责 |
|------|------|------|
| notebook_service.py | NotebookService | 笔记本业务逻辑 |
| sources_service.py | SourcesService | 来源处理服务 |
| notes_service.py | NotesService | 笔记服务 |
| chat_service.py | ChatService | 对话服务 |
| podcast_service.py | PodcastService | 播客生成服务 |
| search_service.py | SearchService | 搜索服务 |
| credentials_service.py | CredentialsService | 凭证管理服务 |
| models_service.py | ModelsService | AI 模型服务 |
| embedding_service.py | EmbeddingService | 嵌入向量服务 |
| command_service.py | CommandService | 命令队列服务 |

#### 3. LangGraph 工作流 (open_notebook/graphs/)

| 文件 | 实体 | 职责 |
|------|------|------|
| source.py | SourceGraph | 内容摄取工作流 |
| chat.py | ChatGraph | 对话代理工作流 |
| ask.py | AskGraph | 问答搜索工作流 |
| transformation.py | TransformationGraph | 内容转换工作流 |
| source_chat.py | SourceChatGraph | 来源对话工作流 |
| tools.py | Tools | LangChain 工具定义 |

#### 4. AI 模块 (open_notebook/ai/)

| 文件 | 实体 | 职责 |
|------|------|------|
| models.py | ModelManager | AI 模型管理器 |
| provision.py | provision_langchain_model | 模型配置工厂 |
| model_discovery.py | ModelDiscovery | 模型发现服务 |
| connection_tester.py | ConnectionTester | 连接测试器 |
| key_provider.py | KeyProvider | API 密钥管理 |

#### 5. 领域模型 (open_notebook/domain/)

| 文件 | 实体 | 职责 |
|------|------|------|
| notebook.py | Notebook | 笔记本实体 |
| notebook.py | Source | 来源实体 |
| notebook.py | Note | 笔记实体 |
| notebook.py | SourceInsight | 来源洞察 |
| credential.py | Credential | 凭证实体 |
| transformation.py | Transformation | 转换配置 |
| provider_config.py | ProviderConfig | 提供商配置 |
| content_settings.py | ContentSettings | 内容设置 |

#### 6. 数据库层 (open_notebook/database/)

| 文件 | 实体 | 职责 |
|------|------|------|
| repository.py | Repository | 仓库模式基类 |
| migrate.py | MigrationManager | 数据库迁移 |
| async_migrate.py | AsyncMigrationManager | 异步迁移管理 |

### 前端实体 (TypeScript/React)

#### 1. 页面组件 (frontend/src/app/)

| 文件/目录 | 类型 | 职责 |
|-----------|------|------|
| notebooks/ | Page | 笔记本列表/详情页 |
| sources/ | Page | 来源管理页 |
| notes/ | Page | 笔记编辑页 |
| chat/ | Page | 聊天界面 |
| podcasts/ | Page | 播客生成页 |
| search/ | Page | 搜索结果页 |
| settings/ | Page | 设置页面 |

#### 2. 共享组件 (frontend/src/components/)

| 目录 | 说明 |
|------|------|
| ui/ | Shadcn/ui 基础组件 |
| notebooks/ | 笔记本相关组件 |
| sources/ | 来源展示组件 |
| notes/ | 笔记编辑器组件 |
| chat/ | 聊天消息组件 |
| podcasts/ | 播客播放器组件 |

#### 3. 状态管理

| 文件 | 类型 | 职责 |
|------|------|------|
| lib/store/ | Zustand | 全局状态管理 |
| lib/api/ | API Client | HTTP 客户端 |
| lib/hooks/ | Custom Hooks | React Hooks |

---

## 关键依赖关系

### 后端依赖图

```
api/main.py
├── routers/
│   ├── notebooks.py
│   ├── sources.py
│   ├── notes.py
│   ├── chat.py
│   ├── podcasts.py
│   └── ... (22个路由)
├── *_service.py (服务层)
└── models.py (Pydantic 模型)

LangGraph Workflows
├── source.py
│   └── provision_langchain_model()
├── chat.py
│   └── provision_langchain_model()
├── ask.py
│   └── provision_langchain_model()
└── transformation.py
    └── provision_langchain_model()

AI Module
├── models.py (ModelManager)
├── provision.py (工厂函数)
├── model_discovery.py
└── connection_tester.py

Domain Models
├── notebook.py (Notebook, Source, Note)
├── credential.py (Credential)
└── transformation.py (Transformation)
```

### 前端依赖图

```
Next.js App
├── app/
│   ├── notebooks/[id]/page.tsx
│   ├── sources/page.tsx
│   ├── chat/page.tsx
│   └── ...
├── components/
│   ├── ui/ (shadcn)
│   ├── notebooks/
│   ├── sources/
│   └── chat/
├── lib/
│   ├── store/ (Zustand)
│   ├── api/ (API 客户端)
│   └── hooks/
└── package.json
```

---

## 关键路径分析

### 1. 内容摄取流程

```
[用户上传文件/URL]
         ↓
   POST /sources
         ↓
   sources_service.create()
         ↓
   content-core 提取内容
         ↓
   SourceGraph (LangGraph)
   ├── extract → embed → save
         ↓
   SurrealDB 存储
   ├── 文本内容
   ├── 向量嵌入
   └── 元数据
```

### 2. AI 对话流程

```
[用户发送消息]
         ↓
   POST /chat
         ↓
   chat_service.chat()
         ↓
   ChatGraph (LangGraph)
   ├── 加载历史消息
   ├── provision_langchain_model()
   ├── 检索相关来源 (向量搜索)
   ├── LLM 生成回复
   └── 保存消息
         ↓
   [返回 AI 回复 + 引用]
```

### 3. 播客生成流程

```
[用户选择来源并生成播客]
         ↓
   POST /podcasts
         ↓
   podcast_service.create()
         ↓
   Surreal-Commands 提交异步任务
         ↓
   podcast-creator 库
   ├── 生成播客脚本
   ├── 文本转语音 (TTS)
   └── 合并音频
         ↓
   [轮询 /commands/{id} 获取状态]
```

---

## 模块依赖矩阵

```
                    Router  Service  Graph   AI      Domain  DB      Utils
Router              ─       ●        ○       ○       ○       ○       ○
Service             ○       ─        ●       ●       ●       ○       ○
Graph               ○       ○        ─       ●       ●       ○       ●
AI                  ○       ○        ○       ─       ○       ○       ○
Domain              ○       ○        ○       ○       ─       ●       ○
DB                  ○       ○        ○       ○       ○       ─       ○
Utils               ○       ●        ●       ●       ○       ○       ─

● = 强依赖 (直接调用)
○ = 弱依赖 (通过其他层间接调用)
─ = 无依赖
```

---

## 重要发现

### 1. 架构亮点

- **三层架构清晰**: Frontend → API → Database 分离明确
- **LangGraph 工作流**: 使用状态机处理复杂 AI 流程
- **多提供商支持**: 18+ AI 提供商统一接口
- **异步任务队列**: 播客生成等耗时任务异步处理
- **向量搜索**: SurrealDB 内置向量存储和语义搜索

### 2. 代码质量指标

| 指标 | 数值 | 状态 |
|------|------|------|
| Python 文件数 | ~180 | ✅ |
| TypeScript 文件数 | ~110 | ✅ |
| 路由文件数 | 22 | ✅ |
| LangGraph 工作流 | 6 | ✅ |
| 测试文件数 | ~10 | ⚠️ 中等 |
| 文档完整度 | 高 | ✅ |

### 3. 与 BiliNote 的对比

| 特性 | Open Notebook | BiliNote |
|------|--------------|----------|
| 数据库 | SurrealDB (图数据库) | SQLite |
| 工作流引擎 | LangGraph | 自定义串行执行器 |
| 前端框架 | Next.js 16 | React 19 + Vite |
| 播客生成 | ✅ 高级多说话者 | ❌ |
| 浏览器扩展 | ❌ | ✅ |
| 桌面端 | ❌ | ✅ (Tauri) |
| AI 提供商 | 18+ | 4+ |
| 内容处理 | content-core 库 | yt-dlp + FFmpeg |

---

## 查询示例

### Q1: 如何添加一个新的 AI 提供商？

```
路径: open_notebook/ai/
步骤:
1. Esperanto 库已支持大多数提供商
2. 在 credentials.py 添加提供商配置
3. 更新 model_discovery.py 支持模型发现
4. 前端添加提供商 UI 配置
```

### Q2: 内容摄取的完整数据流是怎样的？

```
路径: SourceGraph
数据流:
1. 用户上传文件/URL → sources_service.create()
2. content-core 提取内容
3. SourceGraph 工作流:
   - extract_node: 提取文本和元数据
   - embed_node: 生成向量嵌入
   - save_node: 保存到 SurrealDB
4. 返回 Source 对象
```

### Q3: 如何创建自定义内容转换？

```
路径: open_notebook/graphs/transformation.py
步骤:
1. 在 transformations.py 定义新的转换类型
2. 使用 Prompter 构建提示词
3. provision_langchain_model() 获取 LLM
4. 在 transformation_router.py 添加端点
```

### Q4: 播客生成的异步流程如何实现？

```
路径: podcast_service.py
流程:
1. 提交播客生成请求
2. Surreal-Commands 创建异步命令
3. 后台 worker 处理播客生成
4. podcast-creator 库生成音频
5. 前端轮询 /commands/{id} 获取进度
```

---

## 文件清单

### 核心文件 (Top 20)

| 排名 | 文件路径 | 类型 | 重要性 |
|------|----------|------|--------|
| 1 | api/routers/sources.py | Python | 来源管理核心 (40734 bytes) |
| 2 | api/routers/source_chat.py | Python | 来源对话 (21185 bytes) |
| 3 | api/models.py | Python | API 模型定义 (24091 bytes) |
| 4 | open_notebook/domain/notebook.py | Python | 领域模型 (24966 bytes) |
| 5 | api/credentials_service.py | Python | 凭证管理 (33882 bytes) |
| 6 | open_notebook/ai/model_discovery.py | Python | 模型发现 (26487 bytes) |
| 7 | api/main.py | Python | API 入口 |
| 8 | open_notebook/graphs/chat.py | Python | 对话工作流 |
| 9 | open_notebook/ai/models.py | Python | AI 模型管理 |
| 10 | open_notebook/database/repository.py | Python | 数据访问层 |
| 11 | pyproject.toml | Config | 项目配置 |
| 12 | docker-compose.yml | Config | Docker 部署 |
| 13 | frontend/src/CLAUDE.md | Markdown | 前端架构文档 |
| 14 | open_notebook/CLAUDE.md | Markdown | 核心架构文档 |
| 15 | api/CLAUDE.md | Markdown | API 架构文档 |
| 16 | docs/0-START-HERE/index.md | Markdown | 用户入门 |
| 17 | README.md | Markdown | 项目说明 |
| 18 | frontend/package.json | Config | 前端依赖 |
| 19 | prompts/ | Directory | AI 提示词模板 |
| 20 | tests/test_graphs.py | Python | 工作流测试 |

---

## 附录

### A. 技术词汇表

| 术语 | 说明 |
|------|------|
| LangGraph | LangChain 的工作流编排框架 |
| SurrealDB | 分布式图数据库，支持向量搜索 |
| Esperanto | 多提供商 AI 统一接口库 |
| content-core | 内容提取库 (PDF、视频、网页) |
| podcast-creator | 播客生成库 |
| Surreal-Commands | 异步任务队列系统 |
| Zustand | React 状态管理库 |
| TanStack Query | React 数据获取库 |

### B. 外部依赖

**后端:**
- FastAPI, uvicorn - Web 框架
- SurrealDB - 图数据库
- LangGraph, LangChain - AI 工作流
- Esperanto - AI 提供商接口
- content-core - 内容提取
- podcast-creator - 播客生成
- Pydantic v2 - 数据验证

**前端:**
- Next.js 16, React 19 - 框架
- Tailwind CSS - 样式
- Shadcn/ui - 组件库
- Zustand - 状态管理
- TanStack Query - 数据获取

---

*报告生成完成。如需更详细的某模块分析，请指定模块名称。*
