节点从写死参数的程序到运行可配置的组件
动态调节行为

相机驱动节点有一堆设置：
- USB 设备名（`/dev/ttyUSB0` 还是 `/dev/ttyUSB1`）
    
- 帧率 FPS（30 / 60）
    
- 是否仿真模式（true / false）
    

如果你把这些设置都**硬编码**在代码里，会发生什么？

- 换一个 USB 口 → 你要改代码、可能还要重新 build
    
- FPS 想从 60 改成 30 → 继续改代码
    
- 两个相机同时跑 → 难道复制两份代码、改两份变量？
    

很明显：**硬编码不利于复用，更不利于扩展。**

在此例中，可为节点加三个参数：
device_name(string)
fps(int)
simulation_mode(bool)

用同一份代码启动多个节点实例，运行不同配置：
- 相机 A：`/dev/ttyUSB0`，30 FPS，非仿真
    
- 相机 B：`/dev/ttyUSB1`，60 FPS，非仿真


在节点使用参数，两步核心：
1.declare：让参数存在于节点，给默认值
2.get：把参数值读出来，存到变量里参与逻辑

## 4. Python：声明 + 获取 + 用起来

在构造函数声明：

```python
self.declare_parameter("number",2)
self.declare_parameter("publish_period",1.0)
```
提供默认值，保证类型安全（一致），运行时可以再次读取（可能被外部修改）
句式:self.declare_parameter("参数名“,默认值)

再读取参数值
```
self.number_timer_=self.create_timer(self.time_period_,self.publish_number)
```
