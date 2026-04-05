---
url: https://mp.weixin.qq.com/s/DElHVYF_WkKJfOLg7rNRgg
title: untitled
author: 机器人技术笔记
aliases:
  - 
date: 2026-04-05 19:25:35
tags:
banner: https://mmbiz.qpic.cn/mmbiz_jpg/HXJUciaxf0UlWFZvibdicxCuHnvHRgvUDyicY1FMYy1So1B7PBIXHczBtytrIWM60Fs0TiaF0Kg6iciaDoO9znribiaEgZVl7CDGfp2wO6xnObUicTaGA/0?wx_fmt=jpeg
banner_icon: 🔖
---
# 很多人学强化学习（Reinforcement Learning, RL）时，最先记住的是 DQN、Policy Gradient、PPO 这些算法名字。但真正开始写代码时，首先要面对的通常不是损失函数，而是环境怎么创建、动作怎么发出去、观察怎么拿回来，以及一轮交互什么时候结束。本文重点不是直接讲训练算法，而是先把强化学习程序最基础的运行框架搭起来：环境对象、`reset()`、`step()`、动作空间（action space）、观察空间（observation space），以及后面非常重要的 Wrapper 机制。

换句话说，强化学习程序首先不是一个 “输入数据，输出预测” 的静态程序，而是一个持续交互的循环系统。智能体每次拿到当前观察，给出一个动作，环境执行这个动作后返回新的观察、奖励和结束标志，然后程序再进入下一轮。后续很多算法虽然形式不同，但底层都离不开这套交互骨架。

## 创建环境

先看最基础的一步：创建环境并重置。

```
import gymnasium as gym
env = gym.make("CartPole-v1")
obs, info = env.reset()
print("obs =", obs)
print("info =", info)
env.close()

```

这里最关键的是 `reset()`。新创建的环境不能直接开始正常交互，通常都要先调用一次 `reset()`，拿到初始观察。以 CartPole 为例，初始观察是一个 4 维浮点向量，`info` 则通常是一个附加信息字典。

