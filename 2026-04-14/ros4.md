action用于请求要执行很久，服务快速调用不适合
功能：
执行进度反馈
取消执行
目标状态


service处理机器人移动难处理的需求：
1.反馈：走到一半，想知道进度，client不会立即收到service
2.想要取消：没有标准取消机制，得另外学一套取消服务
3.处理多个请求：server一次调用一次返回

工作流程
1.client发送goal（目标）
2.sever立即决定接受还是拒绝
3.接受后开始执行
4.执行完成返回result

额外提供
feedback：执行过程不断反馈进度
cancel：client提出取消，server决定是否停止，并尽力安全停止
goal status：（目标状态）action也是通信，底层是封装好的topics+services，内部状态流转
同名action只能有一个server：有多个提出请求，一个处理请求


接口组成：goal：目标计数target_number+每次间隔delay
，feedback:当前计数current_number
，result:最终到达数字reached_number（如果取消就返回最后的数字）


在接口新增action/目录
创建.action文件

































