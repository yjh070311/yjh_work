# launch file
## 启动描述文件：在一个文件里声明要启动哪些节点，给他们配置参数，重命名，remap，namespace等

可以用ros2 launch 包名 launch file名一键启动


专门建一个启动入口包，名字以_bringup结尾
这个包不写节点，只放：
launch/:启动文件（XML/PYTHON）
config/:YAML参数文件


一个命令启动 number 应用里的两个节点：

- `number_publisher`
    
- `number_counter`

XML里写
```XML
<launch>
	<node pkg="my_py_pkg" exec="number_publisher"/>
	<node pkg="my_py_pkg" exec="number_counter"/>
<launch>
```
节点输出都在同一个地方


默认用XML写主launch
需要高级功能写一个小Python launch
把Python launch include进launch里

###  XML 里 include 另一个 launch

XML 里 include 语法很直观：

```
<launch>
	<include file="$(find_pkg_share my_robot_bringup)/launch/number

```












