# launch file
## 启动描述文件：在一个文件里声明要启动哪些节点，给他们配置参数，重命名，remap，namespace等

可以用ros2 launch 包名 launch file名一键启动


专门建一个启动入口包，名字以_bringup结尾
这个包不写节点，只放：
launch/:启动文件（XML/PYTHON）
config/:YMAMLc