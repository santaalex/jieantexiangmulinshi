# 给 Windows Codex 的完整验收任务

你现在负责在这台真实 Windows x64 电脑上，对三个 FuinnoAgent 插件做一次受控的端到端 HIL 验收。目标是验证“普通用户安装、私有运行环境、一个真实样件全链、重复请求缓存与低配 CPU 性能”，不是修改算法、重打包或扩大测试集。

## 严格边界

- Release 中的 `authorized-test-fixtures-step-pdf-2026-08-26.zip` 已经明确获准公开。只使用其中的 `H0A2Z3-DT.stp` 与 `H0A2Z3-DT.pdf`；不要运行包内其他样件。
- 除上述已授权原始测试 ZIP 外，不得把解压文件、绝对路径、运行日志或任何派生结果再次提交到这个公开 GitHub 仓库。
- 不修改三个插件 ZIP，不替换包内文件，不修改算法或 QA 阈值。
- 每个模块运行一次；只有完全相同的 `outer-shape` 请求为验证缓存而额外运行一次。失败后最多做一次诊断性重试，不增加样件。
- 不把结构 QA 的 PASS 说成量产批准。工艺、模具、RFM 结果均保持 `DRAFT_REVIEW`。

## 1. 机器与包检查

1. 记录 Windows 版本、x64、CPU 型号/核心数、内存、可用磁盘、是否有 GPU、FuinnoAgent 版本和当前电源模式。不要记录用户名或机器序列号。
2. 从本仓库的 `windows-hil-2026-08-27` Release 下载三个插件 ZIP、`authorized-test-fixtures-step-pdf-2026-08-26.zip` 和 `SHA256SUMS.txt`。
3. 用 PowerShell `Get-FileHash -Algorithm SHA256` 逐个核对；任一不一致立即停止并报告。
4. 把测试夹具解压到本机新建的私有测试目录，确认默认配对文件 `H0A2Z3-DT.stp` 与 `H0A2Z3-DT.pdf` 均存在；不要把解压目录加入 Git。
5. 在 FuinnoAgent 中移除这三个插件的旧版本，然后直接安装插件 ZIP，不要解压：
   - CAD 管件形线与壁厚 `0.7.0`
   - 工程图纸与产品定义 `0.2.0`
   - Giant 管件工艺与模具 `0.4.0`
6. 记录安装成功与工具是否可发现。安装界面若必须由用户点击，可以请求一次协助，但不要绕过权限。

## 2. 私有运行环境预热

按顺序、不要并行，对三个插件分别调用 `runtime_info(bootstrap=true)`，记录每个插件的冷启动耗时、下载/安装结果、Python 版本、runtime_id，以及 OCP/VTK/VMTK 或 OCR 依赖能否正常加载。完成后再调用一次 `runtime_info(bootstrap=false)`，记录热启动耗时。

首次环境安装时间与后续任务计算时间必须分开统计。若安装失败，保留错误码和最后 20 行相关日志，不要手工安装系统 Python。

## 3. 一个样件的完整链路

### A. PDF/产品定义插件

对 `H0A2Z3-DT.pdf` 依次执行：

1. `inspect_sources`
2. `extract_evidence`
3. `propose_definition`
4. 把 T1–T4、TL1–TL4、TLM1–TLM3、斜面宽长及冲突逐项展示给工程师。
5. 只有工程师明确接受、修正或驳回后，才执行 `confirm_definition`；不得由模型自己批准。

记录每步耗时、候选数量、需要人工确认的字段、最终 acceptance 状态和产品定义结果包。若资料本身没有某字段，应失败关闭或标为待确认，不得猜数。

### B. CAD 外形插件

将 `H0A2Z3-DT.stp` 直接执行 `analyze`，模式必须为 `outer-shape`；正常流程不要先跑 `inspect`，也不要使用 STEP 内壁计算捷安特工艺壁厚。

确认并记录：

