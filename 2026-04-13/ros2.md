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
        msg = Int64()
        msg.data = self.number_
        self.number_publisher_.publish(msg)
```
- `create_publisher(数据类型, topic名, 队列queue_size)`