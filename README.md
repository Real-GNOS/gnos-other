# gnos-other

（中文说明在下面；English follows.）

GNOS 主仓库（[Real-GNOS/GNOS](https://github.com/Real-GNOS/GNOS)）的**杂物间**：
主仓库里那些「未被 NOTICE/CONTRIBUTING 记载的第三方代码、随手实验、
会话记录、别的 OS 项目副本」全部搬到了这里，主仓库只保留 GNOS 内核本体
（`src/`、`Makefile`、`tools/`、`limine/`、文档与许可证）。

## 里面有什么

- **别人的 OS 项目副本/归档**：`APINUX-archive`、`BanJiuOS`、`CoreZ-OS`、
  `CoCoChinese`、`hudos`、`cthulhu`、`FastBoot`、`Fire-kernel`、`HanOS`、
  `palook`、`miku-test` 等。
- **第三方/外部工程整棵拷贝**：`emsdk`、`sdlpal`、`simmerv`、`rustix`、
  `rustix-vkernel`、`hac-mcp`、`gl4es_pic`、`typescript`、`microweb`、
  `mylibrary`、`myvideo`、`llvm-project`（占位）、`gperf_*.deb` 等。
- **GitHub Pages / 杂项仓库**：`all.github.io`、`casm.github.io`、
  `guangshan.github.io`、`PhyMBIT.github.io`、`web.github.io` 等。
- **一次性脚本与工具**：`ccw_selfcheck.sh`、`ccwdown.js`、`dbg_analyze.py`、
  `dbg_gnuhash.py`、`gitsub.py`、`runall.sh`、`freeda-save-project.user.js`、
  `cpuid` / `cpuid.asm`。
- **随手文件**：`*.LSP`、`baba.mp3`、`baba.mp4`、`FONT16.BIN`、`PYDICT.BIN`、
  `USERS.DAT`、`gperf_3.1-1_amd64.deb`、`result.txt`、`index.html` 等。
- **AI 会话记录**：`session-ses_*.md`、`atomcode-session-*.md`、`g.md`。
- **7 个子模块**（见 `.gitmodules`）：`32os`、`DE-CPOS`、`FastOS`、`MWOS`、
  `SpiritFoxOS`、`TinyOS-V1`、`build-your-operating-system`。

> **注意**：`FastOS` 的上游 `https://github.com/laolizhennui/FastOS.git`
> 已被原作者删除（404），无法再作为子模块克隆；其条目仅在此备录。

## 许可

本仓库自身的整理工作按主仓库的 `LICENSE`/`NOTICE` 处理；
**其中每个第三方目录各自的版权归其原作者**，各自携带的许可证文件为准。
这里只做归档，不声明任何所有权。

---

## English

This is the **junk drawer** of the GNOS project: everything that used to sit in
the root of [Real-GNOS/GNOS](https://github.com/Real-GNOS/GNOS) but was never
covered by its NOTICE/CONTRIBUTING — third-party source drops, experiments,
session transcripts, copies of other OS projects, pages sites and one-off
scripts — moved here so the main repository contains only the kernel itself.

Contents are listed in the Chinese section above.  Seven upstream repositories
are included as submodules (`.gitmodules`); `laolizhennui/FastOS` no longer
exists upstream (404) and is recorded in the README only.

Each third-party directory keeps its original authors' copyright and its own
license files; this archive claims no ownership over them.
