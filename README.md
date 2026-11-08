# Xue Duyang (薛度扬)

Undergraduate at **Beijing University of Chemical Technology (北京化工大学)**, College of Information Science and Technology.  
I work on **quadruped robotics**, **multi-agent reinforcement learning**, and **hierarchical control**, with additional experience in **ROS**, **computer vision**, and **knowledge-graph systems**.

GitHub is the public record of my course and research-oriented engineering. The projects below are the ones I would highlight in a university application.

---

## Research interests

- Multi-agent RL for cooperative quadrupeds (MAPPO / hierarchical policies)
- Sim-to-real pipelines in Isaac Lab and ROS
- Vision-based localization and docking (AprilTag, RGB-D)
- Knowledge graphs and RAG for structured domain data

---

## Featured projects

### [Unitree Go2 Multi-Team Cooperative Carry](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry)

Hierarchical MAPPO on **Isaac Lab + HARL** for cooperative bar carrying.

- **Low level:** two Go2 robots track bar velocity (forward, yaw, along-axis, height) at 50 Hz with 12-DoF joint actions.
- **High level:** one skill agent per team at 1 Hz; two teams carry logs through a gated mid-wall.
- Reproducible overlay (env, configs, train/play scripts) for IsaacLab-HARL.

This is the project that best shows my current research direction: **team-level cooperation on physical quadrupeds in simulation**.

### [Go2-W AprilTag Auto-Dock](https://github.com/ASUKA36/go2w-apriltag-autodock)

Map-free auto-docking for **Unitree Go2-W**.

- Relative pose from AprilTag (`tag36h11`); optional mid-360 lidar for the final approach.
- Staged controller: pre-align → enter charging bay → crouch.
- Same control stack for Gazebo simulation and a D435i real-robot entry point.

This project shows **perception-to-control** engineering on a wheeled-legged platform, not only a learning pipeline.

### [Museum Knowledge Graph Platform](https://github.com/ASUKA36/software-engineering-2303)

Course capstone (Software Engineering): a **museum knowledge management** system.

- Knowledge-graph construction (crawling, cleaning, MySQL / Neo4j)
- Web frontend and Spring Boot backend
- RAG-based question answering
- HarmonyOS mobile client

I contributed the **mobile museum app** (HarmonyOS + Flask). The repository documents a full-stack, multi-module collaboration.

---

## Technical stack

| Area | Tools |
|------|--------|
| Simulation & RL | Isaac Lab, Isaac Sim, HARL, MAPPO, PyTorch |
| Robot software | ROS, Gazebo, Unitree Go2 / Go2-W |
| Perception | AprilTag, Intel RealSense D435i, lidar |
| Software systems | Python, Vue, Spring Boot, FastAPI, Flask, MySQL, Neo4j, HarmonyOS |

---

## How to read this profile

1. Start with **cooperative carry** if you care about multi-agent RL and quadruped control.
2. Then **auto-dock** for robotics systems and hardware-oriented design.
3. Use **software-engineering-2303** as evidence of team software engineering and full-stack delivery.

Repositories are public. Each featured project has its own README with setup and method notes.

---

## 中文简介

**北京化工大学**信息科学与技术学院本科生。主要方向为**四足机器人**、**多智能体强化学习**与**分层控制**，并具备 ROS、视觉对接与知识图谱系统开发经验。

- [多智能体协作搬运](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry)：Isaac Lab + HARL 上的分层 MAPPO，双机抬杆速度跟踪 + 两队过门高层策略。
- [Go2-W 自动入库对接](https://github.com/ASUKA36/go2w-apriltag-autodock)：无全局地图的 AprilTag 对接状态机，仿真与实机入口统一。
- [博物馆知识图谱平台](https://github.com/ASUKA36/software-engineering-2303)：软件工程课程大作业；本人负责鸿蒙移动端。

欢迎通过 GitHub Issues 或仓库主页联系交流。
