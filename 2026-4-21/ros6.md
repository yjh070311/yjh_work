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

 <launch>
	<include file="$(find_pkg_share my_robot_bringup)/launch/number_app.launch.py"/>
<launch>



## 7. Launch File 里能配置什么？（核心三件套）

你在命令行 `ros2 run` 里能做的事情，launch 基本都能做。最常用的是三类：

1. **重命名节点**（避免冲突）
    
2. **Remap 通信名**（topic/service/action 重映射）
    
3. **加载参数**（单个 param 或 YAML 文件）




XML 写法是在 node 内部加 `<remap>`：

```
<node pkg="my_cpp_pkg" exec="number_counter">
	 <remap from="/number" to="/my_number" />
</node>
```

如果你启动两个 publisher 和一个 counter，并希望它们都用同一个新 topic，就把同样的 remap 写到每个相关节点里。


YAML参数文件：
把参数放到config/*.yaml,launch只负责加载
**参数文件也要安装到package share目录里，否则运行找不到路径

XML加载YAML的写法：
```
<param from="$find-pkg-share my_robot-bringup)/config/number_params.yaml"/>
```

namespace(命名空间)：把两套同样系统隔离开
同时启动两台同类型/启动两份同构节点

namespace作用到topic名写number无前导/，会自动随namespace变成/abc/number
/number是全局名字，不会受namespace影响

















