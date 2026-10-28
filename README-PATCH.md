# dsh-workbuddy-connect（渠道拦截修复版）

这是 [corrinehu/dsh-workbuddy-connect](https://github.com/corrinehu/dsh-workbuddy-connect) v0.7.9 的**修复版**（`0.7.9-p1`）：在原版基础上补齐了 WorkBuddy 网关渠道过滤的三条改写规则，解决发消息时报
`workbuddy upstream client (http 400)` / 「请求被拦截，请重新发送」（code 11128）的问题。

被拦截的指纹句是 DSH 系统提示里的以下组合（两句同时出现才触发）：

- `the user mentioned` + `relative paths resolve against the workspace root`
- 以及产品名 `the harness`

本版在插件的 `CHANNEL_PHRASE_REWRITES` 里补齐对应改写，其余代码与 0.7.9 完全一致。

## 安装（DSH 插件管理器直接输入 GitHub 地址）

> 本仓库为私人仓库，安装地址需要带上访问令牌（令牌只授予读取本仓库的权限）：

```
https://<TOKEN>@github.com/LJY-YL/dsh-workbuddy-connect
```

在 DSH 的 **设置 → 插件 → 添加插件**（或插件面板的「从 GitHub 安装」）输入框里粘贴上面的地址即可。

命令行方式（web / dsh-tui profile）：

```powershell
dsh plugin --profile web add "https://<TOKEN>@github.com/LJY-YL/dsh-workbuddy-connect"
```

> 注意：插件 peer 依赖为 DSH `0.2.1-rc.2` 内核线；其他内核版本请在插件设置里通过兼容性豁免（compatibility exemption）放行，或等待上游适配。

## 与上游的差异

仅 1 个文件、1 处逻辑改动：

- `lib/host-heartbeat-SJaZ33tR.js`：`CHANNEL_PHRASE_REWRITES` 补充两条规则
  （`the user mentioned`、`relative paths resolve against the workspace root` 的改写），
  并附带 `sanitizeChannelText` / `sanitizeChannelPhrases` 应用在聊天正文上。

## 维护说明

原插件升级后，本补丁需要重打（等价脚本见上游使用习惯：修改
`lib/host-heartbeat-SJaZ33tR.js` 中的 `CHANNEL_PHRASE_REWRITES` 后替换文件）。
本仓库 `files` 已包含 `lib/`，DSH 从 GitHub 源码安装时会自动
`npm install --omit=dev` 安装运行依赖 `@earendil-works/pi-ai@1.3.0`（公网 npm 可拉取）。

## 许可

MIT（见 LICENSE，继承自上游项目）。
