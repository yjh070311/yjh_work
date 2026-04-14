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

