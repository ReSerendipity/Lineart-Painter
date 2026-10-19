# 发布 / 回滚 规范（Lineart-Painter 本地化）

> **来源与权威**：公共条款见 `ReSerendipity/.github` 的 `REPOSITORY_GUIDELINES.md` §10（发布 / 分发统一规范）。
> 本文件是本仓对 §10 的**本地化落地**：只填本仓特有内容，**不重写、不抵触 §10 的公共条款**。
> 文内 `<...>` 占位符已逐项替换为仓库实测值；无法确定的项按约定写「待定（负责人 + 触发条件）」，不留空。

---

## 0. 远端优先（根原则，不可改写）

本仓发布链遵循 §10.1 的**远端优先**原则：构建、打包、扫描、上传、推送**全部在 GitHub Actions 远端完成**；本地只做三件事——改版本位、落 CHANGELOG、`git tag` + `git push`。本地不执行打包 / 发布上传。

- 本仓发布链的运行位置：GitHub Actions，`windows-latest` runner（见 `.github/workflows/release.yml`）。本地 `start`/GUI 仅是**运行态**，不产出交付物。

## 1. 版本号规范

- 遵循 **SemVer** `MAJOR.MINOR.PATCH`；预发布用 `-alpha.N` / `-beta.N` / `-rc.N`。
- **唯一权威版本位**：本仓**无** `pyproject.toml` / `package.json` / `setup.py` / `__version__` 等版本声明文件。发布版本位**实际由 git tag 承担**，当前最新 tag 为 **`v1.0.0`**（Release `v1.0.0`，发布于 2026-09-13）。
- 必须同步的其它版本位：**无**。
  - 说明：`lineart_painter.py:729` 的 `CONFIG_VERSION = "1.0"` 与 `webui.py:656` 的 `{"version": "1.0"}` 是**配置文件 schema 版本**，与发布版本解耦，**不随发版递增**，不纳入版本位一致性范围。
- 版本位一致性校验入口：**无**（仓内无 `tests/test_version_consistency.py` 类校验器）。收敛为多点一致校验属待办 —— **待定（负责人 + 触发条件）**。
- 自动化方式：**手动 tag（人工）**。本仓**未**使用 release-please；`release.yml` 仅由 `push: tags v*` 与 `workflow_dispatch` 触发。

## 2. CHANGELOG 管理

- 目标口径遵循 **Keep a Changelog**：`Added / Changed / Fixed / Deprecated / Removed / Security`；每次合并 `main` 在 `[Unreleased]` 追加，type 映射 `feat→Added`、`fix→Fixed`、`refactor→Changed`；发版时 `[Unreleased]` → `[VERSION] - YYYY-MM-DD`。
- **本仓现状（2026-10-18 更新）**：仓内**已有根级 `CHANGELOG.md`**（Keep a Changelog 结构，含 `[Unreleased]` 与 `[1.0.0] - 2026-09-13` 段）。`release.yml` 的发布步骤**仍使用** `generateReleaseNotes: true`（GitHub 自动按提交分类生成 release notes），并在其基础上补固定安装说明；自动笔记为空时不阻断发布。
- 在 `release.yml` 中**改用 `CHANGELOG.md` 作为发布说明来源**仍属待办 —— **待定（负责人 + 触发条件）**。

## 3. 发布流程（远端）

标准流程（§10.2）：

1. 确认 `[Unreleased]` 条目完整
2. 同步版本位（见 §1 清单）
3. 本地打 tag 并推送：`git tag v<X.Y.Z>` → `git push origin v<X.Y.Z>`
4. 远端凭 tag 触发 `release.yml` 构建产物
5. 远端建 **draft Release** 并挂资产 → 人工审核后 `published`
6. 盯到终态：`gh run list` 确认绿；红了当场修或 revert 止损

**本仓现状与差异（2026-10-18 实测）**：`release.yml` 由 `push: tags v*` 触发，链路为「回归门禁 → PyInstaller 打包 → 生成 `SHA256SUMS.txt` → 生成 CycloneDX SBOM → `softprops/action-gh-release@v2` 发布」。当前实现**直接 `published`，未走 draft 人工审核**，属与本规范第 5 步的差异，列为待收敛项（**待定（负责人 + 触发条件）**）。此外 `workflow_dispatch` 手动触发时只跑门禁与打包、**不发布** Release（`Publish release` 步骤带 `if: startsWith(github.ref, 'refs/tags/v')` 守卫），可在不误发版的前提下验证门禁。

## 4. 分发产物与完整性

| 产物 | 构建位置 | 标签 / 命名 | 完整性手段 |
|---|---|---|---|
| Windows 便携包 `lineart_painter-windows-<tag>.zip`（PyInstaller `--onedir --windowed` 打包 `dist/`，内含 `lineart_painter.exe`） | **远端**（GitHub Actions `windows-latest`） | 以 tag 命名 `lineart_painter-windows-${github.ref_name}.zip`（不可变） | `SHA256SUMS.txt`（防传输损坏）+ `sbom-cyclonedx.json`（CycloneDX 1.6 需求清单） |

