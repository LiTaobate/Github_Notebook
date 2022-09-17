# Robosuit 运用入门笔记
![image.png](https://upload-images.jianshu.io/upload_images/9147323-0944e4349c732472.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 相关链接

- Robosuit 的web界面：http://robosuite.ai/
- Robosuit库的代码链接：https://github.com/ARISE-Initiative/robosuite
- Robosuit白皮书论文链接：https://robosuite.ai/assets/whitepaper.pdf
- Robosuit官方说明文档链接：http://robosuite.ai/docs/overview.html
## 一、创建自己的机器人

### 第一步：设置世界，采用自带的默认世界MujocoWorldBase

```python3
from robosuite.models import MujocoWorldBase
world = MujocoWorldBase()
```
### 第二步：创建自己的机器人

```python3
from robosuite.models.robots import Panda
#也可以换成别的机器人
#from robosuite.models.robots import UR5e
mujoco_robot = Panda()
```

- 我们可以通过创建一个抓手实例并在机器人上调用 add_gripper 方法来为机器人添加一个抓手。
```python3
from robosuite.models.grippers import gripper_factory
gripper = gripper_factory('PandaGripper') # 调用抓手
mujoco_robot.add_gripper(gripper) # 使用抓手
```
- 要将机器人添加到世界中，我们将机器人放置到所需位置并将其合并到世界中
- 
```python3
mujoco_robot.set_base_xpos([0,0,0]) #设置空间位置#刚好能放置在桌子上，桌子高度为0.8，则该地方为mujoco_robot1.set_base_xpos([0.5,0,0.8])
world.merge(mujoco_robot) # 加入的语句
```
### 第三步：创建表。我们可以初始化创建桌子和地平面整体的，TableArena代表的是一个拥有桌子的整体环境
```python3
from robosuite.models.arenas import TableArena

mujoco_arena = TableArena()
mujoco_arena.set_origin([0.8, 0, 0])  #设置空间位置，这里的set_origin是对所有对象应用恒定偏移。在X轴偏移0.8
world.merge(mujoco_arena) # 加入的语句
```

### 第四步：添加对象。创建一个球并将其添加到世界中。
- 第一种：添加一个球
```python3
# from robosuite.models.objects import BallObject
# sphere = BallObject(
#     name="sphere",
#     size=[0.04],
#     rgba=[0, 0.5, 0.5, 1]).get_obj()
# sphere.set('pos', '1.0 0 1.0')
# world.worldbody.append(sphere)
```

- 第二种：添加一个盒子

```python3
# from robosuite.models.objects import BoxObject
# sphere = BoxObject(
#     name = "sphere",
#     size = [0.07, 0.07, 0.07],
#     rgba=[0, 0.5, 0.5, 1]).get_obj()
# sphere.set('pos', '1.0 0 1.0')
# world.worldbody.append(sphere)
```
- 第三种：添加一个胶囊 
```
from robosuite.models.objects import CapsuleObject
sphere = CapsuleObject(
    name = "sphere",
    size = [0.07,  0.07],
    rgba=[0, 0.5, 0.5, 1]).get_obj()
sphere.set('pos', '1.0 0 1.0')
world.worldbody.append(sphere)
```
```
from robosuite.utils.mjcf_utils import new_joint
```
- 也可以全部一起添加：
```
from robosuite.models.objects import BallObject
sphere1 = BallObject(
    name="sphere1",
    size=[0.04],
    rgba=[0, 0.5, 0.5, 1]).get_obj()
sphere1.set('pos', '1.3 0 1.0')
world.worldbody.append(sphere1)

from robosuite.models.objects import BoxObject
sphere2 = BoxObject(
    name = "sphere2",
    size = [0.07, 0.07, 0.07],
    rgba=[0, 0.5, 0.5, 1]).get_obj()
sphere2.set('pos', '1.4 0 1.0')
world.worldbody.append(sphere2)

from robosuite.models.objects import CapsuleObject
sphere3 = CapsuleObject(
    name = "sphere3",
    size = [0.07,  0.07],
    rgba=[0, 0.5, 0.5, 1]).get_obj()
sphere3.set('pos', '1.0 0 1.0')
world.worldbody.append(sphere3)
```


### 第5步：运行模拟。一旦我们创建了对象，我们可以mujoco_py通过运行获得一个模型
```
model = world.get_model(mode="mujoco_py")
from mujoco_py import MjSim, MjViewer

sim = MjSim(model)#启动仿真
viewer = MjViewer(sim)#可视化仿真
viewer.vopt.geomgroup[0] = 0 # 采用碰撞网格的可视化

for i in range(10000):
  sim.data.ctrl[:] = 0#这个部分不是很懂，后续补充
  sim.step()
  viewer.render()# 渲染render
```
### 整体代码
```python3
#第一步：设置世界，采用自带的默认世界MujocoWorldBase
import numpy as np 
from robosuite.models import MujocoWorldBase
world = MujocoWorldBase()

#第二步：创建自己的机器人
from robosuite.models.robots import UR5e
# mujoco_robot1 = Panda()
mujoco_robot1 = UR5e()

#我们可以通过创建一个抓手实例并在机器人上调用 add_gripper 方法来为机器人添加一个抓手。
from robosuite.models.grippers import gripper_factory
gripper = gripper_factory('PandaGripper')
mujoco_robot1.add_gripper(gripper)

#要将机器人添加到世界中，我们将机器人放置到所需位置并将其合并到世界中
mujoco_robot1.set_base_xpos([0.5,0,0.8])#刚好能放置在桌子上，桌子高度为0.8
world.merge(mujoco_robot1)
#第三步：创建桌子。我们可以初始化创建桌子和地平面,TableArena代表的是一个拥有桌子的整体环境
from robosuite.models.arenas import TableArena

mujoco_arena = TableArena()
mujoco_arena.set_origin([0.8, 0, 0])#这里的set_origin是对所有对象应用恒定偏移。在X轴偏移0.8
world.merge(mujoco_arena)

#第四步：添加对象。创建一个球并将其添加到世界中。

from robosuite.models.objects import BallObject
sphere1 = BallObject(
    name="sphere1",
    size=[0.04],
    rgba=[0, 0.5, 0.5, 1]).get_obj()
sphere1.set('pos', '1.3 0 1.0')
world.worldbody.append(sphere1)

from robosuite.models.objects import BoxObject
sphere2 = BoxObject(
    name = "sphere2",
    size = [0.07, 0.07, 0.07],
    rgba=[0, 0.5, 0.5, 1]).get_obj()
sphere2.set('pos', '1.4 0 1.0')
world.worldbody.append(sphere2)

from robosuite.models.objects import CapsuleObject
sphere3 = CapsuleObject(
    name = "sphere3",
    size = [0.07,  0.07],
    rgba=[0.5, 0.5, 0.5, 1]).get_obj()#颜色(三个0.5是黑色，A值代表透明度)
sphere3.set('pos', '1.0 0 1.0')
world.worldbody.append(sphere3)


from robosuite.utils.mjcf_utils import new_joint


#第5步：运行模拟。一旦我们创建了对象，我们可以通过运行mujoco_py获得一个模型
model = world.get_model(mode="mujoco_py")
from mujoco_py import MjSim, MjViewer

sim = MjSim(model)#启动仿真
viewer = MjViewer(sim)#可视化仿真
viewer.vopt.geomgroup[0] = 0 # 禁用碰撞网格的可视化，就是不显示碰撞提醒（绿色的碰撞覆盖面）

for i in range(10000):
  sim.data.ctrl[:] = 0
#   sim.data.ctrl[:6] = 1
  sim.step()
  viewer.render() #显示渲染过程


```
- 运行截图
![image.png](https://upload-images.jianshu.io/upload_images/9147323-690817c7382ac869.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/9147323-f1422190d826ed69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 二、快速开始
```python3
import numpy as np
import robosuite as suite

# create environment instance创建环境实例
env = suite.make(
    # env_name="Lift", # try with other tasks like "Stack" and "Door"
    env_name="Door", 
    robots="UR5e",  # try with other robots like "Sawyer" and "Jaco"
    has_renderer=True,
    has_offscreen_renderer=False,
    use_camera_obs=False,
    # use_camera_obs=True,
)

# 重置环境
env.reset()

for i in range(1000):
    action = np.random.randn(env.robots[0].dof) # 设置随机运动
    obs, reward, done, info = env.step(action)  # 在环境中采取行动
    env.render()  #渲染展示render：渲染
```
- 运行截图：
![image.png](https://upload-images.jianshu.io/upload_images/9147323-ae1b3dc847c6f516.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三、关于Robosuite的解读
关于Robosuite的控制器分类：
<img width="1261" alt="image" src="https://user-images.githubusercontent.com/73519321/190849962-6b8bd2bf-b989-40e0-a32d-1e73600c4bbf.png">


