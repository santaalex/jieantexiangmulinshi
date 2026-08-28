# Windows bending 性能与几何增量复测

> 状态：`ACCEPTED`。本任务已于 2026-08-28 完成，请勿重复运行；下文仅作为验收过程记录。

Giant 0.5.5 将 bending 从逐三角面 STEP 改为连续截面 NURBS 放样，并增加保守的 0.03 mm 自适应采样与独立 STEP/STL 几何一致性检查。不要重跑 CAD、PDF/OCR、产品定义、`plan_process`、模具、belling、flattening 或 shaping；不要修改插件、结果包或样件。

## 安装

1. 保留 CAD 0.7.0 和产品定义 0.4.0。
2. 卸载 Giant 0.5.4，安装 `giant-tube-engineering-fuinno-windows-x64-0.5.5.zip`。
3. 核对 SHA-256：`ed94b978052de11f1b1585c80b9ea942d0470548dd70ca920ac4845f721775eb`。
4. 完整退出并重启 FuinnoAgent，确认 Giant 0.5.5 为 `loaded`。本轮无需额外调用 `runtime_info`。

## 输入

直接复用本机已有且未修改的文件：

- 已验收的 Giant 0.5.2 `plan_process` PASS ZIP，SHA-256：`12d598a3ca9d85587f7b75085b5a2ff3df63b8816ae26ded6443e30b2dd74cd3`
- 与该结果绑定的原始 H0 STEP
- 上一轮 0.5.4 bending 使用的参数，原样复用

不得把上一轮生成目录、PENDING manifest 或 STEP/STL 当作本轮结果；0.5.5 必须新建一个 job。

## 执行

只调用一次 `run_rfm`，operation=`bending`，不重试。让任务在后端完成；如果前端会话提前结束，先观察同一次任务，不要发起第二次调用。

必须核对：

- engine 正常完成，无 `0xC0000005`、`0xC0000374` 或 ContractError
- `geometry_semantics=TARGET_TUBE_ENVELOPE`
- `manufacturing_ready=false`，工程状态仍为 `DRAFT_REVIEW`
- STEP 导出 `mode=nurbs_thru_sections` 且 `ruled=false`
- 回传 requested/actual axial sections、requested/actual circumferential points
- STEP/STL 均生成且 watertight
- 独立 validator 完成，acceptance=`PASS`
- 回传 STEP/STL 大小、包围盒最大差、体积相对差和结果 ZIP SHA-256

## 回传

只回传一份简洁 handback：

1. Outcome
2. Giant 版本、安装 ZIP SHA、插件 loaded 状态
3. 唯一一次 bending 的工具调用耗时与端到端耗时
4. 实际采样数、容差、STEP exporter 模式、STEP/STL 大小
5. generated model count、watertight、acceptance 与 mandatory check 数
6. STEP/STL 包围盒最大差、体积相对差、结果 ZIP SHA-256
7. 是否出现原生退出码或错误；如失败，给错误原文和最后相关日志
8. 输入 STEP 与 plan PASS ZIP 是否保持大小、SHA-256、mtime 不变
9. Files/external objects changed
10. Exact next action

不上传日志、客户路径或派生结果，不自动修改公开仓库。
