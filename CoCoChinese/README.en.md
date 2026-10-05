# Cocokalo Anime Video Platform

> Open-source Bilibili-style video site. Nuxt 3 + Nitro + PostgreSQL full-stack implementation. For learning and research only.

---

## Architecture

```
┌─────────────────────────────────────────────────┐
│                  Browser (Client)                 │
│  Nuxt 3 SSR Render → Vue 3 SPA Hydration        │
└──────────────────────┬──────────────────────────┘
                       │ HTTP / Cookies
┌──────────────────────▼──────────────────────────┐
│              Nitro Server Engine                  │
│  ┌──────────┐  ┌──────────┐  ┌───────────────┐  │
│  │ API      │  │Middleware│  │  Utils         │  │
│  │ /api/*   │  │  DB Init │  │ db.ts / pg.ts  │  │
│  │          │  │  Admin   │  │ jwt.ts         │  │
│  │          │  │  Auth    │  │ elastic.ts     │  │
│  │          │  │          │  │ prisma.ts      │  │
│  │          │  │          │  │ redis.ts       │  │
│  └─────┬────┘  └──────────┘  └───────┬───────┘  │
└────────┼──────────────────────────────┼─────────┘
         │                              │
┌────────▼────────┐           ┌────────▼────────┐
│   PostgreSQL    │           │   Redis          │
│   14 tables     │           │   Cache (opt)    │
│   Prisma ORM    │           │                  │
└─────────────────┘           └─────────────────┘
         │
┌────────▼────────┐
│ Elasticsearch    │
│ 7.11.1           │
│ Full-text search │
└─────────────────┘
```

---

## Tech Stack

| Category | Technology | Version |
|----------|-----------|---------|
| Frontend | Nuxt 3 + Vue 3 | latest |
| Server Engine | Nitro (Nuxt built-in) | — |
| Database | PostgreSQL | ≥ 14 |
| ORM | Prisma | 5.x |
| Cache | Redis (ioredis) | ≥ 7 |
| Search | Elasticsearch | 7.11.1 |
| Auth | jsonwebtoken (JWT) | 9.x |
| Password | bcryptjs | 2.x |
| Video Player | Plyr | 3.x |
| UI | Bootstrap 5 + FontAwesome 4 | — |
| Build | Vite | — |
| Runtime | Node.js | ≥ 18 |

---

## Database Schema (14 Tables)

| Table | Purpose | Key Fields |
|-------|---------|------------|
| `users` | User accounts | id, slug, username, password, role, avatar_url |
| `user_profiles` | User profiles | user_id, bio, gender, birthday, location, website |
| `videos` | Video metadata | slug, title, author, image_url, video_url, tags[] |
| `carousels` | Homepage banners | image_url, link, title, order |
| `categories` | Categories (hierarchical) | name, icon, parent_id, order |
| `comments` | Comments (threaded) | video_slug, user_id, content, parent_id |
| `danmaku` | Danmaku overlays | video_slug, user_id, content, time, type, color |
| `favorites` | Favorites | user_id, folder_id, video_id |
| `favorite_folders` | Favorite folders | user_id, name, description, is_public |
| `watch_history` | Watch history | user_id, video_id, progress, watched_at |
| `messages` | Private messages | from_user_id, to_user_id, content, is_read |
| `activities` | Activity feed | user_id, type, target_id, content |
| `user_bans` | Ban records | user_id, operator_id, reason, expires_at |
| `media_files` | File metadata | file_id, file_path, name, file_type, file_size |

---

## API Endpoints

