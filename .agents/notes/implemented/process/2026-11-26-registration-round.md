# Agent Note: 2026-11-26 登记轮

Status: implemented

## Problem

本轮起始 `main` 共 152 条插件索引（`8a7c37a6`），宿主基线为 `@deepseek-ai/dsh@0.2.3-alpha.2`。四个卫星仓开放 PR 概况如下：`dsh-pet` 与 `dsh-presets` 各 0 个；`dsh-skins` 仅 `#111`（`whale-desk` 1.1.3，11-24 已 `CHANGES_REQUESTED`，仅过 2 天且无新提交，保持等待）；`dsh-community-plugins` 共有 7 个开放 PR（均无协作者正式 review）：

1. `#263`（`weijiafu14`，`dsh-agy-native` `0.2.0`）：新投稿，基于本机官方 `agy acp --stdio` 与回环 MCP 桥接 Google AI Pro / Ultra 订阅，fork CI（run `45595755687`）停在 `action_required`。
2. `#260`（`PerryLink`，`register PerryLink plugins (128 new entries, 275 total)`）：作者按 11-25 五项修改意见重整后的汇总 PR，已合入最新 `main`，将 128 条新增并替换 5 组已迁出旧条目，fork CI（run `45594496625`）停在 `action_required`。
3. `#119`（`CoolrfulDARkness`，`dsh-handover-protocol`）：作者更新并推送了新提交 `44b8df7ee7`，上游 `12083c7` 已落实 `engines.dsh`、`$DSH_HOME` 状态迁移与 `test/smoke.mjs`，但 PR 分支把 `community.json` 覆盖回 144 条旧快照。
4. `#264`（`SPYfighting`，`dsh-science-review` `0.3.0`）与 `#265`（`SPYfighting`，`dsh-science-plugin` `0.14.1`）：新投稿，存在 `0.2.3-alpha.2` 兼容范围、`dependencies`/`cordis.patch.yml` 挂载方式与缺少 CI 实测问题。
5. `#266`（`forkgo430`，`dsh-gui-forkgo` `1.3.1`）：新投稿，存在覆盖宿主全局 `skill-filesystem` / `tool-skill` 行、npm 包缺少 `LICENSE` 并夹带 `.bak` 残留、上游 CI 失败以及缺少 `os` 护栏与高权限披露问题。
6. `#116`（`WangZW928`，`HPC Style for AI`）：自 11-22 留言后满 4 天（未满 7 天关闭线），上游仓库 `package.json`（`0.3.1`）仍钉死 `engines.dsh: "0.2.1-rc.5"`。

## Decision

**批准并合入 `#263` 与 `#260`（`main` 由 152 条增至 276 条），对 `#119`、`#264`、`#265`、`#266` 提交 `REQUEST_CHANGES` 退回修改，保持 `#116` 与 `dsh-skins#111` 开放等待。**

### 合入 `#263` `dsh-agy-native` `0.2.0`

- 确认 head `04e1b3f16a` 后批准运行 fork CI run `45595755687`，确认 `community index gate, typecheck, test and build` 通过后提交 `APPROVE` 并 squash 合入 `main`（提交 `b7cb4529`）。
- 实用性与稳定性核验：MIT 协议且 `LICENSE` 与 `SECURITY.md` 已随 `0.2.0` npm tarball 发布，零第三方运行时依赖；通过本机已登录的官方 `agy acp --stdio` 与仅监听 `127.0.0.1`、带每会话随机 `x-dsh-mcp-token` 的 Streamable-HTTP MCP 桥接入模型并路由 PTC 工具，不逆向私有接口也不篡改系统提示词，定位与现有条目不重叠；上游 `v0.2.0` CI（run `45580970042`）在 Node 22 / 24 通过（27/28 离线与真实 Cordis 挂载单测通过，1 项需真机登录的 live 测试跳过）。未验证边界：本轮未在真实登录账号下运行 `agy acp` 端到端流式调用。
- 兼容性核验：`cordis.patch.yml` 为纯 LF、行 ID `agy-acp` 与家族包及现有条目均无冲突；`peerDependencies` 声明 `@deepseek-ai/dsh-llm: ">=0.2.0-0"`，在 `0.2.2-rc.5` 与 `0.2.3-alpha.2` 下均通过 `evaluatePluginCompatibility`，`LlmAdapter`、`registerAdapter`、`registerConfigurableProviders` 与 `attachments.readImage` 签名与 `0.2.3-alpha.2` 一致；条目按 `id` 升序落位且 `version` 与 npm `latest`（`0.2.0`）一致。已在 review 附非阻塞建议：下个版本在 `package.json` 补齐 `engines.dsh`。

### 复评并合入 `#260` `PerryLink` 汇总 PR

- 确认 head `e34464a0a5` 已合并最新 `main`、6 个 `FROZEN` 条目已全部移出、5 组旧条目（`dsh-plugin-doctor`、`dsh-ticktick`、`dsh-github`、`dsh-plugin-upgrade`、`dsh-plugin-kit`）已在同一变更内删除替换且未新增空 patch 的 `dsh-scaffold`。
- 全量复核 128 个新增 npm 条目：128 个 GitHub 仓库均未归档、未停用且无冻结标记（`dsh-memento` 已标注 `Active (unfrozen 2026-10-12)`）；128 条全部带有精确 SemVer `version`（全文件 247 条 npm 条目全部具备精确版本，可进入官方 `Plugins -> Discover`），且未改动其余存量条目；128 个 tarball 均含 `LICENSE` 与纯 LF `cordis.patch.yml`，内部、与 `dsh-web` 家族及与存量 118 条带 npm 条目均无行 ID 冲突。
- 兼容性与许可落实：`dsh-permission-rules@0.8.8` 已移除 `@deepseek-ai/dsh-client-web` 引用，`dsh-doublecheck@0.9.37` 已清理 `@deepseek-ai/dsh-invariants` 非可选 peer，128 条在 `0.2.2-rc.5` 与 `0.2.3-alpha.2` 下均通过宿主 `evaluatePluginCompatibility`；`dsh-famling` 中英文描述已明确写入 `PolyForm-Noncommercial-1.0.0`、`CC BY-NC-SA 4.0` 与「非官方、不关联 DeepSeek」。
- 批准运行 fork CI run `45594496625` 并确认全部通过，待 `#263` 合入后确认 `#260` 仍为 `MERGEABLE` 且全部检查绿色，随后提交 `APPROVE` 并 squash 合入 `main`（提交 `b02bf5c2`）。在 review 中附非阻塞提示：`dsh-famling` 在本 PR 推送后约一小时在 npm 发布了 `0.14.3`（像素帧微调），`0.14.1` 仍可正常安装，留待后续单独更新。

