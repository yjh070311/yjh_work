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
    
- `setup.py`（Python 节点的“安装/入口”在这里配置）
-C++ 包会包含：

- `CMakeLists.txt`（编译/链接/安装规则）
    
- `src/`（放 .cpp）
    
- `include/`（可选，放 .hpp）
    
- `package.xml`

colcon build --packages--select 包名
只构建一个包