- 环境、STEP 导入、端面识别、扫描、锚点复用、中心线、81 个最终截面、独立 QA、打包和文件登记都有阶段日志；
- `acceptance.json` 为 PASS，结果包含 `shape-line.csv`、`axial-geometry.csv`、manifest 和结果 ZIP；
- 41 个中心线锚点来自精确扫描复用，最终 81 站和独立关键站重切仍保留；
- STEP 的大小、SHA-256 和 mtime 在运行前后不变；
- 分阶段耗时、总耗时、峰值内存（能安全取得时）和 CPU 利用率。

随后对完全相同的 STEP、profile 和参数再次执行 `outer-shape analyze`：必须报告 `cache_reused=true`，跳过 CAD 几何重算，但重新生成并运行独立 validator；两次 acceptance 均应 PASS。若没有复用，记录原因，不要反复重跑。

### C. Giant 工艺规划

输入同一原始 STEP、B 步 PASS 结果 ZIP、A 步经人工确认的产品定义 PASS 包和原 PDF，执行一次 `plan_process`。确认 source lineage 绑定、T/TL/TLM 映射、工程壁厚折线、扩口/Taper 反向预留、总参数表、固定 seed、候选方案和 acceptance。提案如果仍为 `NEEDS_ENGINEER_CONFIRMATION`，按合同停住，不得强行进入模具。

### D. 模具设计

在工艺规划 PASS 后，分别运行一次 Normal、TP、Taper 的 preview 模式。每类使用插件/资料中明确存在的机台或设计 profile；缺少机台参数时报告 `NEEDS_INPUT`，不得编造。检查 DXF/Excel/JSON 和适用的 STEP/STL 是否可打开、图号是否仍为预览占位、acceptance 是否通过。

### E. RFM 工装

在参数齐全且合同允许时，对同一个工艺规划 PASS 包各运行一次 `belling`、`bending`、`flattening`、`shaping`。检查输出 STEP/STL 可读性、应闭合网格的 watertight、整形外皮的开口边界/法向、source lineage 和 acceptance。缺少明确工艺参数时失败关闭，不得猜测。

## 4. 卡顿与超时判定

- 任何阶段持续运行但进度仍更新、CPU 仍在工作时，不要误报死循环。
- 连续 10 分钟没有新阶段/站位日志，并且 CPU 连续约 5 分钟接近空闲时，记录为“疑似卡死”，保存当时阶段、已用时和最后 20 行日志，再安全停止该次调用。
- 单个几何任务超过 30 分钟但仍有进度时，让当前阶段完成一次并标记 `PERFORMANCE_NEEDS_ATTENTION`；不要启动第二份样件或无限重试。

## 5. 最终回复格式

完成后直接在当前 Codex 对话回复以下 handback，不要自动上传公开仓库：

1. `Outcome`: `ACCEPTED` / `NEEDS_ATTENTION` / `BLOCKED`
2. 匿名硬件与 Windows/FuinnoAgent 版本
3. 三个 ZIP 的文件名、SHA-256、安装状态和可发现工具
4. 三个 runtime 的冷启动/热启动耗时及依赖加载结果
5. PDF产品定义、首次 outer-shape、缓存 outer-shape、工艺规划、Normal/TP/Taper、四类 RFM：逐项状态、耗时、acceptance、主要输出
6. `cache_reused` 证据、独立 validator 重跑证据、阶段进度是否可见
7. 源 STEP/PDF 是否保持大小、哈希、mtime 不变（只写 true/false，不在公开内容中暴露实际哈希）
8. 性能问题：最慢阶段、CPU/内存观察、是否超过 30 分钟
9. 所有错误的工具名、阶段、错误码、可复现步骤和最后 20 行相关日志
10. `Files/external objects changed`：只列插件安装、本机私有结果目录；确认公开仓库未写入客户数据
11. `Exact next action`：只给最小下一步，不主动修改插件
