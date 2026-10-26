# Lineart Painter

[![CI](https://github.com/ReSerendipity/Lineart-Painter/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/ReSerendipity/Lineart-Painter/actions/workflows/ci.yml)
[![Release](https://img.shields.io/github/v/release/ReSerendipity/Lineart-Painter)](https://github.com/ReSerendipity/Lineart-Painter/releases)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB)](https://www.python.org/)
[![Last commit](https://img.shields.io/github/last-commit/ReSerendipity/Lineart-Painter)](https://github.com/ReSerendipity/Lineart-Painter/commits/main)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-A3D5E8)](#-贡献)

本地运行的 AI 临摹重绘工作台：把图片或视频拆成「线稿 → 固有色 → 体积明暗 → 反射光 → 色线 → 高光 → FINAL」七步流水线，在浏览器里逐阶段查看并导出 PNG / MP4。经典路径（XDoG + K-Means）零模型权重、CPU 秒级出图；神经路径按需下载权重，可选 GPU 加速。

[中文](#中文) | [English](#english)

---

## 中文

### 项目简介

Lineart Painter 是一个**纯本地**运行的图片/视频重绘工作台。它把一张照片或一段视频自动跑成「临摹式」的分阶段重绘结果，并在浏览器里提供批量队列、阶段预览、一键打包。项目同时暴露两种入口：

- **本地 Web UI**：`python webui.py`，浏览器打开 `http://127.0.0.1:8765`，无需前端构建工具。
- **命令行**：`python lineart_painter.py input.png --out out`，适合脚本化批处理。

经典路径不依赖任何模型权重，CI 在无 GPU、无权重的 Ubuntu runner 上直接跑通；需要更强效果时，再按需下载神经模型（ControlNet + SD1.5、Anime2Sketch、DDColor、AnimeGANv2、AnimateDiff）。

### 核心特性

- **图片 / 视频双输入**：Web UI 支持多选批量，串行队列处理；视频自动转 H.264 MP4 导出。
- **经典 XDoG + K-Means 管线**：毫秒~秒级，无权重、无 GPU 也能跑完整七步。
- **可选神经上色**：ControlNet + Stable Diffusion 1.5，5 种 ControlNet 变体（anime / manga_line / standard / canny / scribble / depth），可多选加权融合。
- **可选神经线稿**：Anime2Sketch 输出手绘感线条；也支持直接喂入手绘线稿（`--input-lineart`）跳过提取。
- **照片与视频专用模块**：DDColor 黑白老照片上色、AnimeGANv2 照片转动漫、AnimateDiff 视频帧间一致性（缓解闪烁）。
- **浏览器内阶段预览**：每一步一张图，点图下载单步结果，一键打包 ZIP。

### 展示

**主界面**：上传仓内两张样例图（`cad_demo_input.png` 线稿 + `demo_input.png` 彩色插画），线稿/上色引擎、ControlNet 变体、种子、分辨率等参数集中在一张卡片内。

![主界面](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-1-20261026.png)

**参数细项**：XDoG 线稿阈值 `eps=-0.10`、色块数 `k=10`、随机种子 `seed=42`、原图混合强度 `strength=0`、上色提示词均已填好；下方为参考图上传与颜色引导画布。

![参数细项](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-2-20261026.png)

**生成结果（线稿输出）**：对仓内样例 `cad_demo_input.png` 跑经典 XDoG + K-Means（CPU 实测 2.1 秒，未加载任何神经权重），`01 线稿` 即 XDoG 真实输出，后续 `02 固有色` … `06 高光与细节` 与 `FINAL 还原(黑线)` 同屏可见。

![生成结果](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-3-20261026.png)

**批量结果**：一次队列同时上传 `cad_demo_input.png` + `demo_input.png`，结果区依次堆叠两组任务卡片；图上方露出的是上一个任务的 `FINAL 还原(黑线)`，下方是 `demo_input.png` 的六步阶段产物，右上角一键打包 ZIP。

![批量结果](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-4-20261026.png)

**架构总览**：两个入口（Web UI / CLI）→ 输入 → 编排流水线 → 经典或神经两条引擎路径 → 阶段产物与导出。

![架构总览](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-5.svg)

### 快速开始

#### 1. 环境与依赖

建议 Python 3.10+（CI 在 3.12 上验证）。经典路径只装 `requirements.txt` 即可跑通：

```bash
python -m venv .venv
# Windows:  .venv\Scripts\activate
# macOS/Linux:  source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

如需神经功能（Anime2Sketch 线稿、ControlNet 上色、DDColor、AnimeGANv2、AnimateDiff），再追加：

```bash
# 建议先按本机 CUDA 版本装 GPU 版 PyTorch（示例 CUDA 12.8）
python -m pip install torch torchvision --index-url https://download.pytorch.org/whl/cu128
python -m pip install -r requirements-neural.txt
```

#### 2. （可选）下载模型权重

经典路径无需任何权重。神经功能的权重**不入库**，用一键脚本按需拉取（默认走 hf-mirror 镜像，支持断点续传）：

```bash
python scripts/setup_models.py              # 全部（约 10GB）
python scripts/setup_models.py --list       # 查看可下载项
python scripts/setup_models.py --only sd15 cn_anime anime2sketch   # 只下需要的
```

#### 3. 启动 Web UI

```bash
python webui.py            # 默认 http://127.0.0.1:8765
python webui.py 9000       # 自定义端口
```

Windows 用户也可双击 `启动程序.cmd`（若 Python 不在 `C:\Python312\python.exe`，需先改脚本里的路径）。

#### 4. 最小验证

仓内自带两张示例图 `demo_input.png`（照片）与 `cad_demo_input.png`（线稿图），零模型即可跑通：

```bash
python lineart_painter.py demo_input.png --out out_demo
# 输出 out_demo/01_lineart.png … 06_highlight.png、final.png、lineart.svg、replay.html
```

回归测试：

```bash
python scripts/smoke_test.py            # 全量（含神经上色与视频，约 3~5 分钟）
python scripts/smoke_test.py --fast     # 快速（约 30 秒，零权重子集）
```

### 系统要求与兼容性

- **OS**：Windows（主支持，发布产物为 PyInstaller 打包）、Linux / macOS（直接跑 Python 脚本）。
- **Python**：3.10+，CI 验证 3.12。
- **经典路径**：纯 CPU 即可，依赖 numpy / opencv-python / imageio-ffmpeg。
- **神经路径**：建议 NVIDIA GPU（CUDA），无 GPU 时自动回退 CPU（神经上色每张约 2~5 分钟）；4GB 显存可勾「低显存模式」。
- **Web 服务**：默认仅监听 `127.0.0.1`，仅供本机访问，不对公网暴露。

### 使用示例

```bash
# 经典处理（零模型）
python lineart_painter.py input.png --out out_demo

# 视频处理
python lineart_painter.py input.mp4 --out out_video

# 神经线稿 + 神经上色（动漫线稿 ControlNet，默认）
python lineart_painter.py input.png --neural-lineart --neural-color

# 切换 ControlNet 变体（先 setup_models.py 下载对应权重）
python lineart_painter.py input.png --neural-color --cn canny

# 手绘线稿直接上色
python lineart_painter.py sketch.png --input-lineart --neural-color

# 视频帧间一致性（AnimateDiff）
python lineart_painter.py input.mp4 --animate-diff

# 黑白老照片上色 / 照片转动漫
python lineart_painter.py old_photo.png --photo-color
python lineart_painter.py photo.png --anime-gan

# 查看全部参数
python lineart_painter.py --help
```

### 常见问题（FAQ）

**Q1：一定要 GPU 才能用吗？**
不需要。默认经典路径在 CPU 上秒级出图，CI 也是在无 GPU runner 上跑通的。只有开神经上色 / 神经线稿 / AnimateDiff 时才需要 GPU；无 GPU 会自动回退 CPU，只是慢。

**Q2：模型权重在哪？怎么这么大？**
权重不入库（体积大且受上游许可证约束）。经典路径完全不需要权重；神经功能用 `python scripts/setup_models.py` 按需下载，可只下你用得到的子集。

**Q3：Web UI 端口被占用怎么办？**
`python webui.py <端口号>`，例如 `python webui.py 9000`。服务只绑 `127.0.0.1`，不要改绑 `0.0.0.0` 对外暴露。

**Q4：为什么我的文件没被处理？**
不支持的扩展名或文件损坏会被自动跳过并打印原因。需要完整堆栈排查时加 `--debug`。

**Q5：AnimateDiff 视频段边界有轻微跳变正常吗？**
正常。AnimateDiff 以 16 帧为一段生成，段边界可能有轻微跳变；相比逐帧神经上色，它已经显著缓解了快速运动镜头的闪烁。

### 目录结构

```text
Lineart_Painter/
├── webui.py                 # 本地 Web 服务和界面（支持批量）
├── lineart_painter.py       # 核心图片 / 视频处理流程
├── neural.py                # 神经模型适配（AnimateDiff / 深度估计等）
├── basicsr/                 # DDColor 所需网络组件
├── _ddcolor_*.py            # DDColor 推理组件
├── scripts/
│   ├── setup_models.py       # 一键下载模型权重
│   ├── smoke_test.py         # 自动化回归测试
│   └── webui_test.py         # Web 请求处理聚焦测试
├── docs/                     # 文档（含第三方许可与发布治理）
├── models/  weights/         # 本地模型缓存，不入库
├── uploads/  webout/         # Web 上传与产物，不入库
└── pipelines/                # 预置管线配置 JSON
```

### 文档与治理

- [CHANGELOG.md](CHANGELOG.md)：Keep a Changelog 格式的版本记录。
- [docs/release-governance.md](docs/release-governance.md)：本仓发布 / 回滚本地化规范（SemVer、远端构建、人工 tag）。
- [docs/agent-contract.md](docs/agent-contract.md)：在本仓工作的 AI agent 契约（与 `ReSerendipity/.github` 模板对齐）。
- [docs/THIRD_PARTY_NOTICES.md](docs/THIRD_PARTY_NOTICES.md)：随仓内嵌第三方源码（ConvNeXt / detr / DDColor / Anime2Sketch）的许可与义务。

### 贡献、安全与支持

- Issue / PR 欢迎；提交前请本地跑一遍 `python scripts/smoke_test.py --fast`。
- 本仓采用 [Apache License 2.0](LICENSE)；随仓内嵌第三方源码各自遵循其上游许可，见 THIRD_PARTY_NOTICES。
- 模型权重在运行时在线拉取，需自行接受各上游模型许可证。

### 许可证

代码以 [Apache License 2.0](LICENSE) 发布。Copyright 2026 ReSerendipity。

---

## English

### Overview

Lineart Painter is a **fully local** image/video repainting workstation. It turns a photo or video into a staged "art-study" pipeline — lineart → flat colors → volume shading → reflected light → color lines → highlights → FINAL — and previews every stage in the browser. The classic path (XDoG + K-Means) runs with **zero model weights** on CPU in seconds; neural stages are opt-in downloads for users who want generative coloring and have a GPU.

Two entry points ship in the box:

- **Local Web UI**: `python webui.py`, served at `http://127.0.0.1:8765`, no frontend build step.
- **CLI**: `python lineart_painter.py input.png --out out`, for scripted batch runs.

### Highlights

- **Image & video input**: batch queue in the web UI; video export as H.264 MP4.
- **Classic XDoG + K-Means**: full seven-stage pipeline with no weights, no GPU.
- **Optional neural coloring**: ControlNet + Stable Diffusion 1.5, six ControlNet variants (anime / manga_line / standard / canny / scribble / depth), multi-variant weighted blending.
- **Optional neural lineart**: Anime2Sketch, or feed a hand-drawn sketch directly (`--input-lineart`).
- **Photo & video modules**: DDColor black-and-white photo colorization, AnimeGANv2 photo-to-anime, AnimateDiff video frame consistency.
- **In-browser stage preview**: per-step PNG, single-step download, one-click ZIP.

### Showcase

**Main UI**: both bundled samples uploaded (`cad_demo_input.png` lineart + `demo_input.png` colored illustration); lineart/color engines, ControlNet variants, seed, and resolution on one card.

![Main UI](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-1-20261026.png)

**Parameters**: XDoG lineart threshold `eps=-0.10`, palette size `k=10`, seed `42`, img2img strength `0`, and the colorization prompt are pre-filled; reference image upload and color-hint canvas sit below.

![Parameters](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-2-20261026.png)

**Lineart output**: running the classic XDoG + K-Means path on bundled `cad_demo_input.png` (2.1 s on CPU, no neural weights). `01 lineart` is the real XDoG output; `02 flat color` … `06 highlights` and `FINAL` render in the same grid.

![Lineart output](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-3-20261026.png)

**Batch results**: one upload of two files (`cad_demo_input.png` + `demo_input.png`) stacks two task cards; the top strip shows the previous task's `FINAL`, below is the `demo_input.png` six-stage grid, with a one-click ZIP download in the corner.

![Batch results](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-4-20261026.png)

**Architecture**: two entries (Web UI / CLI) → input → orchestrated pipeline → classic or neural engine path → staged outputs and exports.

![Architecture](https://gcore.jsdelivr.net/gh/ReSerendipity/Lineart-Painter@main/docs/assets/readme-lineart-painter-5.svg)

### Quick start

#### 1. Environment

Python 3.10+ (CI validates 3.12). The classic path only needs `requirements.txt`:

```bash
python -m venv .venv
# Windows:  .venv\Scripts\activate
# macOS/Linux:  source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

For neural stages, install the layered list on top (PyTorch first, matching your CUDA build):

```bash
python -m pip install torch torchvision --index-url https://download.pytorch.org/whl/cu128
python -m pip install -r requirements-neural.txt
```

#### 2. (Optional) model weights

The classic path needs nothing. Neural weights are **not committed**; pull them on demand:

```bash
python scripts/setup_models.py              # all (~10GB)
python scripts/setup_models.py --list       # list available
python scripts/setup_models.py --only sd15 cn_anime anime2sketch
```

#### 3. Launch the web UI

```bash
python webui.py            # http://127.0.0.1:8765
python webui.py 9000        # custom port
```

On Windows you can also double-click `启动程序.cmd` (edit the Python path if your install is not `C:\Python312\python.exe`).

#### 4. Smoke check

The repo ships `demo_input.png` (photo) and `cad_demo_input.png` (lineart). Zero weights needed:

```bash
python lineart_painter.py demo_input.png --out out_demo
# writes out_demo/01_lineart.png … 06_highlight.png, final.png, lineart.svg, replay.html
```

Regression tests:

```bash
python scripts/smoke_test.py            # full (neural + video, ~3-5 min)
python scripts/smoke_test.py --fast     # fast (~30 s, zero-weight subset)
```

### Requirements & compatibility

- **OS**: Windows (primary; release artifacts are PyInstaller-packed), Linux / macOS (run the Python scripts directly).
- **Python**: 3.10+; CI validates 3.12.
- **Classic path**: CPU only; depends on numpy / opencv-python / imageio-ffmpeg.
- **Neural path**: NVIDIA GPU with CUDA recommended; falls back to CPU (~2-5 s per image). "Low VRAM mode" for 4 GB cards.
- The web server binds `127.0.0.1` only.

### Examples

```bash
# classic (no model)
python lineart_painter.py input.png --out out_demo

# video
python lineart_painter.py input.mp4 --out out_video

# neural lineart + neural color (anime ControlNet, default)
python lineart_painter.py input.png --neural-lineart --neural-color

# switch ControlNet variant (download weights first)
python lineart_painter.py input.png --neural-color --cn canny

# colorize a hand-drawn sketch directly
python lineart_painter.py sketch.png --input-lineart --neural-color

# video frame consistency (AnimateDiff)
python lineart_painter.py input.mp4 --animate-diff

# B/W photo colorization / photo-to-anime
python lineart_painter.py old_photo.png --photo-color
python lineart_painter.py photo.png --anime-gan

python lineart_painter.py --help   # all flags
```

### FAQ

**Q1: Do I need a GPU?**
No. The default classic path finishes on CPU in seconds, and CI runs it on a GPU-less runner. GPU only matters for neural coloring / lineart / AnimateDiff; without one it falls back to CPU, just slower.

**Q2: Where are the model weights? Why so big?**
Weights are not committed (large, and bound by upstream licenses). The classic path needs none. For neural stages, run `python scripts/setup_models.py` to pull only the subset you need.

**Q3: Port 8765 is busy.**
Run `python webui.py <port>`. The server binds `127.0.0.1` only; do not expose it to `0.0.0.0`.

**Q4: Some files are skipped.**
Unsupported extensions or corrupt files are auto-skipped with a reason. Pass `--debug` for the full traceback.

**Q5: Slight flicker at AnimateDiff segment boundaries?**
Expected. AnimateDiff generates 16 frames at a time; vs per-frame neural coloring it already greatly reduces flicker on fast motion.

### Repository layout

```text
Lineart_Painter/
├── webui.py                 # local web service + UI (batch-aware)
├── lineart_painter.py       # core image / video pipeline
├── neural.py                # neural adapters (AnimateDiff, depth, etc.)
├── basicsr/                 # DDColor network components
├── _ddcolor_*.py            # DDColor inference components
├── scripts/
│   ├── setup_models.py       # on-demand weight downloader
│   ├── smoke_test.py         # regression suite
│   └── webui_test.py         # web request handler focused tests
├── docs/                     # docs (third-party notices, release governance)
├── models/  weights/         # local model cache, gitignored
├── uploads/  webout/         # web uploads and outputs, gitignored
└── pipelines/                # preset pipeline config JSON
```

### Docs & governance

- [CHANGELOG.md](CHANGELOG.md): Keep a Changelog history.
- [docs/release-governance.md](docs/release-governance.md): local release / rollback policy (SemVer, remote build, manual tag).
- [docs/agent-contract.md](docs/agent-contract.md): agent contract for work in this repo (aligned with `ReSerendipity/.github`).
- [docs/THIRD_PARTY_NOTICES.md](docs/THIRD_PARTY_NOTICES.md): notices for vendored sources (ConvNeXt / detr / DDColor / Anime2Sketch).

### Contributing, security, support

- Issues and PRs welcome; run `python scripts/smoke_test.py --fast` locally before pushing.
- Code is released under [Apache License 2.0](LICENSE); vendored third-party sources follow their own upstream licenses — see THIRD_PARTY_NOTICES.
- Model weights are pulled at runtime and bound by their upstream licenses.

### License

Code released under [Apache License 2.0](LICENSE). Copyright 2026 ReSerendipity.
