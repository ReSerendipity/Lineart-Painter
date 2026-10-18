# Changelog

本仓所有值得记录的变更均登记于此文件。

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)，
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html)。

> **版本口径与本文件来源**：本仓**无** `pyproject.toml` / `package.json` / `setup.py` / `__version__` 等版本声明文件，
> 发布版本位**由 git tag 承担**（当前最新 tag 为 `v1.0.0`）；本文件的版本段与 git tag / GitHub Release 一一对应。
> 发布 / 回滚口径详见 [docs/release-governance.md](docs/release-governance.md)。
>
> **收录口径**：分类遵循 Keep a Changelog；`feat→Added`、`fix→Fixed`、`refactor→Changed`。为便于溯源，每条目
> 保留其原始提交类型前缀与短 SHA；`docs` / `ci` / `chore` 等非规范类型按语义就近归类，并在条目前显式标注原前缀。

---

## [Unreleased]

### Added

- **feat(release)**：`release.yml` 增加 CycloneDX SBOM 产物与自动 release notes（`8ad2098`）。
- **R13 / release**：`release.yml` 打包后生成 `SHA256SUMS.txt`，并把带版本名的 zip 与校验文件一并挂进 Release（`6edb387`）。
- **chore**：新增模型下载辅助脚本 `download_models.bat` / `download_models.sh`（`9b00313`、`461778b`、`ae8fb97`，三次提交内容相同）。

### Changed

- **R12**：ONNX 会话按 `ort.get_available_providers()` 选 `CUDA→CPU`；默认提示词改从 `prompts/default_color.json` 读取（`8910a72`）。
- **R12**：K-Means 前固定 OpenCV RNG 种子，保证同输入同输出（可复现）（`4fdbaf8`）。
- **R13**：`smoke_test.py` 三处视频分支加 `os.path.exists(VIDEO)` 守卫，并在 `main()` 清理前打印缺输入提示（`64ccdf5`）。
- **ci**：把 `smoke_test` 零权重子集接入 push/PR 门禁（`e6376bd`）。
- **chore**：标准化 `LICENSE` 为 Apache-2.0，使 GitHub SPDX 识别生效（`9e1ff8d`）。
- **chore(dx)**：落地中期健康检查的机械可执行项（P1）（`b68fe30`）。
- **chore**：提交本地未提交改动（批量同步）（`b77da5d`）。
- **docs**：新增 `THIRD_PARTY_NOTICES.md`，逐条登记随仓内嵌的第三方源码与许可证（`01e361e`）；README 许可段落改为指向该文件（`d657ea3`）；补登记基准/tip/release 三处 blob 一致性核验与核验日期（`5c541d0`）。
- **docs**：README 补最小可运行示例（内置 `demo_input.png`）与版权行（`479485f`）。
- **docs(icons)**：新增图标设计源（master SVG + 展示页）（`304cc38`）。
- **docs(release)**：新增本仓发布 / 回滚规范 `docs/release-governance.md`（`0104a6a`）。

### Fixed

- **fix(webui)**：正确服务 `/favicon.ico`（移除失效的 204 stub 分支）（`d22e8f7`）。
- **fix(release)**：SBOM 步强制 UTF-8，修复 Windows 读取含中文注释 `requirements` 时的 `charmap` 崩溃（`d4a8cf1`）。
- **fix(release)**：zip 产物名带版本号，对齐 `SHA256SUMS` / files 登记（`66199c8`）。

## [1.0.0] - 2026-09-13

本仓首个公开发布（`Initial open-source release`，见 commit `5f1117d`），对应 git tag `v1.0.0`（commit `f55f712`）。

> **溯源说明**：GitHub Release `v1.0.0` 的发布说明（body）为**空**——发布时由 GitHub 自动生成的 notes 无条目，
> 亦未见人工撰写的变更条目。为**不编造内容**，本节仅登记「版本 ↔ tag ↔ commit」对应关系，不回填未经验证的分类条目。
> 该 Release 实际仅附单个资产 `lineart_painter-windows.zip`（既发布于当前 `release.yml` 产物与命名口径之前）。
> 分类变更条目自本文件建立后开始维护。

[Unreleased]: https://github.com/ReSerendipity/Lineart-Painter/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/ReSerendipity/Lineart-Painter/releases/tag/v1.0.0
