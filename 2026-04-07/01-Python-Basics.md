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
队列后方添加元素叫做enqueue，前端移除叫dequeue，O（1）
linked list（链表）是线性数据结构，每个节点连接序列的下一个节点
常用于实现queue，stack和deque，实现关键的图算法，如深度广度优先搜索

单链表（singly ll）每个节点通过存储对其的引用与下一个相连
搜索只能向前
每个节点单一引用从头到尾遍历链表
头节点A开始，头节点通常是唯一可以直接访问的
尾节点用于判断合适到达链表终点

链表没有固定大小，根据需要调整
可以插入节点
不需按特定顺序存储节点，由连接决定
在开头插入时O（1),只需更新对头的引用和下个节点的连接
在末尾插入要到达末尾，所以是O（n）
插入中间空间O（1）不依赖链表大小
删除类似，删除节点不需要额外内存

双链表（doubly ll）每个节点两个引用，一个指向下一个，一个指向前一个
可以双向遍历
但也要更多内存，每个节点存储俩
插入删除区别是要更新两个引用

抽象数据类型（ADT）是一种数据类型的概念性表示，包括可以对数据执行哪些操作以及数据的属性
描述可执哪些操作
map（ADT）具体高效管理键值并操作
在映射中，每个值都关联到一个特定的键
键必须唯一，值可以重复。
还定义了重要操作，如插入键值对、获取与键对应的值、更新键对应的值、删除键值对以及检查映射中是否存在键

哈希映射（hash map)映射ADT的具体实现
使用哈希函数为每个元素生成哈希值，基于键生成，其对应数组是存储键值对的数据结构
两个键得到相同索引（碰撞），使用链式策略，将索引元素存储在链表中
“开放寻址”用搜索序列搜索下一个可用索引
hash_table = [None] * 8

def set_item(key, value):
    index = hash(key) % len(hash_table)
    # 如果该位置已被占用（碰撞发生）
    while hash_table[index] is not None:
        # 线性探测：找下一个空位
        index = (index + 1) % len(hash_table)
    hash_table[index] = (key, value)
一般O（1）
py的词典就是hash map

集合（sets）也是动态

```py
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    sorted_list = []
    i = 0
    j = 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            sorted_list.append(left[i])
            i += 1
        else:
            sorted_list.append(right[j])
            j += 1

    sorted_list.extend(left[i:])
    sorted_list.extend(right[j:])

    return sorted_list
```
merge_sort 递归调用自己，直到单个有序直接输出
每次循环把小的轮流放入
left,right有序
先拆分再合并
一分为二O（logn），合并遍历O（n）
新的sorted列表，空间O（n）

动态规划：
重叠子问题，最优子结构（最优解包含子问题的最优解）
爬楼梯用递归复杂度呈指数级增长

图（graph）适用于表示对象之间联系的数据结构
由节点和边组成
边（edge）是节点之间的连接
无向图（undirected graph）没有特定方向，反之，有向图
顶点标记图（vertex labeled graph),除数据外，还关联tag，用于识别节点，可视化表示他们
循环图（cyclic graph）沿着edge走会回到初始节点
edge labeled graph edge上有tag
加权图（weighted graph）edge上的tag用于算数运算，有些权重高，这些权重代表了edge的cost
有向无环图，有方向没循环
断开图（disconnect）有没有edge连接的


广度优先搜索（BFS-breadth first search）：图的下一层之前访问所以相邻节点
可以用于在无加权图找最短路径，因为分析所有节点，用队列（fifo）访问过的节点先出
在内存中存储队列，有大量节点，空间要求大
a分bc各自分defg
1.访问a队列a
2.访问abc队列bc
3.访问abcde队列bcde
4.访问anbcdefg队列defg
5.逐个去队列的defg查是否有其未访问的子节点
一层层访问

深度优先搜索（DFS-depth first search）：深入跟踪每个分支再回溯












## ✅ 完成内容
<!-- 列出你完成的具体内容 -->
学了py的算法基础，图，抽象数据类型

## 💡 收获与思考
<!-- 你的收获、疑问、下一步想法 -->


## 🔗 参考资料
<!-- 用到的教程、文档链接 -->
[Python 认证 |freeCodeCamp.org --- Python Certification | freeCodeCamp.org](https://www.freecodecamp.org/learn/python-v9#lecture-working-with-common-data-structures)

---
*此文件由 yjh 🦞 自动创建 | 最后更新：2026-04-06*
