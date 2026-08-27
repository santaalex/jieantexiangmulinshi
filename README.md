# 捷安特管件工程插件｜Windows HIL 验收中转

本仓库只用于在一台真实 Windows x64 电脑上验收三个 FuinnoAgent 插件候选包。插件仍是工程试用候选，所有工艺、模具和工装输出均为 `DRAFT_REVIEW`，不得直接用于量产批准。

## 下载

从 [Windows HIL 2026-08-27 预发布](https://github.com/santaalex/jieantexiangmulinshi/releases/tag/windows-hil-2026-08-27) 下载三个 ZIP 和 `SHA256SUMS.txt`：

1. `cad-tube-centerline-thickness-fuinno-windows-x64-0.7.0.zip`
2. `engineering-product-definition-fuinno-windows-x64-0.2.0.zip`
3. `giant-tube-engineering-fuinno-windows-x64-0.4.0.zip`

不要解压 ZIP；在 FuinnoAgent 插件页直接安装。旧版本应先移除，不要同时保留多个版本。

## 测试

把 [WINDOWS_HIL_TEST_PROMPT.md](WINDOWS_HIL_TEST_PROMPT.md) 全文复制给 Windows 电脑上的 Codex，并在本机提供一组获准测试的同源 STEP 与 PDF。测试资料、源文件哈希、客户名称、完整日志和生成结果不得上传到本公开仓库。

本次验收只使用一个代表性样件，每个能力模块运行一次；缓存验证额外重复一次完全相同的 `outer-shape` 请求。失败时最多做一次诊断性重试，不扩充测试样件。
