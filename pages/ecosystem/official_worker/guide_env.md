---
title:
  en: Environment Variables
  zh-CN: 环境变量
categories:
  - ecosystem
  - eco_worker
top: 972
---

## Environment Variables { lang="en" }

## 配置变量 { lang="zh-CN" }

:::en

### Required variables

The following parameters are required:

| Parameter   | Optional values                                                   | Description             |
| ----------- | ----------------------------------------------------------------- | ----------------------- |
| `DB_FORMAT` | `map` (default) / `key` / `sql`                                   | Data persistence format |
| `DB_DRIVER` | `auto` (default) / `blob` / `cfkv` / `kv` / `d1` / `do` / `mysql` | Storage location        |

#### Security variables

In addition to `DB_FORMAT` and `DB_DRIVER`, it is strongly recommended to configure the following security-related variables:

| Variable     | Required    | Description                                                                                                                  |
| ------------ | ----------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `JWT_SECRET` | Recommended | JWT signing key (≥16 chars), also used for data encryption and cron task auth. Auto-generated and persisted to KV when unset |
| `ADMIN_PASS` | Optional    | Skip the install wizard and auto-initialize the admin with this password                                                     |
| `ALLOW_URLS` | Optional    | Comma-separated CORS allowlist                                                                                               |

#### Other optional variables

| Variable     | Default    | Description                                                      |
| ------------ | ---------- | ---------------------------------------------------------------- |
| `MAX_UPLOAD` | `26214400` | Max size (bytes) for a whole upload (`/put`, `/form`)            |
| `MAX_UPPART` | `16777216` | Max size (bytes) per chunk in multipart upload                   |
| `ASSET_URLS` | —          | Frontend asset CDN base URL, supports the `$version` placeholder |
| `ALLOW_SEED` | —          | Allowlist of hosts permitted as seed-data sources                |

#### Concurrent deduplication (singleflight)

When several isolates of the same deployment receive the same read request (directory listing / metadata) at the same time, singleflight issues only one upstream request and lets the others wait and reuse the same result:

| Variable                   | Default | Description                                                                                                                                                      |
| -------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SINGLEFLIGHT`             | `auto`  | `auto`: table coordination when a SQL backend (d1 / mysql / do) is available, otherwise in-process; `db`: force table; `memory`: in-process only; `off`: disable |
| `SINGLEFLIGHT_HANDOFF_MS`  | `1000`  | How long (ms) a finished result stays in the table for **already waiting** concurrent instances to read                                                          |
| `SINGLEFLIGHT_LOCK_TTL_MS` | `30000` | Max lock hold time (ms) for the executor, renewed every 1/3 of the period; an expired lock can be taken over after the executor crashes                          |
| `SINGLEFLIGHT_POLL_MS`     | `200`   | Poll interval (ms) for waiting instances. Values below 100 are not recommended: every poll is a SELECT on the coordination table                                 |
| `SINGLEFLIGHT_WAIT_MS`     | `15000` | Max wait (ms) for waiting instances; on timeout they execute the call themselves instead of hanging                                                              |

Notes:

- **Two levels of deduplication**: in-process merge (effective in all modes) + table coordination (`db` mode, effective across isolates).
- **No result caching**: only requests that observe a running executor on arrival reuse the result; a newly arrived request reclaims the old record and re-executes. Stale reads such as "a deleted file still appears after refresh" therefore cannot happen.
- **Availability first**: coordination-layer failures (missing table, DB timeout, non-serializable result, executor crash) degrade to "each caller executes once", so the business call never fails; exceptions thrown by the business function itself are rethrown without retry.
- The `x_singleflight` table is created automatically by the SQL driver on initialization. It holds runtime data only and is not touched when configuration is saved.
- Runtime statistics (`executed` / `coalesced` / `shared` / `fallback`) are exposed in the `singleflight` field of `/api/debug/info`.

### Storage format differences

| `DB_FORMAT` | Description                                                                                                                        |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `map`       | All data stored as a single JSON. Reads the whole object on every access — lower performance but best compatibility.               |
| `key`       | Each record stored as a Key-Value pair, read on demand — decent compatibility and performance, but no indexing.                    |
| `sql`       | Data stored as SQL tables, fully identical to the Go backend. Supports indexing and full features, but multiple reads take longer. |

### Storage driver differences

| `DB_DRIVER` | Supported platform | Description                                                                |
| ----------- | ------------------ | -------------------------------------------------------------------------- |
| `auto`      | Universal          | Auto-detect, priority: blob → cfkv → kv → d1 → memory                      |
| `blob`      | EdgeOne / ESA      | Persistence provided by EdgeOne or Alibaba ESA Blob, free                  |
| `cfkv`      | Universal          | Cloudflare KV REST API, for remote access on platforms without persistence |
| `kv`        | CF / EO / ESA      | KV database, fast and free                                                 |
| `d1`        | CF                 | D1 database, Cloudflare only                                               |
| `do`        | CF                 | Durable Objects, Cloudflare only                                           |
| `mysql`     | Universal          | Connect to your own MySQL database                                         |

### Valid combinations

| `DB_FORMAT` | `DB_DRIVER` | Description                                                           |
| ----------- | ----------- | --------------------------------------------------------------------- |
| `map`       | `auto`      | JSON storage, auto-selects blob / kv / d1 based on the platform       |
| `map`       | `blob`      | JSON storage via EdgeOne / ESA Blob (recommended for those platforms) |
| `map`       | `cfkv`      | JSON storage via remote CF KV outside CF Workers                      |
| `map`       | `kv`        | JSON storage via KV on CF Workers (recommended)                       |
| `map`       | `d1`        | JSON storage via D1 on CF Workers (not recommended)                   |
| `map`       | `do`        | JSON storage via DO on CF Workers (not recommended, may incur cost)   |
| `key`       | `kv`        | Key-Value storage via KV on CF Workers (recommended)                  |
| `key`       | `d1`        | Key-Value storage via D1 on CF Workers (recommended)                  |
| `key`       | `mysql`     | Key-Value storage via MySQL on CF Workers (not recommended)           |
| `sql`       | `d1`        | SQL-Table storage via D1 on CF Workers (recommended)                  |
| `sql`       | `mysql`     | SQL-Table storage via MySQL                                           |

### Remote database configuration

If you bound `cfkv`, set the following variables:

| Variable     | Description                         |
| ------------ | ----------------------------------- |
| `CF_ACCOUNT` | Cloudflare account ID               |
| `CF_KV_UUID` | Cloudflare KV namespace ID          |
| `CF_API_KEY` | Cloudflare API token with KV access |

If you bound MySQL, set the following variables:

| Variable     | Description                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------ |
| `MYSQL_URL`  | Connection string, e.g. `mysql://user:pass@host:3306/db`. When set, the fields below are ignored |
| `MYSQL_HOST` | Database host                                                                                    |
| `MYSQL_PORT` | Database port (`3306`)                                                                           |
| `MYSQL_USER` | Database user                                                                                    |
| `MYSQL_PASS` | Database password                                                                                |
| `MYSQL_NAME` | Database name                                                                                    |

