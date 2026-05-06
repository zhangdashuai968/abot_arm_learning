# E08 · ROS 中 rqt 工具使用实验

**来源**：Jason O'Kane《A Gentle Introduction to ROS》全书分散讲解 + 官方实验讲义
**阶段**：Phase 1
**核心概念**：rqt_graph、rqt_plot、rqt_console、rqt_reconfigure、rqt_tf_tree

---

## 关键问题（执行前先尝试用直觉回答）

1. 你写了一个 pub/sub 节点，但 sub 没有收到消息。你会用 rqt_graph 怎么排查？图中看到哪几种情况说明"topic 名拼错了"？
2. rqt_plot 和 `rostopic echo` 都能看数据，什么场景下 rqt_plot 更直观？什么场景下 `rostopic echo` 更实用？
3. rqt_reconfigure 能让节点在线改参数不重启，节点端需要写什么代码配合？如果你调一个别人的节点发现 rqt_reconfigure 里看不到它，说明什么？
4. 调试 ROS 问题的标准流程是什么？O'Kane 总结的"第一步、第二步、第三步"分别是什么工具？
5. rqt_tf_tree 和 rviz 的 TF Display 都能看到坐标系关系，两者有什么区别？什么时候用哪个？

---

## 这节解决什么问题

ROS 系统运行起来后，怎么**理解**和**调试**它？节点太多了谁在跟谁说话？某个 topic 上的数值对不对？哪里在报错？参数能不能在线改？rqt 套件解决这类问题。

O'Kane 在书中多个章节穿插使用 rqt 工具，尤其在 Chapter 2（rqt_graph 理解 turtlesim 拓扑）、Chapter 7（rqt_reconfigure 在线调参）、Chapter 8（rqt_tf_tree 理解坐标系）中反复出现。

---

## rqt_graph — 最关键的一个

```bash
rosrun rqt_graph rqt_graph
```

画出**当前运行中的所有 Node 和 Topic 的连接关系**，椭圆形 = Node，矩形 = Topic，箭头从 Publisher → Subscriber。

### 常用选项

| 选项 | 含义 |
|---|---|
| Nodes only | 只显示节点，隐藏 topic 方框 |
| Nodes/Topics (all) | 显示所有 |
| Nodes/Topics (active) | 只显示有数据流动的 |
| Hide Debug | 隐藏 `/rosout` 等调试 topic |
| Group by namespace | 按命名空间分组 |

### 实战：用 rqt_graph 检测问题

```bash
# 场景：你以为 A 节点在向 B 节点发数据，但 B 没反应
rosrun rqt_graph rqt_graph

# 看图中 A → /topic_name → B 这条箭头是否存在
# 如果不存在 → topic 名拼错了、或者 B 的 subscribe 注册失败
# 如果存在但 B 没反应 → B 的回调函数有 bug
```

> O'Kane 的建议：**每次写新的 pub/sub 组合后，第一步就打开 rqt_graph 确认连线是对的**。

---

## rqt_plot — 看数据波形

```bash
rosrun rqt_plot rqt_plot
```

在文本框输入 topic 字段名（如 `/turtle1/pose/x`），会画出时间序列曲线。支持同时画多条曲线对比。

```
# 同时看乌龟的 x 和 y 位置
/turtle1/pose/x
/turtle1/pose/y

# 看速度指令
/turtle1/cmd_vel/linear/x
/turtle1/cmd_vel/angular/z
```

**实用场景**：
- PID 调参：同时画 `/joint_states/velocity` 和 `/cmd_vel`，看超调和响应时间
- 导航调试：画 `/cmd_vel/linear/x`，看加减速是否平滑
- 数据验证：画两个关联量确认数学关系正确

---

## rqt_console — 看日志

```bash
rosrun rqt_console rqt_console
```

GUI 版的 ROS 日志查看器，能按级别过滤：

| 级别 | 含义 |
|---|---|
| DEBUG | 调试信息（默认不显示） |
| INFO | 一般信息 |
| WARN | 警告 |
| ERROR | 错误 |
| FATAL | 致命错误（节点会退出） |

