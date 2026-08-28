# 捷安特管件工程插件｜Windows HIL 验收中转

本仓库用于三个 FuinnoAgent 插件的 Windows x64 HIL 中转。Giant 0.5.5 只优化 bending 目标管件包络的生成与 STEP 导出，当前状态为 `WINDOWS_HIL_PENDING`；插件仍是工程试用版本，所有工艺、模具和工装输出均为 `DRAFT_REVIEW`，不得直接用于量产批准。

## 下载

从 [Windows HIL 2026-08-28 预发布](https://github.com/santaalex/jieantexiangmulinshi/releases/tag/windows-hil-2026-08-28) 下载三个插件 ZIP、公开测试夹具和 `SHA256SUMS.txt`：

1. `cad-tube-centerline-thickness-fuinno-windows-x64-0.7.0.zip`
2. `engineering-product-definition-fuinno-windows-x64-0.4.0.zip`
3. `giant-tube-engineering-fuinno-windows-x64-0.5.5.zip`
4. `authorized-test-fixtures-step-pdf-2026-08-26.zip`

三个插件 ZIP 不要解压，直接在 FuinnoAgent 插件页安装；测试夹具 ZIP 需要解压到本机测试目录。旧插件版本应先移除，不要同时保留多个版本。

## 测试

Giant 0.5.5 的 [WINDOWS_HIL_TEST_PROMPT.md](WINDOWS_HIL_TEST_PROMPT.md) 只要求复用既有 H0 输入增量运行一次 bending。测试夹具已经明确获准公开；完整日志和生成结果仍不得上传到本公开仓库。

本地发布验收：0.5.5 保留 152 个轴向候选站，按 0.03 mm 几何容差生成 52 点截面，输出非 ruled NURBS STEP；私有 2052 样件 STEP/STL 均为 watertight，独立 QA 9/9 PASS，包围盒最大差 0.084 mm、体积差 0.28%。Windows 实机耗时和稳定性以本轮 HIL 为准。
