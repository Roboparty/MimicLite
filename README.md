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

| Policy | Actor hidden dimensions | Parallel environments | Checkpoint | Training compute |
| --- | --- | ---: | --- | ---: |
| MimicLite-Huge | `[1024, 1024, 1024]` | `32 × 8192` | [`xua2csee`](https://wandb.ai/elijahgalahad/mimic_lite/runs/xua2csee) | 139.71 GPU h |
| MimicLite-Base | `[256, 256, 256]` | `8 × 8192` | [`iij0q0b5`](https://wandb.ai/elijahgalahad/mimic_lite/runs/iij0q0b5) | 34.70 GPU h |
| MimicLite-Small | `[128, 128, 128]` | `4 × 8192` | [`zb9e19ih`](https://wandb.ai/elijahgalahad/mimic_lite/runs/zb9e19ih) | 13.79 GPU h |

The 3.1-hour result above is the latest four-GPU system acceptance result. The checkpoint table retains the measured compute of the released, evaluation-matched variants shown below rather than mixing results from different runs.

![MimicLite checkpoint scaling and SONIC comparison](assets/mimiclite_vs_sonic_readme.png)

Compared with SONIC, MimicLite retains more progress on dynamic LAFAN motions and improves global root tracking while maintaining comparable local tracking accuracy.

## Training Data

Released training datasets are collected in the [`any4hdmi` Hugging Face collection](https://huggingface.co/collections/elijahgalahad/any4hdmi). The [`BONES-SEED` dataset](https://huggingface.co/datasets/bones-studio/seed) is the exception: to respect its license and redistribution terms, users obtain it from the original source, while [`EGalahad/any4hdmi`](https://github.com/EGalahad/any4hdmi) provides only the conversion scripts and processing tools.

## Deployment Support

The [`sim2real`](https://github.com/EGalahad/sim2real) runtime provides a modular observation interface that separates policy-specific input construction from the shared deployment runtime. Integrating a policy requires only an observation class and a YAML specification; the inference, simulator, and robot interfaces remain unchanged. This common path supports integrated MuJoCo evaluation and real-robot execution for MimicLite, HEFT, TeleopIT, Humanoid-GPT, BFM-Zero, SONIC, and TWIST2. Policy inference is decoupled from robot I/O through interchangeable MuJoCo and physical Unitree G1 backends.

## License

This integration repository is released under GPL-3.0-or-later. Component repositories retain their own histories and license files; verify dataset and component licenses before redistribution.
