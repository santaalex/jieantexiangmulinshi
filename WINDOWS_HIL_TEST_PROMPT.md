# 给 Windows Codex 的 0.4.0 / 0.5.0 增量验收任务

你负责在真实 Windows x64 FuinnoAgent 上验收工程图纸与产品定义 0.4.0 和 Giant 管件工艺与模具 0.5.0。CAD 0.7.0 外形链及缓存已经 PASS，本轮禁止重跑 CAD 几何、修改插件、重打包或增加测试样件。

## 1. 严格边界

- 只使用 Release 中获准公开的 `authorized-test-fixtures-step-pdf-2026-08-26.zip`，且只运行 `H0A2Z3-DT.stp` 与 `H0A2Z3-DT.pdf`。
- 不得把解压文件、绝对路径、日志、客户资料或派生结果提交到公开仓库。
- 所有结果保持 `DRAFT_REVIEW`，不得称为量产批准。
- 失败后最多做一次诊断性重试；不增加样件，不放宽 QA。
- 源 STEP/PDF 不得修改；前后只报告大小、SHA-256、mtime 是否保持不变，不公开实际哈希。

## 2. 安装与哈希

从 `windows-hil-2026-08-28` Release 下载三个插件、测试夹具和 `SHA256SUMS.txt`，逐个用 PowerShell `Get-FileHash -Algorithm SHA256` 核对。任一不一致立即停止。

保留 CAD 0.7.0；卸载产品定义 0.3.2 和 Giant 0.4.2；安装产品定义 0.4.0 和 Giant 0.5.0。ZIP 直接安装，不要解压或替换包内文件。完成后完整退出并重启 FuinnoAgent。

期望哈希：

```text
66f517c2aa3f37de555b9defb78893da712eb963c01d4f86cbd64c52b6e46318  cad-tube-centerline-thickness-fuinno-windows-x64-0.7.0.zip
cfb0c24034456460ccfab3edc73c36f4937a9b511983b739cc274f0bd5300bf7  engineering-product-definition-fuinno-windows-x64-0.4.0.zip
ce1deb4261152f67c283194cc649c2e88d0f24a6d2bba95473673e645af17fcb  giant-tube-engineering-fuinno-windows-x64-0.5.0.zip
ce4101901218ffe6ecbfc26da072de24d952f6c4a3c254fd8e38634f7f5d657f  authorized-test-fixtures-step-pdf-2026-08-26.zip
```

记录匿名 Windows、CPU、内存、可用磁盘、GPU、电源模式、FuinnoAgent 版本、安装耗时和工具是否可发现。不要记录用户名或序列号。

## 3. 运行环境

1. 产品定义调用一次 `runtime_info({"bootstrap":true})`，要求 `ready=true`、`probe_status=PASS`、`ocr_session_initialized=true`。
2. Giant 调用一次 `runtime_info({"bootstrap":true})`，要求 `ready=true`、runtime 为 `win-amd64-cp312-giant-v2`，且无 `0xC0000374`、`0xC0000005`。
3. 分开记录首次安装与热态耗时；不要手工安装系统 Python。

## 4. H0 图纸语义链

H0 是旧工艺图/历史 oracle，不是新项目的最终产品要求。对 `H0A2Z3-DT.pdf` 依次运行：

1. `inspect_sources`：设置 `usage_mode=historical_replay`，文档角色包含 `HISTORICAL_ORACLE`。
2. `extract_evidence`：检查 `1.4、1.1、1.6` 被关联到抽管/材料工序平台；`1.7、1.3、1.4、1.9` 被识别为后续工序检查点，而不是全部混成最终成品壁厚。
3. `propose_definition`：报告每个尺寸的 stage、entity_type、authority、证据位置和冲突。
4. 只有人工逐项接受、修正或驳回后，调用一次 `confirm_definition`。不得由模型自行确认。
5. 允许 1–4 个平台壁厚和 0–3 个过渡段；不存在的 TLM 保持 `NOT_APPLICABLE`，不得填零或编造正长度。
6. 若存在由总长减去已知分段得到的派生长度，必须保留公式、输入证据和残差，并由 validator 独立复算。

确认包应为 `historical_replay`。用它调用一次 Giant `plan_process`，期望失败关闭为 `HISTORICAL_ORACLE_NOT_PLANNING_INPUT` 或等价明确错误；如果它被当成新规划输入并 PASS，判为严重问题并停止。

## 5. 可选过渡段兼容回归

在这台 Windows 机器上定位上一轮 0.3.2 已经人工确认的产品定义 PASS ZIP，期望 SHA-256 为：

```text
f8a71813435b04909f0dcc133b0eb2282d17931541536db9ef347c5c8ece1253
```

该包只用于旧合同兼容回归，不代表新的产品定义方法。复用既有 CAD 0.7.0 PASS ZIP 和同一原始 STEP/PDF，调用一次 Giant 0.5.0 `plan_process`：

- T1–T4 与 TL1–TL4 必须全部保留。
- TLM1=80 mm；TLM2/TLM3 必须保持 `NOT_APPLICABLE`。
- 不得再出现 `wall transitions must be exactly one between every adjacent platform`。
- 缺少过渡段表示相邻平台之间允许立即厚度台阶；不得将其伪造为 0 mm 过渡实体，也不得截断 T2/T3/T4。
- 检查 source lineage、总参数表、工程壁厚折线、扩口/Taper 预留、固定 seed、候选方案和独立 acceptance。

如果旧 PASS ZIP 已被删除或哈希不符，不要重做 0.3.2 提取；报告 `NEEDS_INPUT` 并停止这一分支。

## 6. 下游冒烟

只有兼容回归的 `plan_process` PASS 后，才对其结果各运行一次：

- Normal preview
- TP preview
- Taper preview
- RFM `belling`、`bending`、`flattening`、`shaping`

每类使用已有明确 profile。缺少机台、图号或工艺参数时返回 `NEEDS_INPUT`，不得猜数。检查 JSON/XLSX/DXF/STEP/STL 可读性、source lineage、acceptance；需要闭合的 STL 检查 watertight，整形外皮保留开口边界和正确法向。

## 7. 性能与最终回传

- 进度持续更新且 CPU 工作时不要误报卡死。
- 连续 10 分钟无阶段日志且 CPU 约 5 分钟接近空闲，记录疑似卡死并安全停止。
- 单阶段超过 30 分钟但仍有进度，完成当前阶段后标记 `PERFORMANCE_NEEDS_ATTENTION`，不要启动第二样件。

最终在当前对话回传：

1. `Outcome`: `ACCEPTED` / `NEEDS_ATTENTION` / `BLOCKED`
2. 匿名硬件、Windows/FuinnoAgent 版本
3. 三个插件 ZIP 的文件名、SHA-256、安装状态和工具列表
4. 两个新 runtime 的冷/热耗时与 probe
5. H0 的 stage/entity/authority 结果、壁厚候选、确认 acceptance
6. historical 包进入 Giant 时是否正确失败关闭
7. 旧 v1 PASS 包兼容回归的 plan_process 状态、T/TL/TLM、acceptance 和耗时
8. Normal/TP/Taper 与四类 RFM 的状态、耗时和主要输出
9. 源文件是否保持大小、哈希、mtime 不变
10. 错误工具、阶段、错误码、复现步骤和最后 20 行日志
11. `Files/external objects changed`
12. `Exact next action`

不要自动提交公开仓库，也不要修改插件。
