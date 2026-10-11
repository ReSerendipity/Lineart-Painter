# 第三方源码与许可证声明（Third Party Notices）

本文件登记**随本仓库分发（in-tree）的第三方代码**：它们的来源、许可证、版权主体，以及本仓为履行义务所做的标注。

- 本仓自研代码：Apache-2.0，全文见根目录 [`LICENSE`](LICENSE)（11,314 B）。
- 运行时在线拉取的模型权重与 pip 依赖**不在 git 树内**，不构成本仓的再分发物，口径见文末「范围说明」。
- 文中「逐字节相同」= 两侧文件的 git blob SHA 相等（SHA 按内容寻址，相等即字节相等）；「行尾归一后相同」= 把 CRLF/CR 统一为 LF 后字节相等。

登记对象：`Lineart-Painter @ 479485f94abc45e8b0fa4c74a6da4305011fa7b7`

---

## 一、逐条登记

### 1. `basicsr/archs/ddcolor_arch_utils/convnext.py`（8,513 B）— **MIT（Meta 来源，经 DDColor 改编）**

该文件前 4 行逐字：

```
# Copyright (c) Meta Platforms, Inc. and affiliates.
# All rights reserved.
# This source code is licensed under the license found in the
# LICENSE file in the root directory of this source tree.
```

| 项 | 内容 |
|---|---|
| 版权主体 | Meta Platforms, Inc. and affiliates. |
| 来源 | `facebookresearch/ConvNeXt`（`main`）的 ConvNeXt 实现 |
| 载体 | 与 `piddnad/DDColor` 同路径同名文件**逐字节相同**（blob SHA 相等） |
| 许可证 | **MIT**，全文见 §三·A |

**两点如实说明，别把这条读成"原样拷贝"：**

1. 本仓这份与 ConvNeXt 仓内**三份** `convnext.py` **均不逐字节相同**（`models/convnext.py` 9,321 B、
   `object_detection/mmdet/models/backbones/convnext.py` 7,434 B、`semantic_segmentation/backbone/convnext.py` 7,447 B，
   本仓这份 8,513 B）⇒ 它是 **DDColor 侧的改编版**，Meta 的版权头随改编保留。
2. 头部那句 "the LICENSE file in the root directory of this source tree" 是 Meta 的标准写法，**不含 "MIT" 字样**：
   在 ConvNeXt 仓内它指向 MIT（该仓 `LICENSE` 1,088 B，首行 `MIT License`），
   而这份副本所在的 DDColor 仓根 `LICENSE` 是 Apache-2.0。本文件按**代码原始来源**登记为 MIT。
   两者皆为宽松许可，与本仓 Apache-2.0 兼容，义务（保留版权头、随附许可文本）在两种解读下都已履行。

义务：该文件头部的 4 行版权声明**不得删除**。

### 2. `basicsr/archs/ddcolor_arch_utils/position_encoding.py`（2,023 B）— **Apache-2.0，相对 detr 已修改**

该文件前 2 行逐字：

```
# Copyright (c) Facebook, Inc. and its affiliates.
# Modified from: https://github.com/facebookresearch/detr/blob/master/models/position_encoding.py
```

| 项 | 内容 |
|---|---|
| 上游 | `facebookresearch/detr` 的 `models/position_encoding.py`（3,336 B / 89 行） |
| 许可证 | Apache-2.0（detr 仓 `LICENSE` 11,354 B） |
| 载体关系 | 本仓这份与 **DDColor 那份逐字节相同** ⇒ 下述修改由 DDColor 上游作出，本仓原样转发 |

按 Apache-2.0 §4(b) 标注**实质修改**（本仓这份 51 行 vs detr 上游 89 行）：

1. 删除 `from util.misc import NestedTensor` 依赖；
2. 接口签名由 `forward(self, tensor_list: NestedTensor)` 改为 `forward(self, x, mask=None)`；
3. 折叠 `torch.stack` 的多行排版（无语义变更）；
4. 整体删除 `PositionEmbeddingLearned` 类与 `build_position_encoding` 函数。

义务：上述 "Modified from" 行**不得删除**。

### 3. `basicsr/archs/ddcolor_arch_utils/` 其余三份 — **Apache-2.0（经 DDColor）**

上游：`piddnad/DDColor` @ `master`（tip `2adb63f2656ac41cbdf7b894cddd94121a3faf13`），路径与本仓**完全同名同路径**。

| 本仓 = 上游路径 | 字节数 | 关系 |
|---|---|---|
| `basicsr/archs/ddcolor_arch_utils/unet.py` | 7,489 | 逐字节相同 |
| `basicsr/archs/ddcolor_arch_utils/transformer_utils.py` | 7,200 | 逐字节相同 |
| `basicsr/archs/ddcolor_arch_utils/__init__.py` | 0 | 逐字节相同（空文件） |

- 版权主体：`piddnad/DDColor`（仓级 `LICENSE` 11,356 B，Apache-2.0）。这三份文件本身不含版权头，与上游一致。
- **Apache-2.0 §4(d) 不触发**：DDColor 仓库根目录只有 `LICENSE` 一个许可相关文件，**没有** `NOTICE`、`NOTICE.txt`、`THIRD-PARTY-NOTICES`、`COPYING` ⇒ 本仓无 NOTICE 传递义务。
- 补充：本仓另有 `basicsr/__init__.py`、`basicsr/archs/__init__.py` 两个 **0 字节占位文件**（上游 DDColor 对应文件有内容，本仓将其置空以避开 BasicSR 的 `import *` 副作用）。空文件无著作权内容，不作为登记对象。

### 4. `_ddcolor_net.py`（9,316 B）、`_ddcolor_pipeline.py`（4,384 B）— **Apache-2.0（经 DDColor，仅改名）**

