# 本仓库的来源与本地改动

## 来源

上游仓库 `YuJunZhiXue/dsh-purge` 已被作者删除，无法再从 GitHub 获取。本仓库是从**本机已安装的副本**完整复原出来的快照：

| 项 | 值 |
|---|---|
| 复原来源 | `C:\Users\Qoc\.dsh\profiles\web\node_modules\dsh-purge` |
| 上游版本 | `dsh-purge@1.1.90` |
| 上游安装方式 | `https://codeload.github.com/YuJunZhiXue/dsh-purge/tar.gz/refs/heads/master`（GitHub tarball，非 npm 包） |
| 复原时间 | 2026-11-16 |
| 复原方式 | 整目录复制 104 个文件，剔除包内 `node_modules/`（仅 3 个文件，均为 pnpm 生成的 `.bin` 占位） |

因为上游发布的是 codeload tarball（仓库整体打包，而非 npm 构建产物），安装目录本身就等于仓库快照，所以 `lib/`、`scripts/`、`docs/`、`presets/`、`.github/workflows/`、`README.*`、`release-notes.md` 全部是原始内容，不是二次构建的产物。

## 复原后缺少的东西

| 缺失项 | 原因 | 影响 |
|---|---|---|
| `.git/` 历史 | tarball 不含版本历史 | 无历史可追溯；本仓库以一次初始提交作为起点 |
| `lib/default-prompt-inject.md` | 上游 `.gitignore` 与 `.npmignore` 双双排除（它是 `embed-prompt` 脚本的输入原稿，不随发布分发） | 依赖 `scripts/embed-default-prompt.mjs` 从别处重建该文件；日常运行不读它（运行期读的是 `lib/default-prompt-inject.zh.md` / `.en.md`） |
| `client.js.bak` | 上游 `.gitignore` 有 `*.bak` | 那是本地改动前的备份，不入库是正确的 |

## 本地改动（相对上游 1.1.90）

### 1. 停靠区标签中文化：`dsh-purge` → `破甲`

原因：topbar 与右侧停靠区里 `dsh-purge` 这个名字和左侧「规则设定」导航重复，改成中文二字更清楚。

改动位置（两处一致，避免重建时被覆盖）：

- `client.js` 第 132–134 行（中文 locale）与第 493–495 行（英文 locale）：
  ```js
  "dock.toggle": "破甲",
  "dock.newSession": "破甲",
  "dock.inSession": "破甲",
  ```
- `scripts/patch-client-dock.mjs` 第 28–29 行与第 52–53 行：同样把 `"dsh-purge"` 改成 `"破甲"`，保证将来重跑补丁脚本时生成结果一致。

**未改**：`nav: "dsh-purge"`（左侧导航「规则设定」的 value）、包名 `dsh-purge`、`package.json` 的 `name`。只改显示文案，不动任何标识符，因此 profile 的依赖解析、`dsh.bundle.patch` 注入、`exports` 映射全部不受影响。

### 2. 无其他改动

`lib/**`、`bin/**`、`presets/**`、`docs/**`、`cordis.patch.yml` 与上游一致，逐字节未改。

## 如何把这份复原仓库装回 dsh

两种方式，二选一。

### A. 本地目录直接装（立即生效，不依赖 GitHub）

编辑 `C:\Users\Qoc\.dsh\profiles\web\package.json`，把 dsh-purge 那行改为本地目录：

```json
"dsh-purge": "file:D:/Applications/dsh/workspace/dsh-purge-fork"
```

然后：

```powershell
cd C:\Users\Qoc\.dsh\profiles\web
pnpm install
```

### B. 推到自己的 GitHub 后按 tarball 装（推荐，可跨机器）

```powershell
cd D:\Applications\dsh\workspace\dsh-purge-fork
gh repo create dsh-purge --private --source . --push
```

再把 profile 的依赖改成自己的仓库：

```json
"dsh-purge": "https://codeload.github.com/<你的用户名>/dsh-purge/tar.gz/refs/heads/master"
```

## 许可

上游为 Apache-2.0（见 `LICENSE`），另有 `LICENSE-COMMERCIAL.md` 与 `NOTICE`。复原与二次分发请遵守这些条款，尤其是 `NOTICE` 里的署名要求。
