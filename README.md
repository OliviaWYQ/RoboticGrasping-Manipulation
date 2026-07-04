# RoboticGrasping-Manipulation

机器人抓取与操作项目集合，涵盖从经典路径规划到深度学习策略的完整技术栈，基于 MuJoCo 仿真环境与松灵 PiPER 6-DOF 机械臂。

## 项目概览

| 子项目 | 简介 | 技术栈 |
|--------|------|--------|
| [Project1](https://github.com/OliviaWYQ/Project1) | ACT 模仿学习训练 PiPER 机械臂 | MuJoCo, PyTorch, ACT / Transformer |
| [Project2](https://github.com/OliviaWYQ/Project2) | RRT/RRT-Connect 路径规划算法实现 | MuJoCo, 数值 IK, RRT |
| [Project3](https://github.com/OliviaWYQ/Project3) | PoseCNN 6D 姿态估计与抓取 | MuJoCo, PoseCNN, RRT |
| [Project4](https://github.com/OliviaWYQ/Project4) | GR-ConvNet 抓取点检测与执行 | MuJoCo, GR-ConvNet, RealSense |
| [Project5](https://github.com/OliviaWYQ/Project5) | Diffusion Policy 模仿学习抓取 | MuJoCo, Robopal, Robomimic, Diffusion Policy |
| [Project6](https://github.com/OliviaWYQ/Project6) | PPO 强化学习训练 PiPER 机械臂抓取 | MuJoCo, Stable-Baselines3, PPO |
| [Project7](https://github.com/OliviaWYQ/Project7) | GR00T-N1-2B VLA 模型微调与 LIBERO 仿真验证 | GR00T-N1-2B, LIBERO, Docker, WebSocket, LeRobot |

## 子项目详情

### [Project1](https://github.com/OliviaWYQ/Project1) · ACT 模仿学习

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

### [Project2](https://github.com/OliviaWYQ/Project2) · RRT 路径规划

基于 RRT/RRT-Connect 算法的 6-DOF 机械臂路径规划实现，包含路径平滑优化与物体抓取执行。

- **Task 1**：RRT 路径规划与物体抓取
- **Task 2**（可选）：基于 IK 的路径规划与数据采集
- **依赖**：`pip install ikpy transformations`

---

### [Project3](https://github.com/OliviaWYQ/Project3) · PoseCNN 姿态估计

基于 PoseCNN 的 6D 物体姿态估计，结合 IK + RRT 实现目标物体抓取。

- **环境配置**：`conda env create -f environment.yml`
- **额外依赖**：`pip install mujoco==2.3.7 dm_control==1.0.14 ikpy transformations`
- **数据集**：PROPS-Pose-Dataset（[百度网盘下载](https://pan.baidu.com/s/1WYyfJvNFmu7qoLIoFuD1Gw?pwd=dkfv)，提取码：dkfv）

---

### [Project4](https://github.com/OliviaWYQ/Project4) · GR-ConvNet 抓取检测

基于 GR-ConvNet（生成残差卷积神经网络）的抓取点预测，结合 IK + RRT 执行机械臂抓取任务。

- **核心算法**：从 RGB-D 图像直接预测抓取位置、角度与开口宽度
- **环境**：复用 `project1_act` 环境，补充安装 `pip install pyrealsense2`
- **论文**：[Antipodal Robotic Grasping](https://arxiv.org/abs/1909.04810)

---

### [Project5](https://github.com/OliviaWYQ/Project5) · Diffusion Policy 模仿学习

基于扩散策略（Diffusion Policy）的机械臂抓取任务。利用 Robopal 框架在 MuJoCo 中采集键盘演示数据，训练扩散策略模型并执行抓取。

- **核心框架**：[Robopal](https://github.com/OliviaWYQ/Project5/tree/main/robopal)（基于 MuJoCo 的机器人仿真框架）+ [Robomimic](https://github.com/OliviaWYQ/Project5/tree/main/robomimic)（模仿学习工具链）
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

### [Project6](https://github.com/OliviaWYQ/Project6) · PPO 强化学习

基于 PPO（Proximal Policy Optimization）的机械臂抓取强化学习训练，支持 IK 控制（关节空间）与直接抓取（末端执行器空间）两种模式。

- **IK模式**：机械臂通过逆运动学求解关节角到达目标位姿
- **Grasp模式**：直接预测末端执行器抓取姿态
- **环境**：MuJoCo + Stable-Baselines3 PPO
- **运行示例**：
  ```bash
  python rl_policy/rl_piper_ik_train.py    # IK模式训练
  python rl_policy/rl_piper_grasp_train.py  # 抓取模式训练
  ```

---

### [Project7](https://github.com/OliviaWYQ/Project7) · GR00T-N1-2B 微调与 LIBERO 仿真验证

基于 NVIDIA [GR00T-N1-2B](https://huggingface.co/nvidia/GR00T-N1-2B) 视觉-语言-动作（VLA）基础模型，在 LIBERO 机器人操作基准上进行微调，并通过 LIBERO 仿真环境验证抓取性能。采用客户端-服务端架构：A100 云服务器运行 GR00T-N1-2B 模型推理（Docker 部署），RTX 4070 本地机器运行 LIBERO 仿真客户端，双方通过 WebSocket 通信。

- **核心成果**：微调前成功率 0% → 微调后成功率 96.2%（10 个任务 × 50 次 = 500 次测试）
- **实验设计**：
  - 对照组 `checkpoint-1`：仅训练 1 步，作为微调前基线
  - 微调组 `checkpoint-20000`：训练 20000 步，为主要实验模型
- **数据集**：[libero_object_no_noops_lerobot](https://hf-mirror.com/datasets/IPEC-COMMUNITY/libero_object_no_noops_lerobot)（LeRobot 格式，可通过阿里云 OSS 下载）
- **架构**：A100 服务端（Docker + GR00T-N1-2B 推理）+ RTX 4070 客户端（LIBERO + MuJoCo 仿真），WebSocket 通信
- **环境配置**：
  - 服务端：`conda create -n gr00t python=3.10`，需安装 [Isaac-GR00T](https://github.com/NVIDIA/Isaac-GR00T) + openpi-client
  - 客户端：`conda create -n gr00t_sim python=3.8`，需安装 LIBERO + robosuite + MuJoCo + openpi-client
- **关键文件**：
  - `gr00t_finetune_libero.py` — 微调训练脚本（全量微调）
  - `gr00t_primitive_libero.py` — 仅动作头训练脚本
  - `server/serve_policy.py` — 服务端推理入口
  - `server/websocket_policy_server.py` — WebSocket 策略服务器
  - `server/patches/` — Isaac-GR00T 补丁（FrankaDataConfig + LiberoSingleDataset）
  - `sim/libero/main.py` — LIBERO 仿真评估入口
  - `sim/libero/convert_libero_data_to_lerobot.py` — 数据格式转换
  - `modality.json` — GR00T N1 数据模态配置
- **训练命令**：
  ```bash
  conda activate gr00t
  cd Isaac-GR00T
  python scripts/gr00t_finetune_libero.py \
      --dataset-path $DATASET_PATH \
      --base-model-path $BASE_MODEL_PATH \
      --output-dir $OUTPUT_ROOT/franka_libero_object_no_noops_lerobot_20000 \
      --data-config franka \
      --batch-size 1 --max-steps 20000 --save-steps 5000 \
      --tune-visual --tune-projector --tune-diffusion-model \
      --num-gpus 1 --learning-rate 1e-4 --report-to tensorboard
  ```
- **硬件要求**：服务端建议 A100 显卡（显存 ≥ 40GB），客户端需 RTX 4070 及以上
- **参考资源**：
  - 模型：[NVIDIA/Isaac-GR00T](https://github.com/NVIDIA/Isaac-GR00T)
  - 仿真：[LIBERO](https://github.com/Lifelong-Robot-Learning/LIBERO)
  - 客户端库：[openpi](https://github.com/Physical-Intelligence/openpi)

---

每个子项目拥有独立的 Git 仓库，通过 Git 子模块（submodule）集成到本仓库：

| 本地路径 | 仓库地址 |
|----------|----------|
| `Project1/` | [github.com/OliviaWYQ/Project1](https://github.com/OliviaWYQ/Project1) |
| `Project2/` | [github.com/OliviaWYQ/Project2](https://github.com/OliviaWYQ/Project2) |
| `Project3/` | [github.com/OliviaWYQ/Project3](https://github.com/OliviaWYQ/Project3) |
| `Project4/` | [github.com/OliviaWYQ/Project4](https://github.com/OliviaWYQ/Project4) |
| `Project5/` | [github.com/OliviaWYQ/Project5](https://github.com/OliviaWYQ/Project5) |
| `Project6/` | [github.com/OliviaWYQ/Project6](https://github.com/OliviaWYQ/Project6) |
| `Project7/` | [github.com/OliviaWYQ/Project7](https://github.com/OliviaWYQ/Project7) |

克隆时需初始化子模块：
```bash
git clone git@github.com:OliviaWYQ/RoboticGrasping-Manipulation.git
git submodule update --init --recursive
```
