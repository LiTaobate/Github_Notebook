
# Mujoco学习笔记
## 一、Mujoco使用的优点：
![优点.png](https://upload-images.jianshu.io/upload_images/9147323-273c3df0826bf341.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 二、具体课程内容
![课程安排.png](https://upload-images.jianshu.io/upload_images/9147323-1a2fc5a6ba4e8997.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 三、学习笔记
### 1、环境需求与搭建（推荐使用python3.6或者python3.7）
![（1) 打开终编 mkdir -.muloco.png](https://upload-images.jianshu.io/upload_images/9147323-37628af2c2ff3f36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 2、mujoco_py安装
![MWoco_Py安装与配置.png](https://upload-images.jianshu.io/upload_images/9147323-d13ccc8e1279b668.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
测试代码：
```py
import mujoco_py
import os
mj_path, _ = mujoco_py.utils.discover_mujoco()
xml_path = os.path.join(mj_path,'model','humanoid.xml')
model = mujoco_py.load_model_from_path(xml_path)
sim = mujoco_py.MjSim(model)
print(sim.data.qpos)
sim.step()
print(sim.data.qpos)
```

### 3、python基础（推荐阿里源，要相对通畅一些）

![1. Python基础.png](https://upload-images.jianshu.io/upload_images/9147323-7912067d56e9f678.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4、XML基础

![概念.png](https://upload-images.jianshu.io/upload_images/9147323-1f418406d84ede50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![元素名称不能以数字或特殊字符开头.png](https://upload-images.jianshu.io/upload_images/9147323-6eb2671b1d57d2a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### URDF知识 

![e.ce a 人.png](https://upload-images.jianshu.io/upload_images/9147323-89e5cae49ca8946b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
URDF里边，mesh文件用来描述属性，collision是碰撞检测的，使用.sti文件
获取URDF的方式
![如何获得URDF？.png](https://upload-images.jianshu.io/upload_images/9147323-0886438531f2ead7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
MJCF格式文件（与urdf相似）
两者之间转换的方式：
![进入mujoco下的bin目录.png](https://upload-images.jianshu.io/upload_images/9147323-2e4263da11e18c83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
元素分析；xml文件的元素内容
![2. 元素as50t定义所有mesh为子元素.png](https://upload-images.jianshu.io/upload_images/9147323-ab30e4954bc53767.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
控制器接口
![控制器是机器人最主要的部件，没有之一.png](https://upload-images.jianshu.io/upload_images/9147323-09a5bce1925542ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

控制器在mujoco中的体现：在下列描述的文件路径里边，通过改变ctrl的值来控制施加的力。
![midata结构：.png](https://upload-images.jianshu.io/upload_images/9147323-66b94e7930fcd78e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![1用牛额-欧拉方程对楼你骨进行分新时，动力学方程可以写成如下形式.png](https://upload-images.jianshu.io/upload_images/9147323-d203784bafffb05b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![mujoco中有很多自定义基本数据结构.png](https://upload-images.jianshu.io/upload_images/9147323-e152be9de4d5c5a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下图的代表UR5的六个电机，ctrllimited代表电机输入限制，后边的参数代表输入范围
中间的语句用来查看XML文件的电机设置的输入范围，值单位是N*米
![Physical Lisits of the.png](https://upload-images.jianshu.io/upload_images/9147323-65d4374f4e917112.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![3. sim.step0.png](https://upload-images.jianshu.io/upload_images/9147323-69296ad814446992.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
实际例子：

```py
import mujoco_py as mp
model = mp.load_model_from_path('ur5.xml')#编译出来的UR5模型
sim = mp.MjSim(model)#启动仿真
viewer = mp.MjViewer(sim)#就是一个显示器

for i in range(3000):      #循环三千次
    # sim.data.ctrl[:6] = 1
    sim.step()
    viewer.render()  #显示仿真过程的
```

![import mujoco_py as •D.png](https://upload-images.jianshu.io/upload_images/9147323-e62f1212c9612d83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

PID控制器搭建
![1.PID控制原理.png](https://upload-images.jianshu.io/upload_images/9147323-7398e220422817ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![simple_pid库.png](https://upload-images.jianshu.io/upload_images/9147323-08d826278badb3de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![ Python中文网.png](https://upload-images.jianshu.io/upload_images/9147323-a6609babb35f3286.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![设定点位.png](https://upload-images.jianshu.io/upload_images/9147323-27df85571817dd20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Gym 库的注册（把自己的环境加进去gym的模型里边）
![注册.png](https://upload-images.jianshu.io/upload_images/9147323-2d8f9e599f77add1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
