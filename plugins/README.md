# plugins/ — 插件归档目录

存放从 DSH 插件市场安装的插件副本(不含 `node_modules`),用于备份、版本对照和离线安装。

## 约定

- 每个插件一个子目录,目录名 = npm 包名;带 scope 的包保持原样(如 `@scope/name` 形成嵌套目录);
- 只复制安装后的包内容,排除 `node_modules`;保留 `package.json` 与 `LICENSE`;
- 版本以子目录里 `package.json` 的 `version` 为准;升级时覆盖并提交,commit message 写成
  `plugin: <name>@<old> -> <new>`;
- 提交前确认体积和许可证允许再分发(第三方插件请保留其 LICENSE)。

## 当前已归档

| 插件 | 版本 | 来源 |
|---|---|---|
| `dsh-private-plugins` | 1.0.0 | npm(`https://www.npmjs.com/package/dsh-private-plugins`) |

## 从本机归档一个已安装插件

在仓库根目录执行(profile 名按实际填,如 `desktop` / `web`):

```powershell
$plugin  = 'dsh-private-plugins'
$profile = 'web'
$src = "$env:USERPROFILE\.dsh\profiles\$profile\node_modules\$plugin"
$dst = "plugins\$plugin"
New-Item -ItemType Directory -Path $dst -Force | Out-Null
Copy-Item "$src\*" $dst -Recurse -Force
git add "plugins/$plugin"
git commit -m "plugin: archive $plugin@<version>"
```

## 让索引引用这里的副本

市场索引的 git 源支持 `path`,可以把归档目录作为安装目标:

```json
{
  "version": "1.0.0",
  "publishTimestamp": 1787842010,
  "source": {
    "type": "git",
    "url": "https://github.com/bgs473709380/dsh-plugin-market",
    "commit": "<40 位 commit SHA>",
    "path": "plugins/dsh-private-plugins"
  }
}
```

> git 源固定到 commit:归档更新后要同步更新索引里的 `commit`,否则市场仍按旧 commit 安装。
