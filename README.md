# 智能代码生成系统

## 📖 项目简介

智能代码生成系统是一个基于大语言模型的交互式代码生成平台。它通过**需求澄清对话**，自动识别用户需求中的模糊点，引导用户补充细节，最终生成高质量、可直接运行的代码。系统支持多模型对比、代码自动测试、高质量案例库、用户专业能力测评等功能，为开发者提供从需求到代码的全流程辅助。

## ✨ 核心特性

- **智能需求分析**：自动识别需求中的模糊点（输入规格、输出格式、异常处理等），支持人工增删改模糊点
- **多轮澄清对话**：根据模糊点生成引导性问题，通过对话收集完整需求
- **高质量代码生成**：支持多种大模型（DeepSeek、豆包、通义千问等），生成带有类型注解、错误处理、注释的健壮代码
- **实验组 vs 对照组**：同时生成“澄清后代码”和“直接生成代码”，便于对比需求澄清的效果
- **一键自动化测试**：自动生成 pytest 测试代码，并运行测试，判定代码是否通过
- **用户专业能力测评**：通过在线测评题目，计算用户专业权重（0-100），影响高质量案例的评分计算
- **技能管理**：用户可自定义澄清模板，并将多个模板组合为“技能”，一键应用到新会话
- **高质量案例库**：用户可标记高质量代码，结合专业权重与系统评分生成质量分，供后续参考
- **历史任务追溯**：保存所有会话、生成代码、规格说明书，支持搜索、编辑、删除
- **多模型配置**：管理员可动态添加/启用/停用模型，前端即时切换
- **管理员后台**：用户管理、模型配置、权重调整

## 🛠️ 技术栈

### 后端

- **框架**：FastAPI + Uvicorn
- **数据库**：MySQL + SQLAlchemy ORM
- **认证**：JWT (python-jose)
- **大模型客户端**：requests 调用各模型 API（支持 DeepSeek、豆包、通义千问等）
- **测试执行**：subprocess 调用 pytest，资源限制
- **日志**：logging 模块

### 前端

- **框架**：React 18 + React Router 6
- **UI 库**：Ant Design 5
- **状态管理**：React Context (AuthContext)
- **HTTP 请求**：axios (带拦截器、自动 token 管理)
- **代码高亮**：react-syntax-highlighter
- **图表**：recharts

### 部署要求

- Python 3.8+
- Node.js 16+
- MySQL 5.7+

## 📦 安装与运行

### 1. 克隆仓库

```bash
git clone https://github.com/yourname/intelligent-codegen.git
cd intelligent-codegen
```

### 2. 后端配置

#### 2.1 创建 Python 虚拟环境

```bash
cd backend
python -m venv venv
source venv/bin/activate   # Linux/Mac
venv\Scripts\activate      # Windows
```

#### 2.2 安装依赖

```bash
pip install -r requirements.txt
```

#### 2.3 配置环境变量

复制 `.env.example` 为 `.env`，并填写数据库连接和 API Keys：

```env
DATABASE_URL=mysql+pymysql://root:password@localhost:3306/codegen
SECRET_KEY=your-secret-key-change-in-production

# 模型 API Keys（至少配置一个启用的模型）
DEEPSEEK_API_KEY=sk-xxx
ARK_API_KEY=xxx
DASHSCOPE_API_KEY=sk-xxx
```

#### 2.4 初始化数据库

系统启动时会自动创建表，并创建默认管理员 `admin / admin123`。如果未配置模型 API Key，需要管理员登录后手动添加模型配置。

#### 2.5 启动后端服务

```bash
python main.py
```

默认运行在 `http://localhost:8000`

### 3. 前端配置与运行

#### 3.1 安装依赖

```bash
cd ../frontend
npm install
```

#### 3.2 配置代理（可选）

开发环境下，`src/setupProxy.js` 已将 `/api` 代理到 `http://localhost:8000`。若后端地址不同，请修改该文件。

#### 3.3 启动前端

```bash
npm start
```

访问 `http://localhost:3000`

### 4. 首次登录

- 注册新用户 → 完成专业能力测评 → 进入首页
- 管理员账号：`admin / admin123`（无需测评，自动拥有所有权限）

## 🗺️ 使用指南

### 核心流程：需求澄清助手

1. **选择模型**（如 deepseek）
2. **输入自然语言需求**，例如：“写一个函数，计算两个整数的和”
3. **系统识别模糊点**，弹出对话框，可增、删、改模糊点
4. **确认后进入问答环节**，回答系统生成的引导问题
5. **生成代码**：实验组（澄清后代码）和对照组（直接生成代码）并排展示
6. **一键测试**：系统自动生成测试代码并运行，显示通过率
7. **评分与收录**：用户可给出 0-100 分，高分代码可收录到高质量案例库

### 其他功能模块

- **历史任务**：查看所有会话生成的代码、评分，支持重新编辑评分
- **规格说明书**：保存每次会话的原始需求、澄清规格和对话历史，支持 JSON 格式编辑
- **技能管理**：创建澄清模板（维度+问题），组合成技能，新会话一键应用
- **高质量案例**：浏览所有用户标记的优质代码，管理员可删除
- **代码分析**：展示评分趋势、各模型平均分、测试通过率对比
- **模型配置**（管理员）：添加/编辑/启用/停用大模型
- **用户管理**（管理员）：增删改用户、重置密码、调整专业权重

## 🔌 API 概览

主要 API 端点（前缀 `/api`）：

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/register` | 用户注册 |
| POST | `/login` | 登录 |
| POST | `/start` | 创建新会话 |
| POST | `/detect` | 分析需求模糊点 |
| POST | `/confirm_ambiguities` | 确认模糊点并开始澄清 |
| POST | `/answer` | 回答澄清问题 |
| POST | `/generate_test_code` | 生成测试代码 |
| POST | `/test_code` | 执行测试 |
| GET | `/session/{id}/status` | 获取会话状态 |
| POST | `/rate_code` | 代码评分 |
| GET | `/analysis/*` | 分析数据 |
| GET/POST/PUT/DELETE | `/model_configs` | 模型配置管理（需管理员） |
| GET/POST/PUT/DELETE | `/skills` | 技能管理 |
| GET/POST/PUT/DELETE | `/templates` | 模板管理 |

详细 API 文档可访问 `http://localhost:8000/docs`（自动生成的 Swagger UI）。

## 🧪 测试与验证

- 代码可测试性检查：自动检测代码是否包含函数定义、是否包含 GUI 框架、是否有语法错误
- 单元测试执行：生成 pytest 测试代码，在隔离环境中运行（CPU 时间 5 秒、内存 256MB 限制）
- 测试结果存入数据库，支持二值判定（所有用例通过 = 通过）

## 📁 项目结构

```
.
├── backend/
│   ├── agents/               # 智能体：分析、提问、代码生成、历史、回滚
│   ├── auth.py               # JWT 认证
│   ├── database.py           # SQLAlchemy 模型与连接
│   ├── llm_clients.py        # 多模型客户端封装
│   ├── main.py               # FastAPI 主入口
│   ├── models.py             # Pydantic 模型
│   ├── orchestrator.py       # 会话状态机
│   └── requirements.txt
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── pages/            # 页面组件：CodeGen, Dashboard, History, ...
│   │   ├── AuthContext.js    # 认证上下文
│   │   ├── Layout.js         # 布局组件
│   │   ├── App.js            # 路由配置
│   │   └── setupProxy.js     # 开发代理
│   └── package.json
└── README.md
```