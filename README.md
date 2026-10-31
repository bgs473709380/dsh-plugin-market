# DSH Plugin Market

DeepSeek Harness 插件市场配置仓库 / DeepSeek Harness plugin market configuration.

## 用途

本仓库用于维护 DeepSeek Harness(DSH)插件市场的配置:

- 插件来源清单(公开 npm 包 / 私有 git 仓库 / 本地 tarball)
- 插件版本与更新策略
- 启用状态与分环境配置

## 目录结构

```
.
├── README.md
├── .gitignore
├── LICENSE
└── market/            # 市场配置(索引、来源、开关)
```

## 使用方式

1. 在 `market/` 下按约定维护市场配置;
2. 通过 DSH 的插件管理入口(设置 → 插件)导入或指向本仓库;
3. 变更走 commit / PR,保留可追溯的配置历史。

## 约定

- 不提交任何 token、私钥、账号口令;敏感项用环境变量或 CI secret;
- 每个插件条目至少包含:名称、来源、版本范围、用途说明;
- 破坏性变更在 commit message 中标注 `BREAKING`。

## License

MIT
