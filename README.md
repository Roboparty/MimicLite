[中文版](README_cn.md) | [English](README.md)

# MimicLite

MimicLite is an efficient, general humanoid motion-tracking system that can train a deployable policy in 3.1 hours on four RTX 4090 GPUs while retaining competitive tracking quality. Under a matched MuJoCo evaluation, MimicLite improves global root tracking over SONIC while achieving comparable local tracking accuracy. The same policy supports low-latency Pico-driven teleoperation and highly dynamic motion tracking on a physical Unitree G1.

## Project Repositories

This repository is the project landing page. Training, evaluation, dataset conversion, and deployment instructions are maintained in their respective repositories:

| Component | Repository | Contents |
| --- | --- | --- |
| MimicLite | [`EGalahad/mimic-lite`](https://github.com/EGalahad/mimic-lite) | Training, evaluation, policy export, task configs, and learning code. |
| Training framework | [`Agent-3154/active-adaptation`](https://github.com/Agent-3154/active-adaptation) | Simulation backends, distributed launchers, environments, and shared infrastructure. |
| Motion data toolkit | [`EGalahad/any4hdmi`](https://github.com/EGalahad/any4hdmi) | Motion conversion, validation, visualization, and dataset tooling. |
| Deployment runtime | [`EGalahad/sim2real`](https://github.com/EGalahad/sim2real) | ONNX inference, MuJoCo sim2sim, Pico teleoperation, and Unitree G1 deployment. |

## Released Checkpoints

The released checkpoint set contains three PPO policies trained for 4,000 iterations. GPU-hours are computed from the checkpoint-matched run runtime and world size; the table and evaluation figure use the same checkpoints.

| Policy | Actor hidden dimensions | Checkpoint | Training compute | Validation |
| --- | --- | --- | ---: | --- |
| MimicLite-Huge | `[1024, 1024, 1024]` | [`xua2csee`](https://wandb.ai/elijahgalahad/mimic_lite/runs/xua2csee) | 139.71 GPU h | MuJoCo evaluation and physical G1 Pico teleoperation. |
| MimicLite-Base | `[256, 256, 256]` | [`iij0q0b5`](https://wandb.ai/elijahgalahad/mimic_lite/runs/iij0q0b5) | 34.70 GPU h | MuJoCo evaluation. |
| MimicLite-Small | `[128, 128, 128]` | [`zb9e19ih`](https://wandb.ai/elijahgalahad/mimic_lite/runs/zb9e19ih) | 13.79 GPU h | MuJoCo evaluation. |

The 3.1-hour result above is the latest four-GPU system acceptance result. The checkpoint table retains the measured compute of the released, evaluation-matched variants shown below rather than mixing results from different runs.

![MimicLite checkpoint scaling and SONIC comparison](assets/mimiclite_vs_sonic_readme.png)

The comparison reports training GPU-hours, full-motion LAFAN-40 and horizon-normalized PHUMA-30 progress, Root-8 final XY displacement error, and PHUMA-30 local body-position error. Evaluation uses aligned MuJoCo rollouts and termination rules.

## Training Data

Released training datasets are collected in the [`any4hdmi` Hugging Face collection](https://huggingface.co/collections/elijahgalahad/any4hdmi). Source or seed datasets with redistribution or copyright risk are intentionally not mirrored; for those datasets, [`EGalahad/any4hdmi`](https://github.com/EGalahad/any4hdmi) provides conversion scripts and processing tools only.

## Deployment Support

The deployment runtime consumes an exported ONNX model and its policy YAML, so inference is independent of whether the policy was trained with an on-policy or off-policy algorithm. Publicly validated MimicLite deployment support is:

| Item | Supported scope |
| --- | --- |
| Robot | Unitree G1. |
| Simulation | MuJoCo sim2sim. |
| Physical deployment | Unitree G1 through inline Unitree I/O or a ZMQ bridge; x86 host and onboard Orin layouts are supported. |
| MimicLite-Huge PPO | MuJoCo evaluated and verified on a physical G1 with Pico teleoperation. |
| MimicLite-Base / Small PPO | MuJoCo evaluated and compatible with the same export/runtime contract; no separate physical-robot validation is claimed. |

The [`sim2real`](https://github.com/EGalahad/sim2real) runtime also contains adapters for BFM-Zero, HEFT, Humanoid-GPT, SONIC, TeleopIT, and TWIST2. See that repository for artifact downloads, setup, and the validation status of each adapted policy family.

## License

This integration repository is released under GPL-3.0-or-later. Component repositories retain their own histories and license files; verify dataset and component licenses before redistribution.
