---
url: https://mp.weixin.qq.com/s/pTYD_vmGjW_68_sjzuQPPw
title: untitled
author: 机器人技术笔记
aliases: 
 - 
date: 2026-04-05 19:26:28
tags:

banner: "https://mmbiz.qpic.cn/mmbiz_jpg/HXJUciaxf0Ulo1GDvEiarvPMnVtkOZYnNSGiarQTTu20JuibiaUHJibISrAq2Xeicor7pHTVQSGdVaA5UTYhSWTCdhLzxCQKlic7bZ5rJHOOdU3NwTs/0?wx_fmt=jpeg"
banner_icon: 🔖
---
在强化学习（Reinforcement Learning, RL）里，大家常把注意力放在价值函数（Value Function）、策略（Policy）、回报（Return）这些概念上，但真正把算法跑起来的底层载体，往往是深度学习框架。这里最关键的一层，不是 “某个具体 RL 算法”，而是张量（Tensor）、自动求导（Automatic Differentiation）、神经网络（Neural Network, NN）、损失函数（Loss Function）和优化器（Optimizer）如何协同工作。相关内容的组织顺序也很清楚：先讲张量，再讲梯度，然后进入网络模块、损失与优化，最后过渡到 TensorBoard 监控和 Ignite 训练循环抽象。

如果把强化学习看成一个 “会不断试错的决策系统”，那么 PyTorch 更像它背后的数值引擎。前面两部分内容解决的是 “智能体（Agent）如何与环境（Environment）交互”，这一部分解决的则是 “交互产生的数据，最终如何变成可训练的模型参数”。也就是说，强化学习不是跳过深度学习直接存在的，很多时候它只是把监督学习里熟悉的训练机制，搬到了一个由状态、动作和奖励组成的新问题里。

## 为什么一开始学的不是模型，而是张量？

很多初学者一上来就想看网络结构，想知道几层全连接（Fully Connected Layer）够不够，卷积层（Convolution Layer）要不要加，激活函数（Activation Function）选什么。但在 PyTorch 里，更底层、更通用的单位其实是张量。

张量本质上可以理解为 “可被统一计算系统处理的多维数组”。标量是一阶最简单的情况，向量、矩阵以及更高维数据，都只是张量在不同维度下的表现形式。PyTorch 之所以把它放在最前面，是因为后面网络中的输入、参数、输出、损失，本质上全都是张量对象，只不过它们承担的角色不同。

看一个很小的例子就够了：

```
import torch
import numpy as np
a = torch.zeros(3, 4)
print(a)
n = np.zeros((3, 2), dtype=np.float32)
b = torch.tensor(n)
print(b)

```

```
tensor([[0., 0., 0., 0.],
        [0., 0., 0., 0.],
        [0., 0., 0., 0.]])
tensor([[0., 0.],
        [0., 0.],
        [0., 0.]])

```

这个例子看起来很普通，但它说明了一件重要的事：PyTorch 不只是 “存数字”，它是在为后续计算图（Computation Graph）和梯度传播准备统一的数据表示。NumPy 默认常常是 `float64`，而深度学习里更常用的是 `float32`，因为双精度通常没有必要，反而会带来额外的内存和性能开销。

## 张量计算和普通数值计算，差别到底在哪？

如果只是拿张量做加减乘除，它和一般数值库看起来没有本质区别。真正的分水岭在于：深度学习里的运算不是 “算完就结束”，而是 “算完之后还要知道它是怎么算出来的”。也就是说，普通数值计算只关心结果，深度学习训练还关心结果对参数的依赖关系。

这就引出了动态图（Dynamic Graph）的意义。PyTorch 的一个核心特点，就是在张量运算发生时，自动把依赖关系记录下来。你可以把它理解成：前向传播（Forward Pass）不仅产生输出，也顺手搭建出一张 “误差该往哪里传” 的路径图。等到调用反向传播（Backpropagation）时，系统就沿着这条图自动把梯度算出来。

