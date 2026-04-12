ros2 service call  /服务名称（具体作用，类似网址，用于节点间交流），创建具体服务时（接口类型，服务名称，处理函数即回调函数，回调函数同一节点编写）
 数据格式（服务接口，ros2 interfacdee show ）
创建请求对象满足接口格式，响应函数同样，开始要导入接口，就不需自定义了
请求数据（具体参数）
 服务用于两个节点之间的通信
请求/响应对象携带具体要处理的数据
请求响应对象是独立的，只不过要满足接口要求，服务是同一个

Action 可以先理解成：  
**“更适合耗时任务的 Service”**。
有执行过程中的反馈，可以取消，适合交互性
action接口内容分为goal,result,feedback
发送一个goal希望做的事：ros2 action send_goal 
<action_name> <action_interface> "<goal_in_json>"

topic service action 都是通信
parameter是在启动节点给他不同配置
参数属于节点

launch file 启动文件：一次启动多个节点以及他们的参数

建立工作空间
建包
写节点
构建配置
build编译，将源代码转换成可执行程序   
从.py.cpp到.exe.bin，代码修改后
source执行脚本，每次新终端用来加载环境变量，让终端认识节点，有环境变量执行结束后才有输出，不需要+x权限，
setup.bash一般在install里，手动source一次
为了每次开终端都自动生效，把它写进 `~/.bashrc`，而且**顺序非常重要**：先 source 全局 ROS，再 source 工作空间。

```
source /opt/ros/jazzy/setup.bash
source ~/ros2_ws/install/setup.bash
```

colcon build 
colcon是ros构建系统，哪怕现在没有任何包，也可以 build 一下，让工作空间生成必要结构

包是应用的子模块
创建包声明依赖
py生成结构大致是
- `my_py_pkg/`（里面还有一层同名目录，用来放 python 代码）写代码的地方
    
- `package.xml`（所有包都必须有）
    
- `setup.py`（Python 节点的“安装/入口”在这里配置，安装说明书）
-C++ 包会包含：

- `CMakeLists.txt`（编译/链接/安装规则）
    
- `src/`（放 .cpp）
    
- `include/`（可选，放 .hpp）
    
- `package.xml`

colcon build --packages--select 包名
只构建一个包
## 1）创建文件并赋予可执行权限

进入 Python 包的代码目录：

```
cd ~/ros2_ws/src/my_py_pkg/my_py_pkg/
touch my_first_node.py
chmod +x my_first_node.py
```
## 2）最小节点模板：能启动、能 spin

把下面代码写进 `my_first_node.py`：
```

#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
class MyCustomNode(Node):
    def __init__(self):
        super().__init__('my_node_name')
        self.get_logger().info("Hello World")
def main(args=None):
    rclpy.init(args=args)
    node = MyCustomNode()
    rclpy.spin(node)
    rclpy.shutdown()
if __name__ == '__main__':
    main()
```
初始化节点
创建节点对象node
spin处理回调
shutdown关闭通信
## 3）为什么 Python 也要 build？
可以执行脚本
但最好安装在工作空间里
这样才能：用run启动，可以被launch file 管理
怎样安装在工作空间可被run：同时要在setup.py配置可执行入口
找到 `entry_points`，在 `console_scripts` 里加入：

```
entry_points={
    'console_scripts': [
        'test_node = my_py_pkg.my_first_node:main',
    ],
},
```
找到entry_points,即进入配置加入，语法是
可执行名（终端可以直接输入的命令）=包名.文件名.函数名
