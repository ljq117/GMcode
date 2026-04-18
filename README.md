# GMcode · 我的项目导航台

> 一个纯前端单页应用，集中管理本地开发项目的元信息，借助 GitHub API 实现零后端云端同步。

🌐 **在线访问**：[https://ljq117.github.io/GMcode/](https://ljq117.github.io/GMcode/)

---

## 简介

GMcode 是一个运行在浏览器中的项目管理看板，帮助开发者快速记录、查找和使用本地项目的关键信息：

- 项目路径、运行命令、安装命令一键复制
- 按技术标签分类，支持关键词搜索
- 数据存储在你自己的 GitHub 仓库，完全私有可控
- Token 仅保存在本地浏览器，不经过任何第三方服务器

---

## 功能特性

| 功能 | 说明 |
|------|------|
| 项目卡片管理 | 新增、编辑、删除项目，信息一览无余 |
| 一键复制 | 点击图标即可复制项目路径或运行命令 |
| 关键词搜索 | 实时过滤项目名称、描述、标签 |
| GitHub 云同步 | 每次增删改自动推送到 GitHub，生成语义化 commit |
| 手动刷新 | 点击刷新图标从 GitHub 拉取最新数据 |
| 跨设备使用 | 在任意设备配置同一仓库 Token，自动同步所有数据 |
| 防冲突写入 | 每次写入前先获取最新文件 SHA，避免多设备并发覆盖 |

---

## 快速开始

### 1. 访问页面

打开 [https://ljq117.github.io/GMcode/](https://ljq117.github.io/GMcode/)

首次访问会自动弹出配置弹窗。

### 2. 配置 GitHub Token

点击右上角 **齿轮图标**，填写以下信息：

| 字段 | 说明 |
|------|------|
| GitHub 用户名 | 你的 GitHub 账号名，如 `ljq117` |
| 仓库名 | 存储数据的仓库名，如 `GMcode` |
| 分支 | 默认 `main` |
| Token | GitHub Personal Access Token（需要 `repo` 权限） |

> **获取 Token**：GitHub → Settings → Developer settings → Personal access tokens → Generate new token
> 勾选 `repo` 权限即可。

填写完成后点击「验证并保存」，页面会自动从 GitHub 加载数据。

### 3. 新增项目

点击右上角「**新增项目**」按钮，填写项目信息后保存，数据自动同步到 GitHub。

---

## 数据同步原理

采用三层容错架构，保证任何网络状况下都能正常使用：

```
┌──────────────────────────────────────────────┐
│  第 1 层（主存储）：GitHub 仓库 projects.json │
│      通过 GitHub Contents API 读写            │
├──────────────────────────────────────────────┤
│  第 2 层（备份缓存）：浏览器 localStorage    │
│      每次推送成功后自动同步写入              │
├──────────────────────────────────────────────┤
│  第 3 层（兜底预设）：内置示例数据           │
│      首次访问或 GitHub 不可用时使用          │
└──────────────────────────────────────────────┘
```

**读取流程**（页面加载 / 点击刷新）：
1. 请求 GitHub API 获取 `projects.json`
2. 失败则读取 localStorage 缓存
3. 无缓存则加载内置预设数据

**写入流程**（新增 / 编辑 / 删除）：
1. 先 GET 获取当前文件的最新 SHA
2. 再 PUT 上传新内容（含 SHA 防冲突）
3. 成功后同步更新 localStorage 缓存

---

## 项目数据字段

每条项目记录包含以下字段：

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | string | 自动 | 唯一标识，格式 `proj-时间戳-随机串` |
| `name` | string | ✅ | 项目名称 |
| `description` | string | ✅ | 项目功能描述 |
| `path` | string | ✅ | 本地存放路径，如 `/Users/one/myapp` |
| `runCommand` | string | ✅ | 运行命令，如 `python3 main.py` |
| `installCommand` | string | — | 安装依赖命令，如 `pip install -r requirements.txt` |
| `tags` | array | — | 技术标签，如 `["Python", "FastAPI"]` |
| `notes` | string | — | 补充备注 |
| `createdAt` | string | 自动 | 创建日期，格式 `YYYY-MM-DD` |

---

## 文件结构

```
GMcode/
├── index.html       # 核心应用文件（HTML + CSS + JS 合一）
├── projects.json    # 项目数据文件（GitHub 主存储）
├── favicon.svg      # 网站图标
├── .gitignore       # 忽略 .DS_Store 和 *.log
└── README.md        # 项目说明文件
```

---

## 注意事项

### Token 安全
- Token 仅存储在**你的浏览器** localStorage 中，不会上传到任何服务器
- 不要将 Token 写入代码或分享给他人
- 如 Token 泄露，立即在 GitHub 上撤销并重新生成

### 只读分享
- 将页面链接分享给他人时，对方**没有 Token，只能查看，无法修改数据**
- 页面本身不包含任何鉴权信息，安全可分享

### 跨设备使用
- 在新设备上打开页面，点击齿轮图标重新填写相同的仓库配置和 Token
- 数据会自动从 GitHub 同步，无需手动导入导出

### 数据备份
- 所有数据存储在 GitHub 仓库中，天然具备版本历史
- 每次操作都会产生一条 Git commit，可随时回滚

---

## License

MIT
