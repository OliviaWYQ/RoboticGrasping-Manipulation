# RoboticGrasping-Manipulation

机器人抓取与操作项目集合，包含四个独立子项目。

## 项目概览

| 子项目 | 简介 | 技术栈 |
|--------|------|--------|
| [grasping-agent](./grasping-agent/) | 反思型机械臂抓取智能体 | MuJoCo, YOLOv8, RRT, DeepSeek API |
| [Project1](./Project1/) | ACT 模仿学习训练松灵 PiPER 机械臂 | MuJoCo, PyTorch, ACT |
| [Project2](./Project2/) | RRT/RRT-Connect 路径规划算法实现 | MuJoCo, 数值 IK, RRT |
| [Project3](./Project3/) | PoseCNN 6D 姿态估计与抓取 | MuJoCo, PoseCNN, RRT |

## 子项目详情

### [grasping-agent](./grasping-agent/)

反思型机械臂抓取智能体。基于 MuJoCo 物理仿真，集成 YOLOv8n 目标检测、RRT/RRT-Connect 路径规划、数值 IK 与 DeepSeek API 反思策略。使用 Piper 6-DOF 机械臂 + Robotiq 双指夹爪。

- **当前阶段**：规则策略 + MuJoCo 真值验证，验证机械臂控制与仿真物理稳定性
- **下一步**：集成 YOLOv8 视觉检测 → 集成 DeepSeek 反思策略

### [Project1](./Project1/) · ACT 模仿学习

基于 ACT（Action Chunking with Transformers）在 MuJoCo 仿真环境中训练松灵 PiPER 机械臂抓取任务。

- **任务**：夹取红色方块并放置到蓝色圆盘
- **环境配置**：`conda create -n project1_act python=3.8.10`
- **参考命令**：
  ```bash
  conda activate project1_act
  python3 imitate_episodes.py --task_name sim_pick_n_place_cube_scripted \
    --ckpt_dir ckpt_dir --policy_class ACT --kl_weight 10 \
    --chunk_size 100 --hidden_dim 512 --batch_size 8 \
    --dim_feedforward 3200 --num_epochs 2000 --lr 1e-5 --seed 0 \
    --temporal_agg
  ```

### [Project2](./Project2/) · RRT 路径规划

基于 RRT/RRT-Connect 算法的 6-DOF 机械臂路径规划实现，包含路径平滑优化。

- **Task 1**：RRT 路径规划与物体抓取
- **Task 2**（可选）：基于 IK 的路径规划与数据采集
- **依赖**：`pip install ikpy transformations`

### [Project3](./Project3/) · PoseCNN 姿态估计

基于 PoseCNN 的 6D 物体姿态估计，结合 IK + RRT 实现目标物体抓取。

- **环境配置**：`conda env create -f environment.yml`
- **额外依赖**：`pip install mujoco==2.3.7 dm_control==1.0.14 ikpy transformations`
- **数据集**：PROPS-Pose-Dataset（[百度网盘下载](https://pan.baidu.com/s/1WYyfJvNFmu7qoLIoFuD1Gw?pwd=dkfv)，提取码：dkfv）

## 结构说明

每个子项目拥有独立的 Git 仓库，通过 Git 子模块（submodule）集成到本仓库：

```
RoboticGrasping-Manipulation/
├── grasping-agent/  →  git@github.com:OliviaWYQ/grasping-agent.git
├── Project1/        →  git@github.com:OliviaWYQ/Project1.git
├── Project2/        →  git@github.com:OliviaWYQ/Project2.git
└── Project3/        →  git@github.com:OliviaWYQ/Project3.git
```

克隆时需初始化子模块：
```bash
git clone git@github.com:OliviaWYQ/RoboticGrasping-Manipulation.git
git submodule update --init --recursive
```
