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
