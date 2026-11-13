# NSF REU: Rapid Motor Adaptation for Autonomous Racing

**UNLV Smart Cities · NSF Research Experiences for Undergraduates (Award #2349616) · Summer 2026, continued through fall 2026**

Nathan Chong, advised by Prof. Venkata Prashant Modekurthy.

Can a small autonomous race car (F1TENTH) work out how much tire grip it has from its own motion, and drive accordingly, with no map, GPS or friction sensor? I adapted the two-phase **Rapid Motor Adaptation (RMA)** method to this problem.

> **The code, trained models, raw results and paper are in [f1tenth-rma](https://github.com/hawaii-nc/f1tenth-rma).** Start there.

## What the project found (November 2026)

After the summer, the whole pipeline was rebuilt, debugged and retrained, with every hypothesis registered before its runs. The findings changed:

1. **Grip cannot be read from ordinary driving in the F1TENTH simulator.** When the car's actions are kept independent of grip, 0.5 s of motion history predicts grip with R² ≈ 0. Adaptation modules that seem to work are reading their own actions or just driving slowly; a non-adaptive car with a speed limit matches them.
2. **A short active probe fixes this.** A 0.1 s speed pulse on straights asks the tires for more than low grip can give, and grip becomes readable (R² 0.89–0.93).
3. **With the probe, adaptation beats any fixed speed limit.** At grip 0.4 the adaptive car crashes in 2–8% of episodes against 33–47% for a non-adaptive car matched to its speed (3 training seeds, 100 paired episodes each), and it transfers to unseen tracks.
4. **It still works with the car's real sensing** (wheel odometry with slip, no gyro, 40 Hz LiDAR) at racing speed, though less well at the lowest grip.
5. **All probe results are simulation only.** The real-car test is planned and has not been run.

## About the summer (July 2026) numbers

The summer poster reported 44% of the gap to an oracle closed and a 20–21% drop in cross-grip variance. **Those numbers should not be cited.** They came from a pipeline in which the physics encoder was never trained, among other bugs, and they did not survive the rebuild (see `docs/BUGS_AND_LESSONS.md` in f1tenth-rma).

The summer's real-car pilot stands as a pilot: 81 trials through one 90° corner on four tire wraps, with an earlier checkpoint and no probe. Steering differed between bare and electrical-tape tires (p = 0.0001, which survives correction for the nine comparisons made); the yaw-rate difference (p = 0.009) does not survive that correction. It shows the policy responds to a real tire change, not that grip adaptation works on the car.

## Repositories

| Repo | Role |
|---|---|
| [f1tenth-rma](https://github.com/hawaii-nc/f1tenth-rma) | **Current.** Training, evaluation, deployment node, trained models, results, pre-registration, paper |
| This repo | Project overview |
| [f1tenth-vader-deployment](https://github.com/hawaii-nc/f1tenth-vader-deployment) | July 2026 on-car backup (superseded by f1tenth-rma) |
| [research_ws](https://github.com/hawaii-nc/research_ws) | July 2026 training workspace (superseded by f1tenth-rma) |
| [f1tenth_benchmarks](https://github.com/hawaii-nc/f1tenth_benchmarks) | Fork of [BDEvan5/f1tenth_benchmarks](https://github.com/BDEvan5/f1tenth_benchmarks); source of four of the track maps |

## Method in brief

- **Phase 1:** a policy that drives from 5 motion signals and 108 LiDAR ranges, trained by imitation (DAgger) of a grip-aware Pure Pursuit expert, with the true physics compressed to an 8-D vector.
- **Phase 2:** an adaptation module (1-D CNN) that predicts that vector from the last 0.5 s of observations and actions.
- **Active probe:** a brief speed or braking pulse on straights, applied in training and at test time.
- **Hardware:** Traxxas Slash 4×4 F1TENTH car ("VADER") with an NVIDIA Jetson, ROS 2 and PyTorch.

Python · PyTorch · ROS 2 · f1tenth_gym · sim-to-real
