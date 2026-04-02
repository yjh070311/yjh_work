# 成本函数

## 1.数据
```
x_train = np.array([1.0, 2.0])           #(以1000平方英尺为单位的面积)
y_train = np.array([300.0, 500.0])           #(以1000刀为单位的价格)
```
## 2.计算成本
```
def compute_cost(x, y, w, b): 
    """
    Computes the cost function for linear regression.
    
    Args:
      x (ndarray (m,)): Data, m examples 
      y (ndarray (m,)): target values
      w,b (scalar)    : model parameters  
    
    Returns
        total_cost (float): The cost of using w,b as the parameters for linear regression
               to fit the data points in x and y
    """
    # number of training examples
    m = x.shape[0] 
    
    cost_sum = 0 
    for i in range(m): 
        f_wb = w * x[i] + b   
        cost = (f_wb - y[i]) ** 2  
        cost_sum = cost_sum + cost  
    total_cost = (1 / (2 * m)) * cost_sum  

    return total_cost
```
与lab1的f(w,b)大致相同，多了计算cost并结算所有样本平方误差cost_sum