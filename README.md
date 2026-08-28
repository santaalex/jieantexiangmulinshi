# 捷安特管件工程插件｜Windows HIL 验收中转

本仓库用于三个 FuinnoAgent 插件的 Windows x64 HIL 中转。Giant 0.5.4 的 RFM 增量验收已于 2026-08-28 `ACCEPTED`；插件仍是工程试用版本，所有工艺、模具和工装输出均为 `DRAFT_REVIEW`，不得直接用于量产批准。

## 下载

从 [Windows HIL 2026-08-28 预发布](https://github.com/santaalex/jieantexiangmulinshi/releases/tag/windows-hil-2026-08-28) 下载三个插件 ZIP、公开测试夹具和 `SHA256SUMS.txt`：

1. `cad-tube-centerline-thickness-fuinno-windows-x64-0.7.0.zip`
2. `engineering-product-definition-fuinno-windows-x64-0.4.0.zip`
3. `giant-tube-engineering-fuinno-windows-x64-0.5.4.zip`
4. `authorized-test-fixtures-step-pdf-2026-08-26.zip`

三个插件 ZIP 不要解压，直接在 FuinnoAgent 插件页安装；测试夹具 ZIP 需要解压到本机测试目录。旧插件版本应先移除，不要同时保留多个版本。

## 测试

Giant 0.5.4 的 [WINDOWS_HIL_TEST_PROMPT.md](WINDOWS_HIL_TEST_PROMPT.md) 已执行完毕并保留为验收记录，无需重复运行。测试夹具已经明确获准公开；完整日志和生成结果仍不得上传到本公开仓库。

验收结果：0.5.4 成功消费 contract 2.2.0 的 0.5.2 plan PASS 包；belling 与 bending 均生成 watertight STEP/STL、独立 QA 9/9 PASS 和结果 ZIP，未再出现版本绑定错误、`0xC0000005` 或 `0xC0000374`。bending 实测约 25 分 51 秒，属于后续可优化的性能项，不影响本次功能验收。