| 本仓路径（仓根） | 上游原路径（`piddnad/DDColor` @ `master`） | 关系 |
|---|---|---|
| `_ddcolor_net.py` | `ddcolor/model.py`（9,316 B） | 逐字节相同 |
| `_ddcolor_pipeline.py` | `ddcolor/pipeline.py`（4,384 B） | 逐字节相同 |

- 许可证与版权主体同第 3 条（DDColor 仓级 Apache-2.0）。
- **改名不改变许可义务**，故按「原路径 → 本仓路径」登记。这两份文件不带版权头，其归属由本文件指认。
- 义务：Apache-2.0 §4(a) 要求随分发提供许可证副本 ⇒ 本仓根目录 `LICENSE` 即 Apache-2.0 全文，与本文件同分发。

### 5. `_a2s_model.py`、`_a2s_data.py`、`_a2s_test.py` — **MIT（Anime2Sketch）**

上游：`Mukosame/Anime2Sketch` @ `master`（tip `50a6a128c5f66d3a25cf2a7aa67d1cb9669affd8`）。本仓加了 `_a2s_` 前缀，并把行尾由 CRLF 转为 LF。

| 本仓路径（仓根） | 本仓字节数（CRLF 数） | 上游原路径 | 上游字节数（CRLF 数） | 关系 |
|---|---|---|---|---|
| `_a2s_model.py` | 7,482（0） | `model.py` | 7,658（176） | 行尾归一后逐字节相同 |
| `_a2s_data.py` | 3,592（0） | `data.py` | 3,689（97） | 行尾归一后逐字节相同 |
| `_a2s_test.py` | 2,352（0） | `test.py` | 2,407（55） | 行尾归一后逐字节相同 |

- 许可证：**MIT**，版权主体 `Copyright (c) 2021 Xiaoyu Xiang`，全文见 §三·B。
- ⚠️ 这三份文件**当前不含任何版权头**，即 MIT 要求的署名在文件内处于**未履行**状态 ⇒ 由本文件补齐归属声明，**后续整理时不得删除本节**。
- 名称澄清：`setup_models.py` 里的 `gyrojeff/anime2sketch` 是 **Hugging Face 模型仓库 id**（经 `hf_hub_download` 在线拉取），不是 GitHub 仓库名，与 GitHub 侧的 `Mukosame/Anime2Sketch` 不是同一对象；请勿据"GitHub 上查无此仓"去改动该处代码。

---

## 二、汇总

| # | 载体 | 上游 | 许可证 | 完整性 |
|---|---|---|---|---|
| 1 | `basicsr/archs/ddcolor_arch_utils/convnext.py` | facebookresearch/ConvNeXt（经 DDColor 改编） | MIT | 与 DDColor 那份逐字节相同 |
| 2 | `basicsr/archs/ddcolor_arch_utils/position_encoding.py` | facebookresearch/detr | Apache-2.0 | 与 DDColor 那份逐字节相同；相对 detr **已修改** |
| 3 | 同目录 `unet.py` / `transformer_utils.py` / `__init__.py` | piddnad/DDColor | Apache-2.0 | 逐字节相同 |
| 4 | `_ddcolor_net.py` / `_ddcolor_pipeline.py` | piddnad/DDColor | Apache-2.0 | 逐字节相同（仅改名） |
| 5 | `_a2s_model.py` / `_a2s_data.py` / `_a2s_test.py` | Mukosame/Anime2Sketch | MIT | 行尾归一后逐字节相同 |

许可证种类合计：**MIT ×2**（Meta Platforms、Xiaoyu Xiang）、**Apache-2.0 ×3**（detr、DDColor 两份）。
全部为宽松许可，与本仓 Apache-2.0 及商用分发兼容，**无 copyleft（GPL/AGPL）冲突**。

---

## 三、许可证全文

### A. MIT — ConvNeXt（`facebookresearch/ConvNeXt` 的 `LICENSE`，1,088 B，逐字）

```
MIT License

Copyright (c) Meta Platforms, Inc. and affiliates.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### B. MIT — Anime2Sketch（`Mukosame/Anime2Sketch` 的 `LICENSE`，1,069 B，逐字）

```
MIT License

Copyright (c) 2021 Xiaoyu Xiang

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### C. Apache-2.0

第 2、3、4 条适用同一份 Apache-2.0 文本。本仓根目录 [`LICENSE`](LICENSE)（11,314 B）即 Apache-2.0 全文，与本文件一同分发，满足 §4(a)。上游 `facebookresearch/detr`（`LICENSE` 11,354 B）与 `piddnad/DDColor`（`LICENSE` 11,356 B）各自携带同一份 Apache-2.0 文本。

---

## 四、范围说明与未定项

**本文件只覆盖 git 树内、随仓库分发的文件。** 以下两类不在范围内：

1. **运行时下载的权重**：`setup_models.py` 列出的 Stable-Diffusion-1.5、ControlNet、Anime2Sketch、MiDaS、DDColor、AnimeGANv2 权重经镜像站在线拉取，不入库、不再分发 ⇒ 不进本表。使用者自行拉取即自行接受各自的模型条款（其中多个为**非商用研究用途**），本仓不为其许可背书。
2. **pip 依赖**：按各自包内声明的许可证分发，由 `requirements.txt` 与包元数据决定，不在本文件登记。

**未定项（如实列出，不做推测）**：

- `demo_input.png`（1,472,564 B）、`cad_demo_input.png`（1,132,306 B）：仓内**无出处记录**，来源与授权状态未知。若对外商用分发，需补充出处或替换为自制素材。
- 图标（`assets/icons/*`、`lineart-icon.ico`、`docs/icons/lineart-painter-icon.svg`）：按 `docs/icons/README.md` 自述为自制，设计源 SVG 在仓内。