### Public

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/home` | Homepage data (carousels + videos + nav) |
| GET | `/api/carousels` | Carousel items |
| GET | `/api/categories` | Categories |
| GET | `/api/search?q=&page=&size=` | Elasticsearch full-text search |
| GET | `/api/videos?author=&category=` | Video list with optional filters |
| GET | `/api/videos/:id` | Video detail by ID |
| GET | `/api/player/:slug` | Video detail by slug |
| GET/POST | `/api/comments/:slug` | Threaded comments |
| GET/POST | `/api/danmaku/:slug` | Danmaku messages |
| POST | `/api/auth/login` | Login |
| POST | `/api/auth/register` | Register |
| POST | `/api/upload` | Video upload (multipart) |

### Authenticated

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/user/me` | Current user info |
| GET/PUT | `/api/user/profile` | User profile CRUD |
| GET | `/api/user/stats` | User statistics |
| POST | `/api/user/avatar` | Avatar upload |
| GET/POST | `/api/favorites/*` | Favorites management |
| GET/POST | `/api/history/*` | Watch history |
| GET/POST | `/api/messages/*` | Messaging |
| GET/POST | `/api/dynamics/*` | Activity feed |
| GET | `/api/creator/stats` | Creator statistics |

### Admin

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/admin/users` | User list |
| GET | `/api/admin/stats` | Dashboard stats |
| CRUD | `/api/admin/videos/:id` | Video management |
| GET/DELETE | `/api/admin/comments/:id` | Comment moderation |
| CRUD | `/api/admin/bans/:id` | Ban management |

---

## Directory Structure

```
cocokalo/
├── frontend/
│   ├── prisma/schema.prisma       # Prisma ORM models (14 tables)
│   ├── server/
│   │   ├── api/                   # Nitro API endpoints
│   │   │   ├── auth/              # Login / Register
│   │   │   ├── user/              # Profile / Avatar / Stats
│   │   │   ├── videos/            # Video CRUD
│   │   │   ├── comments/          # Threaded comments
│   │   │   ├── danmaku/           # Danmaku
│   │   │   ├── admin/             # Admin dashboard
│   │   │   └── home.ts            # Homepage aggregation
│   │   ├── utils/
│   │   │   ├── prisma.ts          # PrismaClient singleton
│   │   │   ├── db.ts              # Prisma query layer
│   │   │   ├── pg.ts              # Raw pg-pool layer
│   │   │   ├── jwt.ts             # JWT sign/verify
│   │   │   ├── redis.ts           # Redis cache (lazy init)
│   │   │   └── elastic.ts         # ES client + search
│   │   └── middleware/
│   │       ├── database.ts        # Startup: init DB, ES, seed
│   │       └── adminAuth.ts       # Admin route guard
│   ├── components/                # Vue components
│   ├── pages/                     # Vue pages (auto-routed)
│   └── composables/useUser.ts    # Global user state
├── elasticsearch-7.11.1/         # Bundled Elasticsearch
└── README.md
```

---

## Quick Start

### Prerequisites

- Node.js ≥ 18
- PostgreSQL ≥ 14 (localhost:5432)
- Redis ≥ 7 (optional, auto-degrades)

### Commands

```bash
cd frontend
npm install
npx prisma generate     # Generate Prisma client
npm run dev              # Dev mode (auto inits DB + ES)
npm run build            # Production build
node .output/server/index.mjs  # Production start
```

On first startup, `server/middleware/database.ts` auto-creates tables, seeds admin user (`admin / admin123`), and syncs videos to Elasticsearch.

---

## Auth Flow

```
Login/Register → Server signs JWT → httpOnly cookies
     ↓
API request → Read cookies → Verify JWT → Auth check → Response
     ↓
Client useUser() composable syncs login state
```

| Cookie | Content | Attributes |
|--------|---------|------------|
| `cocokalo_token` | Raw JWT string | httpOnly, sameSite=lax, 7d |
| `cocokalo_user` | `{ userId, username, role, token, ... }` | httpOnly, sameSite=lax, 7d |

---

## License

AGPL 3.0. See [LICENSE](LICENSE) for details.

---

> **Disclaimer**: This is an open-source learning project with no commercial purpose. All anime content belongs to their original creators.
