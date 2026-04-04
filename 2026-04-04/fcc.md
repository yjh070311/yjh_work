## dic
dict([一系列元组])
.get(key,default)如果查找不到返回default
.keys,.values分别返回包含字典中所有键和值的视图对象
视图对象只是一种无需创建独立数据副本即可查看词典内容的方法。
.items()方法返回一个视图对象，包含字典中所有键值对，包括键和值
.clear消除
.pop移除你指定为第一个参数的键值对，并返回其值。如果key不存在，它会返回你指定的第二个参数的默认值
.update（新dic）有覆盖，无加上，
pizza.update({ 'price': 15, 'total_time': 25 })
for product in enumerate(products):
    print(product)
返回（index，product）元组
enu（products.items()，可加参数表示index起始值)
返回索引＋元组

## sets
大括号用，分割
无序不存储重复数值
.add添加
.remove/.dicard移除
.clear
可以执行常见的数学集合运算
.issubset（）/.issuperset（）方法分别检查一个集合是另一个集合的子集还是超集。
.isdisjoint(）是否互斥没有任何共同元素
并算符 `|` 返回一个包含两个集合所有元素的新集合：
交算符  &  返回一个仅包含这些集合共有元素的新集合：
差分算子 `——` 返回一个包含第一集合中其他集合中不存在元素的新集合。在这个例子中，数字 1 和 5 属于 my_set但不属于 your_set`：
对称差分算符 `^` 返回一个新集合，其中元素属于第一个集合或第二个集合，但不包含两个集合


if __name__ == '__main__': 
直接运行__name__值为__main__
作为模块__name__值为文件名
这个条件判断的作用是：**只有当脚本被直接运行时，才执行里面的代码；如果被导入，则不执行。**
可以用来在当前文件执行测试代码，当我需要把此文件的函数用到其他文件中时，测试代码不会发生

## error
syntaxerror 语法， Python 不理解你的代码，或者你的逻辑与你处理的数据不匹配时。
nameerror  当 Python 找不到该名称的变量时
typeerror  试对不兼容的数据类型执行操作
indexerror 试图访问列表中不存在的索引
attributeerror 尝试使用该数据类型不存在的方法或属性

*调试方法*
1.print
2.模块pdb.set_trace
(pdb) wha