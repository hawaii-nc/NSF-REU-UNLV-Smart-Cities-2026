# Rapid Motor Adaptation for Autonomous Racing

NSF REU Site in Smart Cities, University of Nevada, Las Vegas (Award #2349616). Summer 2026, continued through fall 2026.

Nathan Chong, advised by Prof. Venkata Prashant Modekurthy.

Can a small autonomous race car work out how much tire grip it has from its own motion, and adjust its driving, without a map, GPS or friction sensor? I adapted Rapid Motor Adaptation (RMA), a two-phase method for inferring hidden physical parameters, to this problem on the F1TENTH platform.

The code, trained models, raw results and paper draft are in [f1tenth-rma](https://github.com/hawaii-nc/f1tenth-rma). This repository is a short overview.

## Findings (November 2026)

After the summer I rebuilt, debugged and retrained the whole pipeline, registering each hypothesis before running it. The results changed substantially.

In the F1TENTH simulator, ordinary driving does not reveal grip. When the car's actions are kept independent of grip, half a second of motion history predicts grip with R² ≈ 0, for three different tire models. Adaptation modules that appeared to work were reading the controller's own actions or simply driving slowly, and a non-adaptive car with a speed limit matched them.

A short active probe solves this. A 0.1 s speed pulse on straights asks the tires for more than low grip can deliver, and grip becomes readable (R² 0.89–0.93). With the probe, the adaptive controller beats any fixed speed limit: at grip 0.4 it crashes in 2–8% of episodes against 33–47% for a non-adaptive car driving at the same speed, across three training seeds, and it transfers to tracks it was not trained on. It also works with the car's actual sensors (wheel odometry, no gyroscope, 40 Hz LiDAR), though less well at the lowest grip.

All of these results are from simulation. The test of the probe on the real car is planned but has not been run.

## About the July 2026 results

The summer poster reported that the adaptive controller closed 44% of the gap to an oracle and reduced cross-grip variance by 20–21%. Those numbers should not be cited: they came from a pipeline in which the physics encoder was never trained, among other bugs, and they did not hold up after the rebuild (`docs/BUGS_AND_LESSONS.md` in f1tenth-rma).

The real-car pilot from the summer still stands as a pilot. Over 81 trials through one 90° corner with four tire wraps, using an earlier checkpoint without the probe, steering differed between bare and electrical-tape tires (p = 0.0001, which survives correction for the nine comparisons made); the yaw-rate difference (p = 0.009) does not survive that correction. This shows the policy responds to a real change in tires, not that grip adaptation works on the car.

## Repositories

| Repository | Role |
|---|---|
| [f1tenth-rma](https://github.com/hawaii-nc/f1tenth-rma) | Current: training, evaluation, deployment node, trained models, results, pre-registration and paper |
| This repository | Project overview |
| [f1tenth-vader-deployment](https://github.com/hawaii-nc/f1tenth-vader-deployment) | July 2026 on-car backup, superseded by f1tenth-rma |
| [research_ws](https://github.com/hawaii-nc/research_ws) | July 2026 training workspace, superseded by f1tenth-rma |
| [f1tenth_benchmarks](https://github.com/hawaii-nc/f1tenth_benchmarks) | Fork of [BDEvan5/f1tenth_benchmarks](https://github.com/BDEvan5/f1tenth_benchmarks), the source of four of the track maps |

## Method

- Phase 1: a driving policy that uses 5 motion signals and 108 LiDAR ranges, trained by imitation (DAgger) of a grip-aware Pure Pursuit expert, with the true physics compressed into an 8-dimensional vector.
- Phase 2: an adaptation module (a 1-D CNN) that predicts that vector from the last 0.5 s of observations and actions.
- Active probe: a brief speed or braking pulse on straights, applied in training and at test time.
- Hardware: a Traxxas Slash 4×4 F1TENTH car (VADER) with an NVIDIA Jetson, running ROS 2 and PyTorch.

Python, PyTorch, ROS 2, f1tenth_gym.