- **不涉及容器镜像**：本仓无 `ghcr.io` 推送、无 `Dockerfile`，因此 §10.3 的镜像条款（不可变 semver + sha 标签、禁 floating `latest`、Trivy 阻断、`provenance`/`sbom`）**本仓均不适用**。SBOM 以 CycloneDX 需求清单形式随 Release 提供。
- **不使用 GPG 分离签名**（2026-10-18 裁决）：本仓 `.github/workflows/` 仅有 `ci.yml` 与 `release.yml`，**不存在** `gpg-signed-release.yml`。
- **无桌面壳签名**：本仓是 PyInstaller 便携包，**非** Tauri / NSIS / APK，故无 ed25519 `*.sig` / `*.sig.ed25519`；完整性仅由 `SHA256SUMS.txt` 承担（防传输损坏）。
- **模型权重不入包**：`models/` 与 `weights/` 由 `.gitignore` 排除，运行时通过 `scripts/setup_models.py` 拉取（约 10GB），不构成 Release 资产。
- **既有 Release 现状（2026-10-18 实测）**：`v1.0.0` Release 实际仅挂**单个资产** `lineart_painter-windows.zip`（66,689,851 B），**未附** `SHA256SUMS.txt` / SBOM；该版发布于当前 `release.yml` 产物与命名口径之前，属历史差异。

## 5. 回滚

- **判定**（满足任一即触发）：本仓为本地桌面工具，**无在线监控 / 无健康端点**，故阈值 **待定（负责人 + 触发条件）**。现实可触发的信号：已发布便携包在目标 Windows 环境启动失败，或本地全量回归 `python scripts/smoke_test.py` 复现红。
- **执行**：
  - 代码与配置回滚：`git revert <bad-commit>`（保历史、可再 forward）。
  - 产物回滚：本仓无远端部署目标，无配置回滚；如需重发已发布版本，**重指向 / 删除 tag、移动任何远端 ref 属破坏性远端动作，须仓库所有者单独授权**。
- **回滚后验证**：`python scripts/smoke_test.py --fast`（零权重最小子集，与 CI 和 `release.yml` 门禁同口径：`--only "经典管线,线稿直填"`）；若本次涉及神经功能，另跑全量 `python scripts/smoke_test.py`。

## 6. SLA / 错误预算

- 本仓为**本地运行桌面工具**（Web UI 端口 `127.0.0.1:8765`），**无在线服务可用性指标**，不适用 §10 之外的在线可用性目标。
- SLA 目标 / 错误预算：**待定（负责人 + 触发条件）**。

## 7. 发布前检查清单

- [ ] 版本位确认：本仓无独立版本声明文件；核对 `git tag v<X.Y.Z>` 与 Release 标题 / 说明一致
- [ ] 发布说明：本仓已有 `CHANGELOG.md`，按 Keep a Changelog 口径核对 `[Unreleased]` 并改版本 + 日期（`release.yml` 仍生成 GitHub 自动 notes，**改用 CHANGELOG 作发布说明来源**属待收敛）
- [ ] 回归门禁通过：`python scripts/smoke_test.py --fast --only "经典管线,线稿直填"` 退出码 0（与 `.github/workflows/ci.yml`、`release.yml` 同口径）
- [ ] 若本次涉及神经功能：本地 `python scripts/smoke_test.py` 全量通过（CI runner 无 torch / onnxruntime 与模型权重，无法覆盖）
- [ ] lint / format 命令：本仓**无** lint / format 门禁，不适用
- [ ] 镜像类仓条目（Trivy 阻断 / `.trivyignore.yaml` / `provenance` + `sbom`）：**本仓无镜像，不适用**
- [ ] **本地未产出任何发布交付物**（打包、`SHA256SUMS.txt`、SBOM 均在远端 `release.yml` 生成）

---

## 附录：本仓现状与差异汇总（2026-10-18 实测）

| 项 | 本仓现状 | 与本规范 / §10 的差异 |
|---|---|---|
| 默认分支 | `main` | 符合 |
| 版本位 | 无声明文件；发布版本由 git tag 承担，最新 `v1.0.0` | 无自动一致性校验，待收敛 |
| CHANGELOG | 已有根级 `CHANGELOG.md`（Keep a Changelog）；`release.yml` 仍用 GitHub 自动 notes | 主体已落地；改用 CHANGELOG 作发布说明来源待收敛 |
| 发布链 | 有：`.github/workflows/release.yml`（tag 驱动）+ `ci.yml` | Release 直接 `published`，未走 draft 人工审核 |
| 产物 | Windows 便携 zip + `SHA256SUMS.txt` + CycloneDX SBOM | 既有 `v1.0.0` 未附校验与 SBOM（历史） |
| 容器镜像 | 无 | §10.3 镜像条款不适用 |
| 扫描 | 无镜像故无 Trivy；SBOM 以 CycloneDX 形式随 Release | §10.4 镜像扫描不适用 |
| floating `latest` | 无（无镜像） | 符合 |
| GPG 工作流 | 无（仅有 `ci.yml` / `release.yml`） | 符合 §10.6 裁决 |

## 禁止事项

- 不得重写 §10 的公共条款（远端优先、tag 驱动、版本位一致、不可变标签、扫描阻断、无 GPG）。
- 不得引入 `latest` 作发布基线；不得保留或新建 GPG 签名流程。
- 不得留空占位符，不得编造阈值；无交付物的形态（如镜像）不得虚构。
