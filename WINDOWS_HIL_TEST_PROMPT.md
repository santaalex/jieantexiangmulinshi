# Windows RFM 增量复测

> 状态：`ACCEPTED`。本任务已于 2026-08-28 完成，请勿重复运行；下文仅作为验收过程记录。

本轮只验证 Giant 0.5.4 对既有 0.5.2 plan PASS 包的兼容，以及 RFM 原生库退出修复。不要重跑 CAD、PDF/OCR、产品定义、`plan_process` 或三个模具 preview；不要修改插件、结果包或测试样件。

## 安装

1. 保留 CAD 0.7.0 和产品定义 0.4.0。
2. 卸载 Giant 0.5.3，安装 `giant-tube-engineering-fuinno-windows-x64-0.5.4.zip`。
3. 核对 SHA-256：`a03a6b57136d1e9d17b73844c320c08f685e34fe8f8c89793f0d7689f4d4b80a`。
4. 完整退出并重启 FuinnoAgent，确认 Giant 0.5.4 loaded。只调用一次 `runtime_info({"bootstrap":false})`。

## 输入

直接复用本机已有且未修改的文件：

- Giant 0.5.2 `plan_process` PASS ZIP，SHA-256：`12d598a3ca9d85587f7b75085b5a2ff3df63b8816ae26ded6443e30b2dd74cd3`
- 原始 H0 STEP，仅用于既有 RFM source lineage
- 0.5.2 失败调用所用的 belling 与 bending 参数原样复用

不得接受 0.5.2/0.5.3 崩溃后留下的 PENDING manifest、STEP/STL 或未验收目录作为 PASS；必须由 0.5.4 分别新建 job。

## 执行

只执行两个调用：

1. `run_rfm`，operation=`belling`
2. `run_rfm`，operation=`bending`

每个调用只运行一次，不做诊断重试。必须确认：

- 不再出现 `process result version, job, or engineering-status binding mismatch`
- engine 正常返回，不再出现 `0xC0000005` 或 `0xC0000374`
- 生成 STEP/STL
- 独立 validator 完成且 acceptance=`PASS`
- 生成结果 ZIP，并回传 SHA-256
- 输出仍为 `DRAFT_REVIEW`

`flattening` 和 `shaping` 本轮不运行；它们此前的 `NEEDS_INPUT` 不是本次缺陷。

## 回传

只回传一份简洁 handback：

1. Outcome
2. Giant 版本、安装 ZIP SHA、runtime 状态
3. belling 与 bending 各自的状态、耗时、generated model count、acceptance、结果 ZIP SHA
4. 是否仍出现原生退出码；如失败，给错误原文和最后相关日志
5. 输入文件和既有 plan PASS ZIP 是否保持不变
6. Files/external objects changed
7. Exact next action

不上传日志或派生结果，不自动修改公开仓库。
