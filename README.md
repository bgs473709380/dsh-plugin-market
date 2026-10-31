# DSH Plugin Market

DeepSeek Harness(DSH)的插件市场索引。本仓库托管一份 **Market Index Format 1** 的 JSON,
DSH 的 Web 端「设置 → 插件 → 发现」可以把它加为索引源,浏览并安装其中列出的插件组合包(bundle)。

## 索引地址

| 用途 | URL |
|---|---|
| 主(CDN,国内通常可直连) | `https://cdn.jsdelivr.net/gh/bgs473709380/dsh-plugin-market@main/index.json` |
| 备(GitHub raw) | `https://raw.githubusercontent.com/bgs473709380/dsh-plugin-market/main/index.json` |

> 索引必须能被 DSH 直接 `fetch` 到:支持 http(s)、不能带凭据/query/fragment,且**不跟随重定向**。
> 因此本仓库是公开的(私有仓库的 raw 地址需要鉴权,市场读取时会失败)。

## 在 DSH 里启用

1. 先确保 profile 启用了插件市场组合包 `@deepseek-ai/dsh-experimental-plugin-market-bundle`
   (它负责挂载 `plugin-market` 服务和「发现」页面;默认不启用);
2. 打开 Web UI → **设置 → 插件 → 发现**;
3. 添加上面的主索引 URL;
4. 之后即可搜索、查看兼容性并安装条目。

也可以写进 profile 里 `plugin-market` 行的配置(`indexes` 是可热更新字段):

```yaml
- id: plugin-market
  name: '@deepseek-ai/dsh-plugin-market'
  config:
    indexes:
      - https://cdn.jsdelivr.net/gh/bgs473709380/dsh-plugin-market@main/index.json
```

安装由 DSH 的插件管理器执行,走它自己的审批与兼容性检查;本索引只是「声明」,
真正装什么以插件包自身的 manifest 为准。

## 当前收录

| 插件 | 版本 | 来源 | 说明 |
|---|---|---|---|
| `dsh-private-plugins` | 1.0.0 | npm | 私有插件导入/管理:私有 git 仓库 + 本地 tarball/文件夹导入,设置内独立入口 |

> 兼容性提示:`dsh-private-plugins@1.0.0` 的 peer 要求是 `@deepseek-ai/dsh-host-webserver ^0.1.1-rc.1`,
> 在 `dsh 0.2.x` 上安装会被兼容性门禁拦下,需要授予 exact-version 豁免
> (`dsh plugin allow-version ... --accept-risk`)后再装。

## 仓库结构

```
dsh-plugin-market/
├── index.json            # 市场索引(format 1)
├── docs/
│   └── index-format.md   # 格式参考:字段、规则、校验要点
├── plugins/              # 从市场安装的插件归档(见 plugins/README.md)
├── README.md
├── LICENSE               # MIT
└── .gitignore
```

## 插件归档

`plugins/` 存放从插件市场安装的插件副本(不含 `node_modules`),用于备份、版本对照和离线安装。
归档约定、按 profile 复制的命令,以及「索引用 git 源直接引用归档目录」的写法,
见 [plugins/README.md](plugins/README.md)。

## 新增一个插件条目

1. 在 `index.json` 的 `plugins` 数组里加一项:
   - `name`:npm 包名(必填,索引内唯一);
   - `title` / `description`:本地化文本,对象形式必须含 `en`;
   - `author`、`homepage`、`repository`;
   - `metadata.category`(需在顶层 `metadata.categories` 声明)、`keywords`、`labels`、`sortKeys`;
   - `versions`:非空数组,每项含精确 SemVer、Unix 秒的 `publishTimestamp`、`source`;
2. 对照 [docs/index-format.md](docs/index-format.md) 自检;
3. `git commit` 并 push。

`source` 两种形式:

```json
{ "type": "npm" }
{ "type": "npm", "registry": "https://registry.npmjs.org" }
{ "type": "git", "url": "https://github.com/owner/repo", "commit": "<40 位 commit SHA>", "path": "packages/plugin" }
```

## 生效时间

- `jsDelivr` 的 `@main` 有缓存:想立刻生效把 URL 里的 `@main` 换成 `@<commit sha>`,或等 CDN 刷新;
- DSH 侧本身还有 `cacheTtlMs`(默认 10 分钟)缓存,发现页可强制刷新。

## License

MIT
