# RoboticGrasping-Manipulation

机器人抓取与操作项目集合，包含三个独立子项目。

## 子项目

### [grasping-agent](./grasping-agent/)
反思型机械臂抓取智能体。基于 MuJoCo 物理仿真，集成 YOLOv8n 目标检测、RRT/RRT-Connect 路径规划、数值 IK 与 DeepSeek API 反思策略。使用 Piper 6-DOF 机械臂 + Robotiq 双指夹爪。

- **技术栈**: Python, MuJoCo (dm_control), ultralytics, DeepSeek API
- **环境**: Conda `project1_act`, Python 3.10

### [Project1](./Project1/)
机器人抓取与操作相关实验项目。

### [Project2](./Project2/)
路径规划算法实现。包含 RRT-Connect 算法与路径平滑优化，附有演示视频。

- **技术栈**: Python, RRT / RRT-Connect

## 结构说明

每个子项目拥有独立的 Git 仓库，分别管理各自的代码版本。
