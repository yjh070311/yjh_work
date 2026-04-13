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
