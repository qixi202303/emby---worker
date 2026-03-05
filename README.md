# Emby Worker Proxy（Cloudflare Workers + D1）

基于 Cloudflare Workers 的 Emby 前后端分离反代。  
支持后台管理（`/admin`）、节点管理、统一/分离兼容与 D1 持久化存储。

---

## 功能特性

- 管理后台：`/admin`
- 节点增删改查（D1 存储）
- 普通 / 分离模式
- 第三方播放器链路兼容
- 第三方播放器一键导入
- 可选直连模式（按需开启）
- 节点缓存与列表缓存优化

---

## 快速部署

### 1）创建 D1 数据库

在 Cloudflare 控制台创建 D1 数据库（例如：`emby-proxy`）。

### 2）初始化数据表

在 D1 控制台依次执行以下 SQL：

`CREATE TABLE IF NOT EXISTS proxy_kv (k TEXT PRIMARY KEY, v TEXT NOT NULL, updated_at INTEGER NOT NULL);`

`CREATE INDEX IF NOT EXISTS idx_proxy_kv_k ON proxy_kv(k);`

### 3）上传 Worker 代码

将仓库内 `worker.js` 部署到 Cloudflare Workers。

### 4）绑定 D1

在 Worker → 设置 → 绑定 中添加 D1：

- 变量名：`EMBY_D1`
- 数据库：你创建的 D1

### 5）配置环境变量

在 Worker → 设置 → 变量 中添加：

- `ADMIN_TOKEN`（必填）

可选变量：

- `ENABLE_DIRECT_PROXY`：`0` 或 `1`（默认 `0`）
- `RAW_ALLOW_HOSTS`：直连白名单，逗号分隔
- `CAPY_STRIP_EMBY`：兼容性开关（默认 `0`）
- `CORS_ALLOW_ORIGIN`：自定义 CORS 来源（可留空）

### 6）配置 DNS（CNAME 到优选域名）

在 Cloudflare DNS 记录中新增 CNAME，把你的入口子域名指向优选域名：

- 类型：`CNAME`
- 名称：例如 `emby`（即 `emby.你的域名`）
- 内容：第三方优选域名
- TTL：自动
- 代理状态：按你的方案选择（常用 `仅 DNS`）


### 7）可选：添加泛解析 `*`

如果你希望未单独配置的子域也指向同一优选域名，可再添加：

- 类型：`CNAME`
- 名称：`*`
- 内容：第三方优选域名
- TTL：自动
- 代理状态：按你的方案选择（常用 `仅 DNS`）

注意：`*` 会影响未单独配置的所有子域，建议确认后再启用。

### 8）访问后台

打开：`https://你的域名/admin`  
使用 `ADMIN_TOKEN` 登录后即可添加节点。

### 9）在后台添加节点时的填写建议

- 名称：自定义（如 `emby`）
- 目标地址：源emby服务器地址
- 代理地址：你的域名+节点自定义（如  https://emby.你的域名/emby）
- 模式：按你的使用场景选择统一 / 分离模式
- 密钥路径：可选
---

## 免责声明

本项目仅供学习与技术测试使用。请遵守当地法律法规及服务条款，使用风险由使用者自行承担。

---

## License

MIT
