# Xue Duyang (薛度扬)

Undergraduate in Computer Science and Technology, College of Information Science and Technology, Beijing University of Chemical Technology.

I work on legged robots: hierarchical multi-agent reinforcement learning for cooperative carrying, and vision-plus-lidar docking of a wheeled-leg platform into a narrow bay. I led a course project on a museum knowledge system, defined its data and interfaces, and independently built the Pocket Museum app.

---

## 1. Multi-team Go2 cooperative carry

[unitree-go2-multi-team-cooperative-carry](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry)

Two teams start on the south side of an arena. Each team is two Unitree Go2 robots lifting one half-round log, and the task is to pass through a door in the mid-wall. The low-level body controller is trained first and then frozen. The high level learns the team skill for crossing the door.

The stack is Isaac Sim / Isaac Lab 4.5 and HARL, with MAPPO. The repository overlays task environments, configs, training, ablation, and playback scripts onto [IsaacLab-HARL](https://github.com/DIRECTLab/IsaacLab-HARL).

| Layer | Design |
|-------|--------|
| Low-level body control | Two Go2 robots, shared MAPPO actor. 60-D observation (proprioception 13 + joint-related 36 + task 11) and a 12-D joint-position increment. At 50 Hz the policy tracks bar commands: normal velocity, yaw rate, along-axis velocity, and height. Episodes last 30 seconds. The log mesh is generated in code. |
| High-level team skill | One `cooperation_skill` agent per team, shared parameters by default. At 1 Hz it outputs a 3-D velocity skill, which is decoded into the low-level bar command. Observations include a 12-ray 2D lidar, features of the other team, and the geodesic distance and gradient to the door. Episodes last 45 seconds. |
| Scene | 15 m arena, east-west mid-wall, 2 m door. The distance field is a geodesic lookup table on an occupancy grid. Door-entry demonstration trajectories provide the imitation warmup. |
| Training | Imitation warmup with decay, and both shared and unshared actors. Ablations cover hierarchical hold, denser sampling near the door, lateral shaping, and demonstration warmup. Imitation and hold logic have tests that run without the Isaac GUI. |

Code: [low-level task](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/low_level_speed/README.md) · [`go2_multi_agent_speed.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/low_level_speed/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_multi_agent_speed.py) · [high-level task](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/README.md) · [`go2_cooperation_skill.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_cooperation_skill.py) · [`go2_center_yard_obstacle_field.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_center_yard_obstacle_field.py) · [`go2_pretrained_body_control.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_pretrained_body_control.py)

## 2. Map-free Go2-W auto-dock

[go2w-apriltag-autodock](https://github.com/ASUKA36/go2w-apriltag-autodock)

Unitree Go2-W docks into a narrow charging bay from the relative pose of an AprilTag (`tag36h11`). Mid-360 lidar estimates the bay corridor on the final approach. Control has three stages: pre-align outside the bay, enter, and crouch. Velocity commands use longitudinal, lateral, and yaw components together, matching the platform's holonomic motion. The controller starts from a pose where the tag is already visible and stops when the tag is lost.

The `go2w_parking` package serves both Gazebo and the real robot through separate launch files. Simulation includes a 2D scene, a 3D scene with the official meshes, and planar-motion and dynamics plugins written for this repository. The hardware launch starts the D435i color stream, tag detection for a chosen ID, and the same controller. Code in this repository is Apache-2.0. The Go2-W meshes come from Unitree and are redistributed under BSD-3-Clause.

Code: [`auto_dock.py`](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/scripts/auto_dock.py) · [`auto_dock.yaml`](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/config/auto_dock.yaml) · [dynamics plugin](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_sim/src/go2w_dynamics_plugin.cpp) · [planar plugin](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_sim/src/go2w_planar_plugin.cpp) · [hardware launch](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/launch/auto_dock_real.launch)

## 3. Museum knowledge system and Pocket Museum

[software-engineering-2303](https://github.com/ASUKA36/software-engineering-2303)

This is a software-engineering course project on Chinese artifacts held in overseas museums. I was the project lead. I designed the database attributes, built the knowledge graph, defined the structural protocols between subsystems, and integrated the whole system. I also wrote the requirements analysis, test documents, user documents, and weekly development meeting notes.

I independently developed the Pocket Museum subsystem:

- Image search with ResNet50, the classification head removed, and cosine-similarity retrieval. The index is `vectors.npy` and `meta.json`
- Offline index construction and search-API integration
- Endpoint configuration for the business API, the image API, and the question-answering API, together with health checks and test scripts
- Integration of artifact browsing, search, login, interaction, and upload

The client is HarmonyOS (ArkTS). The mobile service is Flask. List filtering uses Neo4j, and artifact details use MySQL.

Code: [`image_search.py`](https://github.com/ASUKA36/software-engineering-2303/blob/main/mobile_museum_app/server/image_search.py) · [`build_image_index.py`](https://github.com/ASUKA36/software-engineering-2303/blob/main/mobile_museum_app/server/build_image_index.py)

---

## Technical background

| Area | Where it appears |
|------|------------------|
| Hierarchical multi-agent RL, with a frozen low level and a team-level high level | Speed and CenterYard tasks in cooperative carry |
| Procedural scenes, rigid meshes, rewards, ablations, and playback | `go2_multi_agent_center_yard.py`, the ablation script, `train.py` / `play.py` |
| Occupancy grid and geodesic distance in the observation | `go2_center_yard_obstacle_field.py` and the lookup table |
| ROS, Gazebo, URDF, and custom model plugins | `go2w_sim` |
| AprilTag pose, lidar corridor, and staged velocity control | `auto_dock.py` |
| Shared simulation and hardware entry points | `auto_dock.launch`, `auto_dock_real.launch` |
| Course-system lead: database attributes, knowledge graph, interface protocols, integration, and project documents | software-engineering-2303 |
| Pocket Museum image retrieval, service endpoints, and client integration | `image_search.py`, `build_image_index.py`, `Constants.ets`, `run_project_tests.py` |

The repositories use Python, PyTorch, Isaac Lab, HARL, ROS 1, Gazebo, AprilTag, RealSense D435i, Flask, Neo4j, MySQL, and HarmonyOS ArkTS.

---

# 中文

北京化工大学 · 信息科学与技术学院 · 计算机科学与技术本科

我的工作主要在足式机器人：用分层多智能体强化学习做多机协作搬运，用视觉和激光把轮足平台对接进狭窄库位。课程项目中，我担任海外文物知识系统的总负责人，并独立开发了掌上博物馆。

## 1. 多队 Go2 协作抬杆过门

[unitree-go2-multi-team-cooperative-carry](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry)

两支队伍从场地南侧出发，每队两台 Unitree Go2 抬一根半圆杆，穿过中墙上门洞。低层身体控制先训练并冻结，高层学习队伍级的过门技能。

仿真栈是 Isaac Sim / Isaac Lab 4.5 与 HARL，算法是 MAPPO。仓库在 [IsaacLab-HARL](https://github.com/DIRECTLab/IsaacLab-HARL) 上提供任务环境、配置、训练、消融与回放脚本。

| 层级 | 设计 |
|------|------|
| 低层身体控制 | 两台 Go2、共享 MAPPO actor。观测 60 维（本体 13 + 关节相关 36 + 任务 11），动作是 12 维关节位置增量。50 Hz 跟踪杆指令：法向速度、偏航角速度、轴向速度、高度。回合 30 秒。杆的网格在代码里生成。 |
| 高层队伍技能 | 每队一个 `cooperation_skill`，默认共享参数，1 Hz 输出三维速度技能，再解码成低层杆指令。观测含 12 线二维激光、另一队特征，以及到门的测地距离和梯度。回合 45 秒。 |
| 场景 | 15 m 场地、东西向中墙、2 m 门。距离场来自占据栅格上的测地查找表，过门演示轨迹用于模仿热身。 |
| 训练 | 模仿热身及衰减，共享与非共享 actor。消融包括分层保持、门区加密采样、横向整形和演示热身。模仿与保持逻辑有不启动 Isaac 界面的测试。 |

代码： [低层任务说明](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/low_level_speed/README.md) · [`go2_multi_agent_speed.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/low_level_speed/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_multi_agent_speed.py) · [高层任务说明](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/README.md) · [`go2_cooperation_skill.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_cooperation_skill.py) · [`go2_center_yard_obstacle_field.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_center_yard_obstacle_field.py) · [`go2_pretrained_body_control.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_pretrained_body_control.py)

## 2. Go2-W 无地图自动对接

[go2w-apriltag-autodock](https://github.com/ASUKA36/go2w-apriltag-autodock)

Unitree Go2-W 根据 AprilTag（`tag36h11`）的相对位姿进入窄充电库，末段由 Mid-360 激光估计库位走廊。控制分三段：库外预对准、进库、下蹲。速度指令同时使用纵向、横向和偏航，对应这台轮足平台的全向移动。控制器从标签已经可见的位置开始，标签丢失时停车。

`go2w_parking` 通过不同 launch 分别接入 Gazebo 和实机。仿真包含二维场景、官方网格的三维场景，以及本仓库中的平面运动插件和动力学插件。实机入口启动 D435i 彩色流、指定 ID 的标签检测和同一控制器。本仓库代码为 Apache-2.0；Go2-W 网格来自宇树，按 BSD-3-Clause 再分发。

代码： [`auto_dock.py`](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/scripts/auto_dock.py) · [`auto_dock.yaml`](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/config/auto_dock.yaml) · [动力学插件](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_sim/src/go2w_dynamics_plugin.cpp) · [平面插件](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_sim/src/go2w_planar_plugin.cpp) · [实机 launch](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/launch/auto_dock_real.launch)

## 3. 海外文物知识系统与掌上博物馆

[software-engineering-2303](https://github.com/ASUKA36/software-engineering-2303)

软件工程课程设计，面向海外馆藏中国文物。我是该系统的总负责人，负责数据库属性构建、知识图谱构建、各子系统之间的结构与协议对接、系统联调，以及需求分析、测试文档、用户文档和开发周会纪要的制定。

掌上博物馆子系统由我独立开发，包括：

- 以图搜图：ResNet50 去掉分类头后提取特征，用余弦相似度检索，索引为 `vectors.npy` 与 `meta.json`
- 离线建索引，以及搜索接口联调
- 业务 API、图片 API、问答 API 的地址配置，健康检查和测试脚本
- 文物浏览、搜索、登录、互动和上传的集成

客户端是 HarmonyOS（ArkTS），移动端服务是 Flask，列表筛选使用 Neo4j，文物详情使用 MySQL。

代码： [`image_search.py`](https://github.com/ASUKA36/software-engineering-2303/blob/main/mobile_museum_app/server/image_search.py) · [`build_image_index.py`](https://github.com/ASUKA36/software-engineering-2303/blob/main/mobile_museum_app/server/build_image_index.py)

## 技术背景

| 内容 | 所在位置 |
|------|----------|
| 分层多智能体强化学习，低层冻结，高层为队伍策略 | 协作搬运的 Speed 与 CenterYard 任务 |
| 程序化场景、刚体网格、奖励、消融与回放 | `go2_multi_agent_center_yard.py`、消融脚本、`train.py` / `play.py` |
| 占据栅格与测地距离进入观测 | `go2_center_yard_obstacle_field.py` 与查找表 |
| ROS、Gazebo、URDF 与自写模型插件 | `go2w_sim` |
| AprilTag 位姿、激光走廊与分段速度控制 | `auto_dock.py` |
| 仿真与实机共用控制入口 | `auto_dock.launch`、`auto_dock_real.launch` |
| 课程系统总负责：数据库属性、知识图谱、接口协议、联调与项目文档 | software-engineering-2303 |
| 掌上博物馆的图像检索、服务地址配置与客户端集成 | `image_search.py`、`build_image_index.py`、`Constants.ets`、`run_project_tests.py` |

仓库中使用 Python、PyTorch、Isaac Lab、HARL、ROS 1、Gazebo、AprilTag、RealSense D435i、Flask、Neo4j、MySQL 与 HarmonyOS ArkTS。
