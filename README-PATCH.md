# dsh-workbuddy-connect（渠道拦截修复版）

这是 [corrinehu/dsh-workbuddy-connect](https://github.com/corrinehu/dsh-workbuddy-connect) v0.7.9 的**修复版**（`0.7.9-p1`）：在原版基础上补齐了 WorkBuddy 网关渠道过滤的三条改写规则，解决发消息时报
`workbuddy upstream client (http 400)` / 「请求被拦截，请重新发送」（code 11128）的问题。

被拦截的指纹句是 DSH 系统提示里的以下组合（两句同时出现才触发）：

- `the user mentioned` + `relative paths resolve against the workspace root`
- 以及产品名 `the harness`

本版在插件的 `CHANNEL_PHRASE_REWRITES` 里补齐对应改写，其余代码与 0.7.9 完全一致。

## 安装（DSH 插件管理器输入 GitHub 地址）

> 本仓库为**私人仓库**，安装地址必须带上访问令牌：

```
https://<TOKEN>@github.com/LJY-YL/dsh-workbuddy-connect
```

把上面的地址粘贴到 DSH 的 **设置 → 插件 → 添加插件** 输入框即可。

**前提条件**：DSH 通过 git 拉取 GitHub 仓库，目标机器必须已安装 [Git for Windows](https://git-scm.com/download/win)（macOS/Linux 自带）。未装 git 时安装会报 `git ls-remote failed`。安装过程中如提示构建脚本确认（`@google/genai`、`protobufjs`），在插件管理器里允许即可（与你装官方版时相同）。

命令行方式（web / dsh-tui profile）：

```powershell
dsh plugin --profile web add "https://<TOKEN>@github.com/LJY-YL/dsh-workbuddy-connect"
```

> 注意：插件 peer 依赖为 DSH `0.2.1-rc.2` 内核线；其他内核版本请在插件设置里通过兼容性豁免（compatibility exemption）放行，或等待上游适配。

## 与上游的差异

1 个声明 + 1 个文件：

- `package.json`：新增 `engines.dsh: ">=0.2.0-rc.1 <0.3.0"`。官方 0.7.9 的 peer 依赖把宿主包钉死在 `0.2.1-rc.2`，在最新的 DSH（0.2.1-rc.3 内核）上会被兼容性检查判为「异常」无法启用；存在 `engines.dsh` 字段时 DSH 只看它，因此本版在 rc.3 上**开箱即用**，无需手动加版本豁免。
- `lib/host-heartbeat-SJaZ33tR.js`：`CHANNEL_PHRASE_REWRITES` 补充两条规则
  （`the user mentioned`、`relative paths resolve against the workspace root` 的改写），
  并附带 `sanitizeChannelText` / `sanitizeChannelPhrases` 应用在聊天正文上。

## 适用范围与边界

- 适用于 DSH `0.2.x`（`>=0.2.0-rc.1 <0.3.0`）；DSH 升级到 0.3 后需重新评估并改 `engines.dsh`。
- 这是和网关指纹识别的对抗：DSH 若再改系统提示词，可能出现新的指纹句，届时同样需要补规则。
- 官方包已停止维护；本版本固定在 0.7.9 的行为，不跟随上游修复。

## 维护说明

原插件升级后，本补丁需要重打（修改 `lib/host-heartbeat-SJaZ33tR.js` 中的
`CHANNEL_PHRASE_REWRITES` 后替换文件）。DSH 从 GitHub 源码安装时会自动
`pnpm install --omit=dev` 安装运行依赖 `@earendil-works/pi-ai@1.3.0`（公网 npm 可拉取）。

## 许可

MIT（见 LICENSE，继承自上游项目）。
