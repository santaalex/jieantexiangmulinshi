# 捷安特管件工程插件｜Windows HIL 验收中转

本仓库只用于在一台真实 Windows x64 电脑上验收三个 FuinnoAgent 插件候选包。插件仍是工程试用候选，所有工艺、模具和工装输出均为 `DRAFT_REVIEW`，不得直接用于量产批准。

## 下载

从 [Windows HIL 2026-08-28 预发布](https://github.com/santaalex/jieantexiangmulinshi/releases/tag/windows-hil-2026-08-28) 下载三个插件 ZIP、公开测试夹具和 `SHA256SUMS.txt`：

1. `cad-tube-centerline-thickness-fuinno-windows-x64-0.7.0.zip`
2. `engineering-product-definition-fuinno-windows-x64-0.4.0.zip`
3. `giant-tube-engineering-fuinno-windows-x64-0.5.1.zip`
4. `authorized-test-fixtures-step-pdf-2026-08-26.zip`

三个插件 ZIP 不要解压，直接在 FuinnoAgent 插件页安装；测试夹具 ZIP 需要解压到本机测试目录。旧插件版本应先移除，不要同时保留多个版本。

## 测试

把 [WINDOWS_HIL_TEST_PROMPT.md](WINDOWS_HIL_TEST_PROMPT.md) 全文复制给 Windows 电脑上的 Codex。测试夹具已经明确获准公开；默认只使用其中的 `H0A2Z3-DT.stp` 与 `H0A2Z3-DT.pdf`，不要运行包内其他样件。完整日志和生成结果仍不得上传到本公开仓库。

本轮只验证技术主链：0.5.1 修正旧 v1 PASS 包仍被强制要求三段过渡的问题。缺失的 TLM 保持 `NOT_APPLICABLE`，不会被伪造成数值或截断后续平台。已经 PASS 的 CAD 与产品定义结果直接复用，不重跑 STEP、PDF 或历史语义链。
