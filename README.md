# 🧭 智途 AI 助手

> 计科·人工智能学院「智途」生涯 AI 助手 —— 面向计算机专业应届生的生涯辅导平台。
> 集求职知识库、模拟面试、简历优化、生涯咨询与学情统计于一体，后端 FastAPI + 前端纯 HTML/CSS/JS，开箱即用。

---

## ✨ 功能一览

| 模块 | 页面 | 说明 |
| --- | --- | --- |
| 登录 | `login.html` | 学生凭「学号 + 姓名」登录；管理员账号密码登录 |
| 求职知识库 | `knowledge.html` | 浏览岗位标准、简历模板、证书赛事、校招资料 |
| 模拟面试 | `interview.html` | AI 面试官提问 + 自动评分，记录面试历史 |
| 简历优化 | `resume.html` | 上传 PDF/DOCX/TXT，AI 解析评分并给出优化建议 |
| 生涯咨询 | `career.html` | 结合资料库的 AI 咨询（RAG），流式回答，支持多会话与预约老师 |
| 题库 / 面经 | `questions.html` | 通用面试题库与面经浏览 |
| 管理员看板 | `dashboard.html` | 学情统计：今日活跃、近 7 天趋势、模块使用、预约管理 |

---

## 🛠 技术栈

- **后端**：Python 3.9+ / FastAPI / Uvicorn
- **数据库**：SQLite（`zhitu.db`）+ SQLAlchemy
- **大模型**：阿里云百炼 DashScope · 通义千问（`qwen-plus`）
- **文档解析**：`python-docx`（DOCX）、`PyPDF2`（PDF）
- **前端**：原生 HTML / CSS / JavaScript（无构建步骤，后端直接托管静态文件）
- **密码**：bcrypt 加密

---

## 📁 目录结构

```
backend/
├── app/
│   ├── main.py                  # FastAPI 入口（挂载路由与静态文件）
│   ├── core/                    # 配置（config.py）、数据库（database.py）
│   ├── models/                  # SQLAlchemy 数据模型
│   ├── schemas/                 # Pydantic 请求/响应模型
│   ├── services/                # LLM 服务（llm_service.py）
│   └── api/v1/endpoints/        # 各模块接口（career / interview / resume / knowledge / analytics…）
├── static/                      # 前端页面（8 个 HTML + common.css）
├── requirements.txt             # Python 依赖
├── setup_db.py                  # 建表脚本
├── init_admin.py                # 初始化管理员账号
├── import_all_docs.py           # 导入生涯资料 / 知识库 / 面试题库
├── 一键启动.bat                  # Windows 一键启动（自动装依赖、建表、放行防火墙、启动服务）
├── 一键部署.bat                  # Cloudflare 隧道一键公网部署
├── .env                         # 通义千问 API Key（DASHSCOPE_API_KEY）
└── zhitu.db                     # SQLite 数据库（首次运行自动生成）
```

---

## 🚀 快速开始

### 方式一：Windows 一键启动（推荐）

1. 双击 `一键启动.bat`。
2. 首次运行会：
   - 检查 Python、自动安装依赖；
   - 若未配置密钥，提示你粘贴通义千问 API Key（自动写入 `.env`）；
   - 自动建表、初始化管理员、导入资料；
   - 放行 8000 端口（需管理员权限，普通运行会自动跳过）；
   - 启动服务并自动打开浏览器。
3. 访问 <http://localhost:8000/static/login.html>。

> 停止服务：关闭启动窗口，或按 `Ctrl+C`。

### 方式二：手动启动

```bash
# 1. 安装依赖
pip install -r requirements.txt

# 2. 配置密钥（.env 文件）
#    内容：DASHSCOPE_API_KEY=sk-你的通义千问密钥

# 3. 初始化数据库与数据
python setup_db.py
python init_admin.py
python import_all_docs.py

# 4. 启动服务
python -m uvicorn app.main:app --host 0.0.0.0 --port 8000
```

浏览器打开 <http://localhost:8000/static/login.html> 即可。

---

## 🌐 局域网 / 公网访问

前端接口使用**相对路径** `/api/v1`，因此本机、局域网、服务器、公网隧道均可正常使用，无需改动代码。

### 局域网（同一 WiFi）

其他设备访问 `http://<本机IP>:8000/static/login.html`。
本机 IP 查看：`ipconfig` 中的 IPv4 地址（如 `192.168.1.103`）。
若无法访问，请用管理员身份运行 `一键启动.bat` 放行 8000 端口。

### 公网（外网可访问）

双击 `一键部署.bat`，自动下载 `cloudflared` 并通过 Cloudflare 快速隧道生成公网地址：

```
https://xxxxx.trycloudflare.com/static/login.html
```

把该链接发给任意设备即可访问。
> 隧道地址每次运行都会变化；停止隧道请在任务管理器结束 `cloudflared.exe` 进程。

---

## 🔑 默认账号

| 角色 | 账号 | 密码 |
| --- | --- | --- |
| 管理员 | `admin` | `123456` |

学生无需注册，登录页直接输入「学号 + 姓名」即可进入（身份保存在当前浏览器）。

---

## ⚙️ 配置说明

`.env` 文件只需一行：

```
DASHSCOPE_API_KEY=sk-你的通义千问密钥
```

- 密钥可在[阿里云百炼控制台](https://bailian.console.aliyun.com/)获取。
- 未配置密钥时系统仍可启动，但 AI 问答 / 评分 / 咨询等能力会降级为规则兜底。

---

## 🔌 API 概览

所有接口统一前缀 `/api/v1`，主要模块：

| 模块 | 前缀 | 说明 |
| --- | --- | --- |
| 生涯咨询 | `/career/*` | 咨询问答（含流式 `/career/ask/stream`）、会话管理、预约 |
| 简历优化 | `/resume/*` | 简历上传解析与评分 |
| 模拟面试 | `/interview/*` | 面试问答与评分 |
| 求职知识库 | `/knowledge/*` | 知识资料检索 |
| 面经 | `/interviews/*` | 面经浏览 |
| 学情分析 | `/analytics/*` | 行为埋点、活跃统计、管理员登录 |

交互式接口文档：启动后访问 <http://localhost:8000/docs>。

---

## 📝 常见问题

- **其他设备打不开页面**：确认后端以 `--host 0.0.0.0` 启动，且 8000 端口已放行（用管理员运行一键启动）。
- **AI 回答为空**：检查 `.env` 里的 `DASHSCOPE_API_KEY` 是否正确、是否欠费。
- **手机端上传简历没反应**：请刷新页面后重试（旧缓存页面存在历史兼容问题，已修复）。
- **改了页面不生效**：静态文件已禁用缓存，`Ctrl+F5` 强刷即可。

---

## 📄 License

仅供学院教学与演示使用。
