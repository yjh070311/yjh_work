1.数据集
```
x_train = np.array([1.0, 2.0])
y_train = np.array([300.0, 500.0])
print(f"x_train = {x_train}")
print(f"y_train = {y_train}")
```
2.训练例子的数量m

```
# m is the number of training examples
print(f"x_train.shape: {x_train.shape}")
m = x_train.shape[0]
print(f"Number of training examples is: {m}")
```
numpy数组.shape参数，x_train.shape,每个元素表示一个维度，例如（500,4）表示5行4列
这里