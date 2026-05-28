# RoboticGrasping-Manipulation

机器人抓取与操作项目集合，涵盖从经典路径规划到深度学习策略的完整技术栈，基于 MuJoCo 仿真环境与松灵 PiPER 6-DOF 机械臂。

## 项目概览

| 子项目 | 简介 | 技术栈 |
|--------|------|--------|
| [grasping-agent](./grasping-agent/) | 反思型机械臂抓取智能体 | MuJoCo, YOLOv8, RRT-Connect, DeepSeek API |
| [Project1](./Project1/) | ACT 模仿学习训练 PiPER 机械臂 | MuJoCo, PyTorch, ACT / Transformer |
| [Project2](./Project2/) | RRT/RRT-Connect 路径规划算法实现 | MuJoCo, 数值 IK, RRT |
| [Project3](./Project3/) | PoseCNN 6D 姿态估计与抓取 | MuJoCo, PoseCNN, RRT |
| [Project4](./Project4/) | GR-ConvNet 抓取点检测与执行 | MuJoCo, GR-ConvNet, RealSense |
| [Project5](./Project5/) | Diffusion Policy 模仿学习抓取 | MuJoCo, Robopal, Robomimic, Diffusion Policy |

## 子项目详情

### [grasping-agent](./grasping-agent/) · 反思型抓取智能体

反思型机械臂抓取智能体。基于 MuJoCo 物理仿真，集成 YOLOv8n 目标检测、RRT-Connect 双向路径规划、数值 IK（阻尼最小二乘法）与 DeepSeek API 反思策略。使用 Piper 6-DOF 机械臂 + Robotiq 双指夹爪。

- **当前阶段**：规则策略 + MuJoCo 真值验证，验证机械臂控制与仿真物理稳定性
- **下一步**：集成 YOLOv8 视觉检测 → 集成 DeepSeek 反思策略
- **环境**：`conda activate project1_act`

---

### [Project1](./Project1/) · ACT 模仿学习

基于 ACT（Action Chunking with Transformers）在 MuJoCo 仿真环境中训练松灵 PiPER 机械臂抓取任务。

- **任务**：夹取红色方块并放置到蓝色圆盘（满分 reward = 3）
- **最佳配置**：`batch_size=8, kl_weight=10, num_epochs=2000` → 56% 成功率
- **环境配置**：`conda create -n project1_act python=3.8.10`
- **训练命令**：
  ```bash
  conda activate project1_act
  python3 imitate_episodes.py --task_name sim_pick_n_place_cube_scripted \
    --ckpt_dir ckpt_dir --policy_class ACT --kl_weight 10 \
    --chunk_size 100 --hidden_dim 512 --batch_size 8 \
    --dim_feedforward 3200 --num_epochs 2000 --lr 1e-5 --seed 0 \
    --temporal_agg
  ```

---

### [Project2](./Project2/) · RRT 路径规划

基于 RRT/RRT-Connect 算法的 6-DOF 机械臂路径规划实现，包含路径平滑优化与物体抓取执行。

- **Task 1**：RRT 路径规划与物体抓取
- **Task 2**（可选）：基于 IK 的路径规划与数据采集
- **依赖**：`pip install ikpy transformations`

---

### [Project3](./Project3/) · PoseCNN 姿态估计

基于 PoseCNN 的 6D 物体姿态估计，结合 IK + RRT 实现目标物体抓取。

- **环境配置**：`conda env create -f environment.yml`
- **额外依赖**：`pip install mujoco==2.3.7 dm_control==1.0.14 ikpy transformations`
- **数据集**：PROPS-Pose-Dataset（[百度网盘下载](https://pan.baidu.com/s/1WYyfJvNFmu7qoLIoFuD1Gw?pwd=dkfv)，提取码：dkfv）

---

### [Project4](./Project4/) · GR-ConvNet 抓取检测

基于 GR-ConvNet（生成残差卷积神经网络）的抓取点预测，结合 IK + RRT 执行机械臂抓取任务。

- **核心算法**：从 RGB-D 图像直接预测抓取位置、角度与开口宽度
- **环境**：复用 `project1_act` 环境，补充安装 `pip install pyrealsense2`
- **论文**：[Antipodal Robotic Grasping](https://arxiv.org/abs/1909.04810)

---

### [Project5](./Project5/) · Diffusion Policy 模仿学习

基于扩散策略（Diffusion Policy）的机械臂抓取任务。利用 Robopal 框架在 MuJoCo 中采集键盘演示数据，训练扩散策略模型并执行抓取。

- **核心框架**：[Robopal](./Project5/robopal/)（基于 MuJoCo 的机器人仿真框架）+ [Robomimic](./Project5/robomimic/)（模仿学习工具链）
- **任务流程**：键盘采集演示 → 数据回放验证 → 自动采集 → 训练 Diffusion Policy → 评估
- **环境配置**：
  ```bash
  conda create -n project5_diffusion python==3.9
  conda activate project5_diffusion
  # 安装 PyTorch (版本 >1.0.0, <2.3.0)，以 CUDA 12.1 为例：
  conda install pytorch==2.2.2 torchvision==0.17.2 pytorch-cuda=12.1 -c pytorch -c nvidia
  # 系统依赖
  sudo apt-get install libosmesa6-dev libgl1-mesa-glx libglfw3 libglew-dev patchelf
  # Python 依赖
  pip install mujoco==3.1.6 dm-control==1.0.10
  cd robomimic && pip install -e .
  cd ../robopal && pip install -r requirements.txt
  pip install numpy==1.26.4 d4rl==1.1
  ```
- **论文**：[Diffusion Policy](https://arxiv.org/abs/2303.04137v4)

---

## 仓库结构

每个子项目拥有独立的 Git 仓库，通过 Git 子模块（submodule）集成到本仓库：

```
RoboticGrasping-Manipulation/
├── grasping-agent/  →  git@github.com:OliviaWYQ/grasping-agent.git
├── Project1/        →  git@github.com:OliviaWYQ/Project1.git
├── Project2/        →  git@github.com:OliviaWYQ/Project2.git
├── Project3/        →  git@github.com:OliviaWYQ/Project3.git
├── Project4/        →  git@github.com:OliviaWYQ/Project4.git
└── Project5/        →  git@github.com:OliviaWYQ/Project5.git
```

克隆时需初始化子模块：
```bash
git clone git@github.com:OliviaWYQ/RoboticGrasping-Manipulation.git
git submodule update --init --recursive
```