这种方式和早期那种先定义完整静态图（Static Graph）再统一执行的思路很不一样。动态图更像普通 Python 编程，哪里执行了，哪里就形成一段可求导的结构，所以表达会更自然。动态图的吸引力之一，就是可以用更 “Pythonic” 的方式表达模型和变换过程。

## 梯度不是魔法，它只是 “误差往回传” 的规则

当我们说某个模型 “会学习”，其实意思并不是模型自己突然变聪明了，而是损失函数定义了 “错多少”，梯度给出了 “往哪里改”，优化器负责 “真的去改”。这三者组合起来，才构成训练闭环，其中有几个最关键的属性：`grad`、`is_leaf` 和 `requires_grad`。

这里最容易忽略的是 `requires_grad`。默认情况下，张量并不会自动参与梯度计算，只有显式指定后，它才会被视为需要求导的对象。另一方面，`is_leaf` 用来区分一个张量是用户直接创建的，还是由其他张量运算得到的中间结果。这个区分很重要，因为训练时真正要更新的，通常是最底层的参数张量，而不是中间变量。

看一个极简例子会更直观：

```
import torch
v1 = torch.tensor([1.0, 1.0], requires_grad=True)
v2 = torch.tensor([2.0, 2.0])
v_sum = v1 + v2
v_res = (v_sum * 2).sum()
v_res.backward()
print(v1.grad)

```

```
tensor([2., 2.])

```

这段代码里，`v1` 需要梯度，`v2` 不需要。经过加法、乘法和求和之后，`v_res` 变成了一个标量张量；当调用 `backward()` 时，PyTorch 会自动根据前面的运算链条，把最终结果对 `v1` 的导数算出来。所以，梯度机制不是凭空存在的，而是依附在张量运算过程中逐步建立起来的。

## 神经网络到底是什么？本质上是一堆可组合模块

理解了张量和梯度，神经网络其实就不再神秘了。它不是一个黑盒魔法，而是一系列可组合的函数模块。每一层接受张量输入，做一次变换，再把结果交给下一层；只要整个链条可微，损失函数就能通过反向传播一路把更新信号传回去。

先看一个最小的两层网络：

```
import torch
import torch.nn as nn
net = nn.Sequential(
    nn.Linear(4, 16),
    nn.ReLU(),
    nn.Linear(16, 2)
)
x = torch.randn(3, 4)
y = net(x)
print(y.shape)   # [3, 2]

```

```
torch.Size([3, 2])

```

这段代码里没有任何强化学习特有的东西，但它已经足够代表后面大量 RL 模型的基本形态了。状态（State）会被编码成输入张量，网络输出可能是动作值（Action Value）、策略分布，或者状态价值（State Value）。换句话说，强化学习里看起来复杂的网络，底层依旧是这些标准模块的堆叠和变体。

Pytorch 还有 “自定义层” 这一特性，这一点很重要。因为标准层解决的是通用问题，但研究和工程很快就会遇到特殊需求：有时你想定制前向逻辑，有时想把某种约束直接写进网络层里。PyTorch 的价值不只是现成模块多，更关键的是它允许你在同一套自动求导体系下，自由扩展自己的结构。

## 损失函数负责定义目标，优化器负责推动参数前进

很多人会把损失函数和优化器混在一起理解，其实两者解决的是不同问题。损失函数回答的是 “模型现在偏离目标有多远”，优化器回答的是 “既然知道偏差了，参数应该怎么更新”。这两部分既紧密相关，又各司其职。

训练循环的核心其实一直很简单：拿一批数据，做前向传播，算损失，做反向传播，然后调用优化器更新参数。无论后面学的是 DQN、Policy Gradient，还是 Actor-Critic，这个骨架都没有变化，变化的只是数据从哪里来，损失怎么定义，目标值如何构造。

最经典的 PyTorch 训练骨架就是下面这样：

```
optimizer.zero_grad()
y_pred = net(x)
loss = loss_fn(y_pred, y_true)
loss.backward()
optimizer.step()

```