在代码中控制日志级别：

```cpp
ROS_DEBUG_STREAM("x=" << x);       // 仅在调试模式下打印
ROS_INFO_STREAM("Got message");    // 正常信息
ROS_WARN_STREAM("Low battery!");   // 警告
ROS_ERROR_STREAM("Motor timeout"); // 错误
```

**O'Kane 的关键技巧**：你可以在 rqt_console 中按 Node 名过滤，只盯着一个有问题的节点，不被其他节点的日志刷屏。

---

## rqt_reconfigure — 在线调参

```bash
rosrun rqt_reconfigure rqt_reconfigure
```

不需要重新编译、重启节点，直接拖动滑块修改节点参数。这是 O'Kane Chapter 7（Parameters）推荐的第一调试工具。

### 节点侧配合代码

```cpp
#include <dynamic_reconfigure/server.h>
#include <my_pkg/MyConfigConfig.h>  // 从 .cfg 文件自动生成

void callback(my_pkg::MyConfigConfig &config, uint32_t level) {
    ROS_INFO("Reconfigure Request: %f %d %s",
        config.speed, config.enabled, config.mode.c_str());

    // 把 config.speed 等用于当前节点逻辑
}

int main(int argc, char **argv) {
    ros::init(argc, argv, "dynamic_node");

    dynamic_reconfigure::Server<my_pkg::MyConfigConfig> server;
    server.setCallback(boost::bind(&callback, _1, _2));

    ros::spin();
}
```

配置定义文件 `cfg/MyConfig.cfg`：

```python
#!/usr/bin/env python
from dynamic_reconfigure.parameter_generator_catkin import *

gen = ParameterGenerator()
gen.add("speed", double_t, 0, "Speed in m/s", 0.5, 0.0, 2.0)
gen.add("enabled", bool_t, 0, "Enable motor", True)
gen.add("mode", str_t, 0, "Control mode", "position")
exit(gen.generate("my_pkg", "my_pkg", "MyConfig"))
```

> 需要 `chmod +x cfg/MyConfig.cfg` 并确保 package.xml 有 `dynamic_reconfigure` 依赖。

---

## rqt_tf_tree — 看坐标系

```bash
rosrun rqt_tf_tree rqt_tf_tree
```

比 `view_frames` 更实时的 tf 树查看器，能显示坐标系的父子关系和当前的变换值。调试"激光雷达数据歪了"这类空间问题时的入口工具。

---

## rqt_bag — 可视化 rosbag

```bash
rosrun rqt_bag rqt_bag
```

加载 `.bag` 文件后可以：
- 按时间轴拖动查看
- 选择特定 topic 可视化
- 把 bag 里的数据发给 rqt_plot 画图
- 跳到任意时间点

> 这是 O'Kane 多章节使用的调试方法论：**录 bag → 用 rqt_bag 回放 → 反复用不同参数重放观察。**

---

## 调试工作流（O'Kane 全书总结）

```
发现异常 → rqt_graph 看拓扑对不对
         → rqt_console 看有没有报错
         → rqt_plot 看数据变化趋势
         → rqt_reconfigure 在线改参数试效果
         → rosbag record 录数据
         → rqt_bag 回放 + rqt_plot 分析
```

---

## 常见错误

| 错误 | 原因 |
|---|---|
| rqt_graph 里看不到连接的箭头 | 选了 "Nodes only" 模式；或节点确实没有在通信 |
| rqt_plot 是空的 | `/topic/field` 路径写错了，用 `rostopic echo` 先确认路径 |
| rqt_reconfigure 找不到节点 | 节点没有创建 dynamic_reconfigure server |
| rqt 启动报 "No module named..." | 缺少对应的 rqt 插件包：`sudo apt install ros-<distro>-rqt-<name>` |

---

## 一句话总结

**rqt 是 ROS 的调试瑞士军刀：rqt_graph 看连接、rqt_plot 看波形、rqt_console 看日志、rqt_reconfigure 在线调参；遇到问题先 open rqt_graph，这是 O'Kane 全书反复强调的第一反应。**
