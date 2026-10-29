# COMPAT — 1.1.71-compat.1（破甲冻结 + DSH 0.2.1-rc.3）

相对上游 1.1.81：**能力面 = 1.1.71**。只移植崩溃/安装/宿主兼容修复，**不吸收** lab-only、外挂硬拦、删技能库等收紧。

版本：`package.json` → `1.1.71-compat.1`，`dshTarget` → `0.2.1-rc.3`。

## 冻结表（相对 master / 1.1.71）

| 冻结层 | 路径 | SHA256 前 16 | 规则 |
|---|---|---|---|
| C 门禁 | `lib/net-scope.js` | `B6D7DDC34B9C0092` | **与基线相同，零改动** |
| B 旁路 | `lib/hooks-deny.js` | `03C0C57F9209CF42` | **与基线相同**；DEFAULTS `hooksDenyBypass: true` |
| B 注入 | `lib/identity.js` | `49084B1278B08EFD` | 仅 cwd/静态后缀相关行 |
| D 技能 | `skills/redteam/` | 23 文件 | ≥ 基线，未删库 |
| 配置 DEFAULTS | `lib/index.js` | — | `stripMnemon` / `capabilityProtocol` / `hooksDenyBypass` 默认 true |

## 宿主矩阵

| DSH | 支持 |
|---|---|
| 0.2.0-rc.2 | 尽力（原 71 目标；cwd 变量仍可用时走 `{{cwd}}`） |
| 0.2.1-rc.2 | 阶段 A：移植上游已知修复 |
| **0.2.1-rc.3** | **主验 / 交付**（实证：rc.2→rc.3 以 UI/插件菜单为主；`{{cwd}}` 未注册问题在 rc.3 仍在，本分支装配期静态清洗 + 用户层 personaSuffix 自愈） |
| 0.2.1-rc.4 / 0.2.2-alpha | 不纳入本阶段 |

本机探测说明：当前 `$USERPROFILE\.dsh` 上 CLI 仍见 `0.1.7-rc.1`；阶段 B 以官方 `dsh-v0.2.1-rc.3` 变更说明与公开讨论为准。请在真实 **0.2.1-rc.3** 宿主上按下方清单点一次「应用」验收。

## 已移植（相对 71）

- `{{cwd}}` / `[cwd]` 装配期探测 + 静态后缀；用户层残留 `personaSuffix`/`suffix` 自愈
- redteam 预设去掉 `toolFilter`（0.2.1 注册失败 #138）
- 宿主：`lib/core.js` / `desktop.js` / `surface.js` / `extract-asar.js`（含 patch#40、hostFs、DSH NEXT/DS Bot 识别等）
- #108 停用/卸载清 `selectedDefault=redteam`（`preset-default.js`）
- Hub already queued：包内不再 insert 本体；`profile-insert` + `postinstall` 写 bundles/insert/pnpm allowBuilds
- `skills.js` 补 `fs`；zip 目录条目避免 EEXIST
- `client.inject` 增加 `ui-conversation`

## 明确未吸收（相对上游 74+）

- `isCheatTask` / 会话门外挂硬拦 / Rule 1 禁外挂
- lab-only（仅 RFC1918）、长任务公网 `pre-execute` 加严
- 开源版删除 `skills/redteam`
- 许可改为 Apache-2.0；证据账本等非破甲必需功能

## 本机 Apply 验收（请在 0.2.1-rc.3）

1. 清洗 → 应用 → 自动重启成功  
2. **新开一轮对话**，无 `unknown prompt variable "{{cwd}}"` / `"[cwd]"`  
3. redteam 预设可选中  
4. `/purge status` 正常；「自己的服务器」仍读写 `$DSH_HOME/net-scope-allow.txt`  
5. 停用/卸载后仍可新建会话（#108）  
6. `skills/redteam` 仍在；破甲 DEFAULTS 未变  

可选：`DSH_PURGE_CWD=static` 强制静态后缀；`variable` 强制保留 `{{cwd}}`（仅 0.2.0 有变量时）。
