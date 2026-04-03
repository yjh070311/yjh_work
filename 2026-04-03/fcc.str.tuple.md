## str
str.upper()大写
str.lower
extend添加列表
append添加元素
insert(位置，元素)，插入
remove（值）只会移除首次出现
pop(索引)移除，没有指定移除最后一个
clear（）清空列表
sort（）排序，改变原有
sorted（）输出新的
reverse（）反转
index（元素）寻找该元素索引
## tuple
developer = ('Alice', 34, 'Rust Developer') 
name, \*rest = developer 
print(name) # 'Alice' 
print(rest) # [34, 'Rust Developer']

\*收集剩余元素
count（元素）元素出现次数
index（元素，起始查找位置，末查找位置）=list的
sorted，返回新的列表，可以自定义变量的排序行为
## loop循环
list（range（））创建列表
range只接受整数作为参数
enumerate（os列表，起始值）枚举，返回每个项是包含（计数，元素）的元组
zip（list1，list2），两者组合成一个元素，输出元组组合成的列表
```
words = ['tree', 'sky', 'mountain', 'river', 'cloud', 'sun'] 
def is_long_word(word): 
	return len(word) > 4 
long_words = list(filter(is_long_word, words)) 
print(long_words) # ['mountain', 'river', 'cloud']
```
filte（函数不加括号，可迭代对象）用于从迭代中选择满足特定条件的元素
函数是一个返回布尔值的函数
map函数同理，它对一个可迭代函数应用到其每个元素
sum（，start）函数将迭代函数所有值相加，start是参数不是index，start是求和初始值（不在