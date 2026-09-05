sp_vision_25
│
├── 1. src/              ← 程序入口（把整个视觉系统跑起来）
│
├── 2. tasks/             ← 核心算法模块（真正值得我们深入研究）
│   ├── auto_aim/         ← 自瞄 *****
│   ├── auto_buff/        ← 打符
│   └── omniperception/   ← 全向感知
│
├── 3. io/                ← 输入输出（相机、串口、云台、ROS2）
│
├── 4. tools/             ← 基础工具（EKF、PID、数学、轨迹……）
│
├── 5. calibration/       ← 标定（让“相机看到的世界”变得可计算）