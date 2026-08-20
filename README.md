[中文版](README_cn.md) | [English](README.md)

# MimicLite

MimicLite is an efficient, general humanoid motion-tracking system that can train a deployable policy in 3 hours on 8 RTX 4090 GPUs while retaining competitive tracking quality. Under a matched MuJoCo evaluation, MimicLite improves global root tracking over SONIC while achieving comparable local tracking accuracy. The same policy supports low-latency Pico-driven teleoperation and highly dynamic motion tracking on a physical Unitree G1.

The technical report is available at [`mimic-lite.pdf`](mimic-lite.pdf).

## Project Repositories

This repository is the project landing page. Training, evaluation, dataset conversion, and deployment instructions are maintained in their respective repositories:

| Component | Repository | Contents |
| --- | --- | --- |
| MimicLite | [`EGalahad/mimic-lite`](https://github.com/EGalahad/mimic-lite) | Training, evaluation, policy export, task configs, and learning code. |
| Training framework | [`Agent-3154/active-adaptation`](https://github.com/Agent-3154/active-adaptation) | Simulation backends, distributed launchers, environments, and shared infrastructure. |
| Motion data toolkit | [`EGalahad/any4hdmi`](https://github.com/EGalahad/any4hdmi) | Motion conversion, validation, visualization, and dataset tooling. |
| Deployment runtime | [`EGalahad/sim2real`](https://github.com/EGalahad/sim2real) | ONNX inference, MuJoCo sim2sim, Pico teleoperation, and Unitree G1 deployment. |

## Released Checkpoints

The released checkpoint set contains MimicLite v1.1 and the original Huge and
Base PPO releases. The wall-clock column reports training time on RTX 4090 GPUs.

| Policy | Actor hidden dimensions | Parallel environments | Checkpoint | Wall-clock time |
| --- | --- | ---: | --- | ---: |
| MimicLite-v1.1 | `[1024, 1024, 1024]` | `16 × 16000` (`15000` finetune) | [`ece66d1a`](https://wandb.ai/elijahgalahad/mimic_lite/runs/ece66d1a) | 15 h 29 min |
| MimicLite-Huge | `[1024, 1024, 1024]` | `32 × 8192` | [`xua2csee`](https://wandb.ai/elijahgalahad/mimic_lite/runs/xua2csee) | 3 h 30 min |
| MimicLite-Base | `[256, 256, 256]` | `8 × 8192` | [`iij0q0b5`](https://wandb.ai/elijahgalahad/mimic_lite/runs/iij0q0b5) | 2 h 57 min |

Original training-time sources: Huge [`55ie49o5`](https://wandb.ai/elijahgalahad/mimic_lite/runs/55ie49o5) and Base [`07k900hl`](https://wandb.ai/elijahgalahad/mimic_lite/runs/07k900hl).

Download the MimicLite v1.1 deploy ONNX and YAML from the
[shared sim2real artifacts](https://drive.google.com/drive/folders/1JIIXn_gTPZC94Bg5RneID4rRaFGhX8fo).

![Canonical cross-codebase tracking evaluation](assets/mimic_lite_cross_codebase_tracking_eval.png)

For a fair comparison, we report the motion-lookahead latency required by each
policy, defined by its furthest required future-reference frame. All values use
the shared 50 Hz reference-motion contract.

| Policy | MimicLite-Huge | MimicLite-v1.1 | MimicLite-Base | BFM-Zero | SONIC | SONIC-v1.1 | SONIC low-latency | HoloMotion | TeleopIT | Humanoid-GPT | HEFT | TWIST2 |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Motion-lookahead latency | 0.08 s | 0.08 s | 0.08 s | 0.12 s | 0.90 s | 0.90 s | 0.18 s | 0.20 s | 0.00 s | 0.02 s | 0.12 s | 0.00 s |

## Training Data

Released training datasets are collected in the [`any4hdmi` Hugging Face collection](https://huggingface.co/collections/elijahgalahad/any4hdmi). The [`BONES-SEED` dataset](https://huggingface.co/datasets/bones-studio/seed) is the exception: to respect its license and redistribution terms, users obtain it from the original source, while [`EGalahad/any4hdmi`](https://github.com/EGalahad/any4hdmi) provides only the conversion scripts and processing tools.

## Deployment Support

The [`sim2real`](https://github.com/EGalahad/sim2real) runtime provides a modular observation interface that separates policy-specific input construction from the shared deployment runtime. Integrating a policy requires only an observation class and a YAML specification; the inference, simulator, and robot interfaces remain unchanged. This common path supports integrated MuJoCo evaluation and real-robot execution for MimicLite, HEFT, TeleopIT, Humanoid-GPT, BFM-Zero, SONIC, and TWIST2. Policy inference is decoupled from robot I/O through interchangeable MuJoCo and physical Unitree G1 backends.

## License

This integration repository is released under GPL-3.0-or-later. Component repositories retain their own histories and license files; verify dataset and component licenses before redistribution.