别看它短，这几行几乎概括了后面整个深度强化学习训练过程的最底层机制。你可以把它理解成一个 “参数被误差反复推着走” 的闭环系统。强化学习并没有改写这套机制，它只是把监督学习里相对固定的标签，换成了由环境交互不断产生、往往还带噪声的训练目标。

## 为什么训练不能只看最终结果，还要看过程？

只会写训练代码是不够的，因为训练本身就是一个动态过程。模型可能在学，也可能表面上 loss 在降，实际已经出现梯度异常、模式崩塌，或者训练目标定义有问题。如果你只在最后看一个数字，很可能很晚才发现系统从一开始就跑偏了。监控训练过程在工程上常用的工具有很多，如 TensorBoard 等。

这点和控制系统调试很像。你不会只在实验结束后判断成败，而是会持续观察中间量，看系统是不是朝合理方向演化。深度学习训练里同样如此：loss 曲线、奖励曲线、梯度统计、样本图像，这些都不是附属品，而是判断训练是否健康的必要信息。比如常见的 Atari 图像上的生成对抗网络（Generative Adversarial Network, GAN），就是因为这类任务很适合观察训练过程，而不是只看最终指标。

##  Ignite ——更高层的训练工具

当模型很小时，手写训练循环完全没问题。但只要你开始加日志、加平滑统计、加验证、加定时器、加 TensorBoard 记录，训练代码就会迅速膨胀。PyTorch Ignite 的定位，不是替代 PyTorch，而是在训练循环这一层提供更高阶的组织方式。Ignite 通过 `Engine` 和 `Events` 这样的概念，来简化训练循环的编写和扩展。

最核心的想法其实很简单：把 “每个 batch 要做什么” 写成一个处理函数，然后把 “什么时候记录日志、什么时候验证、什么时候调用别的逻辑” 交给事件机制处理。这样一来，训练主体逻辑和周边工程逻辑就不会混在一起。下面的最简示意代码，也正是围绕 `Engine(training)` 和 `engine.run(data)` 展开的。

```
from ignite.engine import Engine
def train_step(engine, batch):
    optimizer.zero_grad()
    x, y = batch
    y_pred = net(x)
    loss = loss_fn(y_pred, y)
    loss.backward()
    optimizer.step()
    return loss.item()
trainer = Engine(train_step)
trainer.run(data_loader)

```

这段代码相比原始 PyTorch 并没有改变训练本质，但它把训练循环包装成了一个更容易扩展的结构。后面如果你想每隔若干步记录一次 loss，或者每个 epoch 结束后跑一次验证集，就不需要把所有逻辑硬塞在 `for` 循环里，而是可以挂接到事件系统上。Ignite 的价值主要就在 “让训练循环更干净、更可扩展”。

## 这一部分对后面强化学习真正重要的地方是什么？

真正关键的，不是记住多少 PyTorch API，而是建立一套统一视角：训练一个深度模型，本质上是在张量系统上定义前向计算，通过自动求导得到梯度，再借助优化器不断更新参数。神经网络只是函数表达形式，训练循环才是让它真正 “学起来” 的过程。从张量、梯度、网络构件，到损失、优化、训练监控，再到更高层训练循环抽象，这些共同构成了后续强化学习实现的基础。

换句话说，后面你再看到 DQN、策略梯度（Policy Gradient）、A2C 之类方法时，不要把它们当成完全陌生的新东西。它们当然有各自的方法论，但底层仍然是同一套深度学习训练骨架：输入张量、网络输出、定义损失、反向传播、参数更新，只不过训练数据不再来自固定标注集，而是来自智能体与环境交互的轨迹。把这一层吃透，后面的 RL 代码会好懂很多。

参考文献

Lapan, Maxim. _Deep Reinforcement Learning Hands-On: A Practical and Easy-to-Follow Guide to RL from Q-Learning and DQNs to PPO and RLHF_. Packt Publishing, 2024.