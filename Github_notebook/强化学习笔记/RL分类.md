#  **RL分类**

## **Modelfree 和 Modelbased**
![image](https://user-images.githubusercontent.com/73519321/188315846-31f1fb98-ec21-47b5-a0d6-b8a8265e24cc.png)


### **1、不理解环境（Modelfree)**

- **Q-learning**
- **Sarsa**
- **Policy Gradients**
- **DQN**

### **2、理解环境（Modelbased)**

- **子主题 1**
- **子主题 2**
- **子主题 3**
- **子主题 4**

## **基于概率 和 基于价值**
![image](https://user-images.githubusercontent.com/73519321/188315903-928e70a6-f746-4532-9b8d-88b93be9e9de.png)


### **基于概率（Policy-Based RL）：根据概率选取行动**

- **Policy Gradients**

### **基于价值（Value-Based RL）：选价值最高的来选取动作**

- **Sarsa**
- **Q-learning**

## **回合更新 和 单步更新**

![image](https://user-images.githubusercontent.com/73519321/188315916-067fc43c-ffcb-415e-a50e-75bbb30b81ca.png)

### **回合更新（Monte-carlo update）**

- 回合更新指的是游戏开始后, 我们要等待游戏结束, 然后再总结这一回合中的所有转折点, 再更新我们的行为准则.

	- 基础版policy gradients
	- Monte-carlo learning

### **单步更新（Temporal-Difference update）**

- 单步更新则是在游戏进行中每一步都在更新, 不用等待游戏的结束, 这样我们就能边玩边学习了.

	- Q-learning
	- Sarsa
	- 升级版的 policy gradients 

## **在线学习 和 离线学习**

![image](https://user-images.githubusercontent.com/73519321/188315929-48e67347-b600-4e2f-9f1b-e33fe75bec62.png)

### **在线学习（On-Policy）：边玩边学**

- Sarsa
- Sarsa lambda

### **离线学习（Off-Policy）：可以选择自己玩或者看着别人玩**

- Q learning
-  Deep-Q-Network

