<div align="center">

# Atlas

</div>

> AgroTech 协会新一代中型轮式机器人平台（仓库：`Competition-Robot-2026-Atlas`）

> 本仓库由原集合仓库 `Steering-Wheel-Chassis`（一车一目录）拆分而来，当前只维护 **Atlas** 这一台机器人

---

## 1. 当前状态

- **状态：** 开发中（智械争锋全自主区主线）
- **开发计划：** [`docs/plan.md`](docs/plan.md)
- **整车详细说明：** [`Atlas/README.md`](Atlas/README.md)

> 首次形成可复现的稳定版本后，再创建 Git Tag + GitHub Release，并将本 README 更新为该稳定版本的完整使用说明

---

## 2. 参与本项目开发

推荐流程：

**Issue → Branch → Commit → Push → Pull Request → 项目负责人 Merge**

- 开始开发前，原则上先创建或认领 Issue
- 请勿直接在 `main` 开发或 Push
- 如果已经误在 `main` 上产生了有用 Commit，**不要先 `reset --hard`**，先按协作指南把提交保存到新分支

完整流程与常见问题：[`.github/CONTRIBUTING.md`](.github/CONTRIBUTING.md)

---

## 3. 项目简介

`Atlas` 是 AgroTech 协会新一代中型轮式机器人平台，采用舵轮底盘作为移动底盘，使用 STM32 控制板完成底层实时控制，使用树莓派 5 运行 ROS2 Humble 导航系统，并配有 PC 主臂遥操作与 ASRPro 语音链路。

当前主线是**智械争锋全自主区**：MCU 侧触发 AutoPi，Pi 端运行 YASMIN 比赛状态机，完成 A/B 场地识别、语义导航、视觉抓取、园区放置和 DONE/FAIL 上报。

整车角色分工：

| 端 | 目录 | 作用 |
|---|---|---|
| MCU | `Atlas/chassis_control_code/` | 底盘实时控制、机械臂执行、AutoPi 状态、安全边界和串口协议 |
| Pi | `Atlas/chassis-pi-ws/` | ROS2 Humble、MCU bridge、导航、视觉、YASMIN 比赛状态机 |
| PC | `Atlas/chassis-pc-ws/` | 主臂遥操作和上位机调试 |
| ASRPro | `Atlas/atlas_asrpro/` | 语音触发和播报相关链路 |

---

## 4. 环境要求

### 软件

- 树莓派 5，Ubuntu 22.04，ROS2 Humble
- 需安装：`navigation2`、`nav2-bringup`、`cartographer`、`cartographer-ros`、`xacro`、`robot-state-publisher`、`rviz2` 等，以及工作区 `requirements.txt` 管理的 Python 依赖（含 `onnxruntime`）
- PC 端遥操作 App 需要 `python3` + `requirements.txt` 依赖
- MCU 固件使用 STM32CubeMX / EIDE 工具链编译

### 硬件

- 主控：STM32 控制板（MCU 端）
- 上位机：树莓派 5（Pi 端）
- 传感器：LSLIDAR N10P 雷达、IMU
- 执行器：舵轮底盘、五自由度机械臂、末端吸盘

---

## 5. 快速开始

> 下面的命令按实际设备划分：`chassis-pc-ws` 只在遥操作 PC 上运行，`chassis-pi-ws` 只在树莓派的 Ubuntu 22.04 / ROS2 Humble 环境运行。完整执行清单见 [`Atlas/README.md`](Atlas/README.md) 的 Quick Start。

### 5.1 MCU 侧

烧录 `Atlas/chassis_control_code/` 固件后，确认 Pi 能打开 MCU 串口（如 `/dev/ttyACM0`、`/dev/mcu_uart`），且能收到状态、里程计、IMU 和机械臂状态帧。

### 5.2 PC 遥操作

```bash
cd Atlas/chassis-pc-ws
python3 -m pip install -r requirements.txt
python3 scripts/main.py
```

### 5.3 Pi 端

```bash
cd Atlas/chassis-pi-ws
sudo apt update
sudo apt install -y python3-colcon-common-extensions python3-rosdep python3-yaml \
  python3-opencv python3-numpy ros-humble-navigation2 ros-humble-nav2-bringup \
  ros-humble-cartographer ros-humble-cartographer-ros ros-humble-xacro \
  ros-humble-robot-state-publisher ros-humble-rviz2

sudo rosdep init        # 已初始化过则跳过
rosdep update
rosdep install --from-paths src --ignore-src -r -y
python3 -m pip install --user -r requirements.txt

colcon build --symlink-install
source install/setup.bash

ros2 launch atlas_competition_bringup competition_stack.launch.py
```

> 正式比赛优先只改 `competition.yaml`；地图、导航点、扫描位姿、ROI 和放置位姿均需实测后逐项打开 `configured / enabled`，未配置字段保持安全拒绝状态。

---

## 6. 目录结构

```text
Competition-Robot-2026-Atlas/
├── Atlas/
│   ├── atlas_asrpro/                  # ASRPro 语音链路
│   ├── chassis_control_code/          # MCU 底盘与机械臂控制工程
│   ├── chassis-pc-ws/                 # PC 主臂遥操作 App
│   ├── chassis-pi-ws/                 # Pi 端 ROS2 自主任务工作区
│   ├── docs/                          # 整车通信协议和说明文档
│   └── README.md                      # 整车 Quick Start / 联调 / 验证
├── docs/
│   └── plan.md                        # 开发计划
└── README.md
```

---

## 7. 文档

- [`Atlas/README.md`](Atlas/README.md)：整车 Quick Start、A/B 地图与导航配置、MCU 串口配置、实车配置顺序、验证命令、安全原则
- `Atlas/docs/comms_protocol.md`：PC / Pi / MCU 统一通信协议
- `Atlas/docs/Atlas_MCU_ASRPro.md`：MCU ↔ ASRPro 语音链路说明
- `docs/plan.md`：开发计划（必须）

---

## 8. 维护者

- 项目负责人：`@<GitHub-ID>`（待填写）
