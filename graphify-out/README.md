# Graphify 知识图谱输出目录

此目录包含 Open Notebook 项目的代码知识图谱分析结果，由 [Graphify](https://github.com/safishamsi/graphify) 工具生成。

---

## 目录说明

| 文件 | 说明 |
|------|------|
| `GRAPH_REPORT.md` | 完整的知识图谱分析报告，包含架构概述、实体清单、依赖关系等 |
| `graph.json` | 结构化图谱数据，可用于进一步分析或自定义可视化 |
| `graph.html` | 交互式可视化图谱，在浏览器中打开即可浏览 |

---

## 如何使用图谱工具

### 安装 Graphify

```bash
# 使用 uv (推荐)
uv tool install graphifyy && graphify install

# 或使用 pipx
pipx install graphifyy && graphify install

# 或使用 pip
pip install graphifyy && graphify install
```

> **注意**: PyPI 包名为 `graphifyy`（两个 y），但 CLI 命令是 `graphify`。

### 基本用法

```bash
# 分析当前目录
cd /path/to/your/project
graphify .

# 深度分析模式（更详细的提取）
graphify . --mode deep

# 增量更新（只重新提取变化的文件）
graphify . --update

# 查询知识图谱
graphify query "登录功能是如何实现的？"

# 查找两个实体之间的路径
graphify path "User" "Database"

# 解释概念
graphify explain "Authentication"
```

### 排除文件

创建 `.graphifyignore` 文件来排除不需要分析的文件：

```
node_modules/
*.log
*.min.js
dist/
```

---

## 分析结果说明

### 统计概览

- **总文件数**: ~450 个
- **Python 文件**: ~180 个
- **TypeScript/TSX 文件**: ~110 个
- **API 路由**: 22 个
- **LangGraph 工作流**: 6 个

### 核心架构

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

### 技术栈

| 层级 | 技术 |
|------|------|
| 前端 | Next.js 16 (React 19) + TypeScript + Tailwind CSS + Zustand |
| 后端 API | FastAPI + Python 3.11+ |
| 工作流 | LangGraph 状态机 |
| 数据库 | SurrealDB (图数据库) |
| AI 提供商 | Esperanto 库 (18+ 提供商) |
| 内容处理 | content-core 库 |
| 任务队列 | Surreal-Commands |

---

## 浏览交互式图谱

直接在浏览器中打开 `graph.html`：

```bash
# macOS
open graph.html

# Linux
xdg-open graph.html

# Windows
start graph.html
```

### 图谱操作

- **缩放**: 鼠标滚轮或右上角按钮
- **移动**: 拖拽画布
- **查看详情**: 点击节点，详情显示在左侧面板
- **搜索**: 使用左上角搜索框过滤节点
- **筛选**: 点击类型按钮按类别筛选

---

## 集成到 Claude Code

在 Claude Code 中加载报告，让 AI 基于图谱回答问题：

```bash
# 查看分析报告
cat graphify-out/GRAPH_REPORT.md

# 然后在对话中询问
"根据知识图谱，解释一下内容摄取的完整流程"
"LangGraph 工作流有哪些？"
```

---

## 与 BiliNote 的对比

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

## 贡献

此目录由自动化工具生成，不建议手动修改其中的文件。如需更新分析结果，请重新运行 `graphify` 命令。

---

*生成时间: 2026-05-17*  
*工具版本: Graphify v1.0*
