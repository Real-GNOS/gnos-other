# Cocokalo 二次元视频平台

> 开源仿 B 站视频网站，Nuxt 3 + Nitro + PostgreSQL 全栈实现。仅供学习研究。

---

## 目录

- [系统架构](#系统架构)
- [技术栈](#技术栈)
- [数据库设计](#数据库设计)
- [API 概览](#api-概览)
- [项目结构](#项目结构)
- [快速开始](#快速开始)
- [认证体系](#认证体系)
- [部署说明](#部署说明)
- [许可证](#许可证)

---

## 效果预览

| 首页 | 播放页（弹幕） | 动漫内容 | 移动端 |
|:---:|:---:|:---:|:---:|
| ![首页](frontend/public/screenshots/homepage.png) | ![播放页](frontend/public/screenshots/player.png) | ![动漫](frontend/public/screenshots/author-anime.png) | ![移动端](frontend/public/screenshots/mobile.png) |

---

## 系统架构

```
┌─────────────────────────────────────────────────┐
│                  浏览器 (Client)                  │
│  Nuxt 3  SSR 渲染  ←  Vue 3 SPA  hydration      │
└──────────────────────┬──────────────────────────┘
                       │ HTTP / Cookies
┌──────────────────────▼──────────────────────────┐
│              Nitro Server Engine                  │
│  ┌──────────┐  ┌──────────┐  ┌───────────────┐  │
│  │ API 路由   │  │ 中间件    │  │  工具层        │  │
│  │ /api/*    │  │ 数据库初始化│  │ db.ts / pg.ts │  │
│  │           │  │ 管理后台鉴权│  │ jwt.ts        │  │
│  │           │  │          │  │ elastic.ts    │  │
│  │           │  │          │  │ prisma.ts     │  │
│  │           │  │          │  │ redis.ts      │  │
│  └─────┬─────┘  └──────────┘  └───────┬───────┘  │
└────────┼───────────────────────────────┼─────────┘
         │                               │
┌────────▼────────┐            ┌────────▼────────┐
│   PostgreSQL    │            │   Redis          │
│   14 张表        │            │   缓存 (可选)      │
│   Prisma ORM     │            │                   │
└─────────────────┘            └─────────────────┘
         │
┌────────▼────────┐
│ Elasticsearch    │
│ 7.11.1           │
│ 全文搜索 + 中文分词 │
└─────────────────┘
```

### 分层说明

| 层 | 职责 | 关键技术 |
|----|------|---------|
| **表现层** | Vue 3 SPA 组件渲染、用户交互 | Nuxt 3 SSR, Bootstrap 5, Plyr |
| **路由层** | 服务端 API 路由、请求响应 | Nitro, h3, defineEventHandler |
| **业务层** | 认证鉴权、数据校验、业务逻辑 | JWT, bcrypt, requireAuth |
| **数据访问层** | ORM 查询、缓存、全文搜索 | Prisma, ioredis, Elasticsearch |
| **存储层** | 持久化、缓存、搜索索引 | PostgreSQL 14, Redis 7, ES 7.11 |

---

## 技术栈

| 类别 | 技术 | 版本 |
|------|------|------|
| 前端框架 | Nuxt 3 + Vue 3 | latest |
| 服务端引擎 | Nitro (Nuxt 内置) | — |
| 数据库 | PostgreSQL | ≥ 14 |
| ORM | Prisma | 5.x |
| 缓存 | Redis (ioredis) | ≥ 7 |
| 搜索引擎 | Elasticsearch | 7.11.1 |
| 认证 | jsonwebtoken (JWT) | 9.x |
| 密码 | bcryptjs | 2.x |
| 视频播放 | Plyr | 3.x |
| UI 框架 | Bootstrap 5 + FontAwesome 4 | — |
| 构建工具 | Vite | — |
| 运行环境 | Node.js | ≥ 18 |

---

## 数据库设计

### ER 关系总览

```
users (1) ── (1) user_profiles
users (1) ── (N) videos              [author = username]
users (1) ── (N) comments
users (1) ── (N) danmaku             [nullable, SET NULL]
users (1) ── (N) favorites
users (1) ── (N) favorite_folders
users (1) ── (N) watch_history
users (1) ── (N) messages            [from_user / to_user]
users (1) ── (N) activities
users (1) ── (N) user_bans           [operator: users]
videos (1) ── (N) comments           [via slug]
videos (1) ── (N) danmaku            [via slug]
videos (1) ── (N) favorites          [via id]
videos (1) ── (N) watch_history      [via id]
categories (1) ── (N) categories     [自引用 parent_id]
```

### 14 张表说明

| 表名 | 用途 | 核心字段 |
|------|------|---------|
| `users` | 用户账户 | id, slug, username, password, role, avatar_url, display_name |
| `user_profiles` | 用户资料 | user_id, bio, gender, birthday, location, website, followers |
| `videos` | 视频元数据 | slug, title, description, author, image_url, video_url, tags[], category |
| `carousels` | 首页轮播图 | image_url, link, title, order |
| `categories` | 视频分类（支持层级） | name, icon, parent_id, order |
| `comments` | 视频评论（支持嵌套） | video_slug, user_id, content, parent_id |
| `danmaku` | 弹幕 | video_slug, user_id, content, time, type, color |
| `favorites` | 收藏 | user_id, folder_id, video_id |
| `favorite_folders` | 收藏夹 | user_id, name, description, is_public |
| `watch_history` | 观看历史 | user_id, video_id, progress, watched_at |
| `messages` | 站内信 | from_user_id, to_user_id, content, is_read |
| `activities` | 用户动态 | user_id, type, target_id, content |
| `user_bans` | 封禁记录 | user_id, operator_id, reason, expires_at, is_active |
| `media_files` | 媒体文件元信息 | file_id, file_path, name, file_type, file_size |

### 索引策略

- 所有主键自增 SERIAL
- `users.slug`、`users.username`、`users.email` — 唯一索引
- `videos.slug` — 唯一索引
- `media_files.file_id` — 唯一索引
- `comments.video_slug`、`danmaku.video_slug` — 外键索引（级联删除）
- 所有 `user_id` 外键 — 索引（级联删除/置空）

---

## API 概览

### 公开接口

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/home` | 首页数据（轮播 + 视频列表 + 导航 + 页脚） |
| GET | `/api/carousels` | 轮播项列表 |
| GET | `/api/categories` | 分类列表 |
| GET | `/api/search?q=&page=&size=` | Elasticsearch 全文搜索 |
| GET | `/api/videos?author=&category=` | 视频列表（可选过滤） |
| GET | `/api/videos/:id` | 视频详情（按 ID） |
| GET | `/api/player/:slug` | 视频详情（按 slug） |
| GET | `/api/comments/:slug` | 视频评论（含嵌套回复） |
| POST | `/api/comments/:slug` | 发表评论（需登录） |
| GET | `/api/danmaku/:slug` | 视频弹幕列表 |
| POST | `/api/danmaku/:slug` | 发送弹幕 |
| POST | `/api/auth/login` | 登录 |
| POST | `/api/auth/register` | 注册 |
| POST | `/api/upload` | 上传视频（multipart） |

### 认证接口

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/user/me` | 当前用户信息 |
| GET | `/api/user/profile` | 用户完整资料 |
| PUT | `/api/user/profile` | 更新资料 |
| GET | `/api/user/stats` | 用户统计 |
| POST | `/api/user/avatar` | 上传头像 |
| POST | `/api/user/display-name` | 更新显示名 |
| GET/POST | `/api/favorites/*` | 收藏管理 |
| GET/POST | `/api/history/*` | 历史管理 |
| GET/POST | `/api/messages/*` | 消息管理 |
| GET/POST | `/api/dynamics/*` | 动态管理 |
| GET | `/api/creator/stats` | 创作者统计 |

### 管理接口

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/admin/login` | 管理员登录 |
| GET | `/api/admin/users` | 用户列表 |
| GET | `/api/admin/stats` | 仪表盘统计 |
| GET/PUT/DELETE | `/api/admin/videos/:id` | 视频管理 |
| GET/DELETE | `/api/admin/comments/:id` | 评论管理 |
| GET/POST/PUT/DELETE | `/api/admin/bans/:id` | 封禁管理 |

---

## 项目结构

```
cocokalo/
├── frontend/                          # Nuxt 3 全栈项目
│   ├── prisma/
│   │   └── schema.prisma             # Prisma ORM 模型定义（14 张表）
│   │
│   ├── server/
│   │   ├── api/
│   │   │   ├── auth/                  # 登录 / 注册
│   │   │   ├── user/                  # 用户资料 / 头像 / 统计
│   │   │   ├── videos/                # 视频 CRUD
│   │   │   ├── player/                # 播放页数据
│   │   │   ├── comments/              # 评论 + 嵌套回复
│   │   │   ├── danmaku/               # 弹幕
│   │   │   ├── favorites/             # 收藏 + 收藏夹
│   │   │   ├── history/               # 观看历史
│   │   │   ├── messages/              # 站内信
│   │   │   ├── dynamics/              # 动态
│   │   │   ├── creator/               # 创作中心
│   │   │   ├── search/                # Elasticsearch 搜索
│   │   │   ├── categories/            # 分类
│   │   │   ├── carousels/             # 轮播
│   │   │   ├── admin/                 # 管理后台 API
│   │   │   │   ├── stats/             # 仪表盘
│   │   │   │   ├── users/             # 用户管理
│   │   │   │   ├── videos/            # 视频管理
│   │   │   │   ├── comments/          # 评论管理
│   │   │   │   └── bans/              # 封禁管理
│   │   │   └── home.ts                # 首页聚合数据
│   │   │
│   │   ├── utils/
│   │   │   ├── prisma.ts             # PrismaClient 单例
│   │   │   ├── db.ts                 # Prisma 数据访问层
│   │   │   ├── pg.ts                 # 原始 pg 连接 + 辅助函数
│   │   │   ├── jwt.ts                # JWT 签发 / 验证
│   │   │   ├── redis.ts              # Redis 缓存层（惰性初始化）
│   │   │   └── elastic.ts            # ES 客户端 + 索引 + 搜索
│   │   │
│   │   ├── middleware/
│   │   │   ├── database.ts           # Nitro 插件：启动 ES、初始化数据库
│   │   │   └── adminAuth.ts          # 管理后台服务端鉴权
│   │   │
│   │   └── routes/                    # 自定义路由（头像、视频流）
│   │
│   ├── components/
│   │   ├── Nav.vue                    # 顶部导航栏
│   │   ├── Carousel.vue              # 首页轮播
│   │   ├── VideosLine.vue            # 视频卡片行（3 种视觉样式）
│   │   ├── PrimaryPageTab.vue        # 分类标签
│   │   ├── DanmakuOverlay.vue        # 弹幕叠加层
│   │   ├── Footer.vue                # 页脚
│   │   └── GuangGao.vue             # 广告横幅
│   │
│   ├── pages/
│   │   ├── index.vue                 # 首页（轮播 + 视频网格）
│   │   ├── login.vue / register.vue  # 登录 / 注册
│   │   ├── player/[id].vue           # 视频播放页
│   │   ├── upload.vue                # 视频投稿
│   │   ├── user/center.vue           # 用户中心
│   │   ├── creator.vue               # 创作中心
│   │   ├── favorites.vue             # 收藏
│   │   ├── history.vue               # 历史
│   │   ├── messages.vue              # 消息
│   │   ├── dynamics.vue              # 动态
│   │   ├── vip.vue                   # 大会员
│   │   ├── search.vue                # 搜索结果
│   │   ├── edit/[id].vue             # 编辑视频
│   │   └── admin/                    # 管理后台页面
│   │
│   ├── composables/
│   │   └── useUser.ts               # 全局用户状态
│   │
│   ├── assets/css/                   # 样式文件
│   ├── public/
│   │   ├── videos/                   # 上传的视频文件
│   │   ├── uploads/avatars/          # 用户头像
│   │   └── images/                   # 轮播图、封面等静态资源
│   │
│   └── .env                          # 环境变量（DB / Redis / ES）
│
├── elasticsearch-7.11.1/             # 内置 Elasticsearch
├── package.json                      # 根 package（ES 相关）
└── README.md
```

---

## 快速开始

### 前置条件

- Node.js ≥ 18
- PostgreSQL ≥ 14（运行于 localhost:5432）
- Redis ≥ 7（可选，无 Redis 时缓存层自动降级）

### 启动步骤

```bash
# 1. 进入项目目录
cd frontend

# 2. 安装依赖
npm install

# 3. 配置环境变量（已提供默认值，可按需修改）
#    DATABASE_URL, REDIS_URL, PG_HOST/PORT/USER/PASSWORD/DB

# 4. 生成 Prisma 客户端
npx prisma generate

# 5. 开发模式启动（自动初始化数据库表、同步 ES）
npm run dev

# 6. 生产构建
npm run build

# 7. 生产启动
node .output/server/index.mjs
```

### 首次启动说明

首次启动时 `server/middleware/database.ts` 会自动执行：

1. 检查 Elasticsearch 是否已运行（否则启动内置 ES）
2. 创建数据库 `cocokalo`（如不存在）
3. 创建全部 14 张表（IF NOT EXISTS）
4. 创建默认管理员 `admin / admin123`
5. 填充默认轮播数据
6. 同步已有视频到 Elasticsearch 索引

---

## 认证体系

### 流程

```
注册/登录 → 服务端签发 JWT → 设置 httpOnly Cookie
     ↓
请求 API   → 读取 Cookie → 验证 JWT → 鉴权/鉴权 → 响应
     ↓
客户端 useUser() composable 同步登录状态
```

### Cookie 方案

| Cookie | 内容 | 属性 |
|--------|------|------|
| `cocokalo_token` | 原始 JWT 字符串 | httpOnly, sameSite=lax, 7 天 |
| `cocokalo_user` | JSON `{ userId, username, role, avatar_url, token }` | httpOnly, sameSite=lax, 7 天 |

### 鉴权函数

| 函数 | 作用 |
|------|------|
| `getAuthFromEvent(event)` | 从 Cookie 解析用户信息 |
| `requireAuth(event)` | 鉴权 + 封禁检查（401/403） |
| `requireAdmin(event)` | 鉴权 + 管理员校验 |

---

## 部署说明

### 生产环境要求

- Node.js ≥ 18 LTS
- PostgreSQL ≥ 14（建议独立部署）
- Elasticsearch 7.x（或使用内置版本）
- pm2 或 systemd 管理进程

### 推荐部署拓扑

```
              负载均衡（Nginx / Caddy）
                      │
              ┌───────┴───────┐
              │               │
         Nuxt SSR         静态资源
        (node:3000)      (public/)
              │               │
        ┌─────┴─────┐         │
        │           │         │
    PostgreSQL   Redis        │
    Elasticsearch              │
        │                      │
        └──────────────────────┘
```

### 环境变量

| 变量 | 默认值 | 说明 |
|------|--------|------|
| `DATABASE_URL` | `postgresql://postgres:123@localhost:5432/cocokalo` | Prisma 连接串 |
| `REDIS_URL` | `redis://localhost:6379` | Redis 连接串 |
| `PG_HOST` | `localhost` | 数据库主机 |
| `PG_PORT` | `5432` | 数据库端口 |
| `PG_USER` | `postgres` | 数据库用户 |
| `PG_PASSWORD` | `123` | 数据库密码 |
| `PG_DB` | `cocokalo` | 数据库名 |

---

## 许可证

AGPL 3.0。详情见 [LICENSE](LICENSE) 文件。

---

## 项目推荐

1. 你的自荐申请将由我们人工审核，介绍完善、质量高的项目才能被推荐
2. 编辑可能会根据需要来调整推荐软件的描述信息
3. 申请提交后，如一个工作日内未得到推荐，则默认表示被拒

---

> **免责声明**：本项目为开源学习项目，无任何商业目的。禁止用于任何非学术活动。所有二次元内容版权归原作者所有。