### 退回修改 `#119`、`#264`、`#265`、`#266`（不提前放行 fork CI）

- `#119`（`dsh-handover-protocol`）：上游 `12083c7` 已满足 `engines.dsh`、`$DSH_HOME` 状态迁移与 `test/smoke.mjs`（本地 6/6 通过），本条格式也已拆分，但 `44b8df7ee7` 把 `community.json` 覆盖为 144 条旧快照，删除了 `deep-projects` 等 10 条既有插件、带回 `dsh-man-031` 并清掉 114 条 `version`，要求基于最新 `main` 重建分支、仅插入单条。
- `#264`（`dsh-science-review` `0.3.0`）：`engines.dsh: "0.2.2-rc.5"` 在 `0.2.3-alpha.2` 下被 `evaluatePluginCompatibility` 拒绝；`@deepseek-ai/dsh-llm`、`@deepseek-ai/dsh-session` 与 `@deepseek-ai/schemastery` 写在运行时 `dependencies` 而非 `peerDependencies`；`lib/index.js` 调用 `@deepseek-ai/dsh-session` 受限内部接口 `appendPluginRecord`；上游缺少 CI 与 `0.2.3-alpha.2` 实测记录。
- `#265`（`dsh-science-plugin` `0.14.1`）：`engines.dsh` 与 `peerDependencies` 上界 `<0.2.3-0` 拒绝 `0.2.3-alpha.2`；`cordis.patch.yml` 额外插入 `@deepseek-ai/dsh-action-cards` 与 `@deepseek-ai/dsh-client-ui-action-cards`，与 `0.2.3-alpha.2` 宿主 `@deepseek-ai/dsh-web-app/cordis.patch.yml` 默认挂载重复；上游缺少 CI 与 `0.2.3-alpha.2` 实测记录。
- `#266`（`dsh-gui-forkgo` `1.3.1`）：`cordis.patch.yml` 覆写宿主全局 `skill-filesystem`（设 `includeDefaultRoots: false` 冲掉其他技能目录）与 `tool-skill`，违背 `0.2.3-alpha.2` 宿主预设隔离设计且行 ID 与 `dsh-tui` 冲突；npm `1.3.1` tarball 缺少 `LICENSE` 并夹带 `.bak` 备份残留，且上游最近 5 次 CI（含 `45590216618`）在 `structure` 作业均失败；缺少 `"os": ["win32"]`、设 `failOnStartupError: true` 且强依赖 `uvx` / Python 3.14，并需在中英文商店描述补齐 Windows 限定、`uvx` 依赖及 `HKCU` `Run` 自启 / 注册表与系统命令高权限边界。

### 保持 `#116` 与 `dsh-skins#111` 开放等待

- `#116` 距 11-22 反馈满 4 天且上游 `package.json` 仍钉死 `0.2.1-rc.5`，`dsh-skins#111` 距 11-24 反馈满 2 天且无新提交，本轮均保持开放、不重复评论。

## Alternatives considered

- **阻塞 `#260` 要求作者把 `dsh-famling` 从 `0.14.1` 追升到 `0.14.3` 后再合。** 否决：`0.14.1` 在 PR 推送时为 npm `latest`，且至今仍在线可用并兼容 `0.2.3-alpha.2`，`0.14.2`/`0.14.3` 仅为像素帧微调，作非阻塞提示并在后续按需单独升版即可。
- **因 `#263` 未声明 `engines.dsh` 而退回修改。** 否决：按 `#123` 与 `#261` 先例，有合法 `peerDependencies`（`@deepseek-ai/dsh-llm: ">=0.2.0-0"`）且通过 `evaluatePluginCompatibility` 校验时允许合入，并建议作者下个版本补上 `engines.dsh`。
- **先行合入 `#264`、`#265` 或 `#266` 再在后续版本修复 `0.2.3-alpha.2` 兼容与行覆写问题。** 否决：`#264`/`#265` 在当前基线 `0.2.3-alpha.2` 下会被安装预检直接拒绝，`#266` 会覆写全局 `skill-filesystem` 破坏用户及其他插件的技能目录并缺少 `LICENSE`。
- **由维护者代 `#119` 作者重做分支提交。** 否决：PR 分支内容由贡献者自行维护，明确告知差异列表并请作者基于最新 `main` 重建即可。

## Consequences

- `dsh-community-plugins` `main` 由 152 条更新为 276 条（含 `#263` 提交 `b7cb4529` 与 `#260` 提交 `b02bf5c2`），同步在 `dsh-web` 移动 `satellites/dsh-community-plugins` 钉版并重建 `market/dist`，使 Workshop 与官方 `plugin-market-index.json` 上线本次变更。
- `#119`、`#264`、`#265`、`#266`、`#116` 与 `dsh-skins#111` 保持开放等待作者修订。
