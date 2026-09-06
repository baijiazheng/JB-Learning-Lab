# tracker （跟踪器：把连续帧里的装甲板关联成同一个目标）

## 核心功能：
```
这一帧发现了哪些 Armor
        ↓
Tracker
        ↓
哪些 Armor 属于同一个敌人？
敌人当前是什么状态？
目标丢失了吗？
应该跟踪哪个？
        ↓
Target
```
Tracker 的任务不是简单保存 Armor，而是从 Armor 中建立一个连续的 Target（目标状态）。
```
                当前帧
                  ↓
              Armor列表
                  ↓
             ┌─────────┐
             │ Tracker │
             └────┬────┘
                  │
       ┌──────────┼──────────┐
       ↓          ↓          ↓
   set_target  update_target state_machine
       │          │          │
       └──────────┼──────────┘
                  ↓
              target_
                  ↓
              Target
```

## 1、构造函数
```
Tracker(const std::string & config_path, Solver & solver);
    config_path（配置文件路径：读取 Tracker 参数）
    Solver & solver（解算器引用：Tracker 可以调用 Solver 获取目标相关信息）
```

## 2、