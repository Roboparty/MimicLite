[中文版](README_cn.md) | [English](README.md)

# MimicLite

MimicLite provides a lightweight motion-imitation pipeline for Roboparty humanoid robots, covering motion loading, tracking tasks, PPO/SAC training, evaluation, and deployment handoff.
The project focuses on turning curated motion data into deployable tracking policies and keeping the training-to-sim-to-real workflow in one place.

## Structure

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

## Submodules

| Path | Repository | Branch | Commit | Purpose |
| --- | --- | --- | --- | --- |
| `active-adaptation` | [`Agent-3154/active-adaptation`](https://github.com/Agent-3154/active-adaptation) | `dev/hdmi` | `faea675` | Training workspace and launch scripts. |
| `mimic-lite` | [`EGalahad/mimic-lite`](https://github.com/EGalahad/mimic-lite) | `main` | `089d0ad` | MimicLite task, asset, and learning code. |
| `sim2real` | [`EGalahad/sim2real`](https://github.com/EGalahad/sim2real) | `main` | `d1833cf` | Deployment and sim-to-real integration code. |

## Instructions

Clone the integration repo and initialize the pinned submodules:

```bash
git clone --recurse-submodules git@github.com:Roboparty/MimicLite.git MimicLite
cd MimicLite
git submodule update --init --recursive
```

To refresh submodules to the latest configured branch heads:

```bash
git submodule sync --recursive
git submodule update --init --recursive --remote
```

Set up the training environments from the MimicLite project files:

```bash
cd active-adaptation
git submodule update --init projects/mimic-lite
mkdir -p venv/mjlab venv/isaaclab
cp projects/mimic-lite/pyproject-mjlab.toml venv/mjlab/pyproject.toml
cp projects/mimic-lite/pyproject-isaaclab.toml venv/isaaclab/pyproject.toml
uv --project venv/mjlab run aa-discover-projects
```

Run a PPO training job:

```bash
bash scripts/launch_ddp.sh 0,1,2,3 projects/mimic-lite/scripts/train.py venv/mjlab \
  task=lafan_sonic_100style_real +exp=ppo/train algo/ppo/module=residual backend=mjlab
```

Run SAC:

```bash
bash scripts/launch_ddp.sh 0,1,2,3 projects/mimic-lite/scripts/train.py venv/mjlab \
  task=lafan_sonic_100style_real +exp=sac/train backend=mjlab
```

If MuJoCo resolves a version where `mujoco.mjtEnableBit.mjENBL_MULTICCD` is missing, pin MuJoCo below 3.8 in the `mjlab` environment and resync:

```bash
uv --project venv/mjlab add 'mujoco<3.8'
uv --project venv/mjlab sync
```

## Release Boundary

- `reports/template/` is reserved for the common Roboparty technical report template.
- Do not commit secrets, robot logs, non-public datasets, checkpoints, internal
  endpoints, or internal deployment topology.
- Demo credentials in documentation must be fake placeholders and must be marked
  as fake.

## License

This integration repository is released under GPL-3.0-or-later. Component
submodules keep their own repository histories and license files; verify
component license compatibility before publishing a release.
