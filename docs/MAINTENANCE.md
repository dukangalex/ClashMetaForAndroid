# AngelaBox Clash 维护说明

AngelaBox Clash 是独立客户端，不是官方 Clash Meta / CMFA / mihomo 的产品名。
维护目标：内核长期跟随官方 MetaCubeX/mihomo；App 只维护组链体验、运行时覆盖与发布。

姐妹产品：[AngelaBox](https://github.com/dukangalex/AngelaBox)（sing-box 内核）。两套产品共用同一套产品边界，内核互不等价。

## 产品边界（必须遵守）

AngelaBox Clash = 官方 mihomo 内核 + **模块化链式出站覆盖层** + 面向普通用户的操作界面。

- 不重新设计 mihomo，不替换内核，不另做代理协议栈。
- 组链优先使用官方 `dialer-proxy` / 出站嵌套能力，在导入或启动时改运行时配置，不改订阅原文。
- 冲突即停：与官方配置模型无法兼容时停止发版，而不是在内核里开特例。
- 增加的功能只为降低日常操作成本，不为单一订阅商或个人配置定制。
- Fail Closed：链路失败不得静默落到 DIRECT。

## 仓库分工

| 仓库 | 分支 | 职责 |
|------|------|------|
| [dukangalex/mihomo-core](https://github.com/dukangalex/mihomo-core) | `chain-dev` | Clash 内核跟踪仓（从 MetaCubeX/mihomo 的 Alpha / android-real 同步） |
| [dukangalex/ClashMetaForAndroid](https://github.com/dukangalex/ClashMetaForAndroid) | `dev` | AngelaBox Clash Android 客户端 |

注意：`dukangalex/mihomo` 当前是崩坏：星穹铁道 Mihomo API 的 Python 仓库，**不是** Clash 内核，不要把它当作本项目子模块。

| 项目 | 值 |
|------|-----|
| 应用名 | AngelaBox Clash |
| 包名 | `io.chainbox.clash` |
| 更新源 | 仅本仓库 Releases |
| 内部代码包 | `com.github.kr328.clash`（上游遗留，不对外、不整包重命名） |
| 姐妹应用包名 | `io.chainbox.app`（AngelaBox / sing-box） |

## 对外身份

- 对外产品名、README、About、Release、APK 文件名都是 AngelaBox Clash。
- App 帮助与损坏页的 GitHub 链接指向 `dukangalex/ClashMetaForAndroid`。
- 不走 F-Droid / 官方 MetaCubeX 更新源。
- 不得用官方名称或标志上架应用商店。
- 不整包重命名 `com.github.kr328.clash`，以免失去与上游合并的能力。

## 内核同步

官方上游：`https://github.com/MetaCubeX/mihomo`

CMFA 上游文档指定内核来自 `Alpha`（主线）与 `android-open` 合并后的 `android-real`。本项目跟踪策略：

1. 把官方 `Alpha`（及 Android 所需的 `android-real`）merge 进 `dukangalex/mihomo-core` 的 `chain-dev`。
2. 只解决与链式出站覆盖层相关的冲突。
3. 子模块 `core/src/foss/golang/clash` 在内核仓就绪后改为指向 `dukangalex/mihomo-core` 的 `chain-dev`。在此之前可暂时继续指向官方 `MetaCubeX/mihomo` 的 `Alpha`，以免无法构建。
4. 发版记录内核 commit SHA。

```bash
git clone https://github.com/MetaCubeX/mihomo.git mihomo-core-src
cd mihomo-core-src
git checkout Alpha
git remote add angelabox https://github.com/dukangalex/mihomo-core.git
git checkout -B chain-dev
git push -u angelabox chain-dev
```

## App 同步

官方上游：`https://github.com/MetaCubeX/ClashMetaForAndroid`

```bash
git remote add upstream https://github.com/MetaCubeX/ClashMetaForAndroid.git
git fetch upstream
git checkout dev
git merge upstream/main
```

冲突时以 AngelaBox Clash 为准：包名、显示名、组链、更新链接、`version.properties`、本目录文档。

## 发版

1. 改 `version.properties`（`VERSION_NAME` 与 tag 一致，`VERSION_CODE` 必须递增）。
2. 使用现有 Actions 构建 meta/alpha Release，产物按 `AngelaBox-Clash-*` 命名。
3. 发版说明必须包含：内核 commit SHA、官方基线分支、是否启用链式覆盖层。

Secrets 与上游相同：签名仓库的 `signing.properties` / keystore。

## 能力边界

- 支持：官方 Mihomo 协议与规则；通过 `dialer-proxy` 做入口→落地。
- 规划中：跨配置选择落地、订阅更新后保持链路、仪表路径显示（对齐 AngelaBox）。
- 不支持冒充官方；不向官方仓库提交 AngelaBox 产品补丁。
