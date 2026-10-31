# 插件市场索引格式 1（DSH Market Index）

本文是 [`index.json`](../index.json) 的格式参考,来自 DSH 运行时 `@deepseek-ai/dsh-plugin-market-index`
的校验器(`parseMarketIndex`)。校验不通过时 `MarketIndexError` 会给出出错值的 JSON 路径。

## 顶层

| 字段 | 必填 | 规则 |
|---|---|---|
| `format` | ✅ | 必须是数字 `1` |
| `title` | ✅ | 本地化文本,必须含 `en` |
| `description` | ❌ | 本地化文本 |
| `metadata` | ❌ | `categories` / `labels` / `sortKeys` 的声明表 |
| `plugins` | ✅ | 数组,可为空;`name` 不得重复 |

## 本地化文本

- 字符串:直接使用;
- 对象:键是小写语言 id(`/^[a-z]{2,8}(-[a-z0-9]{1,8})*$/`),**必须包含 `en`**,值均为非空白字符串。

## metadata 声明

```json
"metadata": {
  "categories": { "plugins": { "title": { "en": "Plugins", "zh": "插件" } } },
  "labels":     { "private": { "title": { "en": "Private", "zh": "私有" } } },
  "sortKeys":   { "featured": { "title": { "en": "Featured", "zh": "推荐" }, "order": "desc" } }
}
```

- `sortKeys[].order` 只能是 `asc` 或 `desc`;
- 插件里引用的 category / label / sortKey id 必须先在 metadata 中声明,否则校验失败。

## 插件条目

| 字段 | 必填 | 规则 |
|---|---|---|
| `name` | ✅ | 合法 npm 包名,≤214 字符,不得在索引内重复 |
| `title` | ❌ | 本地化文本 |
| `description` | ❌ | 本地化文本 |
| `icon` | ❌ | 相对路径(解析为索引 URL 同源或 https)、绝对 https URL,或 ≤32 KiB 的 base64 `data:`(svg/png/jpeg/webp) |
| `author` | ❌ | 非空字符串 |
| `homepage` | ❌ | 绝对 http(s) URL,允许 fragment |
| `repository` | ❌ | `{ "url": "https://...", "directory"?: "相对目录" }`,url 不得带 query/fragment |
| `metadata` | ❌ | `category`、`keywords`(字符串数组)、`labels`(声明过的 id 数组,不可重复)、`sortKeys`(声明过的 id → 有限数字) |
| `versions` | ✅ | 非空数组,`version` 不得重复;按 SemVer 降序排列 |

## 版本条目

| 字段 | 必填 | 规则 |
|---|---|---|
| `version` | ✅ | 精确 SemVer(允许 prerelease) |
| `publishTimestamp` | ✅ | Unix 秒,整数,`[0, 10^11)` |
| `source` | ✅ | `{ "type": "npm", "registry"?: "https://..." }` 或 `{ "type": "git", "url": "https://...repo", "commit": "40位小写hex", "path"?: "子目录" }` |
| `engines` | ❌ | `{ "dsh": "<SemVer range>" }`;决定 DSH 版本兼容性 |

### 安装目标如何生成

- npm:`name@version`(若给了 `registry`,优先问该 registry);
- git:`git+https://...repo.git#<commit>`(有 `path` 时追加 `&path:/<path>`)。

## 索引 URL 规则

DSH 只接受可作为索引源的 URL:`http:` 或 `https:`、**不含凭据、不含 query/fragment**;
Host 的 fetch **拒绝重定向**,所以要用最终 URL;每次读取整份文档,大小与缓存由
`maxBytes` / `cacheTtlMs` 控制。
