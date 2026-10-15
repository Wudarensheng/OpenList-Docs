---
title:
  en: OpenList Worker
  zh-CN: OpenList Worker
icon: iconfont icon-cloud
# This control sidebar order
top: 40
# A page can have multiple categories
categories:
  - guide
  - installation
tag:
  - Worker
  - Cloudflare
  - EdgeOne
  - Install
---

## What is OpenList Worker { lang="en" }

## OpenList Worker 是什么 { lang="zh-CN" }

::: en
[**OpenList Worker**](https://github.com/OpenListTeam/OpenList-Worker)（仓库 `OpenList-TSWorker`）是 OpenList 官方的 TypeScript + Serverless 移植版。后端由 Go 重写为运行于边缘平台上的 TypeScript 服务，前端复用官方 [OpenList-Frontend](https://github.com/OpenListTeam/OpenList-Frontend)（SolidJS），无需自备服务器即可一键部署。

- **后端**：Hono.js（TypeScript）
- **运行平台**：Cloudflare Workers / EdgeOne Functions / Alibaba Cloud ESA / Vercel / AWS Lambda
- **数据库**：Cloudflare D1（SQLite）、MySQL / MariaDB / PostgreSQL / SQL Server、Cloudflare KV
- **许可证**：AGPL-3.0
  :::

::: zh-CN
[**OpenList Worker**](https://github.com/OpenListTeam/OpenList-Worker)（仓库 `OpenList-TSWorker`）是 OpenList 官方的 TypeScript + Serverless 移植版。后端由 Go 重写为运行于边缘平台上的 TypeScript 服务，前端复用官方 [OpenList-Frontend](https://github.com/OpenListTeam/OpenList-Frontend)（SolidJS），无需自备服务器即可一键部署。

- **后端**：Hono.js（TypeScript）
- **运行平台**：Cloudflare Workers / EdgeOne Functions / 阿里云 ESA / Vercel / AWS Lambda
- **数据库**：Cloudflare D1（SQLite）、MySQL / MariaDB / PostgreSQL / SQL Server、Cloudflare KV
- **许可证**：AGPL-3.0
  :::

## Supported Platforms { lang="en" }

## 支持平台 { lang="zh-CN" }

::: en
| Platform | Persistence | One-click | Notes |
| :--- | :--- | :---: | :--- |
| Cloudflare Workers | D1 / KV / JSON | ✅ | Recommended, global edge network |
| EdgeOne Makers（国际站 / 中国站） | EdgeOne Blob / KV | ✅ | Tencent Cloud edge platform |
| Alibaba Cloud ESA | EdgeKV | ✅ | Alibaba edge function |
| Vercel | In-memory（JSON） | — | No persistence by default |
| AWS Lambda（Serverless Framework） | In-memory（JSON） | — | `serverless.yml` |
:::

::: zh-CN
| 平台 | 持久化 | 一键部署 | 说明 |
| :--- | :--- | :---: | :--- |
| Cloudflare Workers | D1 / KV / JSON | ✅ | 推荐，全球边缘网络 |
| EdgeOne Makers（国际站 / 中国站） | EdgeOne Blob / KV | ✅ | 腾讯云边缘平台 |
| 阿里云 ESA | EdgeKV | ✅ | 阿里云边缘函数 |
| Vercel | 内存（JSON） | — | 默认无持久化 |
| AWS Lambda（Serverless Framework） | 内存（JSON） | — | `serverless.yml` |
:::

## One-click Deploy { lang="en" }

## 一键部署 { lang="zh-CN" }

::: en
Click the button below to deploy to the corresponding platform:
:::

::: zh-CN
点击下方按钮，即可将本项目一键部署到对应平台：
:::

|                                                                                                                                                         EdgeOne Makers · 国际站                                                                                                                                                          |                                                                                                                                                                     EdgeOne Makers · 中国站                                                                                                                                                                     |                                                                         Cloudflare Workers · 全球站                                                                         |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| [![使用 EdgeOne 部署](https://cdnstatic.tencentcs.com/edgeone/pages/deploy.svg)](https://edgeone.ai/pages/new?project-name=openlist-tsworker&repository-url=https://github.com/OpenListTeam/OpenList-Worker&install-command=pnpm%20install%20--no-frozen-lockfile&build-command=pnpm%20run%20build&output-directory=dist&env=JWT_SECRET) | [![使用 EdgeOne 部署](https://cdnstatic.tencentcs.com/edgeone/pages/deploy.svg)](https://console.cloud.tencent.com/edgeone/pages/new?project-name=openlist-tsworker&repository-url=https://github.com/OpenListTeam/OpenList-Worker&install-command=pnpm%20install%20--no-frozen-lockfile&build-command=pnpm%20run%20build&output-directory=dist&env=JWT_SECRET) | [![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/OpenListTeam/OpenList-Worker) |

::: en

> If Cloudflare prompts `无法获取存储库内容`（cannot fetch repository content）, fork this project first, then deploy by connecting to the GitHub repository.
> :::

::: zh-CN

> 若 Cloudflare 提示 `无法获取存储库内容`，则您需要先 Fork 本项目，再通过连接到 GitHub 仓库功能部署。
> :::

## Initialization { lang="en" }

## 部署后初始化 { lang="zh-CN" }

::: en
::: tip
After deployment, the first visit to the site automatically enters an **installation wizard**. Set the admin account and password in the browser to complete initialization — no pre-configured `ADMIN_PASS` is required.
:::

::: zh-CN
::: tip
部署完成后，首次访问站点会自动进入**安装向导**，在浏览器中设置管理员账号与密码即可完成初始化，无需预先配置 `ADMIN_PASS`。
:::

## Environment Variables { lang="en" }

## 环境变量 / Secrets { lang="zh-CN" }

::: en
| Variable | Required | Default | Description |
| :--- | :--- | :--- | :--- |
| `JWT_SECRET` | Recommended | — | JWT signing key, also used for data encryption and cron task auth. Auto-generated and persisted to KV when unset |
| `DB_FORMAT` | Optional | `map` | Storage format: `map` (whole JSON) / `key` (per-key) / `sql` (relational, Go-compatible) |
| `DB_DRIVER` | Optional | `auto` | Database driver: `auto` / `blob` / `cfkv` / `kv` / `d1` / `do` / `mysql` |
| `CF_ACCOUNT` | Optional | — | Cloudflare account ID (required for `cfkv` mode) |
| `CF_KV_UUID` | Optional | — | Cloudflare KV namespace ID (required for `cfkv` mode) |
| `CF_API_KEY` | Optional | — | Cloudflare API token (required for `cfkv` mode) |
| `MYSQL_URL` | Optional | — | MySQL connection string (or use `MYSQL_*` fields below) |
| `MYSQL_HOST` / `MYSQL_PORT` / `MYSQL_USER` / `MYSQL_PASS` / `MYSQL_NAME` | Optional | — | MySQL connection fields (`DB_DRIVER = mysql`) |
| `ADMIN_PASS` | Optional | — | Skip the install wizard and auto-initialize admin with this password |
| `ALLOW_URLS` | Optional | — | Comma-separated CORS allowlist |
| `MAX_UPLOAD` | Optional | `26214400` | Max size (bytes) for a whole upload (`/put`, `/form`) |
| `MAX_UPPART` | Optional | `16777216` | Max size (bytes) per chunk in multipart upload |
| `ASSET_URLS` | Optional | — | Frontend asset CDN base URL, supports the `$version` placeholder |
| `ALLOW_SEED` | Optional | — | Allowlist of hosts permitted as seed-data sources |
| `SINGLEFLIGHT` | Optional | `auto` | Concurrent dedup mode: `auto` / `db` / `memory` / `off` |
| `SINGLEFLIGHT_HANDOFF_MS` | Optional | `1000` | How long (ms) a finished result stays in the table for already-waiting instances |
| `SINGLEFLIGHT_LOCK_TTL_MS` | Optional | `30000` | Max lock hold time (ms) for the executor; an expired lock can be taken over |
| `SINGLEFLIGHT_POLL_MS` | Optional | `200` | Poll interval (ms) for waiting instances; below 100 is not recommended |
| `SINGLEFLIGHT_WAIT_MS` | Optional | `15000` | Max wait (ms) for waiting instances, then they execute themselves |
:::

::: zh-CN
| 变量 | 必要 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| `JWT_SECRET` | 推荐 | — | JWT 签名密钥，同时用于数据加密与定时任务鉴权。未配置时自动生成并持久化到 KV |
| `DB_FORMAT` | 可选 | `map` | 数据存储格式：`map`（整对象 JSON）/ `key`（分 key 存储）/ `sql`（关系表，与 Go 后端一致） |
| `DB_DRIVER` | 可选 | `auto` | 数据库驱动：`auto` / `blob` / `cfkv` / `kv` / `d1` / `do` / `mysql` |
| `CF_ACCOUNT` | 可选 | — | Cloudflare 账号 ID（`cfkv` 模式必填） |
| `CF_KV_UUID` | 可选 | — | Cloudflare KV namespace ID（`cfkv` 模式必填） |
| `CF_API_KEY` | 可选 | — | Cloudflare API token（`cfkv` 模式必填） |
| `MYSQL_URL` | 可选 | — | MySQL 连接串（或用下方 `MYSQL_*` 分项） |
| `MYSQL_HOST` / `MYSQL_PORT` / `MYSQL_USER` / `MYSQL_PASS` / `MYSQL_NAME` | 可选 | — | MySQL 分项连接配置（`DB_DRIVER = mysql`） |
| `ADMIN_PASS` | 可选 | — | 跳过安装向导，以该密码自动初始化 admin |
| `ALLOW_URLS` | 可选 | — | CORS 允许白名单（逗号分隔） |
| `MAX_UPLOAD` | 可选 | `26214400` | 单次整体上传（`/put`、`/form`）大小上限（字节） |
| `MAX_UPPART` | 可选 | `16777216` | 分片上传单片大小上限（字节） |
| `ASSET_URLS` | 可选 | — | 前端静态资源 CDN 地址，支持 `$version` 占位符 |
| `ALLOW_SEED` | 可选 | — | 允许作为种子数据来源的主机白名单 |
| `SINGLEFLIGHT` | 可选 | `auto` | 并发去重模式：`auto` / `db` / `memory` / `off` |
| `SINGLEFLIGHT_HANDOFF_MS` | 可选 | `1000` | 执行完成后结果在表中保留多久（ms），供正在等待的实例读取 |
| `SINGLEFLIGHT_LOCK_TTL_MS` | 可选 | `30000` | 执行者持锁上限（ms），到期可被其他实例接管 |
| `SINGLEFLIGHT_POLL_MS` | 可选 | `200` | 等待方轮询间隔（ms），不建议低于 100 |
| `SINGLEFLIGHT_WAIT_MS` | 可选 | `15000` | 等待方最长等待（ms），超时后自行执行 |
:::

### Concurrent deduplication (singleflight) { lang="en" }

### 并发去重（singleflight） { lang="zh-CN" }

::: en
A deployment runs across multiple isolates, so an in-process map alone cannot merge duplicate calls across instances. With singleflight, identical directory listing / metadata requests arriving at the same time trigger only one upstream request, and the others wait and reuse the same result — cutting rate limiting, account bans and CPU timeouts.

- **Two levels**: in-process merge (all modes) + table coordination via `x_singleflight` (`db` mode, across isolates).
- **Modes** (`SINGLEFLIGHT`, default `auto`): `auto` uses table coordination when a SQL backend (d1 / mysql / do) is available, otherwise in-process; `db` forces the table; `memory` is in-process only; `off` disables dedup entirely.
- **No result caching**: only calls that observe a running executor on arrival reuse the result, so stale reads (e.g. a deleted file still showing after refresh) cannot happen.
- **Availability first**: coordination-layer failures degrade to "each caller executes once"; exceptions from the business function itself are rethrown without retry.
- The `x_singleflight` table is created automatically by the SQL driver and holds runtime data only. Statistics (`executed` / `coalesced` / `shared` / `fallback`) are available in the `singleflight` field of `/api/debug/info`.

See [Environment Variables](/ecosystem/official_worker/guide_env) for details.
:::

::: zh-CN
一个部署会同时运行多个 isolate，仅靠进程内的 Map 无法跨实例合并重复调用。启用 singleflight 后，同一时刻相同的目录列表 / 元信息请求只向上游网盘发起一次，其余请求等待并复用同一结果，从而减少限流、封号与 CPU 超时。

- **两级去重**：进程内合并（所有模式）+ 通过 `x_singleflight` 表协调（`db` 模式，跨 isolate）。
- **模式**（`SINGLEFLIGHT`，默认 `auto`）：`auto` 在有 SQL 库（d1 / mysql / do）时用表协调，否则退回进程内；`db` 强制用表；`memory` 仅进程内；`off` 完全关闭去重。
- **不做结果缓存**：只有「到达时观察到执行者正在运行」的请求才复用结果，因此不会出现「删除文件后刷新仍看到被删文件」这类脏读。
- **可用性优先**：协调层异常一律降级为「各自执行一次」；业务函数自身的异常原样抛出且不重试。
- `x_singleflight` 表由 SQL 驱动自动创建，仅承载运行时数据。运行统计（`executed` / `coalesced` / `shared` / `fallback`）见 `/api/debug/info` 的 `singleflight` 字段。

详见[配置变量](/ecosystem/official_worker/guide_env)。
:::

## Data Backend（DB_FORMAT & DB_DRIVER） { lang="en" }

## 数据存储后端（DB_FORMAT 与 DB_DRIVER） { lang="zh-CN" }

::: en
OpenList Worker separates persistence into two orthogonal layers:

- **`DB_FORMAT`** — how data is serialized and stored
- **`DB_DRIVER`** — which underlying storage system is used

### `DB_FORMAT`（data storage format）

| Value            | Description                                                                 |
| :--------------- | :-------------------------------------------------------------------------- |
| `map`（default） | Whole object serialized as a single JSON value, ideal for KV / Blob storage |
| `key`            | Per-key storage, one record per entity (avoids large JSON)                  |
| `sql`            | Relational tables, fully compatible with the Go backend (for D1 / MySQL)    |

### `DB_DRIVER`（database driver）

| Value             | Description                                                                   | Suitable platform         |
| :---------------- | :---------------------------------------------------------------------------- | :------------------------ |
| `auto`（default） | Auto-detect available driver（priority: blob → cfkv → kv → d1 → memory）      | Universal, works anywhere |
| `blob`            | Tencent EdgeOne Blob / Alibaba ESA Blob                                       | EdgeOne / ESA             |
| `cfkv`            | Cloudflare KV REST API（requires `CF_ACCOUNT` / `CF_KV_UUID` / `CF_API_KEY`） | External / cross-account  |
| `kv`              | Cloudflare KV binding                                                         | Cloudflare Workers        |
| `d1`              | Cloudflare D1 (SQLite)                                                        | Cloudflare Workers        |
| `do`              | Cloudflare Durable Objects (SQLite)                                           | Cloudflare Workers        |
| `mysql`           | External MySQL                                                                | Node.js container runtime |

### Recommended combinations

```bash
# Cloudflare Workers + D1 (recommended, SQL format compatible with Go backend)
DB_FORMAT=sql
DB_DRIVER=d1
```

> **Backward compatibility:** the legacy `DB_DRIVER=json` auto-converts to `DB_FORMAT=map` + auto-detected driver.

#### Table naming (SQL format)

The `sql` format uses columnar tables with the same naming strategy as the Go backend's GORM: snake*case + pluralized table names, plus the fixed `x*` prefix.

| Go struct     | Table name        |
| :------------ | :---------------- |
| `SettingItem` | `x_setting_items` |
| `SharingDB`   | `x_sharing_dbs`   |
| `Storage`     | `x_storages`      |
| `User`        | `x_users`         |
| `Meta`        | `x_metas`         |
| _(TS only)_   | `x_plugins`       |

The prefix is fixed to `x_` (matching the Go backend default), so no extra configuration is needed to share the same physical database with the Go backend.

:::

::: zh-CN
OpenList Worker 将持久化拆分为两个正交的层：

- **`DB_FORMAT`** — 决定数据的序列化与存储方式
- **`DB_DRIVER`** — 决定底层的存储系统

### `DB_FORMAT`（数据存储格式）

| 值            | 说明                                            |
| :------------ | :---------------------------------------------- |
| `map`（默认） | 整对象序列化为单个 JSON 值，适合 KV / Blob 存储 |
| `key`         | 分 key 存储，每实体一条记录（避免大 JSON）      |
| `sql`         | 关系表，与 Go 后端完全一致（用于 D1 / MySQL）   |

### `DB_DRIVER`（数据库驱动）

| 值             | 说明                                                                    | 适用平台           |
| :------------- | :---------------------------------------------------------------------- | :----------------- |
| `auto`（默认） | 自动检测可用驱动（优先级：blob → cfkv → kv → d1 → memory）              | 通用，任何平台可用 |
| `blob`         | 腾讯云 EdgeOne Blob / 阿里云 ESA Blob                                   | EdgeOne / ESA      |
| `cfkv`         | Cloudflare KV REST API（需 `CF_ACCOUNT` / `CF_KV_UUID` / `CF_API_KEY`） | 外部服务 / 跨账号  |
| `kv`           | Cloudflare KV binding                                                   | Cloudflare Workers |
| `d1`           | Cloudflare D1（SQLite）                                                 | Cloudflare Workers |
| `do`           | Cloudflare Durable Objects（SQLite）                                    | Cloudflare Workers |
| `mysql`        | 外部 MySQL                                                              | Node.js 容器运行时 |

### 推荐组合

```bash
# Cloudflare Workers + D1（推荐，SQL 格式与 Go 后端完全兼容）
DB_FORMAT=sql
DB_DRIVER=d1
```

> **向后兼容：** 旧的 `DB_DRIVER=json` 会自动转换为 `DB_FORMAT=map` + 自动检测驱动。

#### 表名对齐（SQL 格式）

`sql` 格式采用列式表，命名策略与 Go 后端的 GORM 一致：snake*case + 复数表名 + 固定前缀 `x*`。

| Go 结构体     | 表名              |
| :------------ | :---------------- |
| `SettingItem` | `x_setting_items` |
| `SharingDB`   | `x_sharing_dbs`   |
| `Storage`     | `x_storages`      |
| `User`        | `x_users`         |
| `Meta`        | `x_metas`         |
| _（仅 TS）_   | `x_plugins`       |

前缀固定为 `x_`（与 Go 后端默认值一致），无需额外配置即可与 Go 后端共享同一物理数据库。

:::

## Deploy to Cloudflare Workers { lang="en" }

## 部署到 Cloudflare Workers { lang="zh-CN" }

::: en

### Prerequisites

- A [Cloudflare](https://dash.cloudflare.com/) account
- Node.js 18+ and pnpm

### Option 1: One-click deploy

Click the **Deploy to Cloudflare Workers** button above. If it prompts that the repository cannot be fetched, fork the project first and deploy via the GitHub repository connection.

### Option 2: Manual deploy with Wrangler

```bash
# 1. Clone and install dependencies
git clone https://github.com/OpenListTeam/OpenList-Worker.git
cd OpenList-Worker
pnpm install

# 2. Configure wrangler.jsonc (JWT_SECRET, KV / D1 bindings)

# 3. Deploy to Cloudflare Workers
pnpm run deploy:worker
# or: pnpm run deploy (builds frontend + deploys backend)
```

### D1 database

Use Cloudflare's _Automatic resource provisioning_: omit `database_id` in `wrangler.d1.jsonc`, and wrangler (>= 4.45.0) auto-creates a D1 database with the same name and writes back the ID on deploy:

```jsonc
{
  "vars": { "DB_FORMAT": "sql", "DB_DRIVER": "d1" },
  "d1_databases": [{ "binding": "DB", "database_name": "openlist-data-base" }],
}
```

After deployment, configure the KV namespace binding and environment variables in the [Worker dashboard](https://dash.cloudflare.com/).
:::

::: zh-CN

### 前置要求

- 一个 [Cloudflare](https://dash.cloudflare.com/) 账号
- Node.js 18+ 与 pnpm

### 方式一：一键部署

点击上方的 **Deploy to Cloudflare Workers** 按钮。若提示无法获取存储库内容，请先 Fork 本项目，再通过连接到 GitHub 仓库功能部署。

### 方式二：使用 Wrangler 手动部署

```bash
# 1. 克隆并安装依赖
git clone https://github.com/OpenListTeam/OpenList-Worker.git
cd OpenList-Worker
pnpm install

# 2. 配置 wrangler.jsonc（填写 JWT_SECRET、KV / D1 绑定）

# 3. 部署到 Cloudflare Workers
pnpm run deploy:worker
# 或：pnpm run deploy（前端构建 + 后端部署）
```

### D1 数据库

使用 Cloudflare 的 _Automatic resource provisioning_：在 `wrangler.d1.jsonc` 中省略 `database_id`，部署时 wrangler（>= 4.45.0）会自动创建同名 D1 库并回写 ID：

```jsonc
{
  "vars": { "DB_FORMAT": "sql", "DB_DRIVER": "d1" },
  "d1_databases": [{ "binding": "DB", "database_name": "openlist-data-base" }],
}
```

部署完成后，请在 [Worker 后台](https://dash.cloudflare.com/) 配置 KV namespace 绑定与环境变量。
:::

## Deploy to EdgeOne { lang="en" }

## 部署到 EdgeOne { lang="zh-CN" }

::: en

### One-click deploy

Click the **EdgeOne** deploy button above, choose the international or China site:

- [International console](https://console.edgeone.ai/makers)
- [China console](https://console.cloud.tencent.com/edgeone/makers)

### Persistence

EdgeOne Makers uses `@edgeone/pages-blob` for persistence. The default `auto` driver auto-detects Blob. You can also explicitly set `DB_DRIVER = "blob"` (with `DB_FORMAT = "map"`) or `DB_DRIVER = "kv"` (with `DB_FORMAT = "key"`).

### Scheduled tasks

EdgeOne supports scheduled refresh via `edgeone.json`. Set `cron_secret` in the payload to your `JWT_SECRET` value, and configure the schedule:

```jsonc
{
  "schedules": [
    {
      "name": "token-refresh",
      "cron": "0 2 * * *",
      "path": "/api/task/refresh",
      "method": "POST",
      "payload": { "cron_secret": "" },
      "timezone": "Asia/Shanghai",
    },
  ],
}
```

:::

::: zh-CN

### 一键部署

点击上方的 **EdgeOne** 部署按钮，选择国际站或中国站：

- [国际站后台](https://console.edgeone.ai/makers)
- [中国站后台](https://console.cloud.tencent.com/edgeone/makers)

### 持久化

EdgeOne Makers 使用 `@edgeone/pages-blob` 进行持久化。默认的 `auto` 驱动会自动探测 Blob，您也可以显式设置 `DB_DRIVER = "blob"`（配合 `DB_FORMAT = "map"`）或 `DB_DRIVER = "kv"`（配合 `DB_FORMAT = "key"`）。

### 定时任务

EdgeOne 通过 `edgeone.json` 支持定时刷新。将 payload 中的 `cron_secret` 设为你的 `JWT_SECRET` 值，并配置定时规则：

```jsonc
{
  "schedules": [
    {
      "name": "token-refresh",
      "cron": "0 2 * * *",
      "path": "/api/task/refresh",
      "method": "POST",
      "payload": { "cron_secret": "" },
      "timezone": "Asia/Shanghai",
    },
  ],
}
```

:::

## Deploy to Alibaba Cloud ESA { lang="en" }

## 部署到阿里云 ESA { lang="zh-CN" }

::: en
OpenList Worker ships a dedicated ESA edge function entry (`esa-entry.ts`), which adapts Alibaba Cloud EdgeKV into the project's KV interface.

### Build & deploy

```bash
# 1. Install dependencies
pnpm install

# 2. Build (produces dist/esa-entry.js)
pnpm run build
```

`esa.jsonc` defines the edge function entry, install/build commands, and static assets directory:

```jsonc
{
  "name": "openlist",
  "entry": "./dist/esa-entry.js",
  "installCommand": "pnpm install --no-frozen-lockfile",
  "buildCommand": "pnpm run build",
  "assets": { "directory": "./dist" },
}
```

### KV namespace

Configure the EdgeKV namespace via the `KV_NAMESPACE` environment variable (default `openlist`).

::: tip
ESA EdgeKV is eventually consistent. The entry implements a module-level TTL cache (60s) to avoid "saved settings revert after refresh" caused by cross-node sync delay.
:::
:::

::: zh-CN
OpenList Worker 内置了专用的 ESA 边缘函数入口（`esa-entry.ts`），将阿里云 EdgeKV 适配为项目的 KV 接口。

### 构建与部署

```bash
# 1. 安装依赖
pnpm install

# 2. 构建（产出 dist/esa-entry.js）
pnpm run build
```

`esa.jsonc` 定义了边缘函数入口、安装/构建命令与静态资源目录：

```jsonc
{
  "name": "openlist",
  "entry": "./dist/esa-entry.js",
  "installCommand": "pnpm install --no-frozen-lockfile",
  "buildCommand": "pnpm run build",
  "assets": { "directory": "./dist" },
}
```

### KV 命名空间

通过 `KV_NAMESPACE` 环境变量配置 EdgeKV 命名空间（默认 `openlist`）。

::: tip
ESA EdgeKV 是最终一致性的。入口实现了带 TTL（60 秒）的模块级缓存，避免跨节点同步延迟导致的「保存设置后刷新复原」问题。
:::
:::

## Local Development { lang="en" }

## 本地开发 { lang="zh-CN" }

::: en

```bash
# 1. Install backend dependencies
pnpm install

# 2. Fetch frontend and run the backend (unified dev server)
pnpm run dev:unified

# Or run the worker directly (frontend built separately)
pnpm run dev:worker
```

:::

::: zh-CN

```bash
# 1. 安装后端依赖
pnpm install

# 2. 拉取前端并启动后端（统一开发服务器）
pnpm run dev:unified

# 或单独运行 worker（前端需另行构建）
pnpm run dev:worker
```

:::

## Production Deploy { lang="en" }

## 生产部署 { lang="zh-CN" }

::: en

```bash
# One-click deploy (frontend build + backend deploy to Cloudflare Workers)
pnpm run deploy
```

:::

::: zh-CN

```bash
# 一键部署（前端构建 + 后端部署到 Cloudflare Workers）
pnpm run deploy
```

:::

## Troubleshooting { lang="en" }

## 常见问题 { lang="zh-CN" }

::: en
::: details Cloudflare prompts "cannot fetch repository content"
Fork the project first, then deploy by connecting to the GitHub repository instead of the direct one-click URL.
:::

::: details Settings revert after save (ESA / EdgeOne)
This is usually a KV/CDN cache consistency issue. The entry already forces `no-cache` on `/api/*` GET responses and implements a module-level KV cache. If it persists, check that your KV namespace is correctly bound and not read-only.
:::

::: details How do I reset the admin password?
The admin password is set during the install wizard. To reset, you can set `ADMIN_PASS` temporarily and redeploy, or clear the persisted config and re-run the wizard.
:::
:::

::: zh-CN
::: details Cloudflare 提示「无法获取存储库内容」
请先 Fork 本项目，再通过连接到 GitHub 仓库功能部署，而不是直接使用一键部署 URL。
:::

::: details 保存设置后刷新又恢复原样（ESA / EdgeOne）
通常是 KV/CDN 缓存一致性问题。入口已对 `/api/*` 的 GET 响应强制 `no-cache`，并实现了模块级 KV 缓存。若仍存在，请检查 KV 命名空间是否正确绑定且非只读。
:::

::: details 如何重置管理员密码？
管理员密码在安装向导中设置。如需重置，可临时设置 `ADMIN_PASS` 并重新部署，或清空已持久化的配置后重新运行向导。
:::
:::

## Repository { lang="en" }

## 项目仓库 { lang="zh-CN" }

- [OpenListTeam/OpenList-Worker](https://github.com/OpenListTeam/OpenList-Worker)
