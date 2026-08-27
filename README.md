# 捷安特管件工程插件｜Windows HIL 验收中转

本仓库只用于在一台真实 Windows x64 电脑上验收三个 FuinnoAgent 插件候选包。插件仍是工程试用候选，所有工艺、模具和工装输出均为 `DRAFT_REVIEW`，不得直接用于量产批准。

## 下载

从 [Windows HIL 2026-08-28 预发布](https://github.com/santaalex/jieantexiangmulinshi/releases/tag/windows-hil-2026-08-28) 下载三个插件 ZIP、公开测试夹具和 `SHA256SUMS.txt`：

1. `cad-tube-centerline-thickness-fuinno-windows-x64-0.7.0.zip`
2. `engineering-product-definition-fuinno-windows-x64-0.4.0.zip`
3. `giant-tube-engineering-fuinno-windows-x64-0.5.0.zip`
4. `authorized-test-fixtures-step-pdf-2026-08-26.zip`

三个插件 ZIP 不要解压，直接在 FuinnoAgent 插件页安装；测试夹具 ZIP 需要解压到本机测试目录。旧插件版本应先移除，不要同时保留多个版本。

## 测试

把 [WINDOWS_HIL_TEST_PROMPT.md](WINDOWS_HIL_TEST_PROMPT.md) 全文复制给 Windows 电脑上的 Codex。测试夹具已经明确获准公开；默认只使用其中的 `H0A2Z3-DT.stp` 与 `H0A2Z3-DT.pdf`，不要运行包内其他样件。完整日志和生成结果仍不得上传到本公开仓库。

0.4.0 增加通用图纸语义图，区分产品要求、材料、工序、检验和历史 oracle，并支持 1–4 个平台、0–3 个可选过渡段。0.5.0 消费该语义合同；缺失的 TLM 保持 `NOT_APPLICABLE`，不会被伪造成零值或截断后续平台。已经 PASS 的 CAD 0.7.0 外形结果直接复用，不再重跑耗时的 STEP 几何。失败时最多做一次诊断性重试，不扩充测试样件。
