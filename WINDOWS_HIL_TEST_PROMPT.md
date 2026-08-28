# Windows 技术主链复测

本轮只验证 Giant 0.5.1 的可选过渡段修复和后续输出能力。不要重跑 CAD、PDF、OCR、历史语义或产品定义确认；不要修改插件和测试样件。

## 安装

1. 保留 CAD 0.7.0 和产品定义 0.4.0。
2. 卸载 Giant 0.5.0，安装 `giant-tube-engineering-fuinno-windows-x64-0.5.1.zip`。
3. 核对 SHA-256：`fc789873e8a7063f0105c3f4a0b2445ecff1588504aee3b61149780d1b0b34ea`。
4. 完整退出并重启 FuinnoAgent，只调用一次 Giant `runtime_info({"bootstrap":false})`。

## 输入

直接复用本机已有文件：

- CAD 0.7.0 PASS ZIP：`6779739152f14f2224f7446c39f13af1193ede6f4352a483dc5bd0b5f5fb3f61`
- 产品定义 0.3.2 PASS ZIP：`f8a71813435b04909f0dcc133b0eb2282d17931541536db9ef347c5c8ece1253`
- 原始 H0 STEP/PDF，仅用于 source lineage；不得重算或修改。

## 执行

只调用一次 `plan_process`。期望输入参数为：

- `T1–T4 = 1.7 / 1.3 / 1.9 / 1.4 mm`
- `TL1–TL4 = 90 / 280 / 140 / 20 mm`
- `TLM1 = 80 mm`
- `TLM2/TLM3 = NOT_APPLICABLE`

必须确认：四个平台全部保留；缺失 TLM 不被猜数；不再出现 `wall transitions must be exactly one between every adjacent platform`。

若 `plan_process` PASS，再各运行一次：

- Normal preview
- TP preview
- Taper preview
- RFM belling
- RFM bending
- RFM flattening
- RFM shaping

缺少明确机台或工艺参数时返回 `NEEDS_INPUT`，不得猜数；其余链路继续。所有输出保持 `DRAFT_REVIEW`。

## 回传

只回传一份简洁 handback：

1. Outcome
2. Giant 版本、ZIP SHA、runtime 状态
3. plan_process 状态、耗时、T/TL/TLM、acceptance、结果 ZIP SHA
4. 三个模具 preview 与四类 RFM 的状态、耗时和主要输出
5. 错误码和原文
6. 源文件是否保持不变
7. Exact next action

不上传日志或派生结果，不自动修改公开仓库。
