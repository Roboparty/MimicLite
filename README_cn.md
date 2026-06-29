[中文版](README_cn.md) | [English](README.md)

# MimicLite 集成仓库

本仓库是 MimicLite 训练与部署栈的统一公开入口。它不复制训练仓库或部署仓库的源码，而是通过 Git submodule 固定各组件版本，让公开边界清晰、可审计、可回滚。

> 命名说明：当前 GitHub 仓库为 `Roboparty/MimicLite`。正式 public release 前，建议按 Roboparty 命名规范改为小写下划线形式，例如 `roboparty_mimic_lite`。

## 目录结构

```text
MimicLite/
  README.md
  README_cn.md
  LICENSE
  MAINTAINERS
  reports/
    template/
  training/
    active-adaptation/        # submodule
      projects/mimic-lite/    # active-adaptation 内部的 nested submodule
  deploy/
    sim2real/                 # submodule
```

## Submodule

| 路径 | 仓库 | 分支 | 用途 |
| --- | --- | --- | --- |
| `training/active-adaptation` | `git@github.com:Agent-3154/active-adaptation.git` | `dev/hdmi` | 训练侧工作区入口。 |
| `training/active-adaptation/projects/mimic-lite` | `git@github.com:EGalahad/mimic-lite.git` | `main` | MimicLite 项目。 |
| `deploy/sim2real` | `git@github.com:EGalahad/sim2real.git` | `main` | 部署与 sim2real 集成代码。 |

`training/active-adaptation/projects/mimic-lite` 由
`training/active-adaptation` 内部的 `.gitmodules` 声明；Git 会把子仓库拥有的
submodule 记录在该子仓库自己的 `.gitmodules` 中。

## 克隆方式

```bash
git clone --recurse-submodules git@github.com:Roboparty/MimicLite.git MimicLite
cd MimicLite
git submodule update --init --recursive
```

已有 checkout 更新：

```bash
git pull --recurse-submodules
git submodule update --init --recursive
```

## 公开边界

- `mimic-lite` submodule 使用已审查的 `main` 分支。
- `reports/template/` 当前刻意保持为空，仅预留给 Roboparty 统一 tech report 模板。
- 禁止提交 secret、机器人日志、私有数据集、checkpoint、内部 endpoint 或内部部署拓扑。
- 文档里的演示密钥必须是假占位符，并明确标注是假密钥。

## 许可证

本集成仓库采用 GPL-3.0-or-later。各 submodule 保留自身仓库历史与许可证文件；正式发布前必须逐项确认组件许可证兼容性。
