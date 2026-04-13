publisher是信号发射器
topic是电台频道
发射器往频道发射数据
subscriber是手机能接收，调到同一个频道，还要用同一信号格式
也就是topic的名字加接口
topic以字母开头
## 1）Python：创建 `number_publisher` 节点

在 `my_py_pkg` 里新建文件：
写入模板
接口用现成的example_interfaces/msg/Int64
只有一个数字
## 2）在构造函数里创建 publisher，并用 timer 定时发布
消息导入Int64
from example_interfaces.msg import Int64
创建publlisher注册timer
```python
class NumberPublisherNode(Node):
    def __init__(self):
        super().__init__("number_publisher")
        self.number_ = 2
        self.number_publisher_ = self.create_publisher(Int64, "number", 10)
        self.number_timer_ = self.create_timer(1.0, self.publish_number)
        self.get_logger().info("Number publisher has been started.")
    def publish_number(self):
        msg = Int64() #回调函数内部处理，单独创建消息变量
        msg.data = self.number_ #成员变量可以在函数外使用
        self.number_publisher_.publish(msg) #发布消息哦
```
- `create_publisher(数据类型, topic名, 队列queue_size)`
- 变量名后带下划线是表明是成员变量，外面可以引用
- `create_timer(周期秒, 回调函数)`
- - 发布时：创建消息对象 → 填字段 → `publish(msg)`对应回调函数的三行

补充依赖
用了新的接口包 `example_interfaces`，要在 `my_py_pkg/package.xml` 里加依赖：
```
<depend>rclpy</depend>
<depend>example_interfaces</depend>
```
在setup.py的console_scripts加
```
'console_scripts': [
    "test_node = my_py_pkg.my_first_node:main",
    "number_publisher = my_py_pkg.number_publisher:main",
],
```
colcon build --packages-select my_py_pkg
source ~/.bashrc
ros2 topic echo /number订阅消息
5）C++：流程同样，差别在语法
C++ 的思路完全一致：

include 对应消息头：example_interfaces/msg/int64.hpp

create_publisher<类型>("number", 10)

timer 回调里创建 msg，填字段，publish

构建时除了 rclcpp 之外，需要：

package.xml 增加 <depend>example_interfaces</depend>

CMakeLists.txt 增加 find_package(example_interfaces REQUIRED)

ament_target_dependencies(... rclcpp example_interfaces)

写节点前都有chmod +x 文件名 赋予权限

# 三、写一个 Topic Subscriber：收到数字就累加并打印


```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from example_interfaces.msg import Int64
class NumberCounterNode(Node):
    def __init__(self):
        super().__init__("number_counter")
        self.counter_ = 0
        self.number_subscriber_ = self.create_subscription(
            Int64, "number", self.callback_number, 10
        )
        self.get_logger().info("Number Counter has been started.")
    def callback_number(self, msg: Int64):
        self.counter_ += msg.data
        self.get_logger().info("Counter: " + str(self.counter_))
def main(args=None):
    rclpy.init(args=args)
    node = NumberCounterNode()
    rclpy.spin(node)
    rclpy.shutdown()
if __name__ == "__main__":
    main()
```
self.get_logger().info打印日志
订阅创建函数
比较create_publisher(数据类型, topic名, 队列queue_size)
create_subscription(数据类型，topic名，回调函数，队列)
订阅者必须在创建时指定回调函数
发布者可以在任何地方调用
回调里拿到数据，在回调函数里做逻辑
setup.py添加路口后在两个终端分别运行两个节点的可执行文件


四、Topic 调试工具：rqt_graph + ros2 topic 写完代码只是开始，真正省时间的是调试工具。
## 1）用 rqt_graph 看连接关系
同时跑 `number_publisher` 和 `number_counter`，再开：

```
rqt_graph
```

刷新并选 `Nodes/Topics (all)`，你会看到：

- 一个节点发布到 `/number`
    
- 一个节点订阅 `/number`
    

如果你把 topic 名写错了（比如 publisher 写 `numberr`），rqt_graph 会直接显示两条线断开：一个在 `/numberr`，一个在 `/number`，瞬间定位问题。

## 2）ros2 topic：列、查、看、发
常用命令
```
ros2 topic list#列
ros2 topic info /number #查消息
ros2 interface show example_interfaces/masg/Int64#查看接口
ros2 topic echo /number #订阅并打印话题消息
```
也可以手动发送topic，只需运行sub
发ros2 topic pub -r 2.0（表示2Hz） /number example_interfaces/msg/Int64"{data:7}"

运行是改topic名
ros2 run my_py_pkg number_publisher --ros-args -r number:=my_number
两边都要remap
启动两个发布者叠加，消息更加快
## 4）用 bag 录 topic、放 topic（复现现场）
录制：
mkdir -p ~/bags
cd ~/bags
ros2 bag record /number -o bag1
会生成bag1目录（里面是录制数据
回放：
ros2 bag play ~/bags/bag1/
启动接收者会收到数据

# 五、自定义 Topic 消息：什么时候需要？怎么做？

Topic 需要接口（消息类型）。两条原则：

1. **先找现成的**：能用就别造轮子
    
2. **找不到完全合适的**：就做自定义消息（语义更清晰，也更标准）

常见消息集合在common_interfaces
安装接口包 sudo apt install ..
查看消息 ros2 interface show..

## 创建自定义接口包：`my_robot_interfaces`

最佳实践：**单独建一个“只放接口”的包**，不要在里面写节点。
在主包的src下创建接口包ros2 pkg create my_robot_interfaces
删除c的src和include建立msg
修改package.xml在依赖ament_cmake后加
<build_depend>rosidl_default_generators</build_depend>
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group>
修改Cmake在find后，ament前加入
find_package(rosidl_default_generators REQUIRED)
rosidl_generate_interfaces(${PROJECT_NAME}
  # 在这里添加 msg 文件
)
ament_export_dependencies(rosidl_default_runtime)

## 4）写一个自定义消息：HardwareStatus.msg

在消息包里touch文件HardwareStatus.msg
写入字段int64 version
float64 temperature
bool are_motors_ready
string debug_message
在rosidl_generate_interfaces加msg/HardwareStatus.msg
同样build

## 5）在代码里使用自定义消息
以py为例
1）加依赖（依赖是包）
<\depend>my_robot_interfaces<\/depend>
2）代码里import