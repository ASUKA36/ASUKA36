# 薛度扬（Xue Duyang）

北京化工大学 · 信息科学与技术学院 · 计算机科学与技术本科

我公开的工作主要在四足与轮足机器人：用分层多智能体强化学习做多机协作搬运，用视觉和激光把轮足平台对接进狭窄库位。课程项目中，我负责移动端的以图搜图，以及和其他子系统的联调。

给老师看代码时，建议按下面的顺序。每项都写了问题、我做的设计，以及仓库里可以直接打开的文件。

---

## 1. 多队 Go2 协作抬杆过门

[unitree-go2-multi-team-cooperative-carry](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry)

两支队伍从场地南侧出发，每队两台 Unitree Go2 抬一根半圆杆，穿过中墙上门洞。低层身体控制先训练并冻结，高层只学习队伍级的过门技能。这是我目前最完整的研究向工程。

仿真栈是 Isaac Sim / Isaac Lab 4.5 与 HARL，算法是 MAPPO。仓库是叠到 [IsaacLab-HARL](https://github.com/DIRECTLab/IsaacLab-HARL) 上的任务环境、配置、训练与回放脚本。

| 层级 | 设计 |
|------|------|
| 低层身体控制 | 两台 Go2、共享 MAPPO actor。观测 60 维（本体 13 + 关节相关 36 + 任务 11），动作是 12 维关节位置增量。50 Hz 跟踪杆指令：法向速度、偏航角速度、轴向速度、高度。回合 30 秒。杆的网格在代码里生成。 |
| 高层队伍技能 | 每队一个 `cooperation_skill`，默认共享参数，1 Hz 输出三维速度技能，再解码成低层杆指令。观测含 12 线二维激光、另一队特征，以及到门的测地距离和梯度。回合 45 秒。 |
| 场景 | 15 m 场地、东西向中墙、2 m 门。距离场来自占据栅格上的测地查找表，过门演示轨迹用于模仿热身。 |
| 训练与核对 | 支持模仿热身及衰减、共享与非共享 actor。消融从分层保持，到门区加密采样、横向整形，再到演示热身。另有不启动 Isaac 界面的模仿与保持逻辑测试。 |

建议打开：

- [低层任务说明](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/low_level_speed/README.md) 与 [`go2_multi_agent_speed.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/low_level_speed/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_multi_agent_speed.py)
- [高层任务说明](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/README.md)、[`go2_cooperation_skill.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_cooperation_skill.py)、[`go2_center_yard_obstacle_field.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_center_yard_obstacle_field.py)
- 冻结低层权重的加载：[`go2_pretrained_body_control.py`](https://github.com/ASUKA36/unitree-go2-multi-team-cooperative-carry/blob/main/high_level_center_yard/source/isaaclab_tasks/isaaclab_tasks/direct/go2_multi_agent/go2_pretrained_body_control.py)

这里能看到的能力是：把协作任务拆成可冻结的身体控制和可训练的队伍技能，把测地距离写进观测，并把训练、消融、回放和测试留在同一套脚本里。

## 2. Go2-W 无地图自动对接

[go2w-apriltag-autodock](https://github.com/ASUKA36/go2w-apriltag-autodock)

Unitree Go2-W 不依赖全局地图和里程计，用 AprilTag（`tag36h11`）的相对位姿进入窄充电库，末段可用 Mid-360 激光估计库位走廊。控制分三段：库外预对准、进库、下蹲。速度指令同时使用纵向、横向和偏航，对应这台轮足平台的全向移动。

同一套 `go2w_parking` 由不同 launch 接到 Gazebo 和实机。仿真侧包含二维场景、官方网格的三维场景，以及自行编写的平面运动插件和动力学插件。实机入口启动 D435i 彩色流、指定 ID 的标签检测和同一控制器。控制器从标签已经可见的位置开始；标签丢失时停车，避免在库内盲走。

建议打开：

- [对接状态机 `auto_dock.py`](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/scripts/auto_dock.py) 与 [参数 `auto_dock.yaml`](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/config/auto_dock.yaml)
- [Gazebo 动力学插件](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_sim/src/go2w_dynamics_plugin.cpp) 与 [平面插件](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_sim/src/go2w_planar_plugin.cpp)
- [实机 launch](https://github.com/ASUKA36/go2w-apriltag-autodock/blob/main/src/go2w_parking/launch/auto_dock_real.launch)

这里能看到的能力是：把视觉位姿、激光走廊和速度状态机接到 ROS 上，并为仿真和实机保留同一个控制包。本仓库代码为 Apache-2.0；Go2-W 网格来自宇树模型，按 BSD-3-Clause 再分发。

## 3. 掌上博物馆中的以图搜图与联调

[software-engineering-2303](https://github.com/ASUKA36/software-engineering-2303)

软件工程课程设计，面向海外馆藏中国文物。课程组的子系统包括知识图谱构建、Web 知识服务、问答、移动端和后台管理。我负责移动端「掌上博物馆」。

应用内部还有页面分工。我名下的部分是：

- 以图搜图：ResNet50 去掉分类头后提取特征，用余弦相似度检索，索引为 `vectors.npy` 与 `meta.json`
- 离线建索引脚本，以及搜索接口联调
- 业务 API、图片 API、问答 API 的地址配置，健康检查和测试脚本
- 参与文物浏览、搜索、登录、互动和上传的集成

客户端是 HarmonyOS（ArkTS），移动端服务是 Flask，列表筛选走 Neo4j，文物详情走 MySQL。图谱爬取、Web 前端、问答后端和后台管理是其他同学的子系统。

建议打开：

- [`image_search.py`](https://github.com/ASUKA36/software-engineering-2303/blob/main/mobile_museum_app/server/image_search.py)
- [`build_image_index.py`](https://github.com/ASUKA36/software-engineering-2303/blob/main/mobile_museum_app/server/build_image_index.py)
- [移动端分工说明](https://github.com/ASUKA36/software-engineering-2303/blob/main/mobile_museum_app/分工说明.md)

这里能看到的能力是：在已经拆开的多服务系统里交付一个可重建的视觉检索模块，并把客户端接到各组接口上。

---

## 能力与对应代码

| 能力 | 在哪里 |
|------|--------|
| 分层多智能体强化学习，低层冻结、高层队伍策略 | 协作搬运的 Speed 与 CenterYard 任务 |
| 程序化场景、刚体网格、奖励、消融与回放 | `go2_multi_agent_center_yard.py`、消融脚本、`train.py` / `play.py` |
| 占据栅格与测地距离进入观测 | `go2_center_yard_obstacle_field.py` 与查找表 |
| ROS、Gazebo、URDF、自写模型插件 | `go2w_sim` |
| AprilTag 位姿、激光走廊、分段速度控制 | `auto_dock.py` |
| 仿真与实机共用控制入口 | `auto_dock.launch` 与 `auto_dock_real.launch` |
| 卷积特征检索与离线索引 | `image_search.py`、`build_image_index.py` |
| 多服务联调与测试 | `Constants.ets`、`run_project_tests.py` |

常用工具包括 Python、PyTorch、Isaac Lab、HARL、ROS 1、Gazebo、AprilTag、RealSense D435i，以及 Flask、Neo4j、MySQL、HarmonyOS ArkTS。

---

## English

I am an undergraduate in Computer Science and Technology at Beijing University of Chemical Technology. The two robotics repositories are the work I would like a faculty member to read.

**Cooperative carry.** Two teams of Unitree Go2 robots carry half-round logs through a door in Isaac Lab 4.5 with HARL and MAPPO. A shared low-level policy tracks bar velocity at 50 Hz (60-D observation, 12-D joint increments) and is then frozen. Each team has one high-level skill agent at 1 Hz. Its 3-D action is decoded into the bar command. High-level observations include a 12-ray lidar, the other team, and a geodesic distance field on an occupancy grid. The repo is an overlay of environments, configs, training, ablation, and tests for IsaacLab-HARL.

**Auto-dock.** A map-free docker for the wheeled-leg Unitree Go2-W. AprilTag `tag36h11` gives relative pose; Mid-360 lidar can estimate the bay corridor on the final approach. The controller pre-aligns, enters, and crouches, using longitudinal, lateral, and yaw velocity. The same ROS package has Gazebo launches (including custom planar and dynamics plugins) and a D435i hardware launch. It starts when the tag is already visible and stops if the tag is lost.

**Course project.** In a museum knowledge-graph course system I owned the HarmonyOS mobile subsystem. My named module is image search: ResNet50 embeddings, cosine retrieval, an offline index, service endpoint configuration, and integration tests. Graph construction, the web service, question answering, and administration belong to other teammates. See `mobile_museum_app/分工说明.md`.