![](https://mmbiz.qpic.cn/mmbiz_png/HXJUciaxf0UlZiamtfY1jJuMQFrVVrAtXcibf9KO67AxZdlRNx5siamuBlB9C7ggiaBJ4QyEQXGRVRWnUa1R2QHfX3sNNZ3FbmvWktibicctvnVaLw/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=0)

CartPole 这个环境本身很经典：它要做的是控制底部的小车左右移动，让上面的杆尽量保持不倒。环境每一步都会给出奖励 1，只要杆还没有倒下，回合就继续，因此累计奖励越高，说明系统被平衡得越久。

## 动作空间与观察空间

环境创建好之后，第一件值得做的事，不是立刻训练，而是先把输入输出接口看清楚。

```
import gymnasium as gym
env = gym.make("CartPole-v1")
obs, info = env.reset()
print("action_space =", env.action_space)
print("observation_space =", env.observation_space)
print("sample action =", env.action_space.sample())
print("sample obs =", env.observation_space.sample())
env.close()

```

```
在 CartPole 里，动作空间是 
Discrete(2)
Box

```

这一步非常重要，因为后面你不管写什么模型，本质上都要先回答两个问题：网络输入长什么样，网络输出长什么样。动作空间决定了输出接口，观察空间决定了输入接口。很多强化学习代码一开始就写乱，往往不是算法不懂，而是这两个接口根本没先看清楚。

## step() 才是交互核心

真正把强化学习程序和普通监督学习程序区分开的，是 `step()` 这个接口。

```
import gymnasium as gym
env = gym.make("CartPole-v1")
obs, info = env.reset()
action = 0
next_obs, reward, done, truncated, info = env.step(action)
print("next_obs =", next_obs)
print("reward =", reward)
print("done =", done)
print("truncated =", truncated)
print("info =", info)
env.close()

```

这里可以先抓住最核心的一点：执行一个动作之后，环境不会只返回一个结果，而是一次性返回 “动作后的完整反馈”。在 CartPole 的交互示例里，执行一步以后会得到新的 4 维观察、奖励 `1.0`、结束标志、截断标志，以及附加信息字典。

也正因为如此，强化学习程序的最小单位不是 “一条样本”，而是 “一次交互”。你不是在直接预测标准答案，而是在执行动作后，根据环境返回的反馈来判断这个动作是否有利。这个思路会贯穿后续所有方法。

## 一个完整回合的最小代码

把 `reset()` 和 `step()` 连起来，其实就已经有了强化学习程序的最小骨架。

```
import gymnasium as gym
env = gym.make("CartPole-v1")
obs, info = env.reset()
total_reward = 0.0
step_idx = 0
while True:
    action = env.action_space.sample()   # 随机动作
    obs, reward, done, truncated, info = env.step(action)
    total_reward += reward
    step_idx += 1
    if done or truncated:
        break
print("steps =", step_idx)
print("total_reward =", total_reward)
env.close()

```

这段代码里还没有任何学习过程，动作只是随机采样，但它已经把一整个回合完整跑通了：初始化环境、循环执行动作、累加奖励、直到回合结束退出。这个骨架非常重要，因为后面无论你接 DQN、策略梯度还是 actor-critic，最外层结构基本都还是这个样子，只是把 “随机动作” 换成“由策略产生动作”。

从工程角度看，先写一个随机版本很有价值。因为在真正训练前，你要先确认环境能正常创建、动作能正常发出、奖励在变化、结束条件也符合预期。否则你后面看到训练不收敛，很难判断是算法有问题，还是环境接口根本没跑通。对于 CartPole，这种随机策略通常只能维持比较短的回合，所以它更适合作为管线检查，而不是作为真正解题方法。

## 用一个 Agent 类组织动作逻辑

虽然最简单的写法是直接 `env.action_space.sample()`，但更好的结构通常是把 “动作决策” 单独封装成一个 Agent 对象。这样后面从随机策略切换到神经网络策略时，环境主循环就不需要大改。

```
import gymnasium as gym
class RandomAgent:
    def __init__(self, action_space):
        self.action_space = action_space
    def __call__(self, obs):
        return self.action_space.sample()
env = gym.make("CartPole-v1")
agent = RandomAgent(env.action_space)
obs, info = env.reset()
total_reward = 0.0
while True:
    action = agent(obs)
    obs, reward, done, truncated, info = env.step(action)
    total_reward += reward
    if done or truncated:
        break
print("total_reward =", total_reward)
env.close()

```

```
这种写法的意义在于，它把环境推进逻辑和策略逻辑拆开了。环境负责维护世界状态，Agent 负责根据当前观察产出动作。现在这个 
RandomAgent

```

## Wrapper：工程范扩展机制

当前面这些基础接口理顺以后，本文后半部分一个很重要的内容就是 Wrapper。Gymnasium 提供了一个很方便的扩展框架：`Wrapper`。它继承自 `Env`，构造时接受一个已有环境对象；如果想增加功能，可以重写 `step()`、`reset()`，或者使用更专门的子类来处理动作、观察、奖励。这个类层级包括 `ActionWrapper`、`ObservationWrapper` 和 `RewardWrapper`。

它的核心意义是：你不需要直接改环境源码，也能在外面 “套一层壳”，改变环境和智能体之间的接口形式。比如你可以改动作、改观察、改奖励，或者插入日志、可视化、统计逻辑。对于强化学习这种强依赖交互接口的程序来说，这种可插拔设计非常实用。

`ActionWrapper示例`

先看一个非常典型的动作包装器。它的思路是：智能体虽然给了一个动作，但我们以一定概率把这个动作替换成随机动作。

```
import gymnasium as gym
import random
class RandomActionWrapper(gym.ActionWrapper):
    def __init__(self, env, epsilon=0.1):
        super().__init__(env)
        self.epsilon = epsilon
    def action(self, action):
        if random.random() < self.epsilon:
            action = self.env.action_space.sample()
            print(f"Random action {action}")
        return action

```

这类包装器的关键点不在 “代码有多复杂”，而在于它展示了 Wrapper 的设计方式：你不去碰环境主体逻辑，只是在动作真正送进环境之前做一次拦截和修改。这里用的是 10% 概率替换为随机动作，这种机制常被用来增强探索。

把它接进环境里也很自然：

```
import gymnasium as gym
env = gym.make("CartPole-v1")
env = RandomActionWrapper(env, epsilon=0.1)
obs, info = env.reset()
while True:
    action = 0
    obs, reward, done, truncated, info = env.step(action)
    if done or truncated:
        break
env.close()

```

这个例子很能体现工程思维：很多时候，强化学习系统的行为不一定只靠模型本身来改变，也可以通过环境接口层的改造来增强探索、鲁棒性或者可调试性。只要接口结构设计得清楚，这些扩展都可以在不破坏主训练循环的前提下完成。

## 观察包装器与奖励包装器

除了动作包装器，Gymnasium 还把观察和奖励的改造也标准化了。`ObservationWrapper` 用来处理环境给出的观察，`RewardWrapper` 用来处理返回给智能体的奖励。前者需要重写 `observation(obs)`，后者需要重写 `reward(rew)`。

举个很简单的观察包装器例子，假设我们想把环境返回的观察乘一个常数再交给智能体：

```
import gymnasium as gym
import numpy as np
class ScaleObservationWrapper(gym.ObservationWrapper):
    def __init__(self, env, scale=0.1):
        super().__init__(env)
        self.scale = scale
    def observation(self, observation):
        return np.asarray(observation, dtype=np.float32) * self.scale
类似地，奖励包装器也可以写成这样：
import gymnasium as gym
class ScaleRewardWrapper(gym.RewardWrapper):
    def __init__(self, env, scale=0.1):
        super().__init__(env)
        self.scale = scale
    def reward(self, reward):
        return reward * self.scale

```

这两个例子都很简单，但思路非常重要。环境本身定义的是任务规则，而 Wrapper 定义的是 “智能体最终看到的接口形式”。这意味着很多训练层面的适配工作，并不需要侵入环境内部，而可以在外层通过统一接口完成。

## 渲染与录像

除了动作、观察、奖励这些 “训练相关” 的包装器，环境还可以通过渲染和录像帮助你调试交互过程。对于 CartPole 这类环境，观察值虽然只是几个数字，但实际运行时的现象更直观：你能直接看到小车怎么动、杆子什么时候倒下。数值日志和可视化结合起来，往往更容易排查问题。相关示例里也展示了渲染与录像包装器的使用方式。

一个常见的写法是：

```
import gymnasium as gym
env = gym.make("CartPole-v1", render_mode="rgb_array")
env = gym.wrappers.HumanRendering(env)
obs, info = env.reset()
while True:
    action = env.action_space.sample()
    obs, reward, done, truncated, info = env.step(action)
    if done or truncated:
        break
env.close()

```

如果想保存过程而不是直接弹窗显示，也可以换成录像包装器：

```
import gymnasium as gym
env = gym.make("CartPole-v1", render_mode="rgb_array")
env = gym.wrappers.RecordVideo(env, video_folder="video")
obs, info = env.reset()
while True:
    action = env.action_space.sample()
    obs, reward, done, truncated, info = env.step(action)
    if done or truncated:
        break
env.close()

```

这些功能看起来像 “附加能力”，但在强化学习里很有实际意义。因为很多问题不是看 loss 曲线能发现的，而是要看环境真实在怎么跑。渲染和录像能帮助你判断：动作是不是反了、回合是不是提前结束了、观察和现象是不是一致。对调试来说，这往往比多看几轮训练日志更直接。

## 本文真正要建立的程序观

看到这里，本文最核心的内容其实已经比较清楚了。强化学习程序首先是一套持续交互的系统接口，而不是一个静态预测器。你需要先知道怎么创建环境、怎么拿到初始观察、怎么执行一步动作、怎么理解动作空间和观察空间、怎么结束一个回合，以及怎么用 Wrapper 改造接口。

把这部分理顺之后，本系列后续再去讲价值函数方法、策略梯度方法或者 actor-critic，一切都会顺很多。因为后续新增的内容，无非是在这套交互骨架上加入 “更聪明的动作生成逻辑” 和“更系统的参数更新方式”。真正的基础，不是某个公式先背下来，而是先把环境接口和数据流看明白。

参考文献

Lapan, Maxim. _Deep Reinforcement Learning Hands-On: A Practical and Easy-to-Follow Guide to RL from Q-Learning and DQNs to PPO and RLHF_. Packt Publishing, 2024.