:::

:::zh-CN

### 必选变量

必须配置这些参数：

| 参数名      | 可选值                                                    | 说明           |
| ----------- | --------------------------------------------------------- | -------------- |
| `DB_FORMAT` | `map`（默认）、`key`、`sql`                               | 数据持久化格式 |
| `DB_DRIVER` | `auto`（默认）、`blob`、`cfkv`、`kv`、`d1`、`do`、`mysql` | 数据库存储位置 |

### 安全变量

除 `DB_FORMAT` 和 `DB_DRIVER` 外，强烈建议配置以下安全相关变量：

| 变量         | 必要 | 说明                                                                                    |
| ------------ | ---- | --------------------------------------------------------------------------------------- |
| `JWT_SECRET` | 推荐 | JWT 签名密钥（≥16 字符），同时用于数据加密与定时任务鉴权；未配置时自动生成并持久化到 KV |
| `ADMIN_PASS` | 可选 | 跳过安装向导，以该密码自动初始化 admin                                                  |
| `ALLOW_URLS` | 可选 | CORS 允许白名单（逗号分隔）                                                             |

#### 其他可选变量

| 变量         | 默认值     | 说明                                            |
| ------------ | ---------- | ----------------------------------------------- |
| `MAX_UPLOAD` | `26214400` | 单次整体上传（`/put`、`/form`）大小上限（字节） |
| `MAX_UPPART` | `16777216` | 分片上传单片大小上限（字节）                    |
| `ASSET_URLS` | —          | 前端静态资源 CDN 地址，支持 `$version` 占位符   |
| `ALLOW_SEED` | —          | 允许作为种子数据来源的主机白名单                |

#### 并发去重（singleflight）

同一时刻对同一存储、同一路径的重复读请求（目录列表 / 元信息）只向上游发起一次调用，其余并发请求等待并复用同一结果：

| 变量                       | 默认值  | 说明                                                                                                            |
| -------------------------- | ------- | --------------------------------------------------------------------------------------------------------------- |
| `SINGLEFLIGHT`             | `auto`  | `auto`：有 SQL 库（d1 / mysql / do）时用表协调，否则退回进程内；`db`：强制用表；`memory`：仅进程内；`off`：关闭 |
| `SINGLEFLIGHT_HANDOFF_MS`  | `1000`  | 执行完成后结果在表中保留多久（ms），供**正在等待的**并发实例读取                                                |
| `SINGLEFLIGHT_LOCK_TTL_MS` | `30000` | 执行者持锁上限（ms），期间按 1/3 周期续租；执行者崩溃后到期可被接管                                             |
| `SINGLEFLIGHT_POLL_MS`     | `200`   | 等待方轮询间隔（ms）。不建议低于 100：每次轮询都是一次对协调表的 SELECT                                         |
| `SINGLEFLIGHT_WAIT_MS`     | `15000` | 等待方最长等待（ms），超时后自行执行，避免被慢请求拖死                                                          |

