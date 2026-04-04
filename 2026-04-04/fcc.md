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
(pdb) whatis 查看元素类型
(pdb) continue 继续运行代码
3.ide工具
f5继续
f10步进
f11进入函数
*exception handling*
异常处理是指在程序执行过程中捕捉和管理错误的过程，以确保你的代码不会意外崩溃。
```py
try:
    x = 10 / 0
except ZeroDivisionError:
    print("You can't divide by zero!")
```
try:
    # 可能发生异常的代码
    pass
except 异常类型1:
    # 发生异常类型1时的处理代码
    pass
except 异常类型2:
    # 发生异常类型2时的处理代码
    pass
else:
    # 没有发生异常时执行的代码
    pass
finally:
    # 无论是否发生异常，都会执行的代码（如关闭文件、释放资源）
    pass
可以把except定义成元组，在一个字句捕捉多个except
更简洁，作用跟分开一样

raise语句可以设置内置异常或创建自定义错误消息
```py
def check_age(age):
    if age < 0:
        raise ValueError('Age cannot be negative')
    return age

try:
    check_age(-5)
except ValueError as e:
    print(f'Error: {e}') # Error: Age cannot be negative
```
raise用于有特殊要求的地方，正常程序无法找出，这个错误本来无法得知，但raise提供条件让这个错误可以被定义并找出。后续用在except的错误排查中
```py
def process_data(data):
    try:
        result = int(data)
        return result * 2
    except ValueError:
        print('Logging: Invalid data received')
        raise  # Re-raises the same ValueError

try:
    process_data('abc')
except ValueError:
    print('Handled at higher level')
```
重新抛出（re-raise)机制，
函数作用
1.记录日志（底层处理）
2.异常上传，能够让主except进行异常处理
这样你就可以在记录错误或清理错误的同时，继续在呼叫堆栈中传播。
```py
class InsufficientFundsError(Exception):
    def __init__(self, balance, amount):
        self.balance = balance
        self.amount = amount
        super().__init__(f'Insufficient funds: ${balance} available, ${amount} requested')

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(balance, amount)
    return balance - amount

try:
    new_balance = withdraw(100, 150)
except InsufficientFundsError as e:
    print(f'Transaction failed: {e}')
```
可以通过定义自己的例外类来创建和提出自定义异常、
用from来连接exception
raise NewError()：想完全隐藏底层细节
raise NewError() from None：不想暴露内部实现（你的代码中对 FileNotFoundError 的处理）
raise NewError() from e：调试时想看到完整原因（你的代码中对 ValueError 的处理）
assert 条件，错误语句
类似，有条件的错误
raise可以让你的代码更可预测、更容易调试，同时给用户清晰反馈出了什么问题

## class
构建一个类来定义共享行为，然后创建使用这些行为的对象。
class的基本语法：
```python
class ClassName:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def sample_method(self):               
        print(self.name.upper())
```
1.`__init__` 的第一个参数总是指向被创建或使用的具体对象。按照惯例，这个参数被命名为 `self`，但从技术上讲，你可以使用任何名字。`Self` 允许你访问对象自身的属性和方法。
2.`self.name = 名称` ，`self.age = age`，是这些对象将拥有的属性。
3.`def sample_method（self）：` 是每个创建对象可以调用的方法。
4.`print（self.name.upper（））` 是 `sample_method` 方法的操作，在这里它打印的是大写字母的名称。

类创建对象：
```python
object_1 = ClassName(attribute_1, attribute_2)
```
调用每个对象中定义的类中的任何方法：
```python
object_1.method_name()
```
类是模版。对象是模版创建的具体实例


\_\_是特殊方法dunder（double underscore）
创建自己的类时，很多字符串数字的特殊方法不会自动处理，因为输出的是一个类，不是一个理想对象
要自定义特殊方法
比如
```python
def __len__(self):
       return self.pages
```
才能使用len（）

有时候，你可能直到程序运行时才知道需要哪些属性，需要动态处理属性，可以用变量名访问、修改、检查甚至删除属性，而不是作为代码中的固定名称。这让你的程序能够灵活地响应不同的数据或用户输入。
getattr（对象，属性名，默认值）读取属性，如果不存在该属性，除非你提供默认值，否则会触发 `AttributeError`
dir（）返回对象上所有属性名称的列表
os.特殊方法也是属性
setattr(对象，属性，改变值)，动态创建新属性或更新已有属性
hasattr（对象，属性）检查属性是否存在
delattr（对象，属性）动态移除一个数

## oop
encapsulation封装
inheritance继承
polymorphism多态性
abstraction抽象

## 封装
1.封装是将对象的属性和方法捆绑到一个单元——类中。
2.通过封装，你可以将对象的内部状态隐藏在一组简单的公共方法和属性后面，这些方法就像门一样。这些门后面是私有属性和方法，它们控制数据的变化以及谁能看到它。
3.属性和方法前加上一个下划线意味着它们是为内部使用而设计的。任何人都不应直接从类外部访问它们，因为这违背了封装原则，可能导致错误。
4.若要访问得在封装时自定义方法

## getter，setter
Getter 和 setter 是可以控制类属性如何访问和修改的方法。用 getter 你获取一个值，而用 setter 则是设置一个值。
用property而不是方法：这主要是关于可读性和惯例。它们让你的代码`更简洁、更易读`。作用类似方法，工作方式类似数据
装饰器是一个函数，可以修改其他函数或类的功能，而不更改它们的原始代码
创建属性时，你需要定义一个方法，并在上面放置 `@property` 装饰器。这会将方法变成属性，因此在内部调用装饰化方法时可以像属性一样访问它。
注意我们在类中用 `_radius` 而不是radius。下划线是 Python 常用的一种惯例，用来表示属性是私有的。换句话说，它表示它是内部使用的，不应直接从类外访问。
创建一个设置器来创建半径，你需要定义另一个同名的方法，并在其上方使用 `@<property_name>.setter`：
其实去掉装饰器就是get_radius,set_radius
getter是只读，将参数输入私有属性，只能用装饰器去getter读它，而且赋值左边不能有不是私有属性的
setter是写，左边有不是私有属性的赋值就触发，用来检查新赋值是否可用