# Python 编程基础 - 变量和控制流程

## 📅 时间
2026-04-07 15:15-16:50 (1h35m)

## 🎯 学习目标
- working with  common data structures
- learn arrays,stacks,queues and linked list
- 


## 📝 学习过程
<!-- 在这里记录你的学习过程、遇到的问题、解决方案 -->
算法特征：有限步骤，每一步第一精确，高效和明确
大o符号不会给出具体数字，是操作次数如何扩展
O(1)常数时间复杂度，与输入大小无关，运行时间相同，总是需要恒定的内存空间，即使输入量增加。
O（log n）对数时间复杂度，随输入增加是缓慢增加，不是同步，常见于常数比例缩小
O（n）线性复杂度，复杂度与时间正比增加，所需的空间会随着输入大小的增加成比例增加。
O（n log n） 对数线性时间复杂度，常见于高效排序算法、
O（n方）二次复杂度，嵌套循环
静态数组（static arrays）大小固定，将元素存储在相邻的内存位置，程序运行时不能更改
索引查找更高效，访问静态数组需要O（1）
当你提前知道要存储的元素数量时，可以使用静态数组
增加static的大小需要新创建一个数组，把旧的复制过来，效率低
如果存储元素数量可能不同，你应该使用动态数组（dynamic arrays），py内置数据结构不包含静态的，访问动态也是O（1）
插入元素要重新定位O(n），仍有空间O（1），没有O（n）
用到.insert(),.pop(),.append()
堆栈（stacks）和队列（queues）是线性数据结构，遵循特定规则来添加和移除元素
栈是last-in,first-out(lifo)数据结构
元素会从堆栈顶部添加和移除
添加元素：推送，推到堆栈上
移除元素：弹出
这俩操作为O（1），所需内存量无论堆栈大小不变
队列是first-in,first-out(fifo)结构
元素被添加到队列的后方，然后从队列前端移除







## ✅ 完成内容
<!-- 列出你完成的具体内容 -->


## 💡 收获与思考
<!-- 你的收获、疑问、下一步想法 -->


## 🔗 参考资料
<!-- 用到的教程、文档链接 -->
[Python 认证 |freeCodeCamp.org --- Python Certification | freeCodeCamp.org](https://www.freecodecamp.org/learn/python-v9#lecture-working-with-common-data-structures)

---
*此文件由 yjh 🦞 自动创建 | 最后更新：2026-04-06*