行为要点：

- **两级去重**：进程内合并（所有模式都生效）+ 数据库表协调（`db` 模式，跨 isolate 生效）。
- **不做结果缓存**：只有「到达时观察到执行者正在运行」的请求才会复用结果；新到达的请求会回收旧记录并重新执行。因此不存在「删除文件后刷新仍看到旧文件」这类脏读。
- **可用性优先**：表不存在、数据库超时、结果无法序列化、执行者崩溃等**协调层**异常一律降级为「各自执行一次」，业务不会失败；而**业务函数自身的异常会原样抛出且不重试**。
- `x_singleflight` 表由 SQL 驱动在初始化时自动创建，属运行时数据，不参与配置读写。
- 运行统计（`executed` / `coalesced` / `shared` / `fallback`）可在 `/api/debug/info` 的 `singleflight` 字段查看。

### 不同存储格式差异

| `DB_FORMAT` 类型 | 描述                                                                           |
| ---------------- | ------------------------------------------------------------------------------ |
| `map`            | 所有数据存储为一个 JSON，每次访问都需要全部读取，性能较差，但兼容性好          |
| `key`            | 单条数据存储为 Key-Value，按需读取，兼容性和性能还行，但不支持索引             |
| `sql`            | 数据存储为 SQL-Table，与 Go 后端完全一致，支持索引，功能齐全，但多次读取耗时长 |

### 不同存储驱动差异

| `DB_DRIVER` 类型 | 支持平台      | 说明                                                   |
| ---------------- | ------------- | ------------------------------------------------------ |
| `auto`           | 通用          | 自动检测，优先级 blob → cfkv → kv → d1 → memory        |
| `blob`           | EdgeOne / ESA | EdgeOne 或阿里云 ESA Blob 提供的持久化工具，免费       |
| `cfkv`           | 通用          | Cloudflare KV REST API，适用于没有持久化的平台远程调用 |
| `kv`             | CF、EO、ESA   | KV 数据库，速度快，免费                                |
| `d1`             | CF            | D1 数据库，仅支持 Cloudflare                           |
| `do`             | CF            | DO 持久化对象，仅支持 Cloudflare                       |
| `mysql`          | 通用          | 连接到自己的 MySQL 数据库                              |

### 可选有效组合变量

| `DB_FORMAT` | `DB_DRIVER` | 说明                                                               |
| ----------- | ----------- | ------------------------------------------------------------------ |
| `map`       | `auto`      | 数据存储为 JSON，自动根据部署环境选择 blob、kv、d1                 |
| `map`       | `blob`      | 数据存储为 JSON，使用 EdgeOne / ESA Blob 持久化存储（推荐）        |
| `map`       | `cfkv`      | 数据存储为 JSON，在非 CF Worker 环境使用远程 CF KV 存储            |
| `map`       | `kv`        | 数据存储为 JSON，在 CF Worker 环境使用 KV 存储（推荐）             |
| `map`       | `d1`        | 数据存储为 JSON，在 CF Worker 环境使用 D1 存储（不推荐）           |
| `map`       | `do`        | 数据存储为 JSON，在 CF Worker 环境使用 DO 存储（不推荐，可能付费） |
| `key`       | `kv`        | 数据存储为 Key-Value，在 CF Worker 环境使用 KV 存储（推荐）        |
| `key`       | `d1`        | 数据存储为 Key-Value，在 CF Worker 环境使用 D1 存储（推荐）        |
| `key`       | `mysql`     | 数据存储为 Key-Value，在 CF Worker 环境使用 MySQL 存储（不推荐）   |
| `sql`       | `d1`        | 数据存储为 SQL-Table，在 CF Worker 环境使用 D1 存储（推荐）        |
| `sql`       | `mysql`     | 数据存储为 SQL-Table，在 CF Worker 环境使用 MySQL 存储             |

### 远程数据库配置项

如果您绑定了 `cfkv`，则需要设置相关变量：

| 变量         | 说明                            |
| ------------ | ------------------------------- |
| `CF_ACCOUNT` | Cloudflare 账号 ID              |
| `CF_KV_UUID` | Cloudflare 绑定的 KV ID         |
| `CF_API_KEY` | Cloudflare 具有 KV 权限的 Token |

如果您绑定了 MySQL，则需要设置相关变量：

| 变量         | 说明                                                                       |
| ------------ | -------------------------------------------------------------------------- |
| `MYSQL_URL`  | 地址串，例如：`mysql://user:pass@host:3306/db`，配置后则不需要配置下方变量 |
| `MYSQL_HOST` | 数据库地址                                                                 |
| `MYSQL_PORT` | 数据库端口（3306）                                                         |
| `MYSQL_USER` | 数据库用户                                                                 |
| `MYSQL_PASS` | 数据库密码                                                                 |
| `MYSQL_NAME` | 数据库名称                                                                 |

:::
