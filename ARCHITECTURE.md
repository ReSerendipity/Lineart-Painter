# 架构 — Lineart-Painter

> 治理定位：`delivery=release`、`lifecycle=maintenance`（`governance/desired-state.json`）。
> 本文按 2026-11-03 的实际目录结构生成；数据流与边界须由维护者在实现变更时同步复核。

## 1. 定位

本地运行的 AI 临摹重绘工作台：把图片或视频拆成「线稿 → 固有色 → 体积明暗 → 反射光 → 色线 → 高光 → FINAL」七步流水线，在浏览器里逐阶段查看并导出 PNG / MP4。经典路径（XDoG + K-Means）零模型权重、CPU 秒级出图；神经路径按需下载权重，可选 GPU 加速。

## 2. 组件与依赖方向

```text
工具层：scripts/ (7)
资产层：assets/ (8)
支撑层：docs/ (12)、.github/ (7)
其他层：basicsr/ (7)、pipelines/ (3)、_archive/ (2)
根级层：根级文件 (19)
```

| 组件 | 目录 | 文件数 | 代表文件 |
|---|---|---|---|
| 根级文件 | `/` | 19 | `.editorconfig`, `.gitignore`, `CHANGELOG.md` |
| `docs` | `docs/` | 12 | `docs/THIRD_PARTY_NOTICES.md`, `docs/agent-contract.md`, `docs/assets/readme-lineart-painter-1-20261026.png` |
| `assets` | `assets/` | 8 | `assets/icons/icon-128.png`, `assets/icons/icon-16.png`, `assets/icons/icon-24.png` |
| `basicsr` | `basicsr/` | 7 | `basicsr/__init__.py`, `basicsr/archs/__init__.py`, `basicsr/archs/ddcolor_arch_utils/__init__.py` |
| `scripts` | `scripts/` | 7 | `scripts/_a2s_data.py`, `scripts/_a2s_model.py`, `scripts/_a2s_test.py` |
| `pipelines` | `pipelines/` | 3 | `pipelines/default_classic.json`, `pipelines/minimal_lineart_only.json`, `pipelines/neural_color.json` |
| `_archive` | `_archive/` | 2 | `_archive/仓库排查报告_学习报告仓库系统性评估.md`, `_archive/同类开源仓库调研报告_GitHub竞品分析.md` |

依赖方向：入口层（应用 / 页面）调用领域层（业务模块），领域层依赖基础设施层（脚本、配置、模型资产）；反向引用不允许。

## 3. 数据流

输入图片 / 视频 → 拆分为线稿与色层 → ComfyUI 节点管线逐层重绘 → 合成输出。

## 4. 外部依赖

| 依赖 | 用途 | 不可用时 |
|---|---|---|
| ComfyUI | 节点管线宿主 | 无法重绘 |
| Python 依赖 | 推理 | 节点加载失败 |

## 5. 边界与非目标

- 发布链只走 §10：远端优先、tag 驱动、资产附 `SHA256SUMS.txt`。
- 分发产物由 CI 生成，不入库。
- 不入库：密钥 / token / 口令 / 连接串、本机绝对路径、`local/` 与 `AGENTS.md`、构建产物。
- 治理正文只在 `ReSerendipity/.github/governance`，本仓不复制规则正文。

## 6. 变更影响面

| 改什么 | 必须同步 | 由谁校验 |
|---|---|---|
| 目录结构 / 入口 | 本文「组件与依赖方向」与「数据流」 | 维护者 |
| CI 与门禁 | 本仓 `README.md` 的测试命令；治理仓 §20 远端载体口径 | `audit-drift.py` |
| 依赖清单 | 锁文件与 `.github/dependabot.yml` 的生态段 | Dependabot / `lock-consistency` |
| README 展示图 | §8.1 真实性红线（图必须来自真实运行） | 治理仓 `readme-image-provenance` |
