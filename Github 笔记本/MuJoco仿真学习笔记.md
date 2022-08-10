## 一、软体模型搭建流程
![软体模型关 键 参 数的副本2.jpg](https://upload-images.jianshu.io/upload_images/9147323-479ad65299f5bdf1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1、mujoco官方文档：
>https://mujoco.readthedocs.io/en/latest/overview.html

2、mujoco的MJCF文件资料：
> https://mujoco.readthedocs.io/en/latest/modeling.html

3、mujoco的XML文件资料：
> https://mujoco.readthedocs.io/en/latest/XMLreference.html

4、XML文件编写 MuJoCo自定义机器人建模指南：
>  https://zhuanlan.zhihu.com/p/143983506

5、Matlab - Solidworks 机器人建模  如何把CAD文件转化成URDF文件:
> https://www.guyuehome.com/24525

### 关于xml建模的composite部分问题
1、官方文档指导：
> https://mujoco.readthedocs.io/en/latest/XMLreference.html

关于composite官方说明是：
> This is not a model element, but rather a macro which expands into multiple model elements representing a composite object. These elements are bodies (with their own joints, geoms and sites) that become children of the parent body containing the macro, as well as tendons and equality constraints added to the corresponding model sections. The automatically-generated bodies are laid out in a regular grid in 1D, 2D or 3D depending on the object type and count attributes. The macro expansion is done by the model compiler. If the resulting model is then saved, the macro will be replaced with the actual model elements. The defaults mechanism used in the rest of MJCF does not apply here, even if the parent body has a childclass attribute defined. Instead there are internal defaults adjusted automatically for each composite object type. Composite objects can only be defined if the model is in local coordinates. Using them in global coordinates results in compiler error. See [Composite objects](https://mujoco.readthedocs.io/en/latest/modeling.html#ccomposite) in the modeling guide for more detailed explanation.
这不是一个模型元素，而是一个扩展为表示复合对象的多个模型元素的宏。这些元素是实体（具有自己的关节、几何图形和站点），它们成为包含宏的父实体的子实体，以及添加到相应模型部分的肌腱和等式约束。根据对象类型和计数属性，自动生成的实体以 1D、2D 或 3D 的规则网格布局。宏扩展由模型编译器完成。如果生成的模型随后被保存，宏将被实际模型元素替换。 MJCF 其余部分中使用的默认机制在这里不适用，即使父主体定义了子类属性。相反，每个复合对象类型都会自动调整内部默认值。只有当模型在局部坐标中时，才能定义复合对象。在全局坐标中使用它们会导致编译器错误。有关更详细的说明，请参阅建模指南中的复合对象。

官方说明文档的主要部分包括：
>  https://mujoco.readthedocs.io/en/latest/XMLreference.html

![image.png](https://upload-images.jianshu.io/upload_images/9147323-eb8668d8aa1256e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
参考大佬文档：
>  https://zhuanlan.zhihu.com/p/143983506

自己写的xml代码：
```
 <mujoco model="soft_box">
  <asset>
    <texture file="../textures/skin.png" type="2d" name="skin-tex"/>
    <material name="skin-material" reflectance="0.5" texrepeat="1 1" texture="skin-tex"/>
  </asset>
  <worldbody>
    <body>
        <body name="object" pos="0.5 0.5 0.06" quat="0.5 0.5 -0.5 -0.5">
          <composite type="ellipsoid" count="9 4 11" spacing="0.035" solrefsmooth="-1324.17 -17.59">
            <geom type="ellipsoid" size="0.0075 0.025 0.25" rgba=".8 .2 .1 1" mass="0.01" friction="0.01 0.005 0.0001" contype="0" group="0"/>
            <skin material="skin-material" texcoord="true"/>
          </composite>
        </body>
        <site rgba="0 0 0 0" size="0.005" pos="0 0 -0.0522" name="bottom_site"/>
        <site rgba="0 0 0 0" size="0.005" pos="0 0 0.0522" name="top_site"/>
        <site rgba="0 0 0 0" size="0.005" pos="0.2 0.2 0" name="horizontal_radius_site"/>
    </body>
  </worldbody>
</mujoco>
```
其中：composite是针对复合对象建模的关键，是在body类下的一个，其参数中的 type包括了：
> type: [particle, grid, rope, loop, cloth, box, cylinder, ellipsoid], required【粒子、网格、绳索、环、布、盒子、圆柱、椭球】

而其body/ geom (*)主要定义了模型的几何结构，type包括了以下：
> type: [plane, hfield, sphere, capsule, ellipsoid, cylinder, box, mesh], “sphere”[平面，hfield，球体，胶囊，椭球体，圆柱体，盒子，网格]

于是使用了椭球体ellipsoid，并定义了三个半径量来搭建出来的模型如下图，但是最主要是的是模型里边的solrefsmooth="-1324.17 -17.59"部分，定义的是该模型的刚度和阻尼等（还需要完善）。
这部分的官方参考文档是：
> https://mujoco.readthedocs.io/en/latest/modeling.html#csolver

![image.png](https://upload-images.jianshu.io/upload_images/9147323-c88144f17fcc915e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 二、将对象模型从solidworks导入Mujoco
![3维模型导入mujoco.png](https://upload-images.jianshu.io/upload_images/9147323-ff747f29887dca4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




