# NSF REU — Rapid Motor Adaptation for Autonomous Racing

**UNLV Smart Cities · NSF Research Experiences for Undergraduates · Summer 2026**

I adapted a two-stage **Rapid Motor Adaptation (RMA)** framework — originally built for quadcopters — to autonomous ground racing, so a car can infer changing tire grip in real time and adjust its driving without any map, GPS, or explicit friction sensor.

## What I Built

- **Phase 1 (base policy):** PPO policy (16M training steps) that drives from **108 LiDAR ranges + motion history** alone.
- **Phase 2 (adaptation module):** Infers latent tire-grip parameters online from recent motion history, letting one policy handle multiple friction conditions.
- **Deployment:** ROS2 + PyTorch on an NVIDIA Jetson aboard a Traxxas Slash 4×4 / F1Tenth car (VADER). See the deployment repo below.

## Results

- Closed **44% of the calibration gap** to an oracle baseline that knows true friction.
- Reduced cross-grip performance variance by **20–21%** vs. a non-adapting policy.
- Validated across **100 simulated episodes per condition on 14 evaluation circuits**.
- Confirmed on **real hardware across 81 trials and 4 tire-friction conditions**, with statistically significant changes in steering and yaw-rate behavior (p = 0.0001, p = 0.009).

## Repo Map

| Repo | Role |
|---|---|
| This repo | Project overview, results, and documentation |
| [research_ws](https://github.com/hawaii-nc/research_ws) | Training workspace — PPO training, imitation learning, environments, and evaluation |
| [f1tenth-vader-deployment](https://github.com/hawaii-nc/f1tenth-vader-deployment) | On-car ROS2 deployment — launch files, checkpoints, and trial tooling |
| [f1tenth_benchmarks](https://github.com/hawaii-nc/f1tenth_benchmarks) | Benchmark suite — classical, mapless, local-map, and end-to-end RL racing agents |

## Tech

Python · PyTorch (PPO) · ROS2 · NVIDIA Jetson · LiDAR · Sim-to-Real Transfer
