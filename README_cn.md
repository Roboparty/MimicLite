[中文版](README_cn.md) | [English](README.md)

# MimicLite

MimicLite 是 Roboparty 面向运动模仿训练、评测和 sim-to-real 部署的公开集成仓库。
它通过 Git submodule 固定训练工作区、MimicLite 项目代码、报告模板区域和部署栈，使每次 release 都可以复现 checkout。

## 目录结构

```text
MimicLite/
  README.md
  README_cn.md
  LICENSE
  MAINTAINERS
  reports/
    template/
  active-adaptation/      # submodule
  mimic-lite/             # submodule
  sim2real/               # submodule
```

## Submodule

| 路径 | 仓库 | 分支 | Commit | 用途 |
| --- | --- | --- | --- | --- |
| `active-adaptation` | [`Agent-3154/active-adaptation`](https://github.com/Agent-3154/active-adaptation) | `dev/hdmi` | `faea675` | 训练工作区和 launch 脚本。 |
| `mimic-lite` | [`EGalahad/mimic-lite`](https://github.com/EGalahad/mimic-lite) | `main` | `089d0ad` | MimicLite task、asset 和 learning 代码。 |
| `sim2real` | [`EGalahad/sim2real`](https://github.com/EGalahad/sim2real) | `main` | `d1833cf` | 部署和 sim-to-real 集成代码。 |

## 使用方式

克隆集成仓库并初始化固定版本的 submodule：

```bash
git clone --recurse-submodules git@github.com:Roboparty/MimicLite.git MimicLite
cd MimicLite
git submodule update --init --recursive
```

把 submodule 刷新到 `.gitmodules` 中配置分支的最新 HEAD：

```bash
git submodule sync --recursive
git submodule update --init --recursive --remote
```

用 MimicLite 项目文件配置训练环境：

```bash
cd active-adaptation
git submodule update --init projects/mimic-lite
mkdir -p venv/mjlab venv/isaaclab
cp projects/mimic-lite/pyproject-mjlab.toml venv/mjlab/pyproject.toml
cp projects/mimic-lite/pyproject-isaaclab.toml venv/isaaclab/pyproject.toml
uv --project venv/mjlab run aa-discover-projects
```

运行 PPO 训练：

```bash
bash scripts/launch_ddp.sh 0,1,2,3 projects/mimic-lite/scripts/train.py venv/mjlab \
  task=lafan_sonic_100style_real +exp=ppo/train algo/ppo/module=residual backend=mjlab
```

运行 SAC：

```bash
bash scripts/launch_ddp.sh 0,1,2,3 projects/mimic-lite/scripts/train.py venv/mjlab \
  task=lafan_sonic_100style_real +exp=sac/train backend=mjlab
```

如果 MuJoCo 版本中缺少 `mujoco.mjtEnableBit.mjENBL_MULTICCD`，在 `mjlab` 环境中把 MuJoCo pin 到 3.8 以下并重新 sync：

```bash
uv --project venv/mjlab add 'mujoco<3.8'
uv --project venv/mjlab sync
```

## 公开边界

- `reports/template/` 预留给 Roboparty 统一 tech report 模板。
- 禁止提交 secret、机器人日志、非公开数据集、checkpoint、内部 endpoint 或内部部署拓扑。
- 文档里的演示密钥必须是假占位符，并明确标注是假密钥。

## 许可证

本集成仓库采用 GPL-3.0-or-later。各 submodule 保留自身仓库历史与许可证文件；正式发布前必须逐项确认组件许可证兼容性。
