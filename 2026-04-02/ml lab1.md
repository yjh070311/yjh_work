# 调整w，b，线性回归的模型函数

## 1.数据集
```
x_train = np.array([1.0, 2.0])
y_train = np.array([300.0, 500.0])
print(f"x_train = {x_train}")
print(f"y_train = {y_train}")
```
np.array，[]内是元素个数，()内是维度个数，此处是两个元素一个维度，若是一维数组（此处），则输出（2，）表示两个元素，若是二维数组，要有两个中括号，则表示几个维度，每个维度有多少元素，总是（第一个括号内的中括号个数，第二个【】的特征数
## 2.训练例子的数量m

```
# m is the number of training examples
print(f"x_train.shape: {x_train.shape}")
m = x_train.shape[0]
print(f"Number of training examples is: {m}")
```
numpy数组.shape参数，x_train.shape,每个元素表示一个维度，例如（500,4）表示500个样本，每个样本四个特征，
因为x_train是数组也可以用len（）函数计算m
## 3.训练实例
```
i = 0 # Change this to 1 to see (x^1, y^1)
x_i = x_train[i]
y_i = y_train[i]
print(f"(x^({i}), y^({i})) = ({x_i}, {y_i})")
```
## 4.绘制数据
```
# Plot the data points
plt.scatter(x_train, y_train, marker='x', c='r')
# Set the title
plt.title("Housing Prices")
# Set the y-axis label
plt.ylabel('Price (in 1000s of dollars)')
# Set the x-axis label
plt.xlabel('Size (1000 sqft)')
plt.show()
```
plt.scatter :（x,y,maker形状，c颜色)
plt.title("标题")
plt.x/ylabel('x/y轴标签‘)
plt.show显示

## 5.模型功能
```
w = 100
b = 100
print(f"w: {w}")
print(f"b: {b}")
```
初始化w，b
```
def compute_model_output(x, w, b):
    """
    Computes the prediction of a linear model
    Args:
      x (ndarray (m,)): Data, m examples 
      w,b (scalar)    : model parameters  
    Returns
      y (ndarray (m,)): target values
    """
    m = x.shape[0]
    f_wb = np.zeros(m)
    for i in range(m):
        f_wb[i] = w * x[i] + b
        
    return f_wb
```
此计算给定w，b线性模型的预测值
m获取样本训练的数量
