# 捷安特管件工程插件｜Windows HIL 验收中转

本仓库用于三个 FuinnoAgent 插件的 Windows x64 HIL 中转。Giant 0.5.5 的 bending 性能与几何增量验收已于 2026-08-28 `ACCEPTED`；插件仍是工程试用版本，所有工艺、模具和工装输出均为 `DRAFT_REVIEW`，不得直接用于量产批准。

## 下载

从 [Windows HIL 2026-08-28 预发布](https://github.com/santaalex/jieantexiangmulinshi/releases/tag/windows-hil-2026-08-28) 下载三个插件 ZIP、公开测试夹具和 `SHA256SUMS.txt`：

1. `cad-tube-centerline-thickness-fuinno-windows-x64-0.7.0.zip`
2. `engineering-product-definition-fuinno-windows-x64-0.4.0.zip`
3. `giant-tube-engineering-fuinno-windows-x64-0.5.5.zip`
4. `authorized-test-fixtures-step-pdf-2026-08-26.zip`

三个插件 ZIP 不要解压，直接在 FuinnoAgent 插件页安装；测试夹具 ZIP 需要解压到本机测试目录。旧插件版本应先移除，不要同时保留多个版本。

## 测试

Giant 0.5.5 的 [WINDOWS_HIL_TEST_PROMPT.md](WINDOWS_HIL_TEST_PROMPT.md) 已完成并保留为验收记录，无需重复运行。测试夹具已经明确获准公开；完整日志和生成结果仍不得上传到本公开仓库。

Windows 实机验收：同一 H0 bending 从 0.5.4 的 1551.192 秒降至 0.5.5 的 25.426 秒，约快 61 倍；保留全部 152 个轴向截面并按 0.03 mm 容差使用 76 点周向截面，输出非 ruled NURBS STEP。独立 QA 9/9 PASS，STEP/STL 最大包围盒差约 0.000097 mm、体积差约 0.114%，未出现 ContractError 或 Windows 原生退出码。
