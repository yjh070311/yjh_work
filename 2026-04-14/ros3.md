`self.` 用于访问**实例变量**和**实例方法**，而**局部变量**不需要

client发亲请求
server处理请求并回响应
接口两部分：request和response
同一个service只能有一个server
topic适合：持续数据流，一直检查数据
service适合：按需动作，快速计算，有回应不断调整（启停电机，触发复位）

# 二、先做自定义 Service 接口：ResetCounter
沿用上一篇功能，发reset为20，counter变成20
request：int64 reset_value
response:bool success+string message

在interfaces包多创建srv
interfaces/srv/
创建接口文件ResetCounter.py
写入定义横杠分割
int64 reset_value
\-\-
bool success
string message

在 `my_robot_interfaces/CMakeLists.txt` 的 `rosidl_generate_interfaces(...)` 里加一行（不要逗号）：
```python
rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/HardwareStatus.msg"
  "srv/ResetCounter.srv"
)
```
interface show验证接口存在
# 三、写 Service Server：
## 1）Python 服务端：导入接口 + create_service + callback

先在 `my_py_pkg/package.xml` 里加依赖：
<\depend>my_robot_interfaces</depend>
再节点文件导入
from my_robot_interfaces.srv import ResetCounter
创建server
```
self.reset_counter_service_=self.create_service(
	ResetCounter,
	"reset_counter",'self.callback_reset_counter
		)  #（接口，“服务名称”，回调函数）
```
### 最小版 callback（能跑通）
```python
def callback_reset_counter(self, request: ResetCounter.Request,
                           response: ResetCounter.Response):
    self.counter_ = request.reset_value
    self.get_logger().info("Reset counter to " + str(self.counter_))
    response.success = True
    response.message = "Success"
    return response
```
加校验（reset必须大于等于0，且不能大于当前counter）
callback调取参数（self,request:接口.Request,response:接口.Response
```python
def callback_reset_counter(self,request:ResetCounter.Request,response:ResetCounter.Response):
	if request.value<0:
		response.success=False
		response.message="不能复位到负数"
	elif request.resert_value>self.counter_:
	
	else:
		self.ounter_=request.reset_value
		self.get_logger().info("Reset counter to"+str(self.counter_))
		response.success=True
		response.message="success"
	return response
	
```

## 2）C++ 服务端：create_service + request/response shared_ptr

C++ 版本做同样的事：

- `package.xml` 增加 `<depend>my_robot_interfaces</depend>`
    
- include：`#include "my_robot_interfaces/srv/reset_counter.hpp"`
    
- 成员：`rclcpp::Service<ResetCounter>::SharedPtr reset_counter_service_;`
    
- 构造函数里 `create_service<ResetCounter>(..., bind(... _1, _2))`
    
- callback 里用 `request->reset_value`、`response->success = ...`
    

C++ callback 一般返回 `void`，不需要 return response（因为你直接填 response 指针）。

并且记得 `CMakeLists.txt` 里：

```
find_package(my_robot_interfaces REQUIRED)
```

```
ament_target_dependencies(number_counter ... my_robot_interfaces)
```

不然会出现 include 找不到的编译错误

# 四、写 Service Client：reset_counter_client
只负责调用服务的client，验证服务是否工作
创建服务之类的变量名没有要求
创建client
self.client_=self.create_client(ResetCounter,"reset_counter")
写方法发送请求：
```python
def call_reset_sounter(self,value):
	#等待请求
	while not self.client_.wait_for_service(1.0):
		self.get_logger().warn("waiting for service")
	#创建请求
	request=ResetCounter.Request()
	request.reset_value=value
	#异步回调（立即返回，不阻塞）
	future=self.client_.call_async(request)
	#注册回调，响应到达自动调用
	future.add_done_callback(self.callback_reset_counter_response)
	#函数立即返回，节点继续spin()
```

response回调
```python
def callback_reset_counter_response(self, future):
    response = future.result()
    self.get_logger().info("Success flag: " + str(response.success))
    self.get_logger().info("Message: " + str(response.message))
```
为什么要用 callback？  
因为如果你在同一个线程里“阻塞等待响应”，节点就无法 spin，响应也处理不了，容易死锁。异步 + 回调是最安全的结构。