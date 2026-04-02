## gradient descent
want min J(w,b)
适用于其他两个参数以上的函数
  min J(w1,w2,wn,b)
  initial guess(0,0)
  continue change until near a minimum
  环顾四周寻找下降值
  
  tmp_w=w-adj/dw
  a:learninig rate控制下坡速度大小
  tmp_b=b-adj/db
  w=tmp_w
  b=tmp_b
  导数在min右边positive，左边negative都契合趋势
## squared erroe cost function for the linear regression model with gradient descent 

最终
w=w-α/m×(f(w,b)-y)×x
b同理没最后的x，由微积分得到

batch gradient decent每一